# Signal Handling in GTK-4 Python3
## Default way, pre-given Signals
You can just connect to the EventListener of a Widget, Window etc. It is pretty straight forward, although there is no list that helps you to find the Signals, you need to search in the GTK 4 Documentation for everything.
```python3

# init func of your Widget/Window
def __init__(...):
  window.connect("event", eventListener)

# eventListener Func
def eventListener (params...):
  print("this event triggered yo")
```
## How to create and use custom signals?
The fun starts when you try to hook custom signal e.g. after destroying the Window and showing it again. After ton of research, I was able to find the required GTK 4 steps. 

First of all import the GObject Class from the gi Repository, and use it to setup a custom SignalObject and hook to our object, which is usually some object that is using the class Widget or Window.

### Create custom Signals

```python3
import gi
gi.require_version('Gtk', '4.0')
gi.require_version('Adw', '1')
from gi.repository import GObject

# init Func of your Widget/Window
def __init__(...):
  GObject.signal_new('custom-signal', self, GObject.SignalFlags.RUN_FIRST, self,())
  self.connect("custom-signal", onCustomSignal)

# the eventListener Func
def onCustomSignal (self, ...):
    print("my custom signal bro")

```
Keep in mind that you have to change the param array after RUN_FIRST, if you want to give in to the eventListener multiple parameters etc. But that works fine.

### Trigger Custom Signals
After initializing our nice custom Signal, we need to use emit to trigger it. 
```python3
emit("custom-signal")
```
And you finally burned the world, good job :-)

Hint: Don't forget to use `self.` if you are operating in a object (e.g. using a custom class). 
