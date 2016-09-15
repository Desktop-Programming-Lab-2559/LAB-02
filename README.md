#ใบงานที่ 2 การเพิ่ม Message Map ลงในโปรแกรม Win32 API

##วัตถุประสงค์

1. เพื่อให้รู้วิธีการเขียนโปรแกรมบน Windows ด้วยฟังก์ชัน Win32 API
1. เพื่อให้มีความคุ้นเคยกับการใช้เครื่องมือต่างๆ ในการสร้าง Application บน IDE

##ลำดับการทดลอง
1. สร้าง Project ใหม่ เป็นชนิด  Win32 Project แบบ empty project ตามใบงานที่ 1
2. พิมพ์โปรแกรมต่อไปนี้ลงไนไฟล์ .cpp ที่ Add เข้ามาใหม่ใน Project  (ไม่ต้องใส่เลขบรรทัดข้างหน้า)

```c 
1 #include <windows.h>
2
3 LONG WINAPI WndProc (HWND, UINT, WPARAM, LPARAM);
4
5 int 	WINAPI WinMain (HINSTANCE hInstance, HINSTANCE hPrevInstance,
6     	LPSTR lpszCmdLine, int nCmdShow)
7 {
8 	WNDCLASS wc;
9  	HWND hwnd;
10 	MSG msg;
11 	/***************** 1. Define Windows class ****************************/
12 	wc.style = 0; // Class style
13 	wc.lpfnWndProc = (WNDPROC) WndProc; // Window procedure address
14 	wc.cbClsExtra = 0; // Class extra bytes
15 	wc.cbWndExtra = 0; // Window extra bytes
16 	wc.hInstance = hInstance; // Instance handle
17 	wc.hIcon = LoadIcon (NULL, IDI_WINLOGO); // Icon handle
18 	wc.hCursor = LoadCursor (NULL, IDC_ARROW); // Cursor handle
19 	wc.hbrBackground = (HBRUSH) (COLOR_WINDOW + 1); // Background color
20 	wc.lpszMenuName = NULL; // Menu name
21 	wc.lpszClassName = "MyWndClass"; // WNDCLASS name
22
23 	/***************** 2. Register the Windows class **********************/
24
25 	RegisterClass (&wc);
26
27 	/***************** 3. Create window **********************/
28 	hwnd = CreateWindow (
29 		"MyWndClass", // WNDCLASS name
30 		"SDK Application", // Window title
31 		WS_OVERLAPPEDWINDOW, // Window style
32 		CW_USEDEFAULT, // Horizontal position
33 		CW_USEDEFAULT, // Vertical position
34 		CW_USEDEFAULT, // Initial width
35 		CW_USEDEFAULT, // Initial height
36 		HWND_DESKTOP, // Handle of parent window
37 		NULL, // Menu handle
38 		hInstance, // Application's instance handle
39 		NULL // Window-creation data
40 	);
41
42 	/***************** 4. Display the window **********************/
43 	ShowWindow (hwnd, nCmdShow);
44 	UpdateWindow (hwnd);
45
46 	/***************** 5. Message loop **********************/
47 	while (GetMessage (&msg, NULL, 0, 0)) {
48 		TranslateMessage (&msg);
49 		DispatchMessage (&msg);
50 	}
51 	return msg.wParam;
52 }
53
54 LRESULT CALLBACK WndProc (HWND hwnd, UINT message, WPARAM wParam,
55 	LPARAM lParam)
56 {
57 	PAINTSTRUCT ps;
58 	HDC hdc;
59
60 	switch (message) {
61
62 	case WM_PAINT:
63 		hdc = BeginPaint (hwnd, &ps);
64 		Ellipse (hdc, 10, 10, 200, 100);
65 		EndPaint (hwnd, &ps);
66 		return 0;
67
68 	case WM_DESTROY:
69 		PostQuitMessage (0);
70 		return 0;
71 	}
72 	return DefWindowProc (hwnd, message, wParam, lParam);
73 }
74
```
3. กดปุ่ม F5 เพื่อดูผลการทำงานของโปรแกรม
4. บันทึกผล

##คำถาม 
1.	นักศึกษาพบปัญหาในกานคอมไพล์โปรแกรมหรือไม่ ถ้าเจอให้บอกที่ผิดและแนวทางการแก้ไข
