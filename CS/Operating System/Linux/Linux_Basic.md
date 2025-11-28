# Linux 용어

1. File Compression % Archiving
	1. Tarball
		1. 여러 개의 파일과 폴더를 하나로 뭉쳐놓은 파일 (.tar, .tar.gz)
		2. Zip (windows) 과의 차이 : Zip 은 묶기 와 압축하기 를 한 번에 수행한다. 반면에 Tar 는 오직 묶기만 할 뿐 용량은 줄어들지 않는다.
		3. Gzip : 압축 전용 도구. 보통 Tar 로 묶은 뒤 Gzip 으로 압축하여 사용ㅎ나다.
		4. 확장자
			1. .tar : 묶기만 한 것.
			2. .tar.gz (.tgz) : 묶어서 압축한 것. 
		5. Tarball 사용 이유
			1. Linux 와 Unix 환경에서는 파일의 권한 (Permission) 과 소유자 정보를 유지하는 것이 매우 종요한데 Zip 은 이 정보가 유실 될 수 있는 만면 'tar' 는 이 메타 데이터를 완벽하게 보존한다.