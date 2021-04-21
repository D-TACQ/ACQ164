# EPICS base and support for acq164 HOSTPC IOC

## History

ACQ164CPCI is fitted with an IOC dating from 2007-2010. It still works, but is impossible to extend and lacks functionality, notably SLOW AI record update.

## Strategy
ACQ164 is well field proven for streaming data, solid at 20kSPS * 32 channels.
So, we propose to set it up to stream to a HOST based IOC, that can benefit from modern EPICS and fast processors.

To build it: PLEASE see INSTALL.md

## Structure

```
pgm@hoy5 ACQ164]$ tree -pd -L 2
.
├── [drwxrwxr-x]  acq164ioc             # the IOC
│   ├── [drwxrwxr-x]  acq164App
│   │   ├── [drwxrwxr-x]  Db
│   │   └── [drwxrwxr-x]  src
│   ├── [drwxrwxr-x]  bin
│   │   └── [drwxrwxr-x]  linux-x86_64
│   ├── [drwxrwxr-x]  configure
│   │   ├── [drwxrwxr-x]  O.Common
│   │   └── [drwxrwxr-x]  O.linux-x86_64
│   ├── [drwxrwxr-x]  db
│   ├── [drwxrwxr-x]  dbd
│   ├── [drwxrwxr-x]  include
│   ├── [drwxrwxr-x]  iocBoot
│   │   └── [drwxrwxr-x]  iocacq164    # run from here
│   └── [drwxrwxr-x]  lib
│       └── [drwxrwxr-x]  linux-x86_64

├── [drwxrwxr-x]  ACQ200API		# ACQ200 COMMS LIB
│   ├── [drwxrwxr-x]  x86
├── [drwxrwxr-x]  base			# EPICS BASE
...
└── [drwxrwxr-x]  support		# EPICS SUPPORT
    ├── [drwxrwxr-x]  asyn
    ├── [drwxrwxr-x]  pcre
    ├── [drwxrwxr-x]  seq
    └── [drwxrwxr-x]  stream

```

## RUN

### First check settings

'''
cat setup.env
export EPICS_HOME=${HOME}/PROJECTS/ACQ164
export EPICS_BASE=${EPICS_HOME}/base
if [ ! -e $EPICS_BASE/Makefile ]; then
	echo ERROR $EPICS_BASE does not exist
	exit 1
else
	echo EPICS_BASE set $EPICS_BASE
fi
export EPICS_HOST_ARCH=$(${EPICS_BASE}/startup/EpicsHostArch)
export PATH=${EPICS_BASE}/bin/${EPICS_HOST_ARCH}:${PATH}
```

### set EPICS runtime env : TOP and UUT for sure
```
cd acq164ioc/iocBoot/iocacq164
[pgm@hoy5 iocacq164]$ cat envPaths 
epicsEnvSet("IOC","iocacq164")
epicsEnvSet("TOP","/home/pgm/PROJECTS/ACQ164.new/acq164ioc")
#epicsEnvSet("TOP","/home/peter/PROJECTS/ACQ164/acq164ioc")
epicsEnvSet("UUT","acq164_080")
epicsEnvSet("SIZE","1024")
epicsEnvSet("NCHAN","32")

```

### Now run it:

```
[peter@andros iocacq164]$ ACQ200_DEBUG=0 ACQ164DEVICE_VERBOSE=0 ./st.cmd 
#!../../bin/linux-x86_64/acq164
< envPaths
epicsEnvSet("IOC","iocacq164")
#epicsEnvSet("TOP","/home/pgm/PROJECTS/ACQ164.new/acq164ioc")
epicsEnvSet("TOP","/home/peter/PROJECTS/ACQ164/acq164ioc")
epicsEnvSet("UUT","acq164_080")
epicsEnvSet("SIZE","1024")
epicsEnvSet("NCHAN","32")

```


### Monitor with cs-studio

```
cd PROJECTS
git clone https://github.com/D-TACQ/ACQ400CSS

cs-studio &
create a workspace
insert the project 



## TODO

*** NB this is an alpha test product. ***
*** It would be great if we can just verify that it works on one UUT and leave it to run ***

1. Extend to triggered modes
2. Extend to multiple UUTS (in one IOC?)
3. Add any more controls that are needed.


