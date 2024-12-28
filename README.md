# X11 Forwarding Thonny from Headless (Lite) Raspberry PI

I like to use Raspberry PI ZERO computers because they are small and cheap. However, because they only have 512MB of memory, I tend to run then with a 32bit OS headless (lite).

I program in Python and, in the past, have used the Remote Interpreter feature of Thonny to access the headless ZERO from another computer for doing development. The downside of using the Remote Interpreter is that you do no have a Debugger. That meant I had to resort to print statements.

I happened to come across [X11 Forwarding]( https://en.wikipedia.org/wiki/X_Window_System#Remote_desktop) which provides Remote Desktop on X11 Systems. When reading about X11 you need to keep in mind the reverse definitions. The device you are using to view the remote content on is the 'server'. The remote device where the app is running is the 'client'. X11 Forwarding/Remote Desktop opened the possibily of running Thonny on the ZERO but have it's screen appear on my computer.

SSH can provide the forwarding of X11. I leave researching this to the reader. 

## Platforms

I am starting with the following computers.
* pitest.local - Raspberry PI ZERO W running 32bit Bullseye Lite
* ambrotype.local - Linux Mint MATE 22

The platforms and versions are significant since I discovered X servers were installed where needed and configuration was simple. 

While I was researching this, I found a number of examples where additional supporting is needed. Some of these are note they are needed but the posts did not have you check to see if they were already installed before installing the software. I lucked out so this is a very simple implementation. 

Many posts suggest using `putty` but `SSH` worked for me.

## Configuration and Installation
Here are the steps I followed

1. Update both systems; `sudo apt update`, `sudo apt full-update`
2. Connect (ssh) to the remote e.g. pitest.local
3. Edit the SSH configuration file to enable X11 forwarding
    1. Open the editor `sudo nano /etc/ssh/sshd_config`
    2. Find `# X11Forwarding no` and change it to `X11Forwarding yes`
    3. Save and exit
4. Restart the SSH Server `sudo systemctl restart sshd`
5. Install Thonny `sudo apt install thonny`
6. Exit connection to RPI ZERO

## Remote Desktop Thonny to Host Computer

This is simple. I did not have to make any changes to Linux Mint since it was already running an X Server.

1. Connect using the X11 forward options `$ ssh -X pitest.local`
2. Run `$ thonny`

You will see a number of messages but I ignored them because the Thonny IDE appeared on my host computer.

I've only done some simple testing to make sure the debugger and other panels will display properly.

## Conclusion

Now the bad news
* CPU goes to 100% on the single core CPU ZERO when Thonny is run. 
* Memory consumption when from around 35MB to 80MB still leaving plenty free. I also showed 14MB of swap but I'm not sure if that's related to running Thonny or from the installation of it.

Thonny performance was not as good as when using Remote Interpreter but this is likely do to the ZERO CPU running at 100%.

However, running Thonny on a Zero and having it's display appear on another computer, in order to get the use of the debugger, is an option.
