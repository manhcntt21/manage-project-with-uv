## UV

Illustrating how to use **uv** for installing and managing libraries

---
## 1. Using uv to install into .venv
It's common for a project to have multiple versions, and for different versions of a library to exist across these project versions. (e.g: alpha and beta are versions of projects here)

In such cases, uv helps manage these library versions, and when combined with Conda, it offers a more efficient workflow.

1. Create a virtual environment using Conda.
2. Install libraries into that virtual environment using uv.
3. Use uv to initialize new projects: uv init <project_name>.
4. Within each project version: install libraries using uv add <package_name>. This will create a .venv directory within the project folder. These packages are independent of the Conda environment created initially.
5. Later, if you want to synchronize the project's libraries with the `venv` environment, execute the uv sync command. At this point, if you want uv to install the libraries in the Conda environment. You can also use the following method: `uv pip install . --system`. Another way is to use a requirements.txt file, which will be discussed later.
   - . represents the current project directory.
   - uv pip install . will read the pyproject.toml file, resolve dependencies (referencing uv.lock for locked versions), and then install the project along with those dependencies.
   - --system directs the installation to the active Conda environment (e.g., uv-example).

_Note_: You need to configure the cache directory to reduce storage usage on the main hard drive.
- UV_CACHE_DIR
  - On Windows, configure this in Environment Variables -> System variables.
  - On Linux, configure this in ~/.bashrc by adding: export UV_CACHE_DIR=/path/to/cache.
- These enviroment variables can be set similarly, but you need to understand their effects. For more information, refer to [here](https://docs.astral.sh/uv/reference/environment/).

---

When starting with a new project, you need to do the following:

1. Create a virtual environment using Conda.
2. Activate that virtual environment.
3. Create and update uv.lock: uv lock. This command will create a uv.lock file in the current directory, analyzing and resolving dependencies.
4. Synchronize the virtual environment (.venv) with uv.lock. You need to perform one of the following two methods.
   - You have to run uv add <package_name> for each package. (**No other method has been found yet.**)
   - uv pip install -r pyproject.toml (installs directly into the Conda environment).

___
## 2. Installing directly into the Conda environment
If you don't care about the version of the code and want to install directly into the Conda environment, follow these steps (the goal is to use uv for installation):

1. Create a virtual environment using Conda.
2. Create a project using one of [the following methods]((https://docs.astral.sh/uv/guides/projects/)):
   - Method 1: uv init <project_name>, cd <project_name>.
   - Method 2: mkdir <project_name>, cd <project_name>, uv init.
   - The project structure will look like this:

```commandline
.
├── .venv
│   ├── bin
│   ├── lib
│   └── pyvenv.cfg
├── .python-version
├── README.md
├── main.py
├── pyproject.toml
└── uv.lock
```
3. Install directly into Conda. This is not recommended as it can cause conflicts between libraries in Conda and those in the project, but if that's your intention, here's how:
   - uv pip install <package_name>
   - uv pip install -r requirements.txt
   - uv pip install . (installs directly into the Conda environment, without creating a .venv)

```commandline
uv pip install [OPTIONS] <PACKAGE|--requirements <REQUIREMENTS>|--editable <EDITABLE>|--group <GROUP>>
```
   - uv pip install . --system
   - uv pip install -r pyproject.toml --system
   - uv pip install <package_name> --system

`--system` nghĩa là:
   - Install packages into the system Python environment.
   - By default, uv installs into the virtual environment in the current working directory or any parent directory
   - option instructs uv to instead use the first Python found in the system PATH

Alternatively, when the environment is activated, the installation is prioritized into that virtual environment, whether it's `Conda` or `.venv`, meaning in this case, you don't need `--system`.

_chú ý_:
   - it does not change the `pyproject.toml` file or the `uv.lock` file. You need to manually add the package to the `pyproject.toml` file if you want it to be recorded or `uv add`, whenever `pyproject.toml` is updated, you need to run `uv lock --upgrade` to update the `uv.lock` file. (sync the lock file with the `venv` environment).
   - it does not remember the package version that you installed with `uv pip install <package_name> --system`, so if you want to use the same version in another environment, you need to specify it in the `pyproject.toml` file or specify exactly the version when installing `uv add <package_name>==<version>`.