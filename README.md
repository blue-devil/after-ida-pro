# After IDA Pro

This repo contains information about what to do after installing IDA Pro v8.3
and above.

**UPDATE**: The plugins I have curated here might had not updated/ported
for IDA Pro v9.0. I am going to update this repo when a plugin is ready to
get used in new ID pro v9. Stay tuned.

- [ifred](#ifred)
- [keypatch](#keypatch)
- [patching](#patching)
- [gepetto](#gepetto)
- [VulChatGPT](#vulchatgpt)
- [IDA Signsrch](#ida-signsrch)
- [IDASignsrch](#idasignsrch)
- [BinDiff](#bindiff)
- [ScyllaHide](#scyllahide)
- [Flare - Capa](#flare---capa)
- [efiXplorer](#efixplorer)
- [ComIDA](#comida)
- [ClassInformer](#classinformer)
- [OllyDumpEx](#ollydumpex---process-memory-dumper)
- [SmartJump](#smartjump)
- [IDA-names](#ida-names)
- [HexRaysPyTools](#hexrayspytools)
- [Lucid](#lucid)
- [yagi](#yagi---yet-another-ghidra-integration-for-ida)
- [HexRaysCodeXplorer](#hexrayscodexplorer)
- [IDAFuzzy](#idafuzzy)

## Python

Install [Python][03] > 3.8 (If you still did not installed). IDA Pro's
scripting engine IDAPython needs Python.

Under IDA Installation path, find `idapyswitch` or `idapyswitch.exe` and run.
`idapyswitch` is a small tool to select between different python installations.

**IMPORTANT**: If you are using Windows 11 ARM64 and Python 3 ARM64 but your
IDA Pro is still x86, `idapyswitch` will not recognize your ARM64 based Python
installation. In this case you can Install Visual Studio. Visual Studio provides
x86 based python installation, or you must install another python instance
which have to be x86 based! Python which Visual Studio provided does not have
[pip][02]. You can get pip by following the two commandlines below:

```txt
curl -sSL https://bootstrap.pypa.io/get-pip.py -o get-pip.py
python get-pip.py
```

Install the required Python packages using pip or your distribution's package
manager:

```txt
six
requests
keystone-engine
flare-capa
openai >= 0.27.0
pyelftools
sip
PyQt5
thefuzz
```

You can also use `requirements.txt` to install needed packages:

```txt
[/path/to/python] -m pip install -r requirements.txt
```

## Plugins

There are repos containing curated lists of plugins for IDA Pro. But in this
repo, I list the plugins which I use on a daily basis. Besides, I've included
tips for installing them.

### Where are plugins?

In your Operating system IDA Pro looks 2 paths for plugins:

Windows:

- User: `%APPDATA%\Hex-Rays\IDA Pro\plugins`
- System: `<IDA_PRO_INSTALL_DIR\plugins>`, generally under `Program Files`

macOS or linux:

- User: `$HOME/.idapro/plugins`
- System: `<IDA_PRO_INSTALL_DIR/plugins>`

**IMPORTANT** : For a better IDA Pro experience use **USER** path.

## [ifred][04]

ifred, provides an easy to use command palette for IDA Pro. IDA Pro has lots
of features and you can be lost on somewhere. But if you remember some keywords
you can use ifred and search for the related commands.

ifred provides two **shortcuts**:

- `Ctrl + P` : Searches strings in binary. IDA Pro generally puts `a` in front
  of string literals, by this way you can easily search strings in your binary.
- `Ctrl + Shift + P`: Provides IDA Pro's own features, all menu items or
  context menu items. With this shortcut you do not need to search where your
  menu item is!

**Bonus**: You can change theme on ifred. On ifred repo, select one theme under
`palette/res/theme/<name>/*` and copy its contents to:
`%APPDATA%/Hex-rays/IDA Pro/plugins/palette/theme/`. I have provided
Solarized (Dark and Light) themes for ifred.

### ifred installation

Unfortunately ifred repo does not contain updated releases. Instead, Jinmo -the
creator- provides [prebuilt binaries][05] in Azure pipelines.

Go to [prebuilt binaries][05] and follow the steps below to find
the compiled binaries:

- Under Jobs click **Windows Python311**
- Click **1 artifact** produced
- At the end of line which starts with `win-7.x-both`, there is a three-dot
  menu, click on the three dot menu and click **Download artifacts**

After downloading the prebuilt binaries, Just move them under plugins directory
where IDA Pro is located.

## [keypatch][09]

[keypatch][09] is a multi-architecture assembler for
IDA Pro.

IDA Pro itself provides patching bytes on disassembled binaries, but it is
cumbersome to use. People at [keystone-engine][10] created
[keypatch][09]. It is way more easy to patch a byte on
IDA Pro with this plugin.

keypatch features and provided **shortcut**:

- `Ctrl + Alt + K`: Opens patcher window. You can both patch a single byte or
  assemble new instructions.
- `Fill Range`: With this functionality you can fill a memory range with
  instructions. e.g. `nop`
- `Search`: You can search instructions with this feature. It is more easy and
  fast compare to the IDA Pro's text search.

### keypatch installation

This plugin needs [keystone-engine][10] to run.

- Go to [keypatch repo][09]
- Download `keypatch.py`. (You only need this file)
- Move `keypatch.py` to `plugins` diretory of IDA Pro

**IDA Pro v9 UPDATE** If you have upgraded to IDA Pro v9 and want to install
keypatch plugin you need to install
[keypatch updated for IDA Pro v9 plugin][45].

More on this. If you have macOS arm64 you might have a problem:

```txt
fail to load the dynamic library. 
```

As of today (16.10.204) if you `homebrew install keystone`; installs
related keystone binaries (like `kstone`) but not `libkeystone.dylib`.
You have to compile keystone from its source and put it under your library
path.

```txt
git clone https://github.com/keystone-engine/keypatch.git
mkdir build
cd build
```

Before building make those changes:

`make-common.sh`:

```txt
on line 28, before
ARCH='i386;x86_64'

after
ARCH='x86_64'
```

`make-share.sh`: I only add my python path to this file. If you use your
stock python you do not need to change someting:

```txt
before
cmake -DBUILD_LIBS_ONLY=$BUILD_LIBS_ONLY -DLLVM_BUILD_32_BITS="$LLVM_BUILD_32_BITS" -DCMAKE_OSX_ARCHITECTURES="$ARCH" -DCMAKE_BUILD_TYPE=$BUILDTYPE -DBUILD_SHARED_LIBS=ON -DLLVM_TARGETS_TO_BUILD="all" -G "Unix Makefiles" ..

after
cmake -DBUILD_LIBS_ONLY=$BUILD_LIBS_ONLY -DLLVM_BUILD_32_BITS="$LLVM_BUILD_32_BITS" -DCMAKE_OSX_ARCHITECTURES="$ARCH" -DCMAKE_BUILD_TYPE=$BUILDTYPE -DBUILD_SHARED_LIBS=ON -DLLVM_TARGETS_TO_BUILD="all" -DPYTHON_LIBRARY="/opt/homebrew/opt/python@3.12/Frameworks/Python.framework/Versions/3.12/lib/libpython3.12.dylib" -DPYTHON_EXECUTABLE="/opt/homebrew/opt/python@3.12/Frameworks/Python.framework/Versions/3.12/bin/python3.12" -G "Unix Makefiles" ..
```

After related/needed changes now we can build keystone:

```txt
../make-share.sh
```

I am not going to use `make install`. Because I already installed keystone
using homebrew. I only copied `libkeystone.0.dylib` under:

```txt
cp libkeystone.0.dylib /opt/homebrew/Cellar/keystone/0.9.2/lib/
```

Make a symbolic link

```txt
cd /opt/homebrew/lib
ln -s ../Cellar/keystone/0.9.2/lib/libkeystone.0.dylib libkeystone.dylib
```

## [patching][06]

[patching][06] is an interactive Binary Patching Plugin for
IDA Pro.

This plugin is a minor fork of [keypatch][09].
This plugin also needs [keystone-engine][10] to run. This plugin is
simplistic and supports x86_32, x86_64 and ARM.

It feels you do not need this plugin if you already have keypatch! But this
plugin's interface is way better than both IDA Pro and keypatch. Small and
simple. Plugin locates itself under context menu and under `Plugins` menu.

Key and main features are:

- `Assemble`: Opens a new window with the focus of where the cursor is. You
  can both assemble a new instruction or patch a byte.
- `Patching > Change byte`: Pops a small window where you can edit bytes
- `NOP`: It fills with NOP instruction where you've clicked.

If you have an older IDA Pro you can use [keypatch][09]
plugin.

patching **does not support** architectures other than intel and arm. So if you
are revering **MIPS**, you have to use keypatch.

### patching installation

- Go to [patching repo][06] and navigate to releases
- Download and extract contents: `patching.py` and `patching` folder.
- Move extracted file and directory under `plugins` folder

Above is the manual way to install plugin. In its own repo author provides
an automatic oneliner solution, you may want to check it!

## [gepetto][07]

IDA plugin which queries OpenAI's gpt-3.5-turbo language model to speed up
reverse-engineering.

gepetto is a plugin which was written in Python. It uses OpenAI's ChatGPT
Engine. Plugin sends decompiled output to ChatGPT and ChatGPT returns an
explanation of the decompiled output. Then plugin takes this explanation of what
the function does and adds it as a comment.

Because you did not give your money to OpenAI you will encounter this error:

```txt
Request to gpt-3.5-turbo sent...
gpt-3.5-turbo could not complete the request: You exceeded your current quota, please check your plan and billing details.
Request to gpt-3.5-turbo sent...
gpt-3.5-turbo could not complete the request: You exceeded your current quota, please check your plan and billing details.
```

### gepetto installation

OpenAI provides developers an API key. Plugin needs this API key to process
properly.

- Register an OpenAI account and retrieve your API key. This plugin needs an
  active working OpenAI API to query decompile output.
- Download repo as zip.
- Move `gepetto.py` and `gepetto` folder to IDA Pro's `plugin` directory.
- Edit `gepetto/config.ini` file and add your OpenAI API key.

## [VulChatGPT][08]

HexRays decompiler with OpenAI(ChatGPT) to find possible vulnerabilities in
binaries.

[VulChatGPT][08] inspired from gepetto and add a new
functionality to gepetto: **querying vulnerabilities**. Rest is the same.
Rename function variables and explain what the decompiled output does.

### VulChatGPT installation

Register a OpenAI account and retrieve an API. This plugin needs an active
working OpenAI API to query decompile output.

- Download [VulChatGPT.py][08]. You only need this file.
- Open VulChatGPT.py and add your OpenAI API key and save.
- Move VulChatGPT.py to IDA Pro's plugins folder.

After those steps VulChatGPT will add itself under context menu. It provides
those features:

- Find possible vulnerability in function
- Create Sample Python Exploit
- Explain function
- Rename variables

You can't query sequentially. You have to wait 20 seconds in each query. So
reverse, analyze, make some comments, reveal some structs and then use this
plugin to query!

## [IDA Signsrch][11]

IDA Pro plug-in of Luigi Auriemma's [signsrch][12] signature
matching tool.

This plugin uses a static XML file which contains most of the cryptographic
constants (and more), to find them inside binaries. So you can analyze faster.

After installation this plugin locates itself under `Edit > Plugins > Signsrch`.
Clicking that menu item pops a small window. Select options and click continue.
A new tab will pop. It contains matched signatures.

Unfortunately this tool only supports Windows!

### IDA Signsrch installation

- Download [IDA Signsrch][11]
- Extract archive and move `IDA_Signsrch.dlL`, `IDA_Signsrch64.dlL` and
  `signsrch.xml`under IDA Pro's `plugins` folder

## [IDASignsrch][13]

This is an IDAPython Plugin for searching signatures and uses xml signature
database from [IDA Signsrch][11]. Because it is written in Python
you can use it not only in Windows but also in macOS and Linux.

Plugin is place under `Edit -> Plugins -> Signsrch` after installation.

### IDASignsrch installation

- Download [signsrch.py][13] and signsrch.xml
- Place them under IDA Pro's plugin folder

## [BinDiff][16]

BinDiff is an open-source comparison tool for binary files, that assists
vulnerability researchers and engineers to quickly find differences and
similarities in disassembled code.

This is a standalone tool for binary diffing but, which also provides IDA Pro
plugins. In comparison to its rival [Diaphora][17] this tool is better.

### BinDiff Installation

- You need Java to run BinDiff.
- Download bindiff8.msi from github releases page
- Install msi package by double-clicking on it
- If BinDiff automatically finds your IDA Pro path, it will install plugin.
- If BinDiff cannot find IDA Pro path, it asks you the path.
- The `msi` package automatically installs **BinExport** plugin

BinDiff resides in `Edit > Plugins > BinDiff` and its shortcut is `Ctrl + 6`

## [ScyllaHide][15]

ScyllaHide is an advanced usermode anti-anti-debugger. Most protector and
malware's try to hook some of the Windows API's to prevent themself from
debugging. This is the ultimate plugin for preventing
**anti-debugging methods**. ScyllaHide provides plugins for IDA Pro and other
debuggers, like x64dbg.

Unfortunately official [ScyllaHide][14] repo does not provide
plugins for IDA Pro v7 and v8. So we need to use an external repo:
[ScyllaHide-IDA7.5][15]. This repo provides plugin for
IDA Pro v7.5 and above.

No need to say but this plugin is only for Windows.

### ScyllaHide Installation

Under IDA directory copy the files below to `plugins` folder:

- HookLibraryx64.dll
- HookLibraryx86.dll
- InjectorCLIx64.exe
- InjectorCLIx86.exe
- ScyllaHideIDAProPlugin.dll
- ScyllaHideIDAProPluginx64.dll
- ScyllaHideIDAServerx64.exe
- ScyllaHideIDAServerx86.exe
- scylla_hide.ini

Only `ScyllaHideIDAProPlugin.dll` and `ScyllaHideIDAProPluginx64.dll` are
plugins but they need other files to bu in the same directory!

> ## **IMPORTANT**
>
> I have created a seperate `ScyllaHide` directory under `plugins` directory
> because ScyllaPlugin sucks sometime. I only use it when I need it!

## [Flare - Capa][31]

Mandiant's Flare team has created a very nice tool which named **capa**. This
tool can analyze PE, ELF and shellcode! And it outputs a nice feasible dump
which tells what the input does: like if it needs http request, internet
connnection, might be a backdoor etc. This tools it very beneficial for malware
reversers.

capa explorer IDA Pro plugin does the same functionality while you are analyzing
your binary in IDA Pro. And capa explorer can do its job only by searching
IDA Pro's database. It can create rules if you want!

### Flare - Capa Installation

First install capa.

Windows:

```txt
python -m pip install flare-capa
```

If you have Win11 ARM64 and you have python3 arm64 build; you need a python3
x86_64 build for flare-capa.

macOS:

```txt
python -m pip install flare-capa
```

Ubuntu ARM64

```txt
pip3 install flare-capa
```

Download/clone [CAPA RULES][33]:

```txt
git clone https://github.com/mandiant/capa-rules.git
```

Capa IDA Pro Plugin asks capa rules directory on the first run!

Then copy `capa_explorer.py` from `https://github.com/mandiant/capa/blob/master/capa/ida/plugin/capa_explorer.py`
to your IDA Pro's `plugin` folder.

Then restart your IDA; plugin will reside under `Edit > Plugins > Capa Explorer`.
Click on `Capa Explorer`. If it is your first run it will ask for the capa rules
folder. Selecl capa rules folder and you are ready to go.

## [efiXplorer][18]

IDA plugin for UEFI firmware analysis and reverse engineering automation.

efiXloader is an IDA Pro loader module, responsible for processing UEFI
drivers within single IDA Pro instance. During UEFI drivers analysis
`efiXloader` identifies each driver's entry.

### efiXplorer Installation

As stated above this plugin comes with a plugin and a loader.

- Form the latest release download your platforms zip file
- Extract zip file and copy `efiXloader` to `loaders` folder and
  `efiXplorer.dll` and `efiXplorer.dll` to `plugins` folder
- Copy `guids` directory under `plugins` folder

## [ComIDA][38]

An IDA Plugin that help during the analysis of modules using COM. It works
by searching data references to known COM GUID (Classes or Interfaces), and
for hex-ray plugin user, infers type that use :

- `CoCreateInstance` function
- `CoGetCallContext` function
- `QueryInterface` method

### ComIDA Installation

Just put the [comida.py][38] script in plugins folder of IDA.

This plugin is not enabled by default. By using `Ctrl + Shift + M` key
combination you can easily enable/disable this plugin.

## [ClassInformer][23]

IDA Pro class vftable finder, namer, fixer, lister plug-in. This pluging helps
a reverser to find virtual function tables. It is an old plugin but still works
on updated IDA Pro.

### ClassInformer Installation

The original author has published pre-built binaries of plugin on [sourceforge][21]. The binaries on sourceforge works on IDA Pro v8.3. But there is an updated [repo][23]. This repo's binaried also work on IDA Pro 8.3. I have prefered the second one. Both Installations are same.

- Download the plugins and copy them under IDA Pro's `plugins` folder.

## [OllyDumpEx - Process memory dumper][25]

This is an enhanced ollydump plugin. Author made this plugin for various tools
such as ollydbg, immunity debugger ida pro... This plugin is process memory
dumper for debuggers. Native 64bit process supported (IDA Pro, WinDbg and
x64dbg) Also it auto calculate many parameters (RawSize, RawOffset,
VirtualOffset, ...)

### OllyDumpEx Installation

- Download latest [OllyDumpEx][25] plugin. Extract the archive
- Copy `OllyDumpEx_IdaRT.dll` and `OllyDumpEx_IdaRT64.dll` to your plugins
  folder

Open IDA Pro under `Edit > Plugins` you will see `OllyDumpEx` menu item. When
you click on it a new will pop up where you can make configurations of your
dump!

## [SmartJump][35]

This plugin simply enhances the IDA Pro's **JumpAsk** `g` command

With this plugin enabled, you can also jump to labels/names. You can use
brackets so you can jump referenced memory addresses.

### SmartJump Installation

- Go to [SmartJump]35 repo
- Download repo as zip.
- In repo there is a plugins folder. Inside it there is `SmartJump.py` and a folder
- Copy `SmartJump.py` and `SmartJump` folder in your IDA Pro's plugins folder

Needed config to enable plugin. Open `IDA_INSTALL_DIR\cfg\idagui.cfg`

```txt
Change this:
"JumpAsk"               =       'g'
To this:
"JumpAsk"               =       0 //'g'
```

## [IDA-names][36]

IDA-names automatically renames pseudocode windows with the current function
name. It can also rename ANY window with `SHIFT-T` hotkey.

Not so important but comes in handy when you are struggling with pseudo
windows more than three!

### Installation

- Go to [IDA-names][36] repo
- Copy `ida_names.py` in plugins folder.

## [HexRaysPyTools][34]

IDA Pro plugin which improves work with HexRays decompiler and helps in
process of reconstruction structures and classes.

The plugin assists in the creation of classes/structures and detection of
virtual tables. It also facilitates transforming decompiler output faster
and allows to do some stuff which is otherwise impossible.

### HexRaysPyTools Installation

- Go to [HexRaysPyTools][34] github repo
- Drop `HexRaysPyTools.py` and `HexRaysPyTools` folder under IDA Pro's plugins
  folder.

## [Lucid][24]

Lucid is a developer-oriented IDA Pro plugin for exploring the Hex-Rays
microcode. It was designed to provide a seamless, interactive experience
for studying microcode transformations in the decompiler pipeline.

In 2018, Hex-Rays released IDA Pro 7.1. This was the first version of IDA to
expose the decompiler’s internal microcode. This gave researchers access to
richer forms of analysis (dataflow, value/type speculation) and the ability
to augment the logic of the decompiler at a much lower level than
previously possible.

The Hex-Rays microcode is an intermediate language (IL). Like most IL’s,
its purpose is to provide a portable and architecture-agnostic platform
suitable for program analysis. By lifting compiled code to an intermediate
language, Hex-Rays can apply a generic set of rules and analysis algorithms
to decompile code from any architecture.

### Lucid Installation

I am not using original [Lucid][24] plugin. There is a way more updated fork:
[Fireboyd78 - lucid][41]. Installation is the same:

- Download the repo as zip: [Fireboyd78 - lucid][41]
- Copy the python file and the folder inside your IDA Pro's plugins folder

After installation the plugin resides:

- On the `PseudoCode` windows plugin adds itself under the context
  menu: `View microcode`
- Plugin's shortcut is `Ctrl + Shift +M`

## [yagi - Yet Another Ghidra Integration for IDA][37]

Yagi intends to include the Ghidra decompilers into both IDA pro and IDA Free.
After installation you can use `F3` to use plugin.

### yagi installation

For installation:

- Windows: double click on *.msi file
- Linux: run *.sh file to install

Additional hot keys:

| key               | interact                                      |
|-------------------|-----------------------------------------------|
| Decompile         | Place cursor on a func tion and press `F3`    |
| Edit Type         | Y                                             |
| Clear Type        | C                                             |
| Edit Name         | N                                             |
| Cross References  | X                                             |
| Navigate          | Duble click on the keyword                    |

## [HexRaysCodeXplorer][19]

This plugin is for Hex-Rays Decompiler. It provides a better code navigation
throughout the reversing process. HexRaysCodeXplorer also automates the code
reconstruction of C++ applications.

**IMPORTANT**: The official repo provides prebuilt plugins for IDA Pro v7.7
Bu I have recompiled the plugin for IDA Pro v8.4. The prebuilt plugins ca be
downloaded from
[Tuts4You Downloads - HexRaysCodeXplorer (Recompiled for IDA Pro) 2.1][42].

## HexRaysCodeXplorer Installation

- Download plugin for Ida Pro [v7.7][19], [v8.3][42] or [v8.4][42]
- Drop the DLL files under your plugins folder

To use the plugin you need to open the `HexRays Pseudocode window` then right
click it. You will see the plugins contect menu items:

```txt
Display Ctree Graph         T
Object Explorer             O
REconstruct Type            R
Extract Types to File       S
Extract Ctrees to File      C
Ctree Item View             V
Jump to Disasm              J
```

## [IDAFuzzy][43]

According to this plugins repo it is:

> IDAFuzzy is fuzzy searching tool for IDA Pro. This tool helps you to find
> command/function/struct and so on. This tool is usefull when
>
> - You don't remember all shortcut.
> -You don't remember all function/struct name exactly.
>
> This tool is inspired by Mac's Spotlight and Intellij's Search Everywhere
> dialog.

But in short it is a replacement for [ifred](#ifred) in Python! I have tested
both the command palette and the IDA Pro generated variable name; both working!

### IDAFuzzy Installation

- You need to install PyPi package [thefuzz][44]!
- Go to updated repo: [IDAFuzzy][43]
- Copy `ida_fuzz.py` under your plugin folder!

Usage: Press `Shift + Space`, a window will pop up and you are good to go!

## Processor Modules

IDA Pro does not provide all processor architectures. Under IDA Pro installation
folder there is a directory called `procs`; which is a short name of
`processors`. Under this folder there are a lot of dll files which provides
IDA Pro to understand processor types. e.g. `arm.dll` and `arm64.dll` provides
IDA Pro to understand these processor architectures.

Because I love ctfing, sometimes I need external IDA Pro processor modules:

## [eBPF_processor][39]

[eBPF_processor][39] IDA Processor and supporting scripts that can be used
to disassemble eBPF bytecode.

[eBPF][01] is a revolutionary technology with origins in the Linux kernel that
can run sandboxed programs in a privileged context such as the operating system
kernel. It is used to safely and efficiently extend the capabilities of the
kernel without requiring to change kernel source code or load kernel modules.

### eBPF_processor Installation

- Download [ebpf.py][39]
- Copy `ebpf.py` under `procs` folder
- install `pyelftools` python package with `pip`

After installation you can use the processor module like this:

- Open the eBPF ELF file in IDA, using the standard ELF loader,
  but selecting the eBPF processor
- Wait for autoanalysis to complete

## Themes

### IDA Pro Solarized Theme - Solarized Theme for IDA Pro 7.3 and above

[IDA Pro Solarized Theme][99] has been
developing by me. I really like the colors of Solarized palette. Put the theme
directory under :

Windows:

```txt
%APPDATA%\Hex-Rays\IDA Pro\themes
```

\*nix:

```txt
~/.idapro/themes
```

You can also put theme folder under installation path but it is not advised.

## Todo

- [x] Add IDA_Signsrch
- [x] Detail on ScyllaHide
- [x] Add efiXplorer

## Resources

- [eBPF -  Extended Berkeley Packet Filter][01]
- [Get PIP][02]
- [Keystone Engine - The Ultimate Assembler][10]
- [PyPi - TheFuzz - Fuzzy string matching like a boss.][44]

Plugins

- [ifred - IDA command palette & more][04]
- [patching - Interactive Binary Patching Plugin][06]
- [keypatch][09]
- [keypatch IDA Pro 9 Update][45]
- [gepetto - IDA plugin which OpenAI's gpt-3.5-turbo for reverse-engineering][07]
- [VulChatGPT - HexRays decompiler with OpenAI(ChatGPT) to find possible vulnerabilities in binaries][08]
- [IDA Signsrch][11]
- [Luigi Auriemma's signsrch][12]
- [IDA Signsrch in Python][13]
- [x64dbg - ScyllaHide][14]
- [notify-bibi - ScyllaHide-IDA7.5][15]
- [BinDiff - Diffing Tool and Plugin Repo][16]
- [BinDiff - Diffing Tool and Plugin Homepage][28]
- [Diaphora][17]
- [Flare - Capa][32]
- [Flare - Capa IDA Pro Plugin][31]
- [Flare - Capa Rules][33]
- [efiXplorer - UEFI firmware analysis][18]
- [ComIDA][38]
- [IDA ClassInformer PlugIn - Windows object RTTI vftable finder, fixer, and lister][21]
- [ClassInformer][22]
- [ClassInformer 8][23]
- [OllyDumpEx - Process memory dumper][25]
- [SmartJump - IDA Pro plugin to enhance the JumpAsk 'g' command][35]
- [ida_names - IDA-names automatically renames pseudocode windows with the current function name][36]
- [HexRaysPyTools - improves work with HexRays decompiler and helps in process of reconstruction structures and classes][34]
- [Lucid - An Interactive Hex-Rays Microcode Explorer][24]
- [Lucid - Updated Fork][41]
- [yagi - Yet Another Ghidra Integration for IDA][37]
- [IDAFuzzy - Fuzzy search tool for IDA Pro (Update)][43]

Other Plugins

- [HexRaysCodeXplorer - Automates code REconstruction of C++ applications][19]
- [lscan - library identification on statically linked/stripped binaries][20]
- [Tenet - A Trace Explorer for Reverse Engineers][26]
- [Lighthouse - A Coverage Explorer for Reverse Engineers][27]
- [Diaphora - Open Spurce Diffing Plugin][17]
- [findyara-ida - Scan binary with Yara rules][29]
- [deREferencing - Adds dereferenced pointers, colors and other useful information, similar to some GDB plugins (e.g: PEDA, GEF, pwndbg, etc).][30]

Processor Modules

- [zandi - eBPF_processor][39]
- [saaph - eBPF_processor][40]

Themes

- [IDA Pro Solarized Theme - Solarized Theme for IDA Pro 7.3 and above][99]

## Author

- Blue DeviL // SCT

## License

This project is under the AGPL v3.0 License.

[01]: https://ebpf.io/what-is-ebpf/
[02]: https://github.com/pypa/get-pip
[03]: https://www.python.org/downloads/
[10]: https://www.keystone-engine.org/
[44]: https://pypi.org/project/thefuzz/

[04]: https://github.com/Jinmo/ifred
[05]: https://jinmo123.visualstudio.com/idapkg/_build/latest?definitionId=1&branchName=master
[06]: https://github.com/gaasedelen/patching
[07]: https://github.com/JusticeRage/Gepetto
[08]: https://github.com/ke0z/VulChatGPT
[09]: https://github.com/keystone-engine/keypatch
[45]: https://github.com/keystone-engine/keypatch/blob/e87f0f90e149aa0d16851c9d919dba214f239e7c/keypatch.py
[11]: https://sourceforge.net/projects/idasignsrch/
[12]: https://aluigi.altervista.org/mytoolz.htm#signsrch
[13]: https://github.com/L4ys/IDASignsrch
[14]: https://github.com/x64dbg/ScyllaHide
[15]: https://github.com/notify-bibi/ScyllaHide-IDA7.5
[16]: https://github.com/google/bindiff
[18]: https://github.com/binarly-io/efiXplorer
[21]: https://sourceforge.net/projects/classinformer/
[22]: https://github.com/nihilus/IDA_ClassInformer
[23]: https://github.com/herosi/classinformer-ida8
[28]: https://www.zynamics.com/bindiff.html
[31]: https://github.com/mandiant/capa/tree/master/capa/ida/plugin
[32]: https://github.com/mandiant/capa
[33]: https://github.com/mandiant/capa-rules
[38]: https://github.com/airbus-cert/comida
[25]: https://low-priority.appspot.com/ollydumpex/
[35]: https://github.com/PwCUK-CTO/SmartJump
[36]: https://github.com/archercreat/ida_names
[34]: https://github.com/igogo-x86/HexRaysPyTools
[24]: https://github.com/gaasedelen/lucid
[41]: https://github.com/Fireboyd78/lucid
[19]: https://github.com/REhints/HexRaysCodeXplorer
[42]: https://forum.tuts4you.com/files/file/2420-hexrayscodexplorer-recompiled-for-ida-pro
[43]: https://github.com/HongThatCong/IDAFuzzy

[17]: https://github.com/joxeankoret/diaphora
[20]: https://github.com/maroueneboubakri/lscan
[26]: https://github.com/gaasedelen/tenet
[27]: https://github.com/gaasedelen/lighthouse
[29]: https://github.com/OALabs/findyara-ida
[30]: https://github.com/danigargu/deREferencing
[37]: https://github.com/airbus-cert/Yagi

[39]: https://github.com/zandi/eBPF_processor
[40]: https://github.com/saaph/eBPF_processor

[99]: https://github.com/blue-devil/IDA-Pro-Solarized-Theme
