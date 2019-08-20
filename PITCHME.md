---?image=assets/images/gitpitch-audience.jpg
@title[Platform Build Lab Ovmf]
<br><br>
<br><br><br>
## <span class="gold"   >UEFI & EDK II Training</span>

<span style="font-size:0.95em" > Platform Build Lab - OVMF Linux  </span>


<br>
<span style="font-size:0.75em" ><a href='http://www.tianocore.org'>tianocore.org</a></span>
Note:
  PITCHME.md for UEFI / EDK II Training  Platform_Build_Linux_Ovmf_Lab

  Copyright (c) 2019, Intel Corporation. All rights reserved.<BR>

  Redistribution and use in source (original document form) and 'compiled'
  forms (converted to PDF, epub, HTML and other formats) with or without
  modification, are permitted provided that the following conditions are met:

  1) Redistributions of source code (original document form) must retain the
     above copyright notice, this list of conditions and the following
     disclaimer as the first lines of this file unmodified.

  2) Redistributions in compiled form (transformed to other DTDs, converted to
     PDF, epub, HTML and other formats) must reproduce the above copyright
     notice, this list of conditions and the following disclaimer in the
     documentation and/or other materials provided with the distribution.

  THIS DOCUMENTATION IS PROVIDED BY TIANOCORE PROJECT "AS IS" AND ANY EXPRESS OR
  IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF
  MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO
  EVENT SHALL TIANOCORE PROJECT  BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL,
  SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO,
  PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS;
  OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY,
  WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR
  OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS DOCUMENTATION, EVEN IF
  ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.



---  
@title[Lesson Objective]
<BR>
### <p align="center"<span class="gold"   >Platform Build Labs </span></p>

<br>
<!---  Add bullets using https://fontawesome.com/cheatsheet certificate
-->
 @fa[certificate gp-bullet-green]<span style="font-size:0.9em">&nbsp;&nbsp;Build a EDK II Platform using OVMF package </span><br><br>
 @fa[certificate gp-bullet-yellow]<span style="font-size:0.9em">&nbsp;&nbsp;Run Ovmf using Qemu </span><br><br>
 
@snap[midpoint span-5 ]
![trainingLogo](/assets/images/Qemu-logo.png)
@snapend
 



---?image=assets/images/binary-strings-black2.jpg
@title[Lab 1 -Build OVMF Section]
<br><br><br><br><br>
## <span class="gold"  >&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Build OvmfPkg</span>
<span style="font-size:0.9em" > &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Setup OvmfPkg to build and run w/ QEMU </span>


---
@title[Ubuntu 16.04 Pre-requisites]
<p align="right"><span class="gold" >@size[1.1em](<b>Pre-requisites Ubuntu 16.04   </b>)</span><span style="font-size:0.75em;" >  </span></p>
<p style="line-height:75%" align="left" ><span style="font-size:0.75em;" >
Instructions from:<a href="https://github.com/tianocore/tianocore.github.io/wiki/Using-EDK-II-with-Native-GCC#Ubuntu_1604_LTS__Ubuntu_1610 "> tianocore wiki Ubuntu_1610</a> <br>
&bull; &nbsp; Example Ubuntu 16.04<br>
&bull; &nbsp; The following need to be accessible for building Edk II Platforms, From the terminal prompt (Cnt-Alt-T) :
</span></p>
```bash
bash$ sudo apt-get install build-essential uuid-dev iasl git gcc-5 nasm python3-distutils
```
```Assembly
; build-essential - Informational list of build-essential packages
; uuid-dev - Universally Unique ID library (headers and static libraries)
; iasl - Intel ASL compiler/decompiler (also provided by acpica-tools)
; git - support for git revision control system
; gcc-5 - GNU C compiler (v5.4.0 as of Ubuntu 16.04 LTS)
; nasm - General-purpose x86 assembler 
; python3-distutils - dist-utils module from the Python standard library
```
```bash
bash$ sudo apt-get install qemu
```
```Assembly
; Qemu – Emulation with Intel architecture with UEFI Shell 
```

@snap[south-east span-10 ]
![Logo](/assets/images/ubuntu-logo.png)
@snapend

Note:

