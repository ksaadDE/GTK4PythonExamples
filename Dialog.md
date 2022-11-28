 # Dialog Boxes
 The thing is self-explanatory aslong you don't try to use ResponseTypes. If you want to use ResponseTypes you have two options.
 
 ## Option No 1
 ```python3
dialog.add_buttons("OK", Gtk.ResponseType.OK, "Cancel", Gtk.ResponseType.CANCEL)
 ```
 This adds two buttons (that works with `dialog.add_button` as well). The first parameter is a string that gives the Button a label. The second gives the Response code internally converted to integer.
 The code should be in your init for GTK.Dialog or a subclass.
 
 ## Option No 2
 You define a custom button in Dialog init, and finally add it with the following loc:
```python3
area = dialog.get_content_area()
btn = Gtk.Button(label="OK")
btn.connect("clicked", self.dialogok_clicked)
area.append (btn) # add btn to area, therefore to dialog
```

Then in your dialog_clicked Listener you will trigger the .response event using the ResponseCode (e.g. .Cancel)
 ```python3
    def dialogok_clicked (self, btn):
        if len (self.objects) > 2:
            self.data = self.objects[2]
        #self.response = Gtk.ResponseType.OK
        return self.response(Gtk.ResponseType.CANCEL) # or .OK whatever
```
Then you can use it if you have abstracted GTK.Dialog in e.g. MessageBox with a return value. Because you have linked the response event of the object created using MessageBox (GTK.Dialog) class using connect
```python3
  #dialog.connect("response", on_dialog_response)
  def on_dialog_response(self, widget, response_id):
    print(widget.getData())
    if response_id == Gtk.ResponseType.OK:
     print("Ok")
    elif response_id == Gtk.ResponseType.CANCEL:
     print("cancel")
    # if the messagedialog is destroyed (by pressing ESC)
    elif response_id == Gtk.ResponseType.DELETE_EVENT:
     print("dialog closed or cancelled")
     widget.destroy()
``` 
 
## Full Example
Sounds complicated huh? Here the full example:
```python3
import gi
gi.require_version('Gtk', '4.0')
gi.require_version('Adw', '1')
from gi.repository import Gtk, Adw

class MessageBox (Gtk.Dialog):
    def __init__ (self, text="Hi", *args, **kwargs):
        super ().__init__(*args, **kwargs)
        self.area = self.get_content_area()
        message= Gtk.Label()
        message.set_text(text)
        self.area.append(message)
        # option 2
        #btn = Gtk.Button(label="OK")
        #btn.connect("clicked", self.dialogok_clicked)
        #self.area.append(btn)
        
        # option 1
        self.add_button ("OK", Gtk.ResponseType.OK)
        #self.add_buttons("OK", Gtk.ResponseType.OK, "Cancel", Gtk.ResponseType.CANCEL)
        self.show()

    # option 2
    # def dialogok_clicked (self, btn):
    #    # ok if label "ok"; .cancel if not
    #    return self.response(Gtk.ResponseType.CANCEL) if not btn.get_label().lower() == "ok" else self.response(Gtk.ResponseType.OK)
        
class EntryWindow(Gtk.ApplicationWindow):
        def __init__(self, *args, **kwargs):
            super().__init__(*args, **kwargs)

            self.ndata = None

            self.set_size_request (200,100)
            self.timeout_id = None
            
            display = self.get_display()
            
            dialog = MessageBox("Hi dude! Please click ok")
            dialog.connect("response", on_dialog_response)
         
        # for both options in Window class
        def on_dialog_response(self, widget, response_id):
            # if response is OK
            if response_id == Gtk.ResponseType.OK:
                print("Ok")
                widget.destroy() # destroy window after doing what you want to do (handling event)
            # if response is CANCEL
            elif response_id == Gtk.ResponseType.CANCEL:
                print("cancel")
                widget.destroy() # destroy window after doing what you want to do (handling event)
            # if the messagedialog is destroyed (by pressing ESC)
            elif response_id == Gtk.ResponseType.DELETE_EVENT:
                print("dialog closed or cancelled")
                widget.destroy() # destroy window after doing what you want to do (handling event)


class MyApp(Adw.Application):
    def __init__(self, **kwargs):
        super().__init__(**kwargs)
        self.connect('activate', self.on_activate)

    def on_activate(self, app):
        self.win = EntryWindow (application=app)
        self.win.present()


app = MyApp(application_id="de.b00bs.test")
app.run(sys.argv)
```
