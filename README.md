# 0ad: fix problems building and running from source

Writing this because I encountered those problems and there was no solution online, so this is what I've done to make everything work.

**DISCLAIMER**: it worked for me, but it might not work for you.

### Premises

- All of this regards Linux.

- I didn't want the editor to create custom maps so I added `--disable-atlas` when updating workspaces, as the [official instructions](https://trac.wildfiregames.com/wiki/BuildInstructions#Dependencies) suggest.

- (again) This worked for me, but it might not work for you.

### Segfault starting 0ad

Similar from https://trac.wildfiregames.com/ticket/5576

```
$ ./binaries/system/pyrogenesis
TIMER| InitVfs: 972.649 us
Writing the mainlog at /home/jmercier/.config/0ad/logs/mainlog.html
TIMER| CONFIG_Init: 189.966 us
Sound: AlcInit success, using OpenAL Soft
ERROR: CVFSFile: file audio/sound_group.rng couldn't be opened (vfs_load: -110100)
ERROR: CXeromyces: failed adding validator for 'audio/sound_group.rng'
ERROR: CSoundManager: failed to load grammar file 'audio/sound_group.rng'
WARNING: Window icon not found.
ERROR: CVFSFile: file hwdetect/hwdetect.js couldn't be opened (vfs_load: -110100)
ERROR: Failed to load hardware detection script
TIMER| RunHardwareDetection: 3.77789 ms
TIMER| write_sys_info: 29.2919 ms
ERROR: CVFSFile: file gui/gui_page.rng couldn't be opened (vfs_load: -110100)
ERROR: CXeromyces: failed adding validator for 'gui/gui_page.rng'
ERROR: CGUIManager: failed to load GUI page grammar file 'gui/gui_page.rng'
ERROR: CVFSFile: file gui/gui.rng couldn't be opened (vfs_load: -110100)
ERROR: CXeromyces: failed adding validator for 'gui/gui.rng'
ERROR: CGUIManager: failed to load GUI XML grammar file 'gui/gui.rng'
ERROR: CVFSFile: file shaders/program.rng couldn't be opened (vfs_load: -110100)
ERROR: CXeromyces: failed adding validator for 'shaders/program.rng'
ERROR: CShaderManager: failed to load grammar shaders/program.rng
TIMER| InitRenderer: 6.8483 ms
ERROR: Failed to open font file fonts/mono-10.fnt
ERROR: Failed to open font file fonts/sans-10.fnt
TIMER| ps_console: 63.577 us
TIMER| ps_lang_hotkeys: 631.214 us
ERROR: CCacheLoader failed to find archived or source file for: "gui/page_pregame.xml"
ERROR: Failed to open font file fonts/mono-stroke-10.fnt
ERROR: CCacheLoader failed to find archived or source file for: "shaders/effects/gui_text.xml"
ERROR: Failed to load effect 'gui_text'
Segmentation fault (core dumped)
```

[Here](https://trac.wildfiregames.com/ticket/5576) the complete log error with useful info, but the solution that worked for me is this one.

The problem is that the game tries to load from `.local/share/0ad/`. If you miss something, it gives this error.

The solution that I adopted is the following: 
- download (if you haven't already done it) data folder [from the official site](https://play0ad.com/download/source/), 
- **make sure that the source version and the data version are the same**, unpack it and unzip `public.zip` and `mod.zip`, which are located in some subfolder
- move everything into `.local/share/0ad/`, making sure not to delete something you want to keep. I found that folder empty, so I didn't have problems.

Here is the script that worked for me for unzipping the 2 zip files.
```console
cd binaries/data/mods/public/
unzip -v public.zip
cd cd ../../mods/mod/
unzip -v mod.zip
```

Then try running again the game and it should start.

## Solve "WARNING: Unknown render path"

The game started, the main menu works fine but when I started the game, I saw an almost completely blank screen with some strange and creepy graphical effects.

There was this warning: `Unknow render path`. 

This is much more trickier error and I didn't found absolutely anything on the internet. 

#### My solution

Copy configuration file `default.cfg` from the `config/` folder to the $XDG_CONFIG_HOME, which by default is `~/.config`. If this is different for you, please change it in the script.
``` console
cp data/config/default.cfg $XDG_CONFIG_HOME/0ad/config/local.cfg
```

Then search for render path in the newly created `local.cfg` file and change the following line

```renderpath = default```

to 

```renderpath = shader```

then restart the game and this should work fine. If not, check [this feed](https://wildfiregames.com/forum/topic/16734-important-please-read-if-you-have-an-older-computer-or-graphics-card/) on the supported graphics (maybe your graphics card is too old).