---
@title[Pre-requisites Clear Linux Project OS ]
<p align="right"><span class="gold" >@size[1.1em](<b>Pre-requisites Clear Linux* Project   </b>)</span><span style="font-size:0.75em;" >  </span></p>
<p style="line-height:75%" align="left" ><span style="font-size:0.75em;" >
&bull; &nbsp; Example Using Clear Linux&ast; Project <br>
&bull; &nbsp; The following need to be accessible for building Edk2, From the terminal prompt (Cnt-Alt-T):
</span></p>
```bash
bash$  sudo swupd bundle-add devpkg-util-linux
```
```Assembly
; Devpkg-util-linux – includes bundles for developer tools for 
; writing  “C” Applications included:
;    gcc, nasm, uuid, etc.
```

```bash
bash$  sudo swupd bundle-add kvm-host
```
```Assembly
; kvm includes Qemu – Emulation with Intel architecture with UEFI Shell 
```


@snap[south-east span-20 ]
![Logo](/assets/images/ClearLinux-logo.png)
<br>
<br>
@snapend




Note:





---?image=/assets/images/slides/Slide6.JPG
@title[Create QEMU run script]
<p align="right"><span class="gold" >@size[1.1em](<b>Create QEMU Run Script </b>)</span><span style="font-size:0.75em;" >  </span></p>
<span style="font-size:0.75em" >1. Create a run-ovmf directory under the home directory</span>
```bash
bash$ cd ~
bash$ mkdir ~run-ovmf
bash$ cd run-ovmf
```
<span style="font-size:0.75em" >2. Create a directory to use as a hard disk image </span>
```bash
bash$ mkdir hda-contents
```
<p style="line-height:34%" align="left" ><span style="font-size:0.75em" >3. Create a shell script to run the 
QEMU,<font face="Consolas">@size[.7em](&nbsp;gedit RunQemu.sh &nbsp;) </font></span></p>

@snap[north-west span-100 ]
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<p style="line-height:25%" align="left" ><span style="font-size:0.35em; font-family:Consolas;">
<font color="black"><br><br><br>&num; The following needs to be on a single line<br>
&nbsp;<b>qemu-system-x86_64 -pflash bios.bin -hda fat:rw:hda-contents -net none     -debugcon file:debug.log <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-global isa-debugcon.iobase=0x402 </b><br>
</font>
</span></p>
@snapend


@snap[south-west span-100 ]
<p style="line-height:50%" align="left" ><span style="font-size:0.75em;" >4.  <b>Save</b> and <b>exit</b>
</span></p>
@snapend


Note:
Create a run-ovmf directory under the home directory

`bash$ cd ~`<br>
`bash$ mkdir ~run-ovmf`<br>
`bash$ cd run-ovmf`

- Create a directory to use as a hard disk image

'bash$ mkdir hda-contents'

- Create a Linux shell script to run the QEMU from the run-ovmf directory

`bash$ gedit RunQemu.sh`
<br>
`qemu-system-x86_64 -pflash bios.bin -hda fat:rw:hda-contents -net none     -debugcon file:debug.log -global isa-debugcon.iobase=0x402 `
<br>
Save and Exit



---
@title[Optional - Downloading the Edk II Source]
<p align="right"><span class="gold" >@size[1.1em](<b>Download the Edk II Source&nbsp;&nbsp;<i>- Optional</i></b>)</span></p>


<span style="font-size:0.9em" ><i>OPTIONAL</i> - Open a  “git” command prompt and create a source working directory</span>
```bash
  bash$ mkdir ~/src
  bash$ cd ~/src
  bash$ mkdir edk2-ws
```

<span style="font-size:0.8em" >OPTIONAL - Internet Proxies – (company Firewall used for example)</span>

```bash
 bash$ git config --global https.proxy <proxyname>.domain.com:<port>
 bash$ git config --global http.proxy <proxyname>.domain.com:<port>
```

<span style="font-size:0.8em" >OPTIONAL - Download edk2 source tree using Git command prompt</span>

```bash
  bash$ git clone --recursive  https://github.com/tianocore/edk2.git
  bash$ git clone https://github.com/tianocore/edk2-libc.git
```

<span style="font-size:0.8em" >OPTIONAL - Build the tools</span>
```bash
  bash$  make -C edk2/BaseTools
```

<span style="font-size:0.7em" ><b>@color[yellow](NOTE:)</b> Lab Material will have a different “edk2” </span>

Note:


- OPTIONAL - Open a terminal prompt and create a source working directory


`bash$ mkdir ~/src` <br>
`bash$ cd ~/src `


- OPTIONAL - Internet Proxies – (company Firewall used for example)


`bash$ export http_proxy=http://proxy-us.company.com:911` <br>
`bash$ export ftp_proxy=$http_proxy`


