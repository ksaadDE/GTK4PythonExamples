# Signal Handling in GTK-4 Python3
## Using default Signals
You can just connect to the EventListener of a Widget, Window etc.
```python3

# init func of your Widget/Window
def __init__(...):
  window.connect("event", eventListener)

# eventListener Func
def eventListener (params...):
  print("this event triggered yo")
```
## How to create custom signals?
The fun starts when you try to hook custom signal e.g. after destroying the Window and showing it again. After ton of research, I was able to find the required GTK 4 steps. 
First of all import the GObject Class from the gi Repository, and use it to setup a custom SignalObject and hook to our object, which is usually some object that is using the class Widget or Window.

```python3
import gi
gi.require_version('Gtk', '4.0')
gi.require_version('Adw', '1')
from gi.repository import GObject

# init Func of your Widget/Window
def __init__(...):
  GObject.signal_new('custom-signal', self, GObject.SignalFlags.RUN_FIRST, self,())
  self.connect("custom-signal", self.onCustomSignal)

# the eventListener Func
def onCustomSignal (self, ...):
    print("my custom signal bro")

```
Keep in mind that you have to change the param array after RUN_FIRST, if you want to give in to the eventListener multiple parameters etc. But that works fine.
