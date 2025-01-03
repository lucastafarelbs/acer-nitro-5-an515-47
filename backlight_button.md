### Backlight was not working at all
To fix it I needed to change a parameter in kernel

#### I'm using systemd-boot
So my kernel params are set in the entry .conf

```
sudo nvim /boot/efi/loader/entries/Pop_OS-current.conf

### add this to the kernel params

acpi_backlight=native
```