- OPTIONAL - Download edk2 source tree using Git

bash$ git clone --recursive https://github.com/tianocore/edk2
bash$ git clone https://github.com/tianocore/edk2-libc.git

- OPTIONAL - Build the tools
bash$ make -C edk2/BaseTools 


- NOTE: Lab Material will have a different “edk2”


---?image=assets/images/binary-strings-black2.jpg
@title[Setup Lab Material sub Section]
<br><br><br><br><br>
## <span class="gold"  >&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Setup Lab Material </span>
<span style="font-size:0.9em" > &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Lab_Material_FW.zip</span>

---
@title[Download Lab_Material_FW -getting the Source ]
### <p align="right"><span class="gold" >Download Lab Material</span><br></span></p>
<span style="font-size:0.9em" >Download the Lab_Material_FW.zip from : </span> @fa[github gp-bullet-white] <span style="font-size:0.7em"><a href="https://github.com/tianocore-training/Lab_Material_FW/archive/master.zip">github.com Lab_Matrial_FW.zip</a></span><br>
<br>
<span style="font-size:0.9em" >OR<br>Use `git clone` to download the Lab_Material_FW<span>
```bash
bash$ cd $HOME
bash$ git clone https://github.com/tianocore-training/Lab_Material_FW.git
```
<span style="font-size:0.9em" >Directory Lab_Material_FW will be created</span>
```bash
   FW 
    - Documentation 
	- DriverWizard 
	- edk2-ws      
	- edk2Linux 
	- LabSampleCode 
```


---?image=/assets/images/slides/Slide10.JPG
@title[Build Ovmf Edk2 -getting the Source ]
<p align="right"><span class="gold" >@size[1.1em](<b>Build EDK II Ovmf  </b>)</span><br>
<span style="font-size:0.75em;" >– Extract the Source  </span></p>


@snap[north-west span-100 ]
<br>
<br>
<p style="line-height:70%" align="left"><span style="font-size:0.750em;  " >1. Extract the Downloaded <font face="Consolas">Lab_Material_FW-master.zip</font> to Home @size[.7em](<br>&lpar; this will create a directory FW&rpar;) </span></p>
<br>
@snapend


Note:
Extract the Downloaded Lab_Material_FW.zip to Home (this will create a directory FW )

---?image=/assets/images/slides/Slide11.JPG
@title[Build Ovmf Edk2 -getting the Source 02]
<p align="right"><span class="gold" >@size[1.1em](<b>Build EDK II Ovmf  </b>)</span><br>
<span style="font-size:0.75em;" >– Copy the Source  </span></p>

@snap[north-west span-100 ]
<br>
<p style="line-height:70%" align="left" ><span style="font-size:0.7em;" >2. Open a terminal prompt  (Alt-Cnt-T) <br><br> 3. 
Create a working space source directory under the home directory "FW" <br>
<span style="background-color: #000000"><font face="Consolas">@size[.7em](&nbsp;&nbsp; C:&bsol;&gt; mkdir ~src &nbsp;&nbsp;&nbsp;&nbsp;) </font></span>
<br><br>4. From the downloaded Lab_Material_FW folder, <b>copy</b> and <b>paste</b> folder <font face="Consolas">". . ./FW/edk2-ws" to "~src"</font>
</span></p>

@snapend


Note:

- Open a terminal prompt  (Alt-Cnt-T)

- Create a working  space source directory under the home directory
   
bash$ mkdir ~src

- From the FW folder, copy and paste folder “~FW/edk2” to ~src

---?image=/assets/images/slides/Slide12.JPG
@title[Build Ovmf Edk2 -getting the Source 03]
<p align="right"><span class="gold" >@size[1.1em](<b>Build EDK II Ovmf  </b>)</span><br>
<span style="font-size:0.75em;" >– Getting <font face="Consolas">BaseTools</font>  </span></p>

@snap[north-west span-100 ]
<br>
<br>
<p style="line-height:60%" align="left" ><span style="font-size:0.75em;" >
&nbsp; 5. Rename or `mv` the direcotry <font face="Consolas">"~src/edk2/BaseTools"<br><br><font face="Consolas"><span style="background-color: #000000; "> 
@size[.7em](&nbsp;&nbsp;bash$ cd ~src/edk2-ws/edk2&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<br>)
@size[.7em](&nbsp;&nbsp;bash$ mv BaseTools BaseToolsX &nbsp;&nbsp;&nbsp;<br>)
@size[.7em](&nbsp;&nbsp;bash$ tar -xf BaseTools.tar.xz  &nbsp;&nbsp;)
</span></font>
<br><br>
&nbsp; 6. Extract <font face="Consolas">@size[.7em](~src/edk2-ws/edk2/BaseTools.tar.xz  to  ~src/edk2-ws/edk2)</font>
</span></p>
<br>
@snapend


