Information about using screen in lxplus: https://hsf-training.github.io/analysis-essentials/shell-extras/screen.html 

In order to open a screen do:

screen

Now that you are inside the screen you can do anything you would normally do in lxplus, e.g. run a code (the environment is the same). In order to exit the screen while the code is running and return back to the "normal" lxplus environment just press: Ctr+A+D at the same time. 

The screen is still functioning in the background and the code is running (even if you log out of lxplus!) so in order to see the screens you have opened connect again to the *same* lxplus and do:

screen -list

This will print something like:

There is a screen on:
	23806.pts-103.lxplus733	(Detached)
1 Socket in /var/run/screen/S-izisopou.

This is the screen you opened earlier. In order to open it again do:

screen -rD 23806.pts-103.lxplus733

Then, when you are inside the screen and the code is finished, if you want to kill the screen just press: Ctr+D.

