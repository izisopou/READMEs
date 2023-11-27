Information about using screen in lxplus: https://hsf-training.github.io/analysis-essentials/shell-extras/screen.html 

In order to open a screen do:

screen -S <screen_name>

Now that you are inside the screen you can do anything you would normally do in lxplus, e.g. run a code (the environment is the same). In order to exit the screen while the code is running and return back to the "normal" lxplus environment just press: Ctr+A+D at the same time. 

The screen is still functioning in the background and the code is still running (even if you log out of lxplus!) so in order to access it again, you have to go to the *same* lxplus you were when you created the screen and do:

screen -list

This will print something like:

There is a screen on:
	23806.<screen_name>	(Detached)
1 Socket in /var/run/screen/S-izisopou.

This is the screen you opened earlier. In order to open it again do:

screen -r <screen_name>

Then, when you are inside the screen and you want to kill it, just press: Ctr+D.
Otherwise, you can kill it without opening it by doing in the "normal" lxplus environment:

screen -XS <screen_name> quit

To kill all screen sessions in an lxplus:

screen -ls | grep Detached | cut -d. -f1 | awk '{print $1}' | xargs kill


