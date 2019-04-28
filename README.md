# Burning_Man_2018 - Do not deviate from this: https://aiyprojects.withgoogle.com/vision#makers-guide--run-your-app-at-bootup
# Note to self: The error messages are super-obtuse. Debugging is hell. Strict to the script.

RUN YOUR APP AT BOOTUP
By default, your Vision Kit runs the Joy Detector demo when it boots up. This is enabled using a systemd service, which is defined with a .service configuration file at ~/AIY-projects-python/src/examples/vision/joy/joy_detection_demo.service, and it looks like this:
```
[Unit]
Description=AIY Joy Detection Demo
Requires=dev-vision_spicomm.device
After=dev-vision_spicomm.device
Wants=aiy-board-info.service
After=aiy-board-info.service

[Service]
Type=simple
Restart=no
User=pi
Environment=AIY_BOARD_NAME=AIY-Board
EnvironmentFile=-/run/aiy-board-info
ExecStart=/usr/bin/python3 /home/pi/AIY-projects-python/src/examples/vision/joy/joy_detection_demo.py --enable_streaming --mdns_name "${AIY_BOARD_NAME}" --blink_on_error

[Install]
WantedBy=multi-user.target
```
The .service file accepts a long list of configuration options, but this example provides everything you need for most programs you want to run at bootup.

To create a service like this to start your own app at bootup, just copy this configuration to a new file such as my_program.service (the name must end with .service). Then change ExecStart so it points to your program's Python file (and passes it any necessary parameters), and change Description to describe your program.

Then you need to put this file into the /lib/systemd/system/ directory. But instead of moving this file there, you can keep it with your program files and create a symbolic link (a "symlink") in /lib/systemd/system/ that points to the file. For example, let's say your config file is at ~/Programs/my_program.service. Then you can create your symlink as follows:

# Create the symlink
```
sudo ln -s ~/Programs/my_program.service /lib/systemd/system
# Reload the service files so the system knows about this new one
sudo systemctl daemon-reload
```
Now tell the system to run this service on bootup:
```
sudo systemctl enable my_program.service
```
All set! You can try rebooting now to see it work.

Or manually run it with this command:
```
sudo service my_program start
```
If you want to stop the service from running on bootup, disable it with this command:
```
sudo systemctl disable my_program.service
```
And to manually stop it once it's running, use this command:
```
sudo service my_program stop
```
You can check the status of your service with this command:
```
sudo service my_program status
```
If you'd like to better understand the service configuration file, see the .service config manual.
