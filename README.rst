PsychoPy on a stick
========================================

This project describes how to set up a USB stick so that PsychoPy experiments can be run off of it. `PsychoPy`_ is a Python package for designing psychological experiment tasks. I am not part of its development team and offer no guarantees that the approach I describe in this repo will work - use your own judgment.

It's been asked a couple of times on the `PsychoPy forums`_ whether it's possible to run PsychoPy experiments directly from a USB stick using Windows. Below are are steps that should work in order to achieve this.

Installation/Setup
----------------------------------------
#. Use a computer that doesn't already have PsychoPy Standalone (see their homepage) installed, or uninstall it before proceeding (I haven't found a way around this).
#. Download PsychoPy Standalone.
#. During the installation process, change the installation directory to a folder on your USB stick, e. g. 'F:\\psychopy_root\psychopy_installation\'.
#. Run the installation.
#. Copy the 'run_experiments.bat' file and 'psychopy_experiments' from this repo. Put them in the **parent** directory of the directory you used for the PsychoPy installation ('psychopy_root' in the example path I described above).
#. If you used another name than 'psychopy_installation' for the installation directory, you need to open up the 'run_experiments.bat' file with a text editor (notepad) and replace the part where it says 'psychopy_installation' with the name of your installation directory.
#. Try running the 'run_experiments.bat' file. You should see one experiment being run first, showing a text for a few seconds, followed by another similar one shortly thereafter.

If the above steps worked as expected, you just ran a PsychoPy experiment using only files stored on a USB stick. You should be able to run the .bat file in a similar fashion on any updated Windows computer, regardless of whether it has Python etc installed or not.

How it works
----------------------------------------
Take a look in 'run_experiments.bat'. '%~dp0' is kind of like an environment variable, and is converted to the directory of where the .bat file is located once you run the file. With this hack, we're able to create a relative path. This means you don't have to worry about the USB drive being named "F:", "G:" or whatever else when you plug it into a computer. All the .bat file says is that the PsychoPy installation's python executable should launch Python and make it run the scripts 'psychopy_experiments\experiment1\exp1.py' and 'psychopy_experiments\experiment2\exp2.py', one after the other.


Running your own experiments
----------------------------------------
In order to run your own experiments, you need to copy their directories over to the 'psychopy_experiments' folder. Say you have a Stroop task experiment, with stroop.psyexp and stroop.py files saved in a directory 'stroop_exp'. Copy the whole **directory** over to 'psychopy_experiments'. Then, open up the 'run_experiments.bat' file and add the following.

.. code-block:: rst

    %~dp0\psychopy_installation\python.exe %~dp0\psychopy_experiments\stroop\stroop.py

It's simplest to copy-paste one of the lines already in the .bat file, and change the parts at the end to the directory/file name for your experiment. Once you understand how this works, it's best to remove the example lines from the .bat file and delete the example experiments in 'psychopy_experiments'.

Remember that you need to generate a new .py file if you're using PsychoPy builder whenever you make changes, since it's the .py files that 'run_experiments.bat' use.

Note that if you want to run multiple experiments back-to-back, you use the method above to add more lines to the 'run_experiments.bat' file. If you want more control you can create one copy of the .bat file for each experiment, each with a single line for making Python run an experiment.

Including monitor specifications
--------------------------------------------
In PsychoPy Builder, the default is to rely on the 'Monitor Center' for storing and loading monitor settings. These settings are saved in a hidden folder on your computer (usually inside of your user's home folder), which is outside of PsychoPy's own directory. This isn't a reliable solution when running experiments on a USB stick. To circumvent this issue, do the following:

1. In your experiment's first routine, add a code component
2. Open up the code component's 'Before Experiment' tab
3. Add the following code snippet

.. code-block:: python

    # import psychopy module for generating
    # monitor instances.
    from psychopy import monitors

    # create monitor instance for use with
    # USB stick experiment
    example_mon = monitors.Monitor(
        name='example_monitor',
        width=49,
        distance=60,
        notes='Example monitor, replace specifications here.')

    # set pixel size (width x height) of monitor
    example_mon.setSizePix([1280, 1024])

4. Edit the configurations in the snippet in accordance with the monitor of the computer that you will run the experiment on
5. Save the code component's contents
6. Open up your experiment's settings and go to the 'Screen' tab
7. In the 'monitor' field, insert '$example_mon', i. e. a '$' and then whatever you named your monitor instance in the code snippet above
8. Use the Builder's 'compile to script' command.

Now the resulting .py experiment script should include your monitor specifications, meaning that the experiment doesn't rely on the Monitor Center. When running the experiment you might see a message about a missing monitor specification, but this simply means that PsychoPy didn't find a 'cached' monitor configuration and is correctly using the monitor instance your script creates. In other words, don't worry about it.

You can find an example .psyexp file with the code component and monitor settings described above inside the directory 'removeme_example_psyexp'.

(the repo's 'psychopy_installation' folder and its contained 'python.exe' file aren't actually meant to be copied/used - their sole purpose is to show what the necessary directory structure looks like)


.. _PsychoPy: https://psychopy.org/
.. _PsychoPy forums: https://discourse.psychopy.org/t/is-it-possible-to-run-psychopy-from-a-usb-stick-on-a-windows-machine/5428