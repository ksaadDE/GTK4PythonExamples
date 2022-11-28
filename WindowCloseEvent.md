# Window Close Event / Close-Request Event
If you are using the `Gtk.ApplicationWindow` you can listen to the event using:
```python3
# connect Listener
win.connect("close-request", onWindowCloseEvent)

# eventListener
def onWindowCloseEvent (self, window):
    print(window)
    print("Window destroyed")
```
Previously there was "destroyed" and "close-event" (or something like this). Those two are obsolete now according to their [Gitlab Bugtracker](https://gitlab.gnome.org/GNOME/gtk/-/issues/3243).

## prevent Window Close Event
To just ignore the event use `return True` in the EventListener

```python3
# eventListener
def onWindowCloseEvent (self, window):
    return True
```
The event shouldn't be executed further

There's a second way, that puts the Window in background and keeps it running. Using the [`set_hide_on_close`](https://docs.gtk.org/gtk4/method.Window.set_hide_on_close.html) Event
```python3
self.set_hide_on_close( True )
```
