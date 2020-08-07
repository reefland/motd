# Message of the Day

Collection of my 'Message of the Day' scripts.


### Requirements

  * [update-motd](https://launchpad.net/update-motd)
  * [figlet](http://www.figlet.org/) & [lolcat](https://github.com/busyloop/lolcat) (for `10-display-name`)
  * [hddtemp](https://savannah.nongnu.org/projects/hddtemp/) (for `36-diskstatus`)
  * [smartmontools](https://www.smartmontools.org/) (for `36-diskstatus`)

### How do I set it up?

Copy the files you want in your MOTD to `/etc/update-motd.d/`. Make sure you have the `PrintMotd`
option set to `yes` in your sshd config.

The duplicate files are different versions of the same, use either one of them. E.g. `30-zpool-simple`
will not print usage bars.

The script `36-diskstatus` greps syslog for smartd entries to read last self-test result.
You have to enable smartd monitoring & run regular self-tests for it to display anything.

If you use `50-fail2ban` you should comment out the `compress` option in `/etc/logrotate.d/fail2ban`,
so that the logs are not compressed and can be grepped.


-----

If `hddtemp` is unable to locate a temperature sensors but smartd shows a sensor exists, it can be added:
```bash
$ sudo hddtemp /dev/sdg
WARNING: Drive /dev/sdg doesnt seem to have a temperature sensor.
WARNING: This doesnt mean it hasnt got one.
WARNING: If you are sure it has one, please contact me (hddtemp@guzu.net).
WARNING: See --help, --debug and --drivebase options.
/dev/sdg: Samsung SSD 840 Series :  no sensor

$ sudo smartctl -a /dev/sda | grep Temp
190 Airflow_Temperature_Cel 0x0032   077   060   000    Old_age   Always       -       23

```
_NOTE: Attribute 194 is common for Hard Drives, however many SSDs use attribute "190" for a temperature sensor, this can be added to the HDDTemp database_

```bash
sudo sh -c 'echo \"Samsung SSD \(840\|860\)\" 190 C \"Temp for Samsung SSDs\" >> /etc/hddtemp.db'

# Check entry
tail -1 /etc/hddtemp.db
"Samsung SSD (840|860)" 190 C "Temp for Samsung SSDs"
```
_Note: All the "\" characters are required, it tells bash not to interpret the following character, accept it as a literal._

* Field 1: Use a string or regex matching the drive's display name (as reported by hddtemp output)
* Field 2: SMART data field number (190 in this case)
* Field 3: temperature unit (C|F)
* Field 4: label string / comment you define
