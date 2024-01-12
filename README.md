<p>
  <a href="https://roots.io/bedrock/">
    <img alt="Bedrock" src="https://cdn.roots.io/app/uploads/logo-bedrock.svg" width="32%" height="50px">
  </a>
  
  <a href="https://roots.io/sage/">
    <img alt="Sage" width="32%" height="50px" src="https://camo.githubusercontent.com/816698628628ce5df08887232d124acb9057ef5b9ecbf860fbefc3c40fa3a4b9/68747470733a2f2f63646e2e726f6f74732e696f2f6170702f75706c6f6164732f6c6f676f2d736167652e737667">
  </a>


  <a href="https://ddev.com/">
    <img style="background-color: white; padding: 0.3em; border-radius: 5px;" alt="DDEV" width="32%" height="50px" src="https://camo.githubusercontent.com/130a145fe53e55281e0c11867bb89d7b9370cacc7dfe304a15a83fec05195c55/68747470733a2f2f646465762e636f6d2f6c6f676f732f646465762e737667">
  </a>

</p>

<hr>

# Local Wordpress With Roots Bedrock and Sage

This is a complete setup for hosting a wordpress environment locally using Docker-based [DDEV](https://ddev.com/). It uses the [Bedrock](https://roots.io/bedrock/) Wordpress boilerplate code from [Roots.io](https://roots.io/), as well as their [Sage](https://roots.io/sage/) theme. This works on Windows Subsystem for Linux (WSL2) running Ubuntu as the distro (and it's quite fast), and should run on Macs and bare metal Linux just fine. I am using it with Docker CE installed directly in WSL, but it should word with docker desktop as well. 

I originally wanted to use Roots Trellis to set up my local Wordpress environment, but it was a real challenge getting Vagrant to play nicely on WSL. It doesn't like the Linux `home/user` filesystem at all, and in WSL you take a noticeable performance hit if you move anything over to the `mnt/c` DrvFs file system. There was also the issue of having to communicate with the Vagrant provider on the Windows desktop, SSHing the virtual machine, and updating the Windows hosts file through WSL. With DDEV and Docker, everything runs smoothly while keeping the environment nicely contained, and I would guess that it is much less of a CPU drain than a full on virtual machine. I admittedly was a little confused when trying to set up the bud/sage development urls and proxy, so I think I will need to revisit that, but for now I am looking forward to diving into developing with Blade!

## Getting Started
#### Install DDEV
You will need to install DDEV to build the local environment container complete with the web-server, database, and dependencies. It uses Docker, but offers its own CLI and many quickstart commands for popular PHP and Python Frameworks. Different options for installation can be found [here](https://ddev.readthedocs.io/en/stable/)

#### Get Yarn Package Manager
You will need to be able to use Yarn outside of the DDEV container if you want to use HMR while editing the Sage theme.
```zsh
npm install --global yarn
```
We will also use Composer, but it is only needed within the container so it doesn't have to be installed directly on the machine.

#### Pull Down the Repository
```zsh
git clone git@github.com:ryanmphill/roots-bedrock-sage-with-ddev.git
```

#### Start DDEV and Install Dependencies

Navigate to the root of the project start the DDEV Container

```zsh
ddev start
```

Install the dependencied within the container using the DDEV command

```zsh
ddev composer install
```
Create a .env file in the root directory with the following (generate and paste keys from `https://roots.io/salts.html`):

```py
DB_NAME='db'
DB_USER='db'
DB_PASSWORD='db'
DB_HOST='db'

# Optionally, you can use a data source name (DSN)
# When using a DSN, you can remove the DB_NAME, DB_USER, DB_PASSWORD, and DB_HOST variables
# DATABASE_URL='mysql://database_user:database_password@database_host:database_port/database_name'

# Optional database variables
# DB_HOST='localhost'
# DB_PREFIX='wp_'

WP_ENV='development'
WP_HOME="${DDEV_PRIMARY_URL}"
WP_SITEURL="${WP_HOME}/wp"

# Generate your keys here: https://roots.io/salts.html
AUTH_KEY='<Generated KEY goes here>'
SECURE_AUTH_KEY='<Generated KEY goes here>'
LOGGED_IN_KEY='<Generated KEY goes here>'
NONCE_KEY='<Generated KEY goes here>'
AUTH_SALT='<Generated KEY goes here>'
SECURE_AUTH_SALT='<Generated KEY goes here>'
LOGGED_IN_SALT='<Generated KEY goes here>'
NONCE_SALT='<Generated KEY goes here>'
```

#### Now, we need to activate the Sage theme
We'll go ahead and launch our local environment

```zsh
ddev launch
```

You should now be able to navigate to wordpress locally in the browser at `https://my-bedrock-wp-site.ddev.site/`! Follow the prompts to install and set up a user.

Once installed and setup successfully, install dependencies for the sage theme. For this step, we can SSH into the container
```zsh
ddev SSH
``` 
and then navigate to `web/app/themes/my-sage-theme` and run:

```bash
composer install
```
and
```bash
yarn install
```

and then exit the container with `exit` or `logout`

Now, activate the sage theme:
```zsh
ddev wp theme activate my-sage-theme
```

Now that the theme is set up, you can navigate to it at `web/app/themes/my-sage-theme` and start the theme development environment with HMR:
```zsh
yarn
```
and
```zsh
yarn dev
```
Now the local development server should be listening on localhost port 5000 and automatically update via HMR when changes are made to the `my-sage-theme/app/resources/views` directory!

## Troubleshooting
Let me know if I missed a step or if something seems off. There are a lot of pieces to put together to make this work, and I very easily could have left something out. I also haven't attempted building the project from this repository yet, but my hope is that it works out of the box. Thanks! 

-Ryan