Note:

- Rename or mv the directory “~src/edk2/BaseTools” 
   bash$ cd ~src/edk2
   bash$ mv BaseTools BaseToolsX
- Extract the file ~FW/edk2Linux/BaseTools.tar.gz  to  ~src/edk2

---?image=/assets/images/slides/Slide13.JPG
@title[Build Ovmf Edk2 - Building BaseTools]
<p align="right"><span class="gold" >@size[1.1em](<b>Build EDK II Ovmf  </b>)</span><br>
<span style="font-size:0.75em;" >– Building <font face="Consolas">BaseTools</font>  </span></p>


@snap[north-west span-80 ]
<br>
<br>
<p style="line-height:65%" align="left" ><span style="font-size:0.7em;" >
<br><br></span></p>
@box[bg-black text-white my-box-pad2  ](<p style="line-height:60% "><span style="font-size:0.9em;" ><br><br><br>&nbsp;</span></p>)
<br>
@snapend

@snap[north-west span-100 ]
<br>
<br>
<br>
<p style="line-height:65%" align="left" ><span style="font-size:0.7em;" >
&nbsp; 7.Export workspace and platform path(see script file setenv.sh )<br><br>
<font face="Consolas">
@size[.7em](&nbsp;&nbsp;bash$ cd ~src/edk2-ws&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<br>)
@size[.7em](&nbsp;&nbsp;bash$ export WORKSPACE=$PWD &nbsp;&nbsp; <br>)
@size[.7em](&nbsp;&nbsp;bash$ export PACKAGES_PATH=$WORKSPACE/edk2:$WORKSPACE/edk2-libc &nbsp;&nbsp; ) </font><br>
</span></p>
<br>
@snapend


@snap[north-west span-40 ]
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<p style="line-height:60%" align="left" ><span style="font-size:0.7em;" >
&nbsp; 8. Run Make
<br>
<br>
<span style="background-color: #000000"><font face="Consolas">
@size[.7em](&nbsp;&nbsp;bash$ cd edk2&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<br>)
@size[.7em](&nbsp;&nbsp;bash$ make -C BaseTools/ &nbsp;&nbsp; ) </font></span> <br>
<br>
<br>
@size[.8em](Make sure the test pass OK)
</span></p>
<br>
@snapend


Note:
- Run Make from the Terminal prompt
Export work space & platform path
bash$ cd ~src/edk2-ws
bash$ export WORKSPACE=$PWD
bash$ export PACKAGES_PATH=$WORKSPACE/edk2:$WORKSPACE/edk2-libc

bash$ cd edk2
bash$ make –C BaseTools
- Run edksetup (note This will need to be done for every new Terminal prompt)

bash$ . edksetup.sh


---?image=assets/images/binary-strings-black2.jpg
@title[Build Ovmf sub Section]
<br><br><br><br><br>
## <span class="gold"  >&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Build OVMF  Platform</span>
<span style="font-size:0.9em" > &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Lab_Material_FW.zip</span>



---?image=/assets/images/slides/Slide15.JPG
@title[Build Ovmf Edk2 -update target.txt]
<p align="right"><span class="gold" >@size[1.1em](<b>Build EDK II Ovmf  </b>)</span><br>
<span style="font-size:0.75em;" >– Update <font face="Consolas">Target.txt</font>  </span></p>

@snap[north-west span-70 ]
<br>
<p style="line-height:60%" align="left" ><span style="font-size:0.7em;" >
@size[1.1em](<b>What is OVMF?</b>) <br><br>
Open Virtual Machine Firmware - Build with edk2
</span></p>
<p style="line-height:45%" align="left" ><span style="font-size:0.5em; font-family:Consolas;">
<span style="background-color: #000000">
&nbsp;&nbsp;bash$ cd ~/src/edk-ws/edk2 &nbsp;&nbsp;&nbsp;&nbsp;    <br>
&nbsp;&nbsp;bash$ . edksetup.sh &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;   <br>
</span>
</span></p>

