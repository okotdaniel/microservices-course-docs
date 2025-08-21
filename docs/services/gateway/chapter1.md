## 1. Installation and Project SetUp

In this chapter, we install FastAPI, starting with a minimal setup.

So lets start of by creating a dicrectory that will hold our project file for the different services that we shall be creating later on. 

i will be craeating this project in the documents directory 
to do this, lets naviaget to the Documents directory

```bash
  cd Documents 
```
Now lets create a folder and name it project. this can be watever you choose. the choice really dont matter. 
```bash
  mkdir Project  
```
Once this is created, we can then now start creating the different service dependent directories such as the auth-service, gateway-service, among others. 

For this part, we shall create the auth-service directory to hold our auth service. 
```bash
  mkdir auth-service 
```
Now that we have our auth-service directory setup, next thing we need to do is to now start working with the project and then proceed. 

For this service, we shall use django as a framework because of its robust features and to do this we shall need a file that weill hold our requiremts

Lets create a ``` requirements.txt ``` file in the auth-service directory that will hold all the priject dependancies related to the auth service that we  shall be working on. 

On Mac or Linux we shall be using the tocuh command. on other operating systems like windows, one can manually create the file. 

```bash
  touch requirements.txt 
```
### 1. Virtual Environment Creation

Lets begin by creating a virtual environment using the built-in Python module `venv`. If you already have Python installed, you might not need to install it separately. However, on Linux, installation may be necessary based on your distribution. In your commandline or terminal, type the following command.

```bash
python3 -m venv venv
```

This command generates a virtual environment in the specified folder (in our example, `venv`). Our Our environment ```venv``` can be any name but for the purpose of simplicity, lets go with that.
This folder is an isolated Python environment that separates the dependencies of our project from the system-wide Python installation.

Now lets go on and activate the virtual environment using the following commands:

On Linux or macOS:

```bash
source venv/bin/activate
```

On Windows:

```bash
venv\Scripts\activate
```

Once activated, your command line will indicate the active virtual environment:

On Linux or macOS:

```bash
(venv) yourusername@yourmachine$
```

On Windows:

```bash
(venv) C:\users\YourUsername>
```

### 2. Directory Structure

At this point, your directory structure should look like this:

```
└── venv
└── requiremnts.txt
```

### 3. Installing Django

Now, let us install Django within the virtual environment. We shall install Django using `pip` with the following command.

```console
(venv) pip install "django"
```

### 4. Freeze Dependencies

Freeze the installed dependencies into a `requirements.txt` file to track the exact versions of our dependencies so that we can easily reproduce them in the future.

```console
(env) pip freeze > requirements.txt
```

### 5. Confirm the installation

Let us confirm our FastAPI installation by running the following command.

```python
(env) python -m django --version
4.2
```

This command will show us the version of the Django that we installed earlier on. 

If the version has been displayed, then we are sure that Django has been installed in our virtual environment.

## Conclusion

By following these steps, you have successfully created a project directory, set up a virtual environment, and installed Django, frozen the dependencies to reproduce the project in the future and you have verified your installation using the ```python -m django --version ``` command. This structured approach ensures a clean and manageable development environment for our Django project. Next, we shall create the simpel auth project that we shall later on build on to create endpoints with django restframework.
