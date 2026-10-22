# 
# MTKClient on Termux - Ultimate Bug Fix Guide 🚀

MTKClient is an excellent tool to unlock or flash MediaTek (MTK) device bootloaders using Termux on Android without needing a PC. However, when installed directly on Termux, it throws critical errors related to `pyserial`, `libfuse`, and `UsbClass backend`, which prevent the tool from connecting to the phone.

This guide shows how to fix all these bugs so that MTKClient runs 100% successfully on Termux.

---

## 🔧 Step 1: Basic Installation

First, install the required packages and Python in Termux. Run the following commands one by one:

```bash
pkg update && pkg upgrade -y
pkg install git python libusb termux-api -y


Now clone the MTKClient source code and enter the folder:
git clone https://github.com/bkerler/mtkclient
cd mtkclient

Install the required Python libraries:
pip install pyusb libusb1 pyserial
pip install -r requirements.txt


🐛 Step 2: The Magic Bug Fixes (Errors Solved)
Normally the setup should be complete here, but on Termux you need to modify some specific files, otherwise the tool will not work. Use the commands below to apply all the fixes.
Fix 1: Pyserial Posix Error
Android cannot scan serial ports, which causes ImportError: no implementation for your platform ('posix'). Fix it with:

find /data/data/com.termux/files/usr/lib/python* -name "list_ports_posix.py" -exec sh -c 'echo "def comports(include_links=False): return []" > "$1"' _ {} +

Fix 2: Libfuse / mfusepy Error
Termux does not support libfuse, which causes unnecessary crashes. Remove it:
pip uninstall mfusepy -y

Fix 3: UsbClass Backend Error
The most annoying error AttributeError: 'UsbClass' object has no attribute 'backend' can be permanently fixed by editing the main code: 
find \~/mtkclient -name "usblib.py" -exec sed -i 's/if self.backend is not None:/if hasattr(self, "backend") and self.backend is not None:/g' {} +


Fix 4: USB Driver Path Fix
Set the path so that Termux can correctly find the USB driver:
export LD_LIBRARY_PATH=/data/data/com.termux/files/usr/lib:$LD_LIBRARY_PATH

⚡ Step 3: Connect & Unlock
All fixes are done! Now it's time to connect the target phone.
1. Check USB Port:
termux-usb -l
(You will get a port address here, for example: /dev/bus/usb/001/026)

2. Grant Permission:
termux-usb -r 

(Replace the address with the port number you got, and press OK when the popup appears on the main phone)
3. Run Unlock Command: 
python mtk.py da seccfg unlock

4. Connect the Phone:
Power off the target phone completely. Then hold both Volume Up (+) and Volume Down (-) buttons together and connect the OTG cable (do not press the power button).
Done! You will see "Success" in Termux and your bootloader will be unlocked! 🎉 