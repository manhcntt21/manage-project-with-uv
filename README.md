## UV

Illustrating how to use **uv** for installing and managing libraries

It's common for a project to have multiple versions, and for different versions of a library to exist across these project versions. (e.g: alpha and beta are versions of projects here)

In such cases, uv helps manage these library versions, and when combined with Conda, it offers a more efficient workflow.

1. Create a virtual environment using Conda.
2. Install libraries into that virtual environment using uv.
3. Use uv to initialize new projects: uv init <project_name>.
4. Within each project version: install libraries using uv add <package_name>. This will create a .venv directory within the project folder. These packages are independent of the Conda environment created initially.
5. Later, if you want to synchronize the project's libraries with the Conda environment, execute the uv sync command. At this point, uv will automatically install the libraries in the Conda environment. You can also use the following method: uv pip install . --system. Another way is to use a requirements.txt file, which will be discussed later.
   - . represents the current project directory.
   - uv pip install . will read the pyproject.toml file, resolve dependencies (referencing uv.lock for locked versions), and then install the project along with those dependencies.
   - --system directs the installation to the active Conda environment (e.g., uv-example).

_Note_: You need to configure the cache directory to reduce storage usage on the main hard drive.
- UV_CACHE_DIR
- On Windows, configure this in Environment Variables -> System variables.
- On Linux, configure this in ~/.bashrc by adding: export UV_CACHE_DIR=/path/to/cache.

---

When starting with a new project, you need to do the following:

1. Create a virtual environment using Conda.
2. Activate that virtual environment.
3. Create and update uv.lock: uv lock. This command will create a uv.lock file in the current directory, analyzing and resolving dependencies.
4. Synchronize the virtual environment (.venv) with uv.lock. You need to perform one of the following two methods.
   - You have to run uv add <package_name> for each package. (**No other method has been found yet.**)
   - uv pip install -r pyproject.toml (installs directly into the Conda environment).