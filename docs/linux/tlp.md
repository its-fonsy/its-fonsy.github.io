# TLP

Install `tlp` package with

    pacman -S tlp

then edit the configuration `/etc/tlp.conf` as needed.
Then enable and start the service

    systemctl enable tlp
    systemctl start tlp

# Battery Care

To guarantee long life on battery laptop is important to set value for
`START_CHARGE_THRESH_BATx` and `STOP_CHARGE_THRESH_BATx`. For specific values
this rules can be followed[^1]
    
> If the laptop is plugged most of the time and rarely unplugged, maximizing
> battery lifetime at the cost of a greatly reduced runtime may be acceptable,
> with values like starting charge at 40% and stopping at 50%.

> On the contrary, if you use it unplugged most of the time, starting charge at
> 85% and stopping at 90% would allow for a much longer runtime and still give a
> lifespan benefit over the factory settings.

To configure TLP using the former edit `/etc/tlp.conf` and change as follows

    START_CHARGE_THRESH_BAT0=40
    STOP_CHARGE_THRESH_BAT0=50

this will force "BAT0" to be charged only when it reaches 40% then at 50% it
will stop.

To learn more read [Battery Care](https://linrunner.de/tlp/faq/battery.html).

# Resources

[^1] : https://linrunner.de/tlp/faq/battery.html#how-to-choose-good-battery-charge-thresholds
