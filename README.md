# Using Full Flash Update (FFU) files to speed up Windows deployment
This repo contains the full FFU process that we use in US Education at Microsoft to help customers with large deployments of Windows as they prepare for the new school year. This process isn't limited to only large deployments at the start of the year, but is the most common.

This process will copy Windows in about 2-3 minutes to the target device, optionally copy drivers, provisioning packages, Autopilot, etc. School technicians have even given the USB sticks to teachers and teachers calling them their "Magic USB sticks" to quickly get student devices reimaged in the event of an issue with their Windows PC. 

While we use this in Education at Microsoft, other industries can use it as well. We esepcially see a need for something like this with partners who do re-imaging on behalf of customers. The difference in Education is that they typically have large deployments that tend to happen at the beginning of the school year and any amount of time saved is helpful. Microsoft Deployment Toolkit, Configuration Manager, and other community solutions are all great solutions, but are typically slower due to WIM deployments being file-based while FFU files are sector-based.

# Updates
2306.1.1
- Included some better error handling if defining optionalfeatures that require source folders (netfx3). ESD files don't have source folders like ISO media, which means installing .net 3.5 as an optional feature would fail. Also cleaned up some formatting. 

2306.1
- Added support to automatically download the latest Windows 10 or 11 media via the media creation tool (thanks to [Michael](https://oofhours.com/2022/09/14/want-your-own-windows-11-21h2-arm64-isos/) for the idea). This also allows for different architecture, language, and media type support. If you omit the -ISOPath, the script will download the Windows 11 x64 English (US) consumer media.

  An example command to download Windows 11 Pro x64 English (US) consumer media with Office and install drivers (it won't download drivers, you'll put those in your c:\FFUDevelopment\Drivers folder)
  
  .\BuildFFUVM.ps1 -WindowsSKU 'Pro' -Installapps $true -InstallOffice $true -InstallDrivers $true -VMSwitchName 'Name of your VM Switch in Hyper-V' -VMHostIPAddress 'Your IP Address' -CreateCaptureMedia $true -CreateDeploymentMedia $true -BuildUSBDrive $true -verbose

  An example command to download Windows 11 Pro x64 French (CA) consumer media with Office and install drivers
  
  .\BuildFFUVM.ps1 -WindowsSKU 'Pro' -Installapps $true -InstallOffice $true -InstallDrivers $true -VMSwitchName 'Name of your VM Switch in Hyper-V' -VMHostIPAddress 'Your IP Address' -CreateCaptureMedia $true -CreateDeploymentMedia $true -BuildUSBDrive $true -WindowsRelease 11 -WindowsArch 'x64' -WindowsLang 'fr-ca' -MediaType 'consumer' -verbose


- Changed default size of System/EFI partition to 260MB from 256MB to accomodate 4Kn drives. 4Kn support needs more testing. I'm not confident yet that this can be done with VMs and FFUs. 
- Added versioning with a new version parameter. Using YYMM as the format followed by a point release.



2023-05-25

Reverted a change to not require the Windows Insider ADK. This means there is no longer a need to enable test/flight signing and disable Secure Boot. **Please redownload if you downloaded prior to 2023-05-25.** If you're interested in the details, see [commit](https://github.com/rbalsleyMSFT/FFU/commit/a81261fa6b8f924f0652861a24d74bf78a156f9e)

If you did enable test/flight signing and disable Secure Boot and install the Insider ADK, do the following to revert the changes
- Remove the Insider ADK and the Insider Windows PE Add-on
- Open a command prompt as admin and run bcdedit /set testsigning off and reboot (the Test Mode water mark on the desktop should be removed. If it's not, run the command again and reboot a second time)
- During the reboot you can also go into your BIOS/UEFI and re-enable Secure Boot
- Install the released Windows 11 22H2 ADK and Windows PE Add-on

2023-05-22

Automated most of the process

2023-03-03

Added script convert-wimToFFU.ps1 which will convert any WIM file to a FFU. If you don't want to capture apps in your FFU, this is a much quicker way to get a FFU file to deploy. Check the ConvertWimToFFU.docx file for more info. 


# Getting Started
If you're not familiar with Github, you can click the Green code button above and select download zip. Extract the zip file and make sure to copy the FFUDevelopment folder to the root of your C: drive. That will make it easy to follow the guide and allow the scripts to work properly. 

If extracted correctly, your c:\FFUDevelopment folder should look like the following. If it does, go to c:\FFUDevelopment\Docs\BuildDeployFFU.docx to get started.

![image](https://github.com/rbalsleyMSFT/FFU/assets/53497092/5400a203-9c2e-42b2-b24c-ab8dfd922ba1)


