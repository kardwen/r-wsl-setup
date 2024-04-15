# Automatic updates for R on Windows with WSL

The usual way of updating R on Windows is to trigger updates manually with the R package ``ìnstallr``. However, a more convenient way to update R is to install R inside a WSL (Windows Subsystem for Linux) distribution like Debian and by leveraging the Windows Update process to update both the WSL distribution and your R installation automatically.

## Windows Subsystem for Linux

WSL2 requires Windows 10 version 2004 and higher (Build 19041 and higher) or Windows 11, detailed installation instructions can be found in the [Microsoft documentation](https://learn.microsoft.com/en-us/windows/wsl/install). If you do not have a WSL installation yet, you can install Debian as WSL distribution by entering the following command in a cmd or Powershell terminal as administrator:

```cmd
wsl --install -d Debian
```

When installing WSL without the ``-d`` option, Ubuntu will be installed as default Linux distribution. Instructions for installing R are very similar on Ubuntu and can be found on <https://www.r-project.org/> as well.

After restarting you can log in to your WSL distribution by simply typing ``wsl ~``.

## R Installation

While R is available in the Debian package repositories, the most reliable way to ensure to always have the latest R version installed is to configure the Debian repository maintained by the R project.

### Configuring the Debian repository

Create a new entry for the additional repository:

```bash
sudo nano /etc/apt/sources.list.d/r-project.list
```

Add an entry for the repository like this, you can find the correct branch for your Debian version [here](https://cran.r-project.org/bin/linux/debian/#supported-branches):

```text
deb http://cloud.r-project.org/bin/linux/debian bookworm-cran40/
```

The second step is to add the key for verifying the package integrity, details can again be found on [r-project.org](https://cran.r-project.org/bin/linux/debian/#secure-apt):

```bash
gpg --keyserver keyserver.ubuntu.com \
    --recv-key '95C0FAF38DB3CCAD0C080A7BDC78B2DDEABC47B7'
gpg --armor --export '95C0FAF38DB3CCAD0C080A7BDC78B2DDEABC47B7' | \
    sudo tee /etc/apt/trusted.gpg.d/cran_debian_key.asc
```

### Installing R

Since you have just added a new repository, run the following commands to retrieve information about newly available packages and install any applicable updates:

```bash
sudo apt update && sudo apt upgrade
```

Finally, [install R](https://cran.r-project.org/bin/linux/debian/#installation) with the following command. The second package ``r-base-dev`` is an optional dependency needed for the development of R packages and can be omitted.

```bash
sudo apt install r-base r-base-dev
```

## RStudio Desktop

Since WSL2 you can [run Linux GUI apps](https://learn.microsoft.com/en-us/windows/wsl/tutorials/gui-apps) in WSL. RStudio Desktop is the most widely used graphical IDE for working with R, it can be installed manually with the provided binary packages on [posit.co](https://posit.co/download/rstudio-desktop/). Unfortunately RStudio is not available in the official Debian repositories.

```bash
wget https://download1.rstudio.org/electron/jammy/amd64/rstudio-2023.12.1-402-amd64.deb -P /tmp
sudo apt install gdebi-core
sudo gdebi /tmp/rstudio-2023.12.1-402-amd64.deb
```

When you try to start RStudio with the command ``rstudio`` you will find that it complains that it is missing a dependency which needs to be installed manually:

```bash
sudo apt install libasound2
```

Once installed you can also find an entry for RStudio in the Windows Start menu.

## Notes

* You can open the Windows explorer at the current location in WSL with ``explorer.exe .``
* Note that transfering large files between WSL2 and Windows is rather slow
* Instructions for installing RStudio Server in WSL can be found on [posit.co](https://support.posit.co/hc/en-us/articles/360049776974-Using-RStudio-Server-in-Windows-WSL2)
