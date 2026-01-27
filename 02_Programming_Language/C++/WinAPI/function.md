# GetConsoleCursorInfo

```c++
BOOL GetConsoleCursorInfo( 
	HANDLE hConsoleOutput, 
	PCONSOLE_CURSOR_INFO lpConsoleCursorInfo 
);
```
- hConsoleOutput : 콘솔 출력 핸들
- lpConsoleCursorInfo : 커서 정보를 저장할 `CONSOLE_CURSOR_INFO` 구조체의 포인터
- 성공 시 `TRUE`, 실패 시 `FALSE`를 반환하며, `GetLastError`로 오류 정보를 확인

# SetConsoleCursorInfo

```c++
BOOL SetConsoleCursorInfo( 
	HANDLE hConsoleOutput, 
	const CONSOLE_CURSOR_INFO* lpConsoleCursorInfo 
);
```
- hConsoleOutput : 콘솔 출력 핸들
- lpConsoleCursorInfo : 변경할 커서 정보를 포함한 `CONSOLE_CURSOR_INFO` 구조체의 포인터
- 성공 시 **`TRUE`**, 실패 시 **`FALSE`**를 반환하며, `GetLastError`로 오류 정보를 확인

# GetStdHandle

```c++
HANDLE GetStdHandle(DWORD nStdHandle);
```
- nStdHandle :가져올 표준 핸들을 지정
	- `STD_INPUT_HANDLE` (`-10`): 표준 입력 핸들.
	- `STD_OUTPUT_HANDLE` (`-11`): 표준 출력 핸들.
	- `STD_ERROR_HANDLE` (`-12`): 표준 에러 핸들.
- 반환
	- 성공 시, 요청된 표준 핸들 (`HANDLE`) 반환.
	- 실패 시, `INVALID_HANDLE_VALUE` 반환. 오류 정보는 `GetLastError()`를 사용해 확인.
# SetConsoleScreenBufferSize

```c++
BOOL SetConsoleScreenBufferSize( 
	HANDLE hConsoleOutput,
	COORD dwSize
);
```
- hConsoleOutput : 콘솔 출력 핸들. `GetStdHandle(STD_OUTPUT_HANDLE)` 로 가져온다.
- dwSize : 화면 버퍼의 크기를 나타내는 `COORD` 구조체
- 반환
	- 성공시 true, 실패 시 false 반환. 오류 정보는 `GetLastError()` 를 통해 확인
- 버퍼 크기는 현재 콘솔 창 크기보다 작을 수 없다.
# SetConsoleWindowInfo

```c++
BOOL SetConsoleWindowInfo( 
	HANDLE hConsoleOutput, 
	BOOL bAbsolute, 
	const SMALL_RECT* lpConsoleWindow 
);
```
- hConsoleOutput : 콘솔 출력 핸들
- bAbsolute : 창 크기를 설정할 때 사용하는 좌표가 절대값인지 여부를 지정
- lpConsoleWindow : 콘솔 창의 크기와 위치를 지정하는 `SMALL_RECT` 구조체 포인터.
- 반환
	- 성공시 `TRUE`, 실패 시 `FALSE` 반환. 오류 정보는 GetLastError() 를 사용해 확인.

# GetWindowLong

```c++
LONG GetWindowLong( 
	HWND hWnd, // 창 핸들 
	int nIndex // 가져올 정보의 인덱스 
);
```
- hWnd : 정보를 가져올 창의 핸들
- nIndex : 가져올 정보의 종류를 지정.
# SetWindowLong

```c++
LONG SetWindowLong( 
	HWND hWnd, // 창 핸들 
	int nIndex, // 설정할 정보의 인덱스 
	LONG dwNewLong // 새로운 값 
);
```
- hWnd : 정보를 가져올 창의 핸들
- nIndex : 가져올 정보의 종류를 지정.
- dwNewLong : `nIndex`에 지정된 속성의 새 값을 설정