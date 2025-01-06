# X11 Forwarding Thonny from Headless (Lite) Raspberry PI

> Since I initially posted this, I discovered I lucked out on my first configuration between Linux MINT and Raspberry PI (RPI) ZERO (1) W. An attempt at replicating this success with a RPI PI 400 and RPI ZERO 2 W failed.
>
> Subsequent research showed that, over the years, problems continue to present themselves with no single solution being easily found. You can see my solution to the RPI 400 and RPI ZERO W in the Troubleshooting section.
>
> So just be warned, successful X11 Forwarding may be difficult to achieve at times.

## Introduction

I like to use Raspberry PI ZERO computers because they are small and cheap. However, because they only have 512MB of memory, I tend to run then with a 32bit OS headless (lite).

I program in Python and, in the past, have used the Remote Interpreter feature of Thonny to access the headless ZERO from another computer for doing development. The downside of using the Remote Interpreter is that you do no have a Debugger. That meant I had to resort to print statements.

I happened to come across [X11 Forwarding]( https://en.wikipedia.org/wiki/X_Window_System#Remote_desktop) which provides Remote Desktop on X11 Systems. When reading about X11 you need to keep in mind the reverse definitions. The device you are using to view the remote content on is the 'server'. The remote device where the app is running is the 'client'. X11 Forwarding/Remote Desktop opened the possibily of running Thonny on the ZERO but have it's screen appear on my computer.

SSH can provide the forwarding of X11. I leave researching this to the reader. 

## Platforms

I ran two different configruations in the test.
1. PI ZERO + LINUX
* pitest.local - Raspberry PI ZERO W running 32bit Bullseye Lite. The Kernet for this is 6.1.21+ with an OS Release Date of 2024-10-22.
* ambrotype.local - Linux Mint MATE 22
2. PI ZERO 2 + RPI P400
* pitimer.local - Raspberry Pi Zero 2 W running 32bit Bulleye Lite. The Kernel for this is 6.1.21-v7+ with an OS release date of 2024-03-12.
* p400pi.local - Raspberry PI 400 running 64bit Bullseye Desktop

The platforms and versions can be significant since I discovered X servers were installed where needed and configuration was simple.

I also state details about the RPI ZERO's since that turned out to be significant in issues that arose. I also discovered the default settings in `sshd_config` were different between the two RPI ZERO's even though they were running the same OS.

While I was researching this, I found a number of examples where additional supporting is needed. Some of these are note they are needed but the posts did not have you check to see if they were already installed before installing the software. I lucked out so this is a very simple implementation. 

Many posts suggest using `putty` but `SSH` worked for me.

## Configuration and Installation
Here are the steps I followed for both configurations

1. Update both systems; `sudo apt update`, `sudo apt full-update`
2. Connect (ssh) to the remote e.g. pitest.local
3. Edit the SSH configuration file to enable X11 forwarding
    1. Open the editor `sudo nano /etc/ssh/sshd_config`
    2. Find `# X11Forwarding no` and change it to `X11Forwarding yes`
    3. Find `# X11DisplayOffset 10` and change it to `X11DisplayOffset 10`
    4. ** IF YOU HAVE PROBLEMS ** Find `#AddressFamily any` and change it to `AddressFamily inet` . It did not need to be set on the RPI ZERO (1) but was needed for RPI ZERO 2
    5. Save and exit
4. Restart the SSH Server `sudo systemctl restart sshd`
5. If needed, install Thonny `sudo apt install thonny`
6. Exit connection to RPI ZERO

## Remote Desktop Thonny to Host Computer
Here are the steps I followed for both configurations

This is simple. I did not have to make any changes to Linux Mint since it was already running an X Server.

1. Connect using the X11 forward options `$ ssh -X pitest.local`
2. Run `$ thonny`

You will see a number of messages but I ignored them because the Thonny IDE appeared on my host computer.

I've only done some simple testing to make sure the debugger and other panels will display properly.

## Conclusion

### Configuration 1 - RPI ZERO (1) + LINUX
Now the bad news
* CPU goes to 100% on the single core CPU ZERO when Thonny is run. 
* Memory consumption when from around 35MB to 80MB still leaving plenty free. I also showed 14MB of swap but I'm not sure if that's related to running Thonny or from the installation of it.

Thonny performance was not as good as when using Remote Interpreter but this is likely do to the ZERO CPU running at 100%.

### Configuration 2 - RPI ZERO 2 + P400

Performance on the RPI ZERO 2 was better because it has 4 cores. There was no peacking of CPU time. 

## Summary

Running Thonny on a Zero, and having it's display appear on another computer, in order to get the use of the debugger, is a viable option.

# Trouble Shooting

Here are some learnings I found from research

## Slow Response Using Thonny

I think this can be because by network performance. The RPI Zeros have small WIFI attenanes and for pitimer.local I've had to add a 2nd USB WIFI adapter with an antenanee to provide access. I also had improved performance by moving to a better location with the Linux MINT.

## Thonny Reports No DISPLAY Set

When you run Thonny, it the messages you will see something like `_tkinter: TcLError: no display name and no $DISPLAY environment variable`. If you run `$ echo $DISPLAY` it comes back empty.

After trying many solutions, the one which worked was changing the `AddressFamily` in `sshd_config`. Note I only had to do that on RPI ZERO 2 and not RPI ZERO (1) where it was still commented out.

You can see some discussions around this issue on [this post]( https://forums.raspberrypi.com/viewtopic.php?p=2283320#p2283320)
