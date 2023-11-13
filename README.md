# Poetry Example
Example project with pytorch and GPUs using poetry.

## Steps:
1. (optional) Install Pyenv: https://github.com/pyenv/pyenv
    * If you decide to use Pyenv, do this first
    * Follow OS-specific installation guide & add startup commands to shell configuration file (.bashrc/.zshrc).  On Linux this may require installing some linux packages.
        ```sh
        # Load pyenv automatically by appending
        # the following to 
        # ~/.bash_profile if it exists, otherwise ~/.profile (for login shells)
        # and ~/.bashrc (for interactive shells) :
        export PYENV_ROOT="$HOME/.pyenv"
        [[ -d $PYENV_ROOT/bin ]] && export PATH="$PYENV_ROOT/bin:$PATH"
        eval "$(pyenv init -)"

        # Load pyenv-virtualenv automatically by adding
        # the following to ~/.bashrc:
        eval "$(pyenv virtualenv-init -)"
        ```
    * Restart shell: `exec $SHELL`
    * How to Use:
        * Download specific python versions: `pyenv install 3.11`, `pyenv install 3.8.10`
        * List all the python version you can install: `pyenv install -l`
        * Set global python version: `pyenv local 3.8.10`
        * List python versions installed with pyenv: `pyenv versions`
        * Within a specific directory, set local python version `pyenv local 3.11`

2. Install Poetry: https://python-poetry.org/docs/#installing-with-the-official-installer
    * `curl -sSL https://install.python-poetry.org | python3 -`
    * Add `export PATH="$HOME/.local/bin:$PATH"` to your shell configuration file (.bashrc/.zshrc).
    * Restart shell: `exec $SHELL`

3. Create Poetry project: https://python-poetry.org/docs/basic-usage/
    * `poetry new poetry-example`
    * this creates a package (subdirectory) in repo, `pyproject.toml` config file
    * you can then add dependencies `poetry add pandas` which are added to the `pyproject.toml` file or you can manually edit the `pyproject.toml` file to specify your configs.
        * Note that you can split up your dependencies by groups for your own logical organization.  You can also install the dependencies leaving out specific groups.
    * I have included an example config that shows how to add pytorch CPU/GPU in the `pyproject.toml` file.
        * Specifically, it shows you how to specify a custom URL source to install pytorch so that you can force poetry to install the specific pytorch GPU package that is compatible with your CUDA version.
    * Note that poetry has a slightly different dependency specification syntax than pip and conda which allows for more expressiveness in your package dependencies: https://python-poetry.org/docs/dependency-specification/
    * Also note that if you have other development tools like `black`, `mypy`, `ruff`, these can all be configured in the same `pyproject.toml` file.  This makes this file (almost) a universal project settings configuration file.

4. (optional) If using pyenv, specify local version of python for project in repo root.
    * In repo root: `pyenv local 3.11`.  This creates a `.python-version` file in the repo that specifies the desired python version. Poetry will automatically use this python version.
    * Make sure the `pyproject.toml` file specifies python versions concordant with this python version

5. Install packages in project
    * In your repo root: `poetry install`.  This solves all the dependencies specified in `pyproject.toml` and builds a `.lock` file, which contains all the specific packages to download and install for your virtual environment.
        * The poetry solver is fast and convenient.
        * All package installation uses `pip` under the hood, so any package that is `pip`-installable can be installed by poetry.
        * If you need to add additional packages after installation, `poetry add xyz_package`, poetry will automatically update the `.lock` file and any virtual environments will have the new packages installed.
    * To list all your installed packages: `poetry show`
    * If you want a `requirements.txt` file: `poetry export -f requirements.txt --without-hashes > requirements.txt`
    * To remove a dependency, delete it from the `pyproject.toml` file and then run `poetry lock --no-update`. This updates the lock file without upgrading the package version from all the dependencies.
    * To force re-solving all dependencies and rebuild environment, just delete the lock file and rerun `poetry install`.

6. Create virtual environment and run code in your virtual environment.
    * Create virtul environment: `poetry shell`.  Creates virtual environment and launches a new shell environment where all
    dependencies.
        * When you run your command, it will also tell you the path to where your virtual environment lists.  You can also find the location with `poetry show -v`.  If you use jupyter notebooks, you can point your notebook to this virtual environment (or the python binary in this virtual environment), and then your notebook will run using the python environment you specified.
        * If you're using VSCode, you need to tell VSCode where to look for poetry environments.  In VSCode Settings, look for setting `Python: Venv Path` and add `$HOME/.cache/pypoetry/virtualenvs/`.  If you restart VSCode, it should now look at that location for virtual environments/python kernels.
    * If you don't want to enter the shell, but you want to run a script in the virtual environment: `poetry run python myscript.py`

7. Importing your own code/package in a script or notebook is easy.  Poetry automatically builds the package for you if you specify it and installs it in your virtual environment.
    * In `pyproject.toml` file, note that under `[tool.poetry]` there is a line that says `packages = [{include = "poetry_example"}]`.  This tells poetry to include the package named `poetry_example` from this repo in the environment.  This means that any python subpackage or module within this package can be imported and referenced in your own code.
    * To show an example, I have created a sample notebook at `poetry_example/notebooks/example_book.ipynb` which imports the function `say_hello` from `poetry_example/src/hello_writer.py`.