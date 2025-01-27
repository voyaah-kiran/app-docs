---
attachments: [Clipboard_2025-01-23-14-08-18.png]
title: PyPI server setup
created: '2025-01-23T08:24:23.462Z'
modified: '2025-01-27T05:55:46.529Z'
---

# PyPI server setup
## Server setup
### What is PyPI server?
A PyPI server is a server that hosts Python packages and provides a central repository for managing and distributing Python software.
### How to setup PyPI server?
#### Step 1 : pip install pypiserver
```
pip install pypiserver
```
This command install 'pypiserver' package.
#### Step 2 : pypi-server run -p <PORT_NUMBER>
#### or
#### Step 2 : pypi-server run -p <PORT_NUMBER> -P <htpasswd_file>
```
pypi-server run -p <PORT_NUMBER>
or
pypi-server run -p <PORT_NUMBER> -P <htpasswd_file>
```
This command starts the server on given <PORT_NUMBER>.
Now, The server has to be running on http://localhost:<PORT_NUMBER>/
![](@attachment/Clipboard_2025-01-23-14-08-18.png)
## Create python package
### What is Python package?
A Python package is a collection of Python modules that are bundled together in a directory hierarchy. A module is just a single file containing Python code, while a package can contain multiple modules, sub-packages, and other resources like data files.

A Python package typically includes:
1. Modules: Python files that contain reusable code, such as functions, classes, or variables.
2. __init__.py file: This file makes the directory a package and can contain initialization code for the package.
3. Other files: Documentation, data files, or other resources needed for the package.
### setup.py
Package which is ready to publish needs setup.py file.
```
from setuptools import setup, find_packages

setup(
    name='package_name',
    version='0.X',
    packages=find_packages(),
    install_requires = [

    ],
)
```
## Python build
### pip install --upgrade build
```
pip install --upgrade build
```
Install build module to build the package.
### python -m build
```
python -m build
```
This command will build the package.
After this you should see two new folders. Later folder structure should like this.
```
.
└── Path/To/Your/Package/Folder/
    ├── package/
    │   ├── __init__.py
    │   └── Folder/with/package/files
    ├── build/
    │   └── build/files
    ├── dist/
    │   ├── <package_name><version>.whl
    │   └── <package_name><version>.tar.gz
    └── setup.py
```
#### Note: make sure you changed the version in setup.py
## Package Upload
### Twine
```
pip install twine
```
twine is the module which is used to upload package to pypi-server.
```
twine upload --repository-url https://<HOST>:<PORT_NUMBER>/ path/to/tar_file/<package_name><version>.tar.gz --username <username> --password <password>
```
use this command to upload your package to pypi-server.
## Package Insall
```
pip install --extra-index-url=https://<HOST>:<PORT_NUMBER>/simple/ <package_name> --trusted-host <HOST>
```
Now the package should be avaiable in virtual environment.
## Host with Docker
Dockerfile
```
# Use official Python 3 image
FROM python:3.9-slim

COPY htpasswd.txt .

# Install pypiserver
RUN pip install pypiserver passlib

# Create a directory for the package repository
RUN mkdir /pypi-repo

# Expose port 8080 for the PyPI server
EXPOSE 8081

# Start pypiserver when the container runs
CMD ["pypi-server", "run", "-p", "8081", "/pypi-repo", "-P", "htpasswd.txt"]

```

commands to run this dockerfile
```
docker build -t <name/tag> .
docker run <name/tag> -p <PORT>:<PORT_ON_CONTAINER>
```
## Additional requirements
### creating htpasswd.txt file
Use command 
```
htpasswd -c <filename.>.txt <username_>
```
