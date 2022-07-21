# Style guide

## Content

1. [Python](#python)
2. [Angular](#angular)
3. [Commits style](#commits-style)
4. [IDE setings](#ide-settings)
   1. [PyCharm settings](#pycharm-settings)

## Python

For comments and docstrings, stick to the [Google style guide](https://google.github.io/styleguide/pyguide.html#s3.8-comments-and-docstrings).

For code formatting, use [blue formatter](https://pypi.org/project/blue/).

For static type checking, use [Mypy](https://mypy.readthedocs.io/en/stable/) - each project contains mypy.ini with its settings.

For linting, use [Pylint](https://pylint.pycqa.org/en/latest/).

Arguments for [dc-sonar-user-layer](https://github.com/ST1LLY/dc-sonar-user-layer): `--max-line-length=119 --disable=too-few-public-methods,import-error,import-outside-toplevel,broad-except,wrong-import-position,duplicate-code --load-plugins pylint_django --django-settings-module=dc_sonar_web.settings`.

Arguments for [dc-sonar-workers-layer](https://github.com/ST1LLY/dc-sonar-workers-layer): `--max-line-length=119 --disable=too-few-public-methods,import-error,import-outside-toplevel,broad-except,wrong-import-position,duplicate-code`.

Arguments for [ntlm-scrutinizer](https://github.com/ST1LLY/ntlm-scrutinizer): `--extension-pkg-whitelist=pydantic --max-line-length=119 --disable=too-few-public-methods,import-error,import-outside-toplevel`.

The limit of 79 chars uses for comments and docs.

The limit of 119 chars uses for code.

## Angular

For formatting code in [dc-sonar-fronted](https://github.com/ST1LLY/dc-sonar-frontend), use [tsconfig.json](https://angular.io/guide/typescript-configuration) located in the repository.

## Commits style

https://www.conventionalcommits.org/en/v1.0.0/.

https://github.com/angular/angular/blob/22b96b9/CONTRIBUTING.md#commit.

## IDE settings

### PyCharm settings

#### Interpreter

Go to File | Settings | Python Interpreter | Add ...:

![add_interpreter](https://raw.githubusercontent.com/ST1LLY/dc-sonar/main/py_charms_settings_scrs/add_interpreter.png)



Choose "Existing environment" then OK:

![choose_existing_env](https://raw.githubusercontent.com/ST1LLY/dc-sonar/main/py_charms_settings_scrs/choose_existing_env.png)

#### Docstring format

Go to File | Settings | Python Integrated Tools, choose Docstring format: Google then Apply:

![docstring_google](https://raw.githubusercontent.com/ST1LLY/dc-sonar/main/py_charms_settings_scrs/docstring_google.png)



#### Mypy

Go to File | Settings | Other Settings | Mypy.

Set the path to Mypy executable: `{YOUR_PATH}\{PROJECT}\venv\Scripts\mypy.exe`.

Set the path to config file: `{YOUR_PATH}\{PROJECT}\mypy.ini`.

Apply.

![mypy_settings](https://raw.githubusercontent.com/ST1LLY/dc-sonar/main/py_charms_settings_scrs/mypy_settings.png)



#### Pylint

Go to File | Settings | Other Settings | Pylint.

Set the path to Pylint executable: `{YOUR_PATH}\{PROJECT}\venv\Scripts\pylint.exe`

Arguments: See [above](#Python).

Apply.

#### Blue formatter

Go to File | Settings | External Tools | Add:

![ext_tools_add](https://raw.githubusercontent.com/ST1LLY/dc-sonar/main/py_charms_settings_scrs/ext_tools_add.png)



Set the name as `Blue Formatter`.

Set the description as `https://pypi.org/project/blue/`.

Set the program as `$PyInterpreterDirectory$\blue.exe`.

Set the arguments as `$FilePath$ --line-length=119`.

Set the working directory as `$ProjectFileDir$`.

![blue_formatter_tool](https://raw.githubusercontent.com/ST1LLY/dc-sonar/main/py_charms_settings_scrs/blue_formatter_tool.png)



Click OK then Apply.

Go to Settings | Keymap | Blue Formatter | Add Keyboard Shortcut

![add_shortcut_blue_formatter](https://raw.githubusercontent.com/ST1LLY/dc-sonar/main/py_charms_settings_scrs/add_shortcut_blue_formatter.png)



Enter `Alt+B` click OK then Apply.



#### Language Injections

Go to Settings | Editor | Language injections.

Disable the setting python:"SQL select/delete/insert/update/create" then click Apply.

![language_injections](https://raw.githubusercontent.com/ST1LLY/dc-sonar/main/py_charms_settings_scrs/language_injections.png)



#### Code Style

Go to Settings | Editor | Code Style.

Set the hard wrap at as `119`.

Set the visual guides as `79, 119`.

Click Apply.

![code_style](https://raw.githubusercontent.com/ST1LLY/dc-sonar/main/py_charms_settings_scrs/code_style.png)



#### Useful shortcuts

Use `Alt + B ` to format a .py file.

Use `Ctrl + Alt + O` to optimize imports in a .py file.
