# AWSFinance

Toolchain script for Financial and Mathematical Programming on Amazon´s AWS EC2 fresh instance.

## List of tools

* Jupyterhub [https://github.com/jupyter/jupyterhub] with support for the following languages:

	* Python 2.7

	* Python 3.5

	* R 3.2.3

	* Julia 0.4

* RStudio Server [https://www.rstudio.com/]

* Shiny Server [https://www.rstudio.com/products/shiny/shiny-server2/]

## Usage

1 - Start a fresh server instance with a minimal installation of *redhat6* or *centos6*.

2 - Access your system as root.

```shell
$ sudo su
```

3 - Install wget.

```
# yum -y install wget
```

4 - Download and run the main script.

```
# cd
# wget https://raw.githubusercontent.com/felipenoris/AWSFinance/master/redhat6/main.sh
# chmod u+x main.sh
# ./main.sh 
```

To let the server run que installation script by itself, do

```
# nohup ./main.sh &
```

## Requirements

*Redhat6* or *CentOS6*.

## System specific notes

### CentOS6

For a fresh *CentOS6* Virtual Machine, you may need to configure network:

```
# dhclient eth0
# yum -y install system-config-network-tui
```

### Redhat6

For a fresh *Redhat6* instance on Amazon's EC2, the default user is `ec2-user`.

## Application specific notes

### Jupyter

Data files are at `/usr/local/share/jupyter/hub`.

To start the server:

```
# jupyterhub
```

By default, will be accessible on the following link: `http://localhost:8000`, and will create state files (`jupyterhub_cookie_secret`, `jupyterhub.sqlite`) on current directory, and use default configuration.

You can generate a sample configuration file with:

```
# jupyterhub --generate-config
```

To start the server using a configuration file, use:

```
# jupyterhub -f jupyterhub_config.py
```

To set IP and port, use:

```
# jupyterhub --ip=192.168.1.2 --port=443
```

You may have to open port for external access:

```
# /sbin/iptables -I INPUT -p tcp -m tcp --dport 8000 -j ACCEPT
# /sbin/service iptables save
```

For `https` support, add the following lines to the config file:

```python
c.JupyterHub.port = 443
c.JupyterHub.ssl_cert = '/root/.ssh/sample-cert.pem'
c.JupyterHub.ssl_key = '/root/.ssh/sample-key.pem'
```

`443` is the default port for https. So the server will be accessible using https://localhost.

`sample-cert.pem` is the signed certificate file, and `sample-key.pem` is the private ssl key.

You can generate self signed certificate file with:

```
mkdir ~/.ssh
openssl req -x509 -newkey rsa:2048 -keyout ~/.ssh/sample-key.pem -out ~/.ssh/sample-cert.pem -days 9999 -nodes -subj "/C=BR/ST=Rio de Janeiro/L=Rio de Janeiro/O=org/OU=unit/CN=website"
chmod 400 sample*.pem

```

But be aware that your browser will not recognize the certificate as trusted.

### RStudio

Configuration files are at `/etc/rstudio`.

Default port is 8787.

Change the default port by editing rserver.conf. The following will change to port 80:

```
# echo -e "www-port=80" | tee /etc/rstudio/rserver.conf
# rstudio-server restart
# rstudio-server verify-installation
```

`auth-pam-sessions-profile` directive on /etc/rstudio.rserver.conf may not work. If that happens, RStudio will look at `/etc/pam.d/rstudio`.


## Packages

**Python**

*root user* will add packages with `pip` or `pip3` command line. Packages will be stored on `/usr/local/lib/python2.7` or `/usr/local/lib/python3.5` directories.

**R**

Check package locations with `$ R -e '.libPaths()'`.

System packages will be installed on `/usr/lib64/R/library`.

Each user can have a local package dir, automatically created under `~/R`.

*root user* will add packages with `R -e 'install.packages("pkg-name")' command.

**Julia**

The toolchain script will set package dir at `/usr/local/share/julia`.

Each user can add new search directories by changing Julia's `LOAD_PATH` variable.

```julia
julia> LOAD_PATH
2-element Array{ByteString,1}:
 "/usr/local/julia/local/share/julia/site/v0.4"
 "/usr/local/julia/share/julia/site/v0.4"
```

*root user* will add packages with `julia -e 'Pkg.add("pkg-name") && using pkg-name' command.

It's important to use `using pkg-name` after installation to precompile the package.

## References

* Jupyter main website: http://jupyter.org/

* Jupyterhub Docs: https://jupyterhub.readthedocs.org/en/latest/index.html

* Full list of supported kernels for Jupyter: https://github.com/ipython/ipython/wiki/IPython-kernels-for-other-languages

* RStudio website: https://www.rstudio.com/

* RStudio Server download page: https://www.rstudio.com/products/rstudio/download-server/

* RStudio Server Admin Guide: https://s3.amazonaws.com/rstudio-server/rstudio-server-pro-0.99.879-admin-guide.pdf

* Shiny Server Admin Guide: http://rstudio.github.io/shiny-server/latest/
