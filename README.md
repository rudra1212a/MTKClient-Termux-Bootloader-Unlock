 # MTKClient on Termux - Ultimate Bug Fix Guide 🚀

MTKClient is an excellent tool to unlock or flash MediaTek (MTK) device bootloaders using Termux on Android without needing a PC. However, when installed directly on Termux, it throws critical errors related to `pyserial`, `libfuse`, and `UsbClass backend`, which prevent the tool from connecting to the phone.

This guide shows how to fix all these bugs so that MTKClient runs 100% successfully on Termux.

---

## 🛠️ Step 1: Basic Installation

First, install the required packages and Python in Termux. Run the following commands one by one:

```bash
pkg update && pkg upgrade -y
```

```bash
pkg install git python libusb termux-api -y
```


Now clone the MTKClient source code and enter the folder:

```bash
git clone https://github.com/bkerler/mtkclient
```

```bash
cd mtkclient
```

Install the required Python libraries:

```bash
pip install pyusb libusb1 pyserial
```

```bash
pip install -r requirements.txt
```


🐛 Step 2: The Magic Bug Fixes (Errors Solved)

Normally the setup should be complete here, but Termux throws 4 major errors. Run these commands to fix them:

Fix 1: Pyserial Posix Error

Android cannot scan serial ports, which causes a crash. Run this:

```bash
find /data/data/com.termux/files/usr/lib/python* -name "posix.py" -exec sed -i 's/TIOCM_RTS/0x5401/g' {} +
```

Fix 2: Libfuse / mfusepy Error

Termux does not support libfuse, which causes python installation to fail. Uninstall it:


```bash
pip uninstall mfusepy -y
```


Fix 3: UsbClass Backend Error

The most annoying error AttributeError: 'UsbClass' object has no attribute 'find'. Fix it using:


```bash
find ~/mtkclient -name "usblib.py" -exec sed -i 's/backend=backend/\#backend=backend/g' {} +
```


Fix 4: USB Driver Path Fix

Set the path so that Termux can correctly find the libusb driver:


```bash
export LD_LIBRARY_PATH=/data/data/com.termux/files/usr/lib
```

Fix 5: Check Tool Help & Options (Optional)
If you want to view the mtkclient help menu and check all available commands, run this:

```bash
python mtk.py -h
```

⚡ Step 3: Connect & Unlock

All fixes are done! Now it's time to connect the device and unlock.

1. Check USB Port:


```bash
termux-usb -l
```

(You will get a port address here, for example /dev/bus/usb/001/002)

2. Grant Permission:


```bash
termux-usb -r /dev/bus/usb/001/002
```


(Replace the address with the exact port number you received)

3. Run Unlock Command:


```bash
python mtk.py da seccfg unlock
```


4. Connect the Phone:

Power off the target phone completely. Then hold both Volume Up + Volume Down buttons and plug in the USB cable from your other phone using an OTG adapter.

Done! You will see "Success" in Termux and your bootloader will be unlocked! 🎉


> **Note:** Whenever you close Termux and want to use MTKClient again later, just use these commands to reopen and run the tool without reinstalling everything.


🚀 How to Run MTKClient Again After Exiting Termux

If you have already completed the installation and closed Termux, you do not need to reinstall everything. Follow these simple steps to run it again:

1.Open Termux on your device.

2.Navigate to the mtkclient directory by typing:

```bash
cd mtkclient
```

3.Connect your target device via OTG cable in BROM mode (Power off, hold Vol+ & Vol-).

4.Run your desired command. For example, to check the help menu or perform a bootloader unlock:

• To check help:

```bash
python mtk.py -h
```

• To unlock the bootloader:

```bash
python mtk.py da seccfg unlock
```


> **Tip:** You do not need to install anything again. Run these quick commands anytime later to re-access and use MTKClient.

