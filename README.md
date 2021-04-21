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

```
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
see release for details: https://github.com/D-TACQ/ACQ164/releases/tag/v0.1.0
```
cd PROJECTS
git clone https://github.com/D-TACQ/ACQ400CSS

cs-studio &
create a workspace
insert the project 
```

### Monitor with CA:
```
[peter@andros ~]$ export EPICS_CA_ADDR_LIST=naboo
[peter@andros ~]$ export EPICS_CA_AUTO_ADDR_LIST=NO
[peter@andros ~]$ camonitor acq164_080:1:AI:CH:01
acq164_080:1:AI:CH:01          2021-04-21 23:44:19.317168 0.454905  
acq164_080:1:AI:CH:01          2021-04-21 23:44:19.352625 0.089256  
acq164_080:1:AI:CH:01          2021-04-21 23:44:19.401595 -2.77146  

```
*** 20 Hz update is possible ***

```
[peter@andros ~]$ camonitor acq164_080:1:AI:CH:01 | grep -n .
.. 
09:acq164_080:1:AI:CH:01          2021-04-21 23:44:48.963058 0.545854  
10:acq164_080:1:AI:CH:01          2021-04-21 23:44:49.016522 0.585734  
11:acq164_080:1:AI:CH:01          2021-04-21 23:44:49.051820 -0.110562  
12:acq164_080:1:AI:CH:01          2021-04-21 23:44:49.103238 -1.78488  
13:acq164_080:1:AI:CH:01          2021-04-21 23:44:49.137683 -0.265771  
14:acq164_080:1:AI:CH:01          2021-04-21 23:44:49.193303 0.0919588  
15:acq164_080:1:AI:CH:01          2021-04-21 23:44:49.227788 0.405199  
16:acq164_080:1:AI:CH:01          2021-04-21 23:44:49.283381 0.544762  
17:acq164_080:1:AI:CH:01          2021-04-21 23:44:49.318829 0.173945  
18:acq164_080:1:AI:CH:01          2021-04-21 23:44:49.373169 0.468343  
19:acq164_080:1:AI:CH:01          2021-04-21 23:44:49.408780 -0.0404742  
20:acq164_080:1:AI:CH:01          2021-04-21 23:44:49.463293 -0.320029  
21:acq164_080:1:AI:CH:01          2021-04-21 23:44:49.498774 -0.545111  
22:acq164_080:1:AI:CH:01          2021-04-21 23:44:49.553267 -0.552313  
23:acq164_080:1:AI:CH:01          2021-04-21 23:44:49.588711 -0.338581  
24:acq164_080:1:AI:CH:01          2021-04-21 23:44:49.643221 0.0079139  
25:acq164_080:1:AI:CH:01          2021-04-21 23:44:49.699567 0.230102  
26:acq164_080:1:AI:CH:01          2021-04-21 23:44:49.734415 0.301145  
27:acq164_080:1:AI:CH:01          2021-04-21 23:44:49.769820 0.54582  
28:acq164_080:1:AI:CH:01          2021-04-21 23:44:49.823233 0.585602  
29:acq164_080:1:AI:CH:01          2021-04-21 23:44:49.858571 -0.0096057  
30:acq164_080:1:AI:CH:01          2021-04-21 23:44:49.913173 -0.569243  
31:acq164_080:1:AI:CH:01          2021-04-21 23:44:49.948434 -0.212307  
32:acq164_080:1:AI:CH:01          2021-04-21 23:44:50.003234 -0.90657  
```



## TODO

*** NB this is an alpha test product. ***
*** It would be great if we can just verify that it works on one UUT and leave it to run ***

1. Extend to triggered modes
2. Extend to multiple UUTS (in one IOC?)
3. Add any more controls that are needed.


