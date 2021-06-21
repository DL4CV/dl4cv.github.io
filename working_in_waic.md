# Working in WAIC
This document contains some explanations on how to work with the WEXAC platform and how to integrate it into your working environment. 
For a more thorough version see [the extended guide](https://paper.dropbox.com/published/Working-in-WAIC--BNEoueUkXGzVIrM8TC11ykILBg-QA8DVFXMFPOb5L4z7DvT2Et)

----------
# First-Time Configurations

## Prerequisites
- To connect to the WEXAC cluster, install MobaXterm from [this link](https://mobaxterm.mobatek.net/download.html).
- To edit and debug your code comfortably on the WEXAC cluster, install VS Code from [this link](https://code.visualstudio.com/).
- If you are not at Weizmann, make sure you are connected to the WIS network through VPN.

## MobaXterm Configurations

Apply some general configurations to MobaXterm:

- `Settings` `→` `keyboard shortcut`: change “Ctrl + M” to “None”.
- `Settings → X11 → OpenGL acceleration`: change “software” to “hardware”.
- `Settings → X11 → remote access`: change from “on-demand” to “full”.
- `Settings → SSH`: check “keep alive”.
- Restart MobaXterm for changes to take affect.

Configure an SSH session to connect to the WEXAC cluster:

- `Session → SSH`
- Remote Host: `access4.wexac.weizmann.ac.il`
- Check “Specify Username”.
- Username: you WIS username.
## Server Side Configurations

Once logged on to the WEXAC cluster, follow these steps:

    $ bash /home/labs/waic/shared/scripts/fix-bashrc/fix-bashrc
    $ exit  # or just Ctrl+D
    $       # reconnect

----------

For those who do not already have a user account on WAIC: we will allocate a temporary user for you, named “classXX”.
We will mail you a “secret key” file “classXX-key”.
To use it (with mobaXterm):
Save the locally on your windows machine in `C:\Users\<your account>\Documents\MobaXterm\home`
Open mobaXterm
`chmod 600 classXX-key`
Login to WAIC access server:
`ssh access3.wexac.weizmann.ac.il -l classXX -i classXX-key`
Replace “XX” with the actual number of the account you were given.   

----------
    
## Interactive Job
Interactive jobs enable us to do command line operations on a Resource Server.
You can submit an interactive job with 16GiB of RAM, 4 CPU threads, 1 GPU:

    $ bsub -q waic-long -gpu num=1:j_exclusive=yes -R rusage[mem=16384] -R affinity[thread*4] -Is /bin/bash
 
---------- 
 
## Jupyter Notebook

Jupyter is an interactive IDE for python development. It is very recommended for short scripts and visualizations.
In order to run Jupyter as a bsub command, add the following lines to your `.lsf` file:

    $ module load miniconda/4.7.12
    $ . activate
    $ conda activate [your-env]
    
    $ JUPYTER_RUNTIME_DIR="${HOME}/.cache/jupyter-runtime/$(shuf -i 100000-999999 -n 1)"
    $ export JUPYTER_RUNTIME_DIR
    $ jupyter notebook --no-browser --ip "0.0.0.0" --port "$(shuf -i 30000-59999 -n 1)"

The first section activates your anaconda environment and the others actually initialize the Jupyter notebook.
Now, start your job ( `bsub < your_job_file.lsf` ) and peek into it ( `bpeek your_job_id` ). 
You should see something similar to:

![output of jupyter notebook job](https://paper-attachments.dropbox.com/s_2BA2184B298312218670366F2059CCCF17C5EFC621E967D8D05F327C201618FD_1606668948097_image.png)


Finally, copy the url containing a remote server (e.g. ibdgx006) to your browser, and add the suffix `.wexac.weizmann.ac.il` after the remote server’s hostname (e.g. `ibdgx006.wexac.weizmann.ac.il:33807/?token...` ). Refresh and you should see your jupyter home tree:

![Jupyter home tree screen](https://paper-attachments.dropbox.com/s_2BA2184B298312218670366F2059CCCF17C5EFC621E967D8D05F327C201618FD_1606669078291_image.png)

## Tensorboard

Tensorboard is a useful utility when wanting to visualize the training process.

    $ tensorboard --logdir=. --port="$(shuf -i 20000-50000 -n 1)" --bind_all

Now access the tensorboard dashboard through the broswer by typing `HOSTNAME:RANDOM_PORT`, e.g. `dgxsw01.wexac.weizmann.ac.il`. If you do not know the hostname of your jobs, you may find it through the `bjobs` command.


