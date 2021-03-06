## [DTC] DownloadTableConfig

With DTC you can easily create download config and give some files prefixes, which highlight that file on config loading (and also can contain some arguments set by server master). You no more need to create bunch of convars or KeyValue config for settings of your files (like scale of the texture, team for the texture etc.). [Download](https://raw.githubusercontent.com/KissLick/DownloadTableConfig/master/addons/sourcemod/scripting/includes/dtc.inc)

## File format

- Each line contains one file at max
  - e.g. `materials/decals/custom/redstar.vtf` 
- Blank and commented lines are skiped
- Each file can have prefix `[PrefixName PrefixArg1 PrefixArg2 PrefixArg3 ...] FilePath`
  - e.g. `[Mark Red 12.0 0.125] materials/decals/custom/redstar.vmt`
  - Prefix are realised via RegEx `/^\[(?:[^\\\]]|\\.)*\]/gm`
- Arguments cannot contain `]` (must be escaped to `\]`)
- String arguments with whitespace must be quoted e.g. `[PrefixName 1.0 "whitespace test"]`
- Lines are trimed before processing, so you can indent lines with whitespaces or tabs

**File example:**
```INI
// comment line
# comment line
; comment line

[GamePrepare 3] 	sound/timeleft/en/unreal/3sec.mp3
[GamePrepare 2] 	sound/timeleft/en/unreal/2sec.mp3
[GamePrepare 1] 	sound/timeleft/en/unreal/1sec.mp3

[GameEnd RedTeam]   sound/sm_hosties/noscopestart1.mp3
[GameEnd BlueTeam] 	sound/sm_hosties/noscopestart1.mp3

[PlayerSkin Red] models/player/prisoner/prisoner_red_fix.mdl
	models/player/prisoner/prisoner_red_fix.dx90.vtx
	models/player/prisoner/prisoner_red_fix.phy
	models/player/prisoner/prisoner_red_fix.vvd

[PlayerSkin Blue] models/player/prisoner/prisoner_blue_fix.mdl
	models/player/prisoner/prisoner_blue_fix.dx90.vtx
	models/player/prisoner/prisoner_blue_fix.phy
	models/player/prisoner/prisoner_blue_fix.vvd

[Mark Red 12.0 0.125] materials/decals/custom/redstar.vmt
	materials/decals/custom/redstar.vtf

[Mark Blue 12.0 0.125] materials/decals/custom/bluestar.vmt
	materials/decals/custom/bluestar.vtf
```

## How to use
1. Include DTC
  ```SourcePawn
#include <dtc>
  ```

2. Create config (optional)
  ```SourcePawn
 public OnPluginStart()
 {
  	  DTC_CreateConfig(sConfigPath, OnCreateConfig);
 }

 public OnCreateConfig(String:sConfigPath[], Handle:hConfigFile)
 {
  	  WriteFileLine(hConfigFile, "// [Mark <team> <scale> <offset>]");
  	  WriteFileLine(hConfigFile, "");
  	  WriteFileLine(hConfigFile, "[Mark Red 0.125 12.0] materials/sprites/laserbeam.vmt");
 }
  ```

3. Load config
  ```Sourcepawn
 public OnPluginStart()
 {
  	  DTC_LoadConfig(sConfigPath, OnFile);
 }
 
 public OnFile(String:sFile[], String:sPrefixName[DTC_MAX_NAME_LEN], Handle:hArgs)
 {
  	  // Catch file with prefix "Mark"
  	  if (StrEqual(sPrefixName, "Mark")) {
	
  	    	  new String:sHelper[64];
  	    	  DTC_GetArg(hArgs, 1, sHelper, sizeof(sHelper), "ERROR");
	
  	    	  LogMessage("File = '%s'", sFile);
  	    	  LogMessage("Arg1 = '%s'", sHelper);
  	    	  LogMessage("Arg2 = '%.3f'", DTC_GetArgFloat(hArgs, 2, 0.0));
  	    	  LogMessage("Arg3 = '%.3f'", DTC_GetArgFloat(hArgs, 3, -1.0));
  	  }
 }
  ```
Many features are shown in [test plugin](https://github.com/KissLick/DownloadTableConfig/blob/master/addons/sourcemod/scripting/DTC_Test.sp) and [here](https://github.com/KissLick/DownloadTableConfig/blob/master/addons/sourcemod/logs/test_log.cfg) is the plugin output.
