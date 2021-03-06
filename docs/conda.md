# Installing Custom Software

## Conda environments

The RCE is a shared resource that is actively administered to keep the cluster stable for all users. System administrators install and update software system-wide, while avoiding conflicts between software dependencies. It is not possible for RCE users to install software system-wide. However, users can install custom software into their own *project shared space* if the required dependencies are already installed system-wide, or if the dependencies can also be installed into the same project space. Frequently, however, it is not possible to manually install the full suite of required software dependencies (the 'dependency tree') for a given program.

A solution to this problem is to use [Conda environments](https://docs.conda.io/projects/conda/en/latest/user-guide/concepts/environments.html), which are siloed containers that can be used to install software - and their dependencies - without affecting other users on the cluster. Since Conda is a [package management system](https://en.wikipedia.org/wiki/Package_manager), it automatically handles the issue of installing appropriately versioned dependencies for any software you install.

A useful resource for interacting with Conda environments will be this [cheatsheet](https://docs.conda.io/projects/conda/en/4.6.0/_downloads/52a95608c49671267e40c689e0bc00ca/conda-cheatsheet.pdf).


## Conda setup

Here, we walk through the steps needed to create a Conda enviroment in your *project space* on the RCE.

1. Start an RCE powered Anaconda shell.
2. Check which shell is being used and change shell to `bash` if necessary.


```r
$ echo $0
$ /bin/bash # change to bash
```
3. Create a new empty Conda environment in the home directory. The `-n` option indicates the environment name comes next. After the environment name, package names (possibly multiple, separated by a space) to be installed can be listed.


```r
$ conda create -n <name-of-environment>
```

4. Make sure there is enough room in the project space to store the `.conda` folder.


```r
$ quotareport ~/shared_space/<user-name>
```

5. Once the Conda environment is configured, use `rsync` to move (NOTE: do not use `mv` or `cp`) the `.conda` hidden directory from the home directory to a new directory (e.g., called `conda`) in the project space. Then delete the old `.conda` hidden directory within the home directory.


```r
$ rsync -rav ~/.conda ~/shared_space/<user-name>/conda
$ rm -r ~/.conda
```

6. Create a symbolic link (symlink) to the new directory from the original location (in the home directory). The `-s` option specifies a symlink, then the new directory is specified, then the previous location of the `.conda` folder in the home directory.


```r
$ ln -s ~/shared_space/<user-name>/conda/.conda ~/.conda
```

7. View list of available Conda environments to check that the symlink worked.


```r
$ conda env list
```

8. Search for program(s) to install (this is optional). (NOTE: surround the search string with asterisks).


```r
$ conda search “*rstan*”
$ conda search “*rstudio*”
```

9. Initiate the environment.


```r
$ conda activate <name-of-environment>
```

10. Install new programs into this active environment.


```r
$ conda install r-rstan rstudio
```

For some programs and packages, you may need to specify a non-default Conda channel using the flag: `-c <channel-name>`. See <https://docs.conda.io/projects/conda/en/latest/user-guide/concepts/channels.html> for more information. For example:


```r
$ conda install -c conda-forge r-nbclust
```

11. Check where program(s) are installed and which version (this is optional).


```r
$ which R
$ which python
$ R --version
$ echo $PATH
```

12. Run a program.


```r
$ R
$ rstudio
```

13. Deactivate environment after use.


```r
$ conda deactivate
```


**NOTE:** if you need to move your existing Conda environment directory to a different location, it's best to remove the old `.conda` directory and start with a fresh one in the new location.
