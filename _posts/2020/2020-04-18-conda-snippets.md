---
title: Conda Utils & Snippets
date: 2020-04-18
description: Some snippets helping w/ conda installation and configuration.
categories:
- Python
tags:
- Python
photos:
---

# Outline<a name="outline"></a>

- [Virtual Environment](#venv)
- [Jupyter Notebook](#ipynb)

# Virtual Environment<a name="venv"></a>

Everything starts w/ an independent virtual environment. Install [conda](https://www.anaconda.com/distribution/) will make it easier.

After installation, it might be necessary to add this line to `.bashrc` or `.zshrc`:

```
export PATH="${PATH_TO_ANACONDA}/bin:$PATH"
```

To create a virtual environment:

```bash
conda create -n lb python=3 numpy pandas
```

To activate the virtual environment:

```bash
conda activate lb
```

To install more packages to virtual environment:

```bash
conda install -n lb matplotlib
```

To export an environment configuration:

```bash
conda activate myenv
conda env export > environment.yml
```

To recreate a venv w/ given `environment.yml`:

```bash
conda env create -f environment.yml
```

To delete a virtual env:

```bash
conda remove -n ${myenv} --all
```

or:

```bash
conda env remove --name ${myenv}
```

> Reference: [conda wiki page](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html)

# Jupyter Notebook<a name="ipynb"></a>

To install jupyter notebook:

```bash
conda install jupyter notebook
```

To easily manage conda virtual environment in jupyter notebook, install `nb_conda`:

```bash
conda install nb_conda
```

To install virtual environment into ipykernel jupyter notebook, run:
    
```bash
python -m ipykernel install --user --name lb --display-name "Python3 (lb)"
```

To remove virtual environment from ipykernel jupyter notebook:

```bash
# notice ${unwanted-kernel} should be name instead of display name
jupyter kernelspec uninstall ${unwanted-kernel}
```

To open jupyter notebook w/o browser and do port forwarding stuffs:

```bash
jupyter notebook --no-browser --port=9000
```

To get access to this jupyter notebook from laptop:

```bash
ssh -N -L localhost:9000:localhost:9000 yzy@10.60.16.15 & sleep 0.5; open -a Safari http://localhost:9000
```

Then the ssh job will be running in the background. To list all jobs, just run `jobs`. In case you want to kill the job, get its id and run `kill %{ssh_job_id}`. For more info on CLI job, go [here](https://gist.github.com/CMCDragonkai/6084a504b6a7fee270670fc8f5887eb4).

To convert a notebook to an HTML file:

```bash
jupyter nbconvert --to html notebook.ipynb
```

Jupyter nbextensions:

```bash
conda install -c conda-forge jupyter_contrib_nbextensions
```