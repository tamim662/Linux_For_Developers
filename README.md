# Linux_For_Developers
Linux tools, useful commands, trifles. (based on Debian)

[echo](https://github.com/tamim662/Linux_For_Developers/blob/master/Documentation/echo_and_cat.md#the-first-one-is-echo "echo")


[cat](https://github.com/tamim662/Linux_For_Developers/blob/master/Documentation/echo_and_cat.md#and-this-second-way-is-use--cat-combined-with-redirection "cat")

[aliases](https://github.com/tamim662/Linux_For_Developers/blob/master/Documentation/aliases.md#aliases "aliases")

[Partitions](https://github.com/tamim662/Linux_For_Developers/blob/master/Documentation/filesystem.md#partitions "partitions")

[Path](https://github.com/tamim662/Linux_For_Developers/blob/master/Documentation/path.md#path "path")

[Process](https://github.com/tamim662/Linux_For_Developers/blob/master/Documentation/process.md#monitoring-and-performance-utilities "process")

[Kernel](https://github.com/tamim662/Linux_For_Developers/blob/master/Documentation/kernel.md#kernel "kernel")

[Memory](https://github.com/tamim662/Linux_For_Developers/blob/master/Documentation/memeory.md#memory "memeory")

[Swap](https://github.com/tamim662/Linux_For_Developers/blob/master/Documentation/memeory.md#swap "swap")

[System Boot](https://github.com/tamim662/Linux_For_Developers/blob/master/Documentation/system_boot.md#system-boot "system boot")

[System Runlevels](https://github.com/tamim662/Linux_For_Developers/blob/master/Documentation/system_boot.md#system-runlevels "system runlevels")

[Managing System Service](https://github.com/tamim662/Linux_For_Developers/blob/master/Documentation/System_services.md#managing-system-services 'system services')

[Add New User](https://github.com/tamim662/Linux_For_Developers/blob/master/Documentation/adding%20User.md#creating-an-account 'user add')

[Upgrading And Patching](https://github.com/tamim662/Linux_For_Developers/blob/master/Documentation/Upgrading%20and%20Patching.md#upgrading-and-patching)

[Using dpkg](https://github.com/tamim662/Linux_For_Developers/blob/master/Documentation/Using%20dpkg.md#using-dpkg)

[Network And Network Interface](https://github.com/tamim662/Linux_For_Developers/blob/master/Documentation/Networking.md#network-and-network-interfaces)

[Video Editing using command line tool](https://github.com/tamim662/Linux_For_Developers/blob/00f4075d5813c26690efd1929d420913e044b63b/Documentation/Video_editing.md)

### Linux power user commands
**To estimate the total video length of all MP4 files in the current directory and its subdirectories:**
```bash
find . -iname '*.mp4' -exec ffprobe -v quiet -of csv=p=0 -show_entries format=duration {} \; | paste -sd+ - | bc -l | awk '{printf "%.2f minutes\n", $1/60}'
```
