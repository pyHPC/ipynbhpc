ipynbhpc: IPython notebook launcher for HPC systems
===================================================

Launch an interactive job via PBS (or equivalent) from a login node,
start IPython notebook on a computing node, and create an SSH tunnel
from the computing node to the login node.
Then that port can be SSH tunneled again to a Linux/MAC/Windows machine
and the IPython notebook opened locally in the browser.

Therefore the IPython notebook frontend runs in the browser on your local machine,
while the IPython kernel runs on the computing node of the cluster,
results, as well as plots are tunneled via SSH from the computing node 
to the local machine, where they are displayed and can be saved as `.png` just
right-clicking on them.

## Local machine setup

* Choose a port number [>1024]
* Create a bash alias (or a Putty profile) to connect to the HPC cluster tunneling that port

        alias sshcluster="ssh yourclusterhostname -L PORT:localhost:PORT"
        
## Cluster setup

You need to be able to SSH passwordless locally:

    ssh localhost

should work without the need to type the password.

If you already have a private key in `.ssh/id_rsa` you can use it, otherwise create a new
key with:

    ssh-keygen

then you can just add the related
public key `.ssh/id_rsa.pub` to `.ssh/authorized_keys` (the same works with a DSA key).

## Script setup (on the cluster)

Clone the repository and symlink the script to a folder in your `PATH`, i.e. `~/bin/`.

Customize the script options with environmental variables, add the `export` commands to
your `.bashrc` or `.profile` so they are set automatically at each login.

Set the port defined in Local machine setup

    export IPYNB_PORT=XXXX
    
Set the PBS job duration in minutes:

    export IPYNB_MINUTES=30
    
Set the template for requesting an interactive job, it should accept the number of minutes as `%d`,
this example is the default and works on SDSC Gordon:

    export IPYNB_QSUB_TEMPLATE="qsub -I -V -l nodes=1:ppn=16:native:flash,walltime=%d:00 -q normal" 

For example on NERSC Carver it would be:

    export IPYNB_QSUB_TEMPLATE="qsub -I -V -l nodes=1:ppn=1,pvmem=20gb -l walltime=%d:00"

Set the template for launching the IPython notebook, generally there should be no need to set this
variable, the default should be enough:

    export IPYNB_NOTEBOOK_TEMPLATE="ipython notebook --pylab=inline --port=%s --no-browser"
    
## Use the script

* SSH in the cluster login node using the bash alias
* Open `screen` to prevent a disconnection to kill your job (if available, on Gordon it is not)
* Launch the script with:

        ipynbhpc
        
* Wait for the terminal to return:

        Succesfully opened notebook!

* Connect with the browser on your local machine to `localhost:PORT`, where `PORT` is the port you chose.
* To kill the process and release the node before completion of the job, just use `CTRL+C` on the terminal

## Feedback

This has been tested only on SDSC Gordon and NERSC Carver, if you find any problem or have a feature request, please open an issue on Github:

https://github.com/pyHPC/ipynbhpc/issues

## Authors

* [Andrea Zonca](http://github.com/zonca), San Diego Supercomputer Center
* [Marius Millea](http://github.com/marius311), UC Davis
