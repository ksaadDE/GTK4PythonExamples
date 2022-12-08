# GTK 4 Notifications
You are probably asking yourself how to use Notifications in GTK 4, right?

## Easy Notification
you can easily setup a simple notification if you are just defining the Notification from Gio, setting the title and sending it
```python3
  notification = Gio.Notification()
  notification.set_title("you are fat is now installed")
  app.send_notification("test", notification)
```

## Using a icon from file 
Load the Icon using Gio.File.new_for_path and add it using Gio.FileIcon + `notification.set_icon(icon)`
```python3
  notification = Gio.Notification()
  notification.set_title("Your coffee is ready!")
  notification.set_body("The coffee machine wants to report, that your coffee is brewed. Please drink!")

  file = Gio.File.new_for_path("/usr/share/icons/<theme>/apps/16/<file>")
  icon = Gio.FileIcon(file=file)

  notification.set_icon(icon)
  app.send_notification("test", notification)
```

## Download Icon from Web
It downloads the Icon from url and overwrite it in a local file. Using Gio.Copy, Gio.FileCopyFlags and FileIcon etc.
```python3
  url="https://icons.iconarchive.com/icons/custom-icon-design/mono-business-2/256/coffee-icon.png"
  localFileName = "./coffee-icon.png"
  file = Gio.File.new_for_uri(url)
  tmpFile = Gio.File.new_for_path (localFileName)
  file.copy(tmpFile, Gio.FileCopyFlags.OVERWRITE)
  icon = Gio.FileIcon(file=tmpFile)
  notification.set_icon(icon)
```