<p style="line-height:50%" align="left" ><span style="font-size:0.7em;" ><br><br>
<b>Edit</b> the file <font face="Consolas">Conf/target.txt</font> @size[.7em](&lpar;change TOOL_CHAIN_TAG&rpar;<br>)
<font face="Consolas">@size[.7em](&nbsp; gedit Conf/target.txt )</font>
</span></p>

@snapend

@snap[north-west span-60 ]
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<p style="line-height:30%" align="left" ><span style="font-size:0.35em; font-family:Consolas;">
<font color="black"><b><br><br>
&nbsp;&nbsp;ACTIVE_PLATFORM&nbsp;       = OvmfPkg/OvmfPkgX64.dsc <br>
&nbsp;&nbsp; &nbsp;&nbsp;. &nbsp;&nbsp;. &nbsp;&nbsp;. <br>
&nbsp;&nbsp;TARGET_ARCH&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;           = X64 <br>
&nbsp;&nbsp; &nbsp;&nbsp;. &nbsp;&nbsp;. &nbsp;&nbsp;. <br>
&nbsp;&nbsp;TOOL_CHAIN_TAG &nbsp;       = GCC5 <br>
</b></font>
</span></p>
@snapend


@snap[south-west span-100 ]
<p style="line-height:50%" align="left" ><span style="font-size:0.75em;" >
@size[.8em](<b>Save</b> and <b>build</b>)<br>
</span></p>
<p style="line-height:50%" align="left" ><span style="font-size:0.5em; font-family:Consolas;">
<span style="background-color: #000000">
&nbsp;&nbsp;bash$ build –D ADD_SHELL_STRING &nbsp;&nbsp;   <br>
</span>
</span></p>
@snapend


