# Getting Started with On-Prem HPC

This tutorial covers how to set up and use the Pangeo environment/stack
on USGS High Performance Computing (HPC) systems:

1) Creating a custom `hytest` conda environment
2) Configuring [Jupyter](https://jupyter.org/) server
3) Launching a Jupyter server
4) Connecting to your Jupyter server and the [Dask](https://dask.pydata.org/)
   dashboard from your personal computer

This document assumes that you already have an access to
either the `Denali` or `Tallgrass` [supercomputers at USGS](https://hpcportal.cr.usgs.gov/hpc-user-docs/index.html) and are comfortable using the command line.

## 1) Set up a `hytest` Conda Environment

This is the same manual process as describe in the
[Quick-Start for HPC](./QuickStart-HPC.md#1b-set-up-a-hytest-conda-environment)
to set up a `hytest` conda environment.

## 2) Configure Jupyter

Jupyter notebook servers include a password for security. This will control who
can use your personal jupyter server on the HPC. First we
generate the Jupyter config file then set the password:

```text
jupyter server --generate-config
jupyter server password
```

This creates the file `~/.jupyter/jupyter_server_config.py`.

Finally, we configure dask's dashboard to forward through
Jupyter.  Add the following line to your `~/.bashrc` file:

```sh
export DASK_DISTRIBUTED__DASHBOARD__LINK="/proxy/8787/status"
```

## 3) Start a Jupyter Notebook Server

Now that we have Jupyter configured, we can start a notebook server on our interactive compute node. Use the [script provided](start_jupyter.sh) to do this.

```text
> salloc -A account_name -t 02:00:00 -N 1 srun --pty bash
> cd $HOME/hytest/
> ./environment_set_up/start_jupyter.sh
  * Wait until this script says the Jupyter server has started.

  * Copy this ssh command into a terminal on your local computer:

        ssh -N -L 8889:denali:8471  username@clustername.cr.usgs.gov

  * Browse to http://localhost:8889 using your web browser on your local computer
```

`account_name` is one of your accounts codes on the supercomputer. You will see a list of your account codes when you first log in to the supercomputer. They are listed in a table after some text saying `Your account codes are:`.

Follow the steps printed out by the script to get connected.
Note that after you execute the `ssh` command, the cursor will hang.  This is expected and you can minimize the window.

## Running Notebooks

You are now ready to connect a web browser to your jupyter server.  Launch
a web browser on your dekstop and connect to the URL provided in the
startup messaging when you launched `jupyter lab`.

> NOTE:  Don't use the `localhost` option.  Use the URL which includes
your HPC host's full network name. That should look something like
<kbd>https://denali:8402/</kbd>.

The Jupyter Server will ask for a password.  This is the one supplied
in [Step 2](#2-start-a-jupyter-notebook-server) above.

You can now run existing notebooks found in `hytest`, or create
your own.

### Special Notes for dask jobqueue

The `tallgrass` node uses a job-scheduling system to manage job submissions
and executions among many users. The [dask-jobqueue](http://dask-jobqueue.readthedocs.io)
package is designed to help dask interface with these job queuing systems. Our notebooks are set up to handle this task for you, but if you would like to learn more, see [here](tallgrass_dask-jobqueue.md) for dask-specific details and how
to interact with `dask-jobqueue` from within your notebook if it will be running on `tallgrass`.