
- Mixed Reset
커밋을 취소하고 커밋 내용을 Changed 파일 형태로 되돌리는 방법

```git
solie@SJE_DESK MINGW64 /c/Unreal_Project/1st-Team1-Final-Project (Feature/Charater)
$ git status
On branch Feature/Charater
Your branch is up to date with 'origin/Feature/Charater'.

nothing to commit, working tree clean

solie@SJE_DESK MINGW64 /c/Unreal_Project/1st-Team1-Final-Project (Feature/Charater)
$ git log --oneline -n5
0da10223 (HEAD -> Feature/Charater, origin/Feature/Charater) [ ✨ update ] Merci Aim Animation
438b3f0a (origin/Feature/Guardian) Merge branch 'Feature/Guardian' into Dev
c8830fb1 Merge branch 'Feature/DungeonEditor' into Dev
97010cf9 (origin/Feature/DungeonEditor) [ ✨ update ] 에셋 수정 및 InGameTestLevel 추가
7012bc68 [ ✨ update ] Build.cs 수정

solie@SJE_DESK MINGW64 /c/Unreal_Project/1st-Team1-Final-Project (Feature/Charater)
$ git branch backup-before-reset

solie@SJE_DESK MINGW64 /c/Unreal_Project/1st-Team1-Final-Project (Feature/Charater)
$ git reset --mixed HEAD~1
Unstaged changes after reset:
M       Config/DefaultEngine.ini
M       Content/Material/A_Surface_Footstep/Niagara_FX/Textures/ParticleFlamesSheet.uasset
M       Content/Material/A_Surface_Footstep/Niagara_FX/Textures/T_FireSheet.uasset
M       Content/Material/A_Surface_Footstep/Niagara_FX/Textures/T_FireSheet2.uasset
M       Content/Material/A_Surface_Footstep/Niagara_FX/Textures/T_FireSheet3.uasset
M       Content/Material/A_Surface_Footstep/Niagara_FX/Textures/T_FireSheet4.uasset
M       Content/Player/Seeker/Merci/Blueprint/ABP_Merci.uasset
M       Content/Player/Seeker/Merci/Blueprint/BP_Merci.uasset
M       Content/Player/Seeker/Merci/Mesh/Body/IK_SKM_Merci_Body_FullSet.uasset
M       Content/Player/Seeker/Merci/Mesh/Body/SKM_Merci_Body_FullSet.uasset
M       Content/Player/Seeker/Merci/Mesh/Body/SK_Merci_Body_FullSet.uasset
M       Content/Player/TEST/TestMap.umap
M       Content/UI/DungeonEditor/Texture/TrapIcon/ArrowTrapIcon.uasset
M       Content/UI/DungeonEditor/Texture/TrapIcon/BearTrapIcon.uasset
M       Content/UI/DungeonEditor/Texture/TrapIcon/CeilingSpikeTrapIcon.uasset
M       Content/UI/DungeonEditor/Texture/TrapIcon/FlameTrapIcon.uasset
M       Content/UI/DungeonEditor/Texture/TrapIcon/LandmineTrapIcon.uasset
M       Content/UI/DungeonEditor/Texture/TrapIcon/LavaTrap2mIcon.uasset
M       Content/UI/DungeonEditor/Texture/TrapIcon/LavaTrap3mIcon.uasset
M       Content/UI/DungeonEditor/Texture/TrapIcon/RisingSpikeTrapIcon.uasset
M       Content/UI/DungeonEditor/Texture/TrapIcon/SawTrapIcon.uasset
M       Content/UI/DungeonEditor/Texture/TrapIcon/SwingingBladeTrapIcon.uasset
M       Content/UI/DungeonEditor/Texture/TrapIcon/WallSpikeTrapIcon.uasset
M       Source/GAS/Private/Character/Player/Seeker/GS_Seeker.cpp
M       Source/GAS/Public/Character/Player/Seeker/GS_Seeker.h
```
- [이후 backup-before-reset 의 pull 처리](Push_&_Pull.md#사용자의_HEAD로_원격_브랜치_덮어쓰기)