@snap[south span-20 fragment ]
<p style="line-height:10%" align="left"><span style="font-size:02.80em;  " ><br><br>
@color[yellow](&#8678;)
</span></p>
@snapend

@snap[south-east span-100 ]
<p align="right"><span style="font-size:0.56em" >More info: <a href=" https://github.com/tianocore/tianocore.github.io/wiki/OVMF "> tianocore - wiki/OVMF </a>
</span></p>
@snapend


Note:

- What is OVMF?
  - Open Virtual Machine Firmware - Build with edk2
  - More info: https://github.com/tianocore/tianocore.github.io/wiki/OVMF 

- Edit the file Conf/target.txt
```bash
bash$ gedit Conf/target.txt
```
   
1. ACTIVE_PLATFORM       = OvmfPkg/OvmfPkgX64.dsc
2. TARGET_ARCH           = X64
3. TOOL_CHAIN_TAG        = GCC5


- Save and Exit
```bash
bash$ cd ~src/edk2
bash$ build
```


---?image=/assets/images/slides/Slide16.JPG
@title[Build Ovmf Edk2 -build inside Terminal]
<p align="right"><span class="gold" >@size[1.1em](<b>Build EDK II Ovmf   </b>)</span><br>
<span style="font-size:0.75em;" > – Inside Terminal</span></p>


@snap[south-east span-40  ]
<p style="line-height:50%" align="right"><span style="font-size:0.8em" >
Finished build
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
</span></p>
@snapend


Note:
- Inside Terminal

---?image=/assets/images/slides/Slide17.JPG
@title[Build Ovmf Edk2 -Verify]
<p align="right"><span class="gold" >@size[1.1em](<b>Build EDK II Ovmf  </b>)</span><br>
<span style="font-size:0.75em;" >– Verify Build Succeeded  </span></p>


@snap[north-west span-100 ]
<br>
<br>
<p style="line-height:80%" align="left"><span style="font-size:0.80em;  " >
 OVMF.fd should be in the Build directory<br>
 &nbsp;&nbsp;&nbsp;- For GCC5 with X64, it should be located at:<br>
 <font face="Consolas"><span style="background-color: #000000; font-size:0.650em;"> 
&nbsp;&nbsp; ~/src/edk2-ws/Build/OvmfX64/DEBUG_GCC5/FV/OVMF.fd &nbsp;&nbsp;</span></font>
</span></p>
<br>
@snapend


Note:

---?image=/assets/images/slides/Slide18.JPG
@title[Build Ovmf Edk2 -invoke QEMU]
### <p align="right"><span class="gold" >Invoke QEMU</span><br></span></p>
<p style="line-height:60%" align="left" ><span style="font-size:0.75em" >Change to run-ovmf directory under the home directory<br>
 <font face="Consolas"><span style="background-color: #000000; font-size:0.650em;"> 
&nbsp;&nbsp;cd $HOME/run-ovmf &nbsp;&nbsp;</span></font>
</span></p>


@snap[north-west span-60 ]
<br>
<br>
<br>
<br>
<p style="line-height:60%" align="left" ><span style="font-size:0.7em;" ><br><br>&nbsp;
</span></p>
@box[bg-black text-white  my-box-pad2  ](<p style="line-height:60% "><span style="font-size:0.9em;" ><br>&nbsp;</span></p>)
<br>
<br>
@box[bg-black text-white  my-box-pad2  ](<p style="line-height:45%" align="left" ><span style="font-size:0.5em; font-family:Consolas;">&nbsp;&nbsp;bash$ . RunQemu.sh &nbsp;&nbsp;   <br>&nbsp;</span></p>)
@snapend




@snap[north-west span-60 ]
<br>
<br>
<br>
<br>
<br>
<p style="line-height:60%" align="left" ><span style="font-size:0.7em;" >
Copy the OVMF.fd BIOS image to the run-ovmf directory naming it bios.bin
</span></p>
<p style="line-height:45%" align="left" ><span style="font-size:0.5em; font-family:Consolas;">
&nbsp;&nbsp;bash$ cp ~/src/edk2-ws/Build/OvmfX64/DEBUG_GCC5/FV/OVMF.fd bios.bin &nbsp;&nbsp;   <br>
</span>
</p>

<p style="line-height:50%" align="left" ><span style="font-size:0.7em;" ><br>
Run the RunQemu.sh Linux shell script
</span></p>
<br>
@snapend

 


Note:

---  
@title[Summary]
##### <p align="center"<span class="gold"   >Summary </span></p><br>
<br>
 @fa[certificate gp-bullet-green]<span style="font-size:0.9em">&nbsp;&nbsp;Build a EDK II Platform using OVMF package </span><br><br>
 @fa[certificate gp-bullet-yellow]<span style="font-size:0.9em">&nbsp;&nbsp;Run Ovmf using Qemu </span><br><br>
 
@snap[midpoint span-5 ]
![QemuLogo](/assets/images/Qemu-logo.png)
@snapend
 

---?image=assets/images/gitpitch-audience.jpg
@title[Questions]
<br>
![Questions](/assets/images/questions.JPG =10x) 

---
@title[return to main]
<p align="center"><span class="gold"   >@size[1.2em](<b>Return to Main Training Page</b>)</span></p>
<br>
<br>
<br>
<br>
<br>
<p align="center"><span style="font-size:0.9em">Return to Training Table of contents for next presentation <a href="https://github.com/tianocore-training/Tianocore_Training_Contents/wiki#schedule--outline">link</a></span></p>

@snap[north span-30 ]
<br>
<br>
<br>
<a href="https://github.com/tianocore-training/Tianocore_Training_Contents/wiki#schedule--outline">
![trainingLogo](/assets/images/returnTrainingLogo.png)</a>
@snapend


---?image=assets/images/gitpitch-audience.jpg
@title[Logo Slide]
<br><br><br>
![Logo Slide](/assets/images/TianocoreLogo.png =10x)


---
@title[Acknowledgements]
#### <p align="center"><span class="gold"   >Acknowledgements</span></p>

```c++
/**
Redistribution and use in source (original document form) and 'compiled' forms (converted
to PDF, epub, HTML and other formats) with or without modification, are permitted provided
that the following conditions are met:

Redistributions of source code (original document form) must retain the above copyright 
notice, this list of conditions and the following disclaimer as the first lines of this 
file unmodified.

Redistributions in compiled form (transformed to other DTDs, converted to PDF, epub, HTML
and other formats) must reproduce the above copyright notice, this list of conditions and 
the following disclaimer in the documentation and/or other materials provided with the 
distribution.

THIS DOCUMENTATION IS PROVIDED BY TIANOCORE PROJECT "AS IS" AND ANY EXPRESS OR IMPLIED 
WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND 
FITNESS FOR A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL TIANOCORE PROJECT BE 
LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES 
(INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, 
DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, 
WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) 
ARISING IN ANY WAY OUT OF THE USE OF THIS DOCUMENTATION, EVEN IF ADVISED OF THE POSSIBILITY 
OF SUCH DAMAGE.

Copyright (c) 2019, Intel Corporation. All rights reserved.
**/

```
