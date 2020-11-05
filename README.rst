PsychoPy on a stick
==============

This project aims to describe how to set up a USB stick so that PsychoPy experiments can be run off of it. `PsychoPy`_ is a Python package for designing psychological experiment tasks. I am not part of its development team and offer no guarantees that the approach I describe in this repo will work - use your own judgment.

It's been asked a couple of times on the `PsychoPy forums`_ whether it's possible to run PsychoPy experiments directly from a USB stick using Windows. Below are are steps that should work in order to achieve this.

Installation/Setup
-----------------
#. Use a computer that doesn't already have PsychoPy Standalone (see their homepage) installed, or uninstall it before proceeding (I haven't found a way around this).
#. Download PsychoPy Standalone.
#. During the installation process, change the installation directory to a folder on your USB stick, e. g. 'F:\\psychopy_root\psychopy_installation\'.
#. Run the installation.
#. Copy the 'run_experiments.bat' file and 'psychopy_experiments' from this repo. Put them in the **parent** directory of the directory you used for the PsychoPy installation ('psychopy_root' in the example path I described above).
#. If you used another name than 'psychopy_installation' for the installation directory, you need to open up the 'run_experiments.bat' file with a text editor (notepad) and replace the part where it says 'psychopy_installation' with the name of your installation directory.
#. Try running the 'run_experiments.bat' file. You should see one experiment being run first, showing a text for a few seconds, followed by another similar one shortly thereafter.

If the above steps worked as expected, you just ran a PsychoPy experiment using only files stored on a USB stick. You should be able to run the .bat file in a similar fashion on any updated Windows computer, regardless of whether it has Python etc installed or not.

How it works
-----------------
Take a look in 'run_experiments.bat'. '%~dp0' is kind of like an environment variable, and is converted to the directory of where the .bat file is located once you run the file. With this hack, we're able to create a relative path. This means you don't have to worry about the USB drive being named "F:", "G:" or whatever else when you plug it into a computer. All the .bat file says is that the PsychoPy installation's python executable should launch Python and make it run the script named 'main_script.py', inside of the 'psychopy_experiments' subdirectory.

Now go to the 'psychopy_experiments' subdirectory. There's a `__init__.py` file, an empty file that informs Python that it should treat the folder as a Python package (the details of this aren't very important here). More interesting is the 'main_script.py' file. It has two import statements, which effectively tell Python to run the 'experiment1/exp1.py' and 'experiment2/exp2.py' scripts.

In order to run your own experiments, you need to copy their directories over to the 'psychopy_experiments' folder. Say you have a Stroop task experiment, with stroop.psyexp and stroop.py files saved in a directory 'stroop_exp'. Copy the whole **directory** over to 'psychopy_experiments'. You could then remove the import statements inside of 'main_script.py' and replace them with just 'from stroop_exp import stroop' (*don't add .py in import statements*). The experiment should then be run. Remember to generate the .py file if you're using PsychoPy builder, since it's the .py file (not .psyexp) that's imported/run by 'main_script.py'.

Note that if you want to run multiple experiments back-to-back, you can simply add more experiment directories and corresponding import statement lines. If you want more control you can create one copy of the .bat file for each experiment, each with a single import statement.

(the repo's 'psychopy_installation' folder and its contained 'python.exe' file aren't actually meant to be copied/used - their sole purpose is to show what the necessary directory structure looks like)

.. _PsychoPy: https://psychopy.org/ 
.. _PsychoPy forums: https://discourse.psychopy.org/t/is-it-possible-to-run-psychopy-from-a-usb-stick-on-a-windows-machine/5428
