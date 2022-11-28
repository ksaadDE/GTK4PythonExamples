# App Icon
If you want to add a App Icon it can get annoying. GTK uses Themes, which are using Icons which are stored in `/usr/share/icons` e.g. `/usr/share/icons/<Theme>/apps/32/icon.png`
```python3
   from gi.repository.Gtk import IconTheme
   display = self.get_display()
   itheme = IconTheme.get_for_display (display)
   self.set_icon_name ("icon")
```

## Helpers (useful)
There are quite a few helper functions which you can use to investigate issues.
```python3
    print(itheme.get_theme_name())
    print(itheme.get_resource_path())
    print(itheme.get_icon_names())
```

## Other Less Useful Functionalities
```python3
    #print(itheme.set_resource_path (["/usr/share/icons"]))
    #print(itheme.set_search_path (["/usr/share/icons"]))
```
