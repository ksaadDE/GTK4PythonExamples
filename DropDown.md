# GTK4.DropDown
You are asking yourself how to use the [GTK4.DropDown](https://docs.gtk.org/gtk4/class.DropDown.html)? The [Forums](https://discourse.gnome.org/t/migrate-from-comboboxtext-to-comborow-dropdown/10565/2) giving a resonable answer to this.
What changed from GTK3 is that the system uses a Model, Factory and some other stuff, to make it more structured and to increase the performance.

## The Model
First of all you need to add a Model Class as GObject 
```python3
class Country(GObject.Object):
    __gtype_name__ = 'Country'

    def __init__(self, country_id, country_name):
        super().__init__()

        self._country_id = country_id
        self._country_name = country_name

    @GObject.Property
    def country_id(self):
        return self._country_id

    @GObject.Property
    def country_name(self):
        return self._country_name
```

## List Store
Then you can, for example, use the `Gio.ListStore` to add items to the list.

```python3
Gio.ListStore(item_type=Country)
```
## List factory
Further you need to setup a Factory
```python3
  factory = Gtk.SignalListItemFactory()
  factory.connect("setup", self._on_factory_setup)
  factory.connect("bind", self._on_factory_bind)
  
  def _on_factory_setup(self, factory, list_item):
    label = Gtk.Label()
    list_item.set_child(label)

  def _on_factory_bind(self, factory, list_item):
    label = list_item.get_child()
    country = list_item.get_item()
    label.set_text(country.country_name)
```

## The DropDown
After setting up the factory you can finally setup the DropDown
```python3
  self.dd = Gtk.DropDown(model=self.model, factory=factory, hexpand=True)
```

You add it to the GTK.Window like every other child using vbox.append or set_child.

### Events of DropDown
If you want to capture the event like selectedItem use notifications and listen for `notify::selected-item`

```python3
self.dd.connect("notify::selected-item", self._on_selected_item_notify)
```

Then you can just add a method that catches the event and e.g. prints the selected item (in this case country)
```python3
    def _on_selected_item_notify(self, dropdown, _):
        country = dropdown.get_selected_item()
        print(country)
```

## Full Example App
Taken from the forums and tested.
```python3
import gi

gi.require_version('Adw', '1')
gi.require_version('Gtk', '4.0')

from gi.repository import Adw, Gio, GObject, Gtk


class Country(GObject.Object):
    __gtype_name__ = 'Country'

    def __init__(self, country_id, country_name):
        super().__init__()

        self._country_id = country_id
        self._country_name = country_name

    @GObject.Property
    def country_id(self):
        return self._country_id

    @GObject.Property
    def country_name(self):
        return self._country_name

class ExampleWindow(Gtk.ApplicationWindow):
    def __init__(self, app):
        super().__init__(application=app, title="DropDown")

        nodes = {
            "au": "Austria",
            "uk": "United Kingdom",
            "us": "United States",
        }

        # Populate the model
        self.model = Gio.ListStore(item_type=Country)
        for n in nodes.keys():
            self.model.append(Country(country_id=n, country_name=nodes[n]))

        # Set up the factory
        factory = Gtk.SignalListItemFactory()
        factory.connect("setup", self._on_factory_setup)
        factory.connect("bind", self._on_factory_bind)

        self.dd = Gtk.DropDown(model=self.model, factory=factory, hexpand=True)
        self.dd.connect("notify::selected-item", self._on_selected_item_notify)

        box = Gtk.Box(spacing=12, hexpand=True, vexpand=True)
        box.props.margin_start = 12
        box.props.margin_end = 12
        box.props.margin_top = 6
        box.props.margin_bottom = 6
        box.append(Gtk.Label(label="Select Country:"))
        box.append(self.dd)
        self.set_child(box)

    # Set up the child of the list item; this can be an arbitrarily
    # complex widget but we use a simple label now
    def _on_factory_setup(self, factory, list_item):
        label = Gtk.Label()
        list_item.set_child(label)

    # Bind the item in the model to the row widget; again, since
    # the object in the model can contain multiple properties, and
    # the list item can contain any arbitrarily complex widget, we
    # can have very complex rows instead of the simple cell renderer
    # layouts in GtkComboBox
    def _on_factory_bind(self, factory, list_item):
        label = list_item.get_child()
        country = list_item.get_item()
        label.set_text(country.country_name)

    # The notify signal is fired when the selection changes
    def _on_selected_item_notify(self, dropdown, _):
        country = dropdown.get_selected_item()
        print(country)


class ExampleApp(Adw.Application):
    def __init__(self):
        super().__init__()
        self.window = None

    def do_activate(self):
        if self.window is None:
            self.window = ExampleWindow(self)
        self.window.present()


app = ExampleApp()
app.run([])


```

