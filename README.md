# Fix connection issues - Linux

Some udev rules are required to allow device access. Refer to the [Chrome USB API documentation](https://developer.chrome.com/apps/app_usb#caveats) for details. Follow those steps.

### 1. setup

#### 1.1 Check if the `plugdev` group exists:

```bash
# check if the plugdev group exists
cat /etc/group | grep plugdev
```


<table>
  <tr>
    <td>
      
**Only** f the above command **does not returns** a result, you need to create the `plugdev` group.     

```bash
# create the plugdev group
groupadd plugdev
```

   </td>
  </tr>
</table>

#### 1.2 Check if you are in the group `plugdev`

```bash
# list user groups
groups
```

<table>
  <tr>
    <td>
      
If the output **does not contains** `plugdev`, it means you are not in the `plugdev` group.

```bash
# add user to the plugdev group
gpasswd -a <user> plugdev
```
*Note: replace `<user>` by your username, e.g for user "mike", it would be `gpasswd -a mike plugdev`.*

After that, you must **logout and in again** for the change to take effect. To verify you are correctly in the `plugdev` group, type:

```bash
# check again if you are on the plugdev group
groups
```

and search for a `plugdev` occurence. If no occurence, you probably missed a step, **go back to step 1.2**.  
  
   </td>
  </tr>
</table>



### 2. add the udev rules

#### install script

Use this command to automatically add the rules and reload udev.

```
wget -q -O - https://raw.githubusercontent.com/LedgerHQ/udev-rules/master/add_udev_rules.sh | sudo bash
```

### Troubleshooting

If still not working, you may try those additional steps:

#### Option 1: add OWNER to udev rules

Edit the `/etc/udev/rules.d/20-hw1.rules` file, and add the `OWNER="username"` parameter to each line, where `username` is your Linux username.

#### Option 2: additionnal rules

Edit the `/etc/udev/rules.d/20-hw1.rules` file and add those lines:

```bash
KERNEL=="hidraw*", SUBSYSTEM=="hidraw", MODE="0660", GROUP="plugdev", ATTRS{idVendor}=="2c97"
KERNEL=="hidraw*", SUBSYSTEM=="hidraw", MODE="0660", GROUP="plugdev", ATTRS{idVendor}=="2581"
```

#### Option 3: alternative rules

If you are on Arch Linux, you can also try these rules

```bash
SUBSYSTEMS=="usb", ATTRS{idVendor}=="2581", ATTRS{idProduct}=="1b7c", MODE="0660", TAG+="uaccess", TAG+="udev-acl"
SUBSYSTEMS=="usb", ATTRS{idVendor}=="2581", ATTRS{idProduct}=="2b7c", MODE="0660", TAG+="uaccess", TAG+="udev-acl"
SUBSYSTEMS=="usb", ATTRS{idVendor}=="2581", ATTRS{idProduct}=="3b7c", MODE="0660", TAG+="uaccess", TAG+="udev-acl"
SUBSYSTEMS=="usb", ATTRS{idVendor}=="2581", ATTRS{idProduct}=="4b7c", MODE="0660", TAG+="uaccess", TAG+="udev-acl"
SUBSYSTEMS=="usb", ATTRS{idVendor}=="2581", ATTRS{idProduct}=="1807", MODE="0660", TAG+="uaccess", TAG+="udev-acl"
SUBSYSTEMS=="usb", ATTRS{idVendor}=="2581", ATTRS{idProduct}=="1808", MODE="0660", TAG+="uaccess", TAG+="udev-acl"
SUBSYSTEMS=="usb", ATTRS{idVendor}=="2c97", ATTRS{idProduct}=="0000", MODE="0660", TAG+="uaccess", TAG+="udev-acl"
SUBSYSTEMS=="usb", ATTRS{idVendor}=="2c97", ATTRS{idProduct}=="0001", MODE="0660", TAG+="uaccess", TAG+="udev-acl”
```
