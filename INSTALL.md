# INSTALL

Try the MAKE script, this should fetch and build everything. Works on CENTOS7
Please study it first, there are some package installs that may be problematic.

## INSTALL STEPS

*** CREATE LOCAL GIT REPO ***
```bash
mkdir PROJECTS;cd PROJECTS
git clone --recursive https://github.com/D-TACQ/ACQ164 ACQ164
```

```bash
(cd ACQ200API; make lib; cp ./x86/lib/* ../acq164ioc/lib/linux-x86_64/)
(cd base; make)

source ./setup.env
mkdir support
(
(cd seq; make)
(cd asyn; make)
(cd pcre; make)
(cd stream; make)

sudo dnf install rpcgen
sudo dnf install libtirpc-devel

)

cd acq164ioc; make
```

 Good Job Freddie! Thanks!


## GIT-FOO : how this git ensemble was created

** NB: you do NOT have to do this, it's more notes for next time **

### Following this install guide .. 
https://docs.epics-controls.org/projects/how-tos/en/latest/getting-started/installation.html
```
git submodule add --force https://github.com/epics-base/epics-base.git base
cd base; make
```

Submodules reference only submodule add --force : actually works
```

mkdir support
git submodule add -b master https://github.com/D-TACQ/EPICS-seq.git support/seq
git submodule add -b master https://github.com/D-TACQ/StreamDevice.git support/stream
git submodule add --force https://github.com/ISISComputingGroup/EPICS-pcre.git support/pcre
git submodule add --force https://github.com/epics-modules/asyn.git support/asyn
git submodule add --force https://github.com/D-TACQ/acq164ioc acq164ioc
```

