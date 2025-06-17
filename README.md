# 🎨 OpenGL 사각형 그리기

이 코드는 Windows API와 OpenGL을 직접 사용해서 컬러 사각형을 렌더링하는 예제입니다.
---

## 💻 코드 설명

### 1. 라이브러리 포함 및 윈도우 메시지 처리 함수

```cpp
#pragma comment(lib, "opengl32.lib")
#include <windows.h>
#include <gl/GL.h>

LRESULT CALLBACK WndProc(HWND h, UINT m, WPARAM w, LPARAM l) {
    if (m == WM_DESTROY) { PostQuitMessage(0); return 0; }
    return DefW
```
### 2. 윈도우 클래스 등록 및 창 생성

```cpp
int WINAPI WinMain(HINSTANCE hInst, HINSTANCE, LPSTR, int nCmd) {
    WNDCLASS wc = { CS_OWNDC, WndProc, 0, 0, hInst, 0, 0, 0, 0, L"GL" };
    RegisterClass(&wc);

    HWND hwnd = CreateWindow(L"GL", L"Rectangle",
        WS_OVERLAPPEDWINDOW, 100, 100, 800, 600,
        0, 0, hInst, 0);

    ShowWindow(hwnd, nCmd);

```

### 3. OpenGL 렌더링 컨텍스트 초기화

```cpp
HDC dc = GetDC(hwnd);

    PIXELFORMATDESCRIPTOR pfd = { sizeof(pfd), 1,
        PFD_DRAW_TO_WINDOW | PFD_SUPPORT_OPENGL | PFD_DOUBLEBUFFER,
        PFD_TYPE_RGBA };

    SetPixelFormat(dc, ChoosePixelFormat(dc, &pfd), &pfd);

    HGLRC rc = wglCreateContext(dc);
    wglMakeCurrent(dc, rc);

```

### 4. 정점 데이터 정의 (위치와 색상)

```cpp
    float v[] = {
        -0.5f, -0.5f, 1, 0, 0,  // 좌하 (빨강)
         0.5f, -0.5f, 0, 1, 0,  // 우하 (초록)
         0.5f,  0.5f, 0, 0, 1,  // 우상 (파랑)

        -0.5f, -0.5f, 1, 0, 0,  // 좌하 (빨강)
         0.5f,  0.5f, 0, 0, 1,  // 우상 (파랑)
        -0.5f,  0.5f, 1, 1, 0   // 좌상 (노랑)
    };

```

### 5. 메시지 루프와 렌더링 코드

```cpp
 MSG msg;
    while (PeekMessage(&msg, 0, 0, 0, PM_REMOVE) || 1) {
        if (msg.message == WM_QUIT) break;

        TranslateMessage(&msg);
        DispatchMessage(&msg);

        glClearColor(0.2f, 0.3f, 0.3f, 1);
        glClear(GL_COLOR_BUFFER_BIT);

        glEnableClientState(GL_VERTEX_ARRAY);
        glEnableClientState(GL_COLOR_ARRAY);

        glVertexPointer(2, GL_FLOAT, 5 * sizeof(float), v);
        glColorPointer(3, GL_FLOAT, 5 * sizeof(float), v + 2);

        glDrawArrays(GL_TRIANGLES, 0, 6);

        SwapBuffers(dc);
    }

```

### 6. 종료 처리

```cpp
 wglMakeCurrent(0, 0);
    wglDeleteContext(rc);
    ReleaseDC(hwnd, dc);
    return 0;
}
```
