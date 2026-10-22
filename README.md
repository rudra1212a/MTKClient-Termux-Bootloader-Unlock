 # MTKClient on Termux - Ultimate Bug Fix Guide 🚀

MTKClient is an excellent tool to unlock or flash MediaTek (MTK) device bootloaders using Termux on Android without needing a PC. However, when installed directly on Termux, it throws critical errors related to `pyserial`, `libfuse`, and `UsbClass backend`, which prevent the tool from connecting to the phone.

This guide shows how to fix all these bugs so that MTKClient runs 100% successfully on Termux.

---

## 🛠️ Step 1: Basic Installation

First, install the required packages and Python in Termux. Run the following commands one by one:

```bash
pkg update && pkg upgrade -y


pkg install git python libusb termux-api -y

Now clone the MTKClient source code and enter the folder:

git clone [https://github.com/bkerler/mtkclient](https://github.com/bkerler/mtkclient)

cd mtkclient

Install the required Python libraries:

pip install pyusb libusb1 pyserial

pip install -r requirements.txt

🐛 Step 2: The Magic Bug Fixes (Errors Solved)
Normally the setup should be complete here, but Termux throws 4 major errors. Run these commands to fix them:

Fix 1: Pyserial Posix Error
Android cannot scan serial ports, which causes a crash. Run this:

find /data/data/com.termux/files/usr/lib/python* -name "posix.py" -exec sed -i 's/TIOCM_RTS/0x5401/g' {} +

Fix 2: Libfuse / mfusepy Error

Termux does not support libfuse, which causes python installation to fail. Uninstall it:

pip uninstall mfusepy -y

Fix 3: UsbClass Backend Error

The most annoying error AttributeError: 'UsbClass' object has no attribute 'find'. Fix it using:

find ~/mtkclient -name "usblib.py" -exec sed -i 's/backend=backend/\#backend=backend/g' {} +

Fix 4: USB Driver Path Fix

Set the path so that Termux can correctly find the libusb driver:

export LD_LIBRARY_PATH=/data/data/com.termux/files/usr/lib

⚡ Step 3: Connect & Unlock

All fixes are done! Now it's time to connect the device and unlock.

1. Check USB Port:

termux-usb -l

(You will get a port address here, for example /dev/bus/usb/001/002)

2. Grant Permission:

termux-usb -r /dev/bus/usb/001/002

(Replace the address with the exact port number you received)

python mtk.py da seccfg unlock

4. Connect the Phone:

Power off the target phone completely. Then hold both Volume Up + Volume Down buttons and plug in the USB cable from your other phone using an OTG adapter.

Done! You will see "Success" in Termux and your bootloader will be unlocked! 🎉


