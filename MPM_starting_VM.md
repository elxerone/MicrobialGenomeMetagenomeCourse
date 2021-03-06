# Prepare the Virtual Machine

---

* [_From microbial genomics to metagenomics_](./README.md)
  * [_Bacterial Genomics_](./Genomics.md)
    * _Prepare the Virtual Machine_
      1. [_Connect to VM_](./MPM_starting_VM.md#connect-to-vm)
      2. [_Transfer data between your computer and the VM_](./MPM_starting_VM.md#transfer-data-between-your-computer-and-the-vm)
      3. [_Prepare the VM_](./MPM_starting_VM.md#prepare-the-vm)
    * [_From reads to assembly: working with INNUca pipeline_](./MPM_workingwithINNUCA.md)
    * [In silico _typing using ReMatCh and Abricate_](./MPM_ReMatCh_Abricate.md)

---

**Note 1:** replace whatever is between `<>` with the proper value. For example, in `<VM.IP>` use your Virtual Machine (VM) IP provided (something like `193.166.24.142`).  
**Note 2:** check the number of CPUs of your VM using `htop` (the CPUs available will be displayed at the top as dynamic horizontal bars, numbered sequentially).

The key for both Unix (mgmc.key) and Windows (mgmc.ppk) users is available for [download](https://www.dropbox.com/sh/vk9mup9wqh9d2y5/AAAQjLoLxBSgRWkAT6-YoocWa?dl=0); *the password for accessing the Dropbox directory will be available during the course*.

---

## Connect to VM

_In your computer_

  * **Note:** The SSH key was produced in Linux using `ssh-keygen -t rsa -f /home/cloud-user/mgmc.key`. More information [here](https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys--2 "Google search: ssh key linux").
  
The key for both Unix (mgmc.key) and Windows (mgmc.ppk) users is available for [download](https://www.dropbox.com/sh/vk9mup9wqh9d2y5/AAAQjLoLxBSgRWkAT6-YoocWa?dl=0); *the password for accessing the Dropbox directory will be available during the course*.

### Unix

Using terminal

```bash
# Give user read only permission to the SSH key
chmod 400 </path/to/provided/private/ssh/key/mgmc.key>

ssh -i </path/to/provided/private/ssh/key/mgmc.key> cloud-user@<VM.IP>
```

### Windows

Using [PuTTY](http://www.putty.org/)
  * **Note 1:** use `putty.exe` and `puttygen.exe` from _"Alternative binary files"_ ([Download PuTTY](https://devops.profitbricks.com/tutorials/use-ssh-keys-with-putty-on-windows/#use-existing-public-and-private-keys "Google search: use ssh key in putty"))
  * **Note 2:** to paste text in PuTTY click on mouse right botton inside PuTTY terminal

For information on how to use SSH Keys with PuTTY see [here](https://devops.profitbricks.com/tutorials/use-ssh-keys-with-putty-on-windows/ "Google search: use ssh key in putty") (specifically [_"Use Existing Public and Private Keys"_](https://devops.profitbricks.com/tutorials/use-ssh-keys-with-putty-on-windows/#use-existing-public-and-private-keys) and [_"Connect to Server with Private Key"_](https://devops.profitbricks.com/tutorials/use-ssh-keys-with-putty-on-windows/#connect-to-server-with-private-key) sections) 

Briefly: open PuTTY; on the right pannel *Connection > SSH > Auth* upload the key.ppk in *Private key file for authentication*, then follow the instructions below 

**Connection settings (after preparing PuTTY to use SSH key):**  

![putty_connection_info](https://user-images.githubusercontent.com/13034956/33169167-859e1676-d03b-11e7-91a2-ecc4fe29024d.png)  
_Yellow steps are optional steps. They serve to avoid introducing always 1-3 steps. After it is saved, everytime it is necessary to connect the VM, it is only need to click 6-8._

---
## Transfer data between your computer and the VM

### Unix

Using terminal

From the Local computer to the VM

```
scp -i </path/to/provided/private/ssh/key/mgmc.key> </path/to/local/data/file> cloud-user@<VM.IP>:</path/to/data/file>
```

From the VM to the Local computer

```
scp -i </path/to/provided/private/ssh/key/mgmc.key> cloud-user@<VM.IP>:</path/to/data/file> </path/to/local/data/file>
```
### Using client software

For information on how to use SSH Keys with [FileZilla](https://wiki.filezilla-project.org/Howto) or [WinSCP](https://winscp.net/eng/docs/ui_login_authentication) please used the following [link](https://github.com/INNUENDOCON/MicrobialGenomeMetagenomeCourse/blob/master/Client.pdf)

---

## Prepare the VM

### Give colour to your terminal

_In the VM_  

Edit `~/.bashrc` (using for example `nano ~/.bashrc`) and uncomment `force_color_prompt=yes` by removing the `#`. More information [here](https://askubuntu.com/questions/517677/how-to-get-a-colored-bash "Google search: get colored bash").
  * **Note:** After editing exit with `Ctrl + X`; type `y` to save changes; don't change the name file by only pressing `Enter`.

### Install generic programs

* [htop](http://hisham.hm/htop/)
  * An interactive process viewer for Unix systems
  * Allows monitoring VM activity (CPUs and memory usage, proccesses running, etc.)
* [GNU Parallel](https://www.gnu.org/software/parallel/)
  * Shell tool for executing jobs in parallel using one or more computers.

_In the VM_  

```bash
sudo apt-get install -y htop parallel
```

### Organize your VM

_In the VM_  

```bash
# Create a folder where all the tools to be used will be placed
mkdir ~/NGStools

# Create a folder to store different databases
mkdir ~/DBs
```

### Install Docker

**_What is_** [Docker](https://www.docker.com/)**_?_**

> "Docker is a tool that can package an application and its dependencies in a virtual container that can run on any Linux server," Lyman explained. "This helps enable flexibility and portability on where the application can run, whether on premise, public cloud, private cloud, bare metal, etc." From [here](https://www.linux.com/news/docker-shipping-container-linux-code).

**Installation**

Information on how to install [Docker](https://www.docker.com/) for Ubuntu is available at this [link](https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/)

_In the VM_  

```bash
sudo apt-get remove -y docker docker-engine docker.io
sudo apt-get update
sudo apt-get install -y linux-image-extra-$(uname -r) linux-image-extra-virtual
sudo apt-get install -y apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo apt-key fingerprint 0EBFCD88

# Key with the fingerprint 9DC8 5822 9FC7 DD38 854A E2D8 8D81 803C 0EBF CD88

sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt-get update
sudo apt-get install -y docker-ce

sudo docker run hello-world
```

**Run Docker without sudo**  

More information [here](https://docs.docker.com/engine/installation/linux/linux-postinstall/).

```bash
sudo groupadd docker
sudo gpasswd -a $USER docker

# logout and login to activate the changes to groups (close the terminal and open it again)

docker run hello-world
```

### Install getSeqENA

**_What is [getSeqENA](https://github.com/B-UMMI/getSeqENA)?_**  

> Download sequences from ENA/SRA databases

**Install dependencies**  

_Aspera Connect_  

_In your computer_  

From the [webpage](http://downloads.asperasoft.com/connect2/):
  * See all installers > Linux > Linux - Select Version > Direct download
  * Copy Link Location (Direct download)

_In the VM_  

```bash
wget http://download.asperasoft.com/download/sw/connect/3.7.4/aspera-connect-3.7.4.147727-linux-64.tar.gz
tar xf aspera-connect-3.7.4.147727-linux-64.tar.gz
bash aspera-connect-3.7.4.147727-linux-64.sh
rm aspera-connect-3.7.4.147727-linux-64.sh aspera-connect-3.7.4.147727-linux-64.tar.gz
mv ~/.aspera/ ~/NGStools/aspera/
echo "export PATH=$HOME/NGStools/aspera/connect/bin"':$PATH' >> ~/.profile
```

*  information on add path to PATH environmental variable [here](https://unix.stackexchange.com/questions/26047/how-to-correctly-add-a-path-to-path "Google search: add path to PATH linux")

_NCBI SRA Toolkit_  

_In your computer_  

In the [webpage](https://trace.ncbi.nlm.nih.gov/Traces/sra/sra.cgi?view=software):
  * Ubuntu Linux 64 bit architecture
  * Copy Link Location (Ubuntu Linux 64 bit architecture)

_In the VM_  

```bash
wget https://ftp-trace.ncbi.nlm.nih.gov/sra/sdk/2.8.2-1/sratoolkit.2.8.2-1-ubuntu64.tar.gz
tar xf sratoolkit.2.8.2-1-ubuntu64.tar.gz
rm sratoolkit.2.8.2-1-ubuntu64.tar.gz
mv sratoolkit.2.8.2-1-ubuntu64/ ~/NGStools/
echo "export PATH=$HOME/NGStools/sratoolkit.2.8.2-1-ubuntu64/bin"':$PATH' >> ~/.profile
```

**Install getSeqENA**  

_In your computer_  

In the [webpage](https://github.com/B-UMMI/getSeqENA):
  * Clone or download > Copy to clipboard

_In the VM_  

```bash
git clone https://github.com/B-UMMI/getSeqENA.git
mv getSeqENA/ ~/NGStools/
echo "export PATH=$HOME/NGStools/getSeqENA"':$PATH' >> ~/.profile
```

### Get INNUca

**_What is [INNUca](https://github.com/B-UMMI/INNUca)?_**  

> INNUENDO quality control of reads, de novo assembly and contigs quality assessment, and possible contamination detection

_In your computer_  

<span style="color:lightblue">In your computer</span>  
From the [webpage](https://github.com/B-UMMI/INNUca):
  * Docker

_In the VM_  

```bash
docker pull ummidock/innuca:3.1
```

### Install ReMatCh

**_What is [ReMatCh](https://github.com/B-UMMI/ReMatCh)?_**  

> Reads mapping against target sequences, checking mapping and consensus sequences production

_In your computer_  

In the [webpage](https://github.com/B-UMMI/ReMatCh):
  * Clone or download > Copy to clipboard

_In the VM_  

```bash
git clone https://github.com/B-UMMI/ReMatCh.git
mv ReMatCh/ ~/NGStools/
echo "export PATH=$HOME/NGStools/ReMatCh"':$PATH' >> ~/.profile
```

### Get ABRicate

**_What is [ABRicate](https://github.com/tseemann/abricate)?_**  

> Mass screening of contigs for antimicrobial resistance or virulence genes. It comes bundled with seven databases: Resfinder, CARD, ARG-ANNOT, NCBI BARRGD, NCBI, EcOH, PlasmidFinder and VFDB.

_In your computer_  

In UMMI Docker Hub [webpage](https://hub.docker.com/u/ummidock/dashboard/):
  * ummidock/abricate

_In the VM_  

```bash
docker pull ummidock/abricate:latest
```

### Get Prokka

**_What is [Prokka](https://github.com/tseemann/prokka)?_**  

> Prokka is a software tool to annotate bacterial, archaeal and viral genomes quickly and produce standards-compliant output files

_In your computer_  

In UMMI Docker Hub [webpage](https://hub.docker.com/u/ummidock/):
  * ummidock/prokka

_In the VM_  

```bash
docker pull ummidock/prokka:1.12
```

### Get Roary

**_What is [Roary](http://sanger-pathogens.github.io/Roary/)?_**  

> Roary is a high speed stand alone pan genome pipeline, which takes annotated assemblies in GFF3 format (produced by Prokka (Seemann, 2014)) and calculates the pan genome

_In your computer_  

In Sanger Pathogens Docker Hub [webpage](https://hub.docker.com/u/sangerpathogens/):
  * sangerpathogens/roary

_In the VM_  

```bash
docker pull sangerpathogens/roary:latest
```

### Get Scoary

**_What is [Scoary](https://github.com/AdmiralenOla/Scoary)?_**  

> Scoary is designed to take the gene_presence_absence.csv file from Roary as well as a traits file created by the user and calculate the assocations between all genes in the accessory genome and the traits. It reports a list of genes sorted by strength of association per trait.

_In the VM_  

```bash
sudo apt-get install python-pip
pip install scoary
```
