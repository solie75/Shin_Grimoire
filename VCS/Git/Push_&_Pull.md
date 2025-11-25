### 푸시 해야할 커밋 목록
```bash
git log @{u}..HEAD --oneline --graph --decorate
```

### 풀 받아야 할 커밋 목록
```bash
git log HEAD..@{u} --oneline --graph --decorate
```

### 사용자의_HEAD로_원격_브랜치_덮어쓰기
```bash
solie@SJE_DESK MINGW64 /c/Unreal_Project/1st-Team1-Final-Project (Feature/Charater)
$ git push --force-with-lease origin Feature/Charater

Total 0 (delta 0), reused 0 (delta 0), pack-reused 0 (from 0)
To https://github.com/NbcampUnreal/1st-Team1-Final-Project.git
 + 0da10223...438b3f0a Feature/Charater -> Feature/Charater (forced update)
```

