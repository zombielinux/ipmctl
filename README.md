# THIS PROJECT IS ARCHIVED   
Intel will not provide or guarantee development of or support for this project, including but not limited to, maintenance, bug fixes, new releases or updates.  
Patches to this project are no longer accepted by Intel.  
 If you have an ongoing need to use this project, are interested in independently developing it, or would like to maintain patches for the community, please create your own fork of the project.  

# This is my attempt at making this utility buildable again for use on Debian Trixie. 
ipmctl was removed from the Debian repos due to [CVE-2024-36275](https://security-tracker.debian.org/tracker/CVE-2024-36275) coupled with Intel no longer maintaining the project. 
Since there are a number of 2nd Gen Xeon Scalable (Skylake/Cascade Lake) servers in service, and given an issue with Supermicro X11DPU motherboards being unable to delete NVDIMM goals, this utility remains the only way to accomplish that. 

# ipmctl

ipmctl is a utility for configuring and managing Intel&#174; Optane&#8482; Persistent Memory modules (PMem).

<a href="https://repology.org/project/ipmctl/versions">
    <img src="https://repology.org/badge/vertical-allrepos/ipmctl.svg" alt="Packaging status" align="right">
</a>

It supports functionality to:
* Discover PMems on the platform.
* Provision the platform memory configuration.
* View and update the firmware on PMems.
* Configure data-at-rest security on PMems.
* Track health and performance of PMems.
* Debug and troubleshoot PMems.

ipmctl refers to the following interface components:

* libipmctl: An Application Programming Interface (API) library for managing PMems.
* ipmctl: A Command Line Interface (CLI) application for configuring and managing PMems from the command line.

Also, metrics exporter for [Prometheus](https://prometheus.io/docs/introduction/overview/) based on libipmctl was provided. For more details take a look [here](https://github.com/intel/ipmctl-exporter)

## Workarounds

### Slow Firmware Updates
When using 02.00.00.x versions of ipmctl software to update or downgrade firmware on Intel® Optane™ PMem 100 Series modules, please use the “-lpmb” CLI option (use DDRT Large Payload transfer). Otherwise the operation may take significantly longer than it normally would.

### Commands Fail on Older Platforms
Some platforms that targeted the Gen 100 modules do not generate a ACPI PMTT table which causes ipmctl (version v02.00.00.xxxx) commands to fail. Particularly
* create -goal 
* show -topology
* show -memoryresources
* show -dimm

If these commands are ran with -v option they present a message about failing to get the PMTT table.

A corrected version is being developed and will hopefully be available soon. Until that is available the best option is to use ipmctl v01.00.00.xxxx or go through the BIOS menus.

## Releases

01.00.00.xxxx  (master_1_0 branch) is for Intel Optane Persistent Memory 100 Series

02.00.00.xxxx  (master_2_0 branch) is for Intel Optane Persistent Memory 200 Series (and is backwards compatible with 100 series) 

03.00.00.xxxx  (master_3_0 branch) is the latest version and supports all series of Intel Optane Persistent Memory.

**Note**: Branches may differ fundamentally. Please pay close attention to README.md of the respective branch.

## Packages

Some distributions include ipmctl allowing installation via their package manager.
For example (on Fedora):
> dnf install ipmctl
This will update the required dependencies.

For systems that cannot reach the Internet use another system to download the following rpms required to install ipmctl and then copy them to the original system (e.g. via thumb drive).

> ipmctl.rpm, libipmctl.rpm, libsafec.rpm (only needed for 1.x releases), libndctl, json-c.rpm
Run

> rpm –ivh *.rpm

CentOS and RHEL systems maybe able to use an EPEL package found at: https://src.fedoraproject.org/rpms/ipmctl

OpenSUSE and SLES packages can be found at: https://build.opensuse.org/package/show/hardware:nvdimm/ipmctl

Ubuntu releases can be found at: https://launchpad.net/ubuntu/+source/ipmctl

### libndctl


ipmctl depends on libndctl (ndctl-libs).

It can be found here https://github.com/pmem/ndctl if not available as a package.


## Build

**Note**: Each branch may require different building procedures. Please follow README.md of the respective branch.

### building latest (03.00.00.xxxx) on Linux
1. clone the ipmctl and edk2 repositories:

`git clone -b master https://github.com/intel/ipmctl.git`

`git clone https://github.com/tianocore/edk2.git`

`cd ipmctl`

2. Run updateedk.sh, this will copy relevant folders from edk2 into ipmctl

`./updateedk.sh`

3. Patch Files
`./patch_OS.sh`  
   
4. Run cmake 
`cmake -DCMAKE_INSTALL_DATAROOTDIR=share -DCMAKE_VERSION=2.8.12 ./`

5. Run make
`make -j all` 

6. Execute
`./output/debug/ipmctl`
