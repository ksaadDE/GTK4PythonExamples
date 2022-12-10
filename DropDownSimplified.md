# GTK.DropDown Simplified
Today I can finally announce that I surrounded the GTK DropDown Stuff with a Custom Class, that improves handling of the GTK4.DropDown.

## The generalized DataDropDownChild Class
First of all I have generalized the GObject for the Gio List, so we can populate as key,value pair using a class (you can extend this further ofc)
```python3
class DataDropDownChild(GObject.Object):
    __gtype_name__ = 'DataDropDownChild'

    def __init__(self, key, value):
        super().__init__()

        self.k = key
        self.v = value

    @GObject.Property
    def key(self):
        return self.k

    @GObject.Property
    def value(self):
        return self.v

    def getKey (self, ):
        return self.k

    def getValue (self,):
        return self.v
```

## The DropDown Class
As you can see the signal receiving methods are capsuled in the class. However, if you give your wished signal receiver method into the init of your DropDown class, you can customize everything. While not loosing any time one messy code.
This is using the previously created Child Class as object, so we can dynamically and easily populate, without loosing control.
```python3 
class DropDown:
    def __init__ (self, data=[], factoryBindSetupMethod=None, factoryBindMethod=None, itemSelectNotifyMethod=None):
        self.box = None
        self.model = Gio.ListStore(item_type=DataDropDownChild)
        for k in data.keys():
            self.model.append(DataDropDownChild(key=k, value=data[k]))

        # Set up the factory
        self.factory = Gtk.SignalListItemFactory()
        if factoryBindSetupMethod is None:
            self.factory.connect("setup", self._on_factory_setup)
        else:
            self.factory.connect("setup", factoryBindSetupMethod)

        if factoryBindMethod is None:
            self.factory.connect("bind", self._on_factory_bind)
        else:
            self.factory.connect("bind", factoryBindMethod)

        self.dd = Gtk.DropDown(model=self.model, factory=self.factory, hexpand=True)

        if itemSelectNotifyMethod is None:
            self.dd.connect("notify::selected-item", self._on_selected_item_notify)
        else:
            self.dd.connect("notify::selected-item", itemSelectNotifyMethod)

    def getDropDownElement (self,):
        return self.dd

    def getDropDownElementWithBoxAndLabel (self, labelText="Select Object:"):
        self.box = Gtk.Box(spacing=12, hexpand=True, vexpand=True)
        self.box.props.margin_start = 12
        self.box.props.margin_end = 12
        self.box.props.margin_top = 6
        self.box.props.margin_bottom = 6
        self.box.append(Gtk.Label(label=labelText))
        self.box.append(self.getDropDownElement())
        return self.box

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
        item = list_item.get_item()
        label.set_text(item.v)

    # The notify signal is fired when the selection changes
    def _on_selected_item_notify(self, dropdown, _):
        item = dropdown.get_selected_item()
        print(item.getKey(), item.getValue())
 ```

## Example use in your Gtk.ApplicationWindow class
The following example uses a Dict to populate a Gio.List using a custom, generalized DataDropDownChild class. It uses the DropDown Class as well and you can see if you change the selected item that it will show 22 in terminal. Means you can put your receiving method wherey you want to place it. 
```python3
# [...]
class ExampleWindow(Gtk.ApplicationWindow):
    def __init__(self, app):
        super().__init__(application=app, title="Test DropDown")
        data={
                "hi":"data",
                "t":"bye"
        }
        self.dd = DropDown(data=data, itemSelectNotifyMethod=self.onItemChangeNotify)
        self.dropDownBox = self.dd.getDropDownElementWithBoxAndLabel(labelText="HI")
        self.set_child(self.dropDownBox)
    
    def onItemChangeNotify(self, dropdown, _):
        item = dropdown.get_selected_item()
        print("22:", item.getKey(), item.getValue())
# [...]
```

## Full Example 
```python3
import gi

gi.require_version('Adw', '1')
gi.require_version('Gtk', '4.0')

from gi.repository import Adw, Gio, GObject, Gtk


class DataDropDownChild(GObject.Object):
    __gtype_name__ = 'DataDropDownChild'

    def __init__(self, key, value):
        super().__init__()

        self.k = key
        self.v = value

    @GObject.Property
    def key(self):
        return self.k

    @GObject.Property
    def value(self):
        return self.v

    def getKey (self, ):
        return self.k

    def getValue (self,):
        return self.v


class DropDown:
    def __init__ (self, data=[], factoryBindSetupMethod=None, factoryBindMethod=None, itemSelectNotifyMethod=None):
        self.box = None
        self.model = Gio.ListStore(item_type=DataDropDownChild)
        for k in data.keys():
            self.model.append(DataDropDownChild(key=k, value=data[k]))

        # Set up the factory
        self.factory = Gtk.SignalListItemFactory()
        if factoryBindSetupMethod is None:
            self.factory.connect("setup", self._on_factory_setup)
        else:
            self.factory.connect("setup", factoryBindSetupMethod)

        if factoryBindMethod is None:
            self.factory.connect("bind", self._on_factory_bind)
        else:
            self.factory.connect("bind", factoryBindMethod)

        self.dd = Gtk.DropDown(model=self.model, factory=self.factory, hexpand=True)

        if itemSelectNotifyMethod is None:
            self.dd.connect("notify::selected-item", self._on_selected_item_notify)
        else:
            self.dd.connect("notify::selected-item", itemSelectNotifyMethod)

    def getDropDownElement (self,):
        return self.dd

    def getDropDownElementWithBoxAndLabel (self, labelText="Select Object:"):
        self.box = Gtk.Box(spacing=12, hexpand=True, vexpand=True)
        self.box.props.margin_start = 12
        self.box.props.margin_end = 12
        self.box.props.margin_top = 6
        self.box.props.margin_bottom = 6
        self.box.append(Gtk.Label(label=labelText))
        self.box.append(self.getDropDownElement())
        return self.box

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
        item = list_item.get_item()
        label.set_text(item.v)

    # The notify signal is fired when the selection changes
    def _on_selected_item_notify(self, dropdown, _):
        item = dropdown.get_selected_item()
        print(item.getKey(), item.getValue())

class ExampleWindow(Gtk.ApplicationWindow):
    def __init__(self, app):
        super().__init__(application=app, title="DropDown")

        data={
                "hi":"data",
                "t":"bye"
        }
        self.dd = DropDown(data=data, itemSelectNotifyMethod=self.onItemChangeNotify)
        self.dropDownBox = self.dd.getDropDownElementWithBoxAndLabel(labelText="HI")

        self.set_child(self.dropDownBox)

    def onItemChangeNotify(self, dropdown, _):
        item = dropdown.get_selected_item()
        print("22:", item.getKey(), item.getValue())


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
