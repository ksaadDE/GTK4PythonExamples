# Window Positioning in GTk 4
You probably want to find a solution. There's none. They removed the API because Wayland and other Window Managers (WMs) do not use coordinates. They won't even offer a API to use some kind of relative CENTERED Positioning or something like that. 

Acoording to the GTK 4 Devs, they see a bad GUI style, if someone is using fixed positioning. Other reasons (reasonable somehow) are security considerations (capturing events, from other Windows etc)

However, in styling props you could use children widget's offets, to exploit this, to enable somekind of coordinates orientation. There is also a away around it using X11. However, I haven't used it yet and not figured it out for GTK 4. Probably it's very time consuming.

Sorry for you! Good luck, inform me if you find a proper solution in Python3 and GTK 4. Thank you!
