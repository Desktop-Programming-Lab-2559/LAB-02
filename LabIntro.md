##การขึ้นทะเบียนแอพพลิเคชัน
ก่อนที่ระบบปฏิบัติการจะสร้างหน้าต่างให้กับแอพพลิเคชันของเรานั้น มันจะต้องรู้ว่าเราต้องการให้หน้าต่างของเรามีรูปร่างหน้าตาเป็นอย่างไร ดังนั้นเราจะต้องทำการขึ้นทะเบียนแอพพลิเคชั่นกับระบบปฏิบัติการก่อน เพื่อที่จะให้ระบบปฏิบัติการสามารถสร้างหน้าต่างให้เราได้อย่างถูกต้อง เราสามารถกำหนดได้เองทั้งหมด ไม่ว่าจะเป็นตำแหน่งขนาดลักษณะ cursor ลักษณะ icon ฯลฯ

ในการขึ้นทะเบียนแอพพลิเคชันนั้น เราต้องระบุลักษณะที่ต้องการเข้าไปในพารามิเตอร์ต่างๆ ของ struct ที่ชื่อว่า WNDCLASS จากนั้นจึงส่งค่าที่อยู่ใน  struct นี้ไปขึ้นทะเบียนกับระบบปฏิบัติการโดยใช้ฟังก์ชัน RegisterClass()

```C
5 int   WINAPI WinMain (HINSTANCE hInstance, HINSTANCE hPrevInstance,
6       LPSTR lpszCmdLine, int nCmdShow)
7 {
8   WNDCLASS wc;
9   HWND hwnd;
10  MSG msg;
11  /***************** 1. Define Windows class ****************************/
12  wc.style = 0; // Class style
13  wc.lpfnWndProc = (WNDPROC) WndProc; // Window procedure address
14  wc.cbClsExtra = 0; // Class extra bytes
15  wc.cbWndExtra = 0; // Window extra bytes
16  wc.hInstance = hInstance; // Instance handle
17  wc.hIcon = LoadIcon (NULL, IDI_WINLOGO); // Icon handle
18  wc.hCursor = LoadCursor (NULL, IDC_ARROW); // Cursor handle
19  wc.hbrBackground = (HBRUSH) (COLOR_WINDOW + 1); // Background color
20  wc.lpszMenuName = NULL; // Menu name
21  wc.lpszClassName = "MyWndClass"; // WNDCLASS name
22
23  /***************** 2. Register the Windows class **********************/
24
25  RegisterClass (&wc);
26
```
 
ในโปรแกรมบรรทัดที่ 8  เราจะสร้างตัวแปร ```wc``` จาก struct ที่ชื่อ WNDCLASS ซึ่งมีรายละเอียดดังต่อไปนี้ 
```c
typedef struct tagWNDCLASS {
  UINT      style;
  WNDPROC   lpfnWndProc;
  int       cbClsExtra;
  int       cbWndExtra;
  HINSTANCE hInstance;
  HICON     hIcon;
  HCURSOR   hCursor;
  HBRUSH    hbrBackground;
  LPCTSTR   lpszMenuName;
  LPCTSTR   lpszClassName;
} WNDCLASS, *PWNDCLASS;
```

ดูรายละเอียดเพิ่มเติมได้จาก [WNDCLASS structure](https://msdn.microsoft.com/en-us/library/windows/desktop/ms633576(v=vs.85).aspx)
 
#การสร้าง windows
 เมื่อมีการขึ้นทะเบียนแอพพลิเคชั่นกับระบบปฏิบัติการเรียบร้อยแล้ว เราก็สามารถสร้างหน้าต่างโปรแกรม โดยการใช้รายละเอียดตาม class ที่ขึ้นทะเบียนไว้ได้ทันที โดยใช้ฟังก์ชัน CreateWindows() โดยมีรายละเอียดการเรียกใช้ดังนี้
```c
27  /***************** 3. Create window **********************/
28  hwnd = CreateWindow (
29      "MyWndClass", // WNDCLASS name
30      "SDK Application", // Window title
31      WS_OVERLAPPEDWINDOW, // Window style
32      CW_USEDEFAULT, // Horizontal position
33      CW_USEDEFAULT, // Vertical position
34      CW_USEDEFAULT, // Initial width
35      CW_USEDEFAULT, // Initial height
36      HWND_DESKTOP, // Handle of parent window
37      NULL, // Menu handle
38      hInstance, // Application's instance handle
39      NULL // Window-creation data
40  );
41
42  /***************** 4. Display the window **********************/
43  ShowWindow (hwnd, nCmdShow);
44  UpdateWindow (hwnd);
```
# การสร้าง message loop
การขึ้นทะเบียนและการสร้างหน้าต่างสำหรับแอพพลิเคชั่นนั้นเป็นแค่เพียงกระบวนการขั้นต้นของการเขียนโปรแกรมบน windows ขั้นตอนที่สำคัญในการรันของแอพพลิเคชั่นก็คือการสร้างloop ในการรับส่งข่าวสารระหว่างชิ้นส่วนต่างๆ ในแอพพลิเคชั่น

โดยปกติ application ของเราจะประกอบด้วยชิ้นส่วนต่างๆ จำนวนมากมาย ดังที่เราพบเห็นได้ใน application ทั่วๆ ไป ไม่ว่าจะเป็นปุ่มกด กล่องข้อความโต้ตอบ เป็นต้น ซึ่งเมื่อชิ้นส่วนเหล่านั้นถูกกระตุ้นโดยผู้ใช้ มันก็ส่งข่าวสารออกไปยังระบบปฏิบัติการ ระบบปฏิบัติการก็จะแจกจ่ายข่าวสารเหล่านั้นไปยังแอพพลิเคชั่นต่างๆ

หากข่าวสารเหล่านั้นเป็นข่าวสารที่แอพพลิเคชั่นของเราสนใจ เราก็ต้องตอบสนองต่อข่าวสารเหล่านั้น หากไม่สนใจ ก็จะส่งกลับคืนไปให้กับระบบปฏิบัติการ 
ในระบบปฏิบัติการจะมีแอพพลิเคชั่นจำนวนเยอะมาก จึงมีข่าวสารจำนวนเยอะมากตามไปด้วย ดังนั้นเมื่อเรารับข่าวสารมาแล้ว ก็ต้องวิเคราะห์ดูว่ามันเป็นข่าวสารที่เราสนใจหรือเปล่า ทั้งนี้ทำได้โดยการเรียกฟังก์ชัน GetMessage() เพื่อดึงข่าวสารจากคิวข่าวสาร (message queue) ของระบบ  และเรียกฟังก์ชัน TranslateMessage() เพื่อแปลความหมายของข่าวสารเหล่านั้น 
```c
46  /***************** 5. Message loop **********************/
47  while (GetMessage (&msg, NULL, 0, 0)) {
48      TranslateMessage (&msg);
49      DispatchMessage (&msg);
50  }
51  return msg.wParam;
52 }
53
```

#ฟังก์ชันประจำ application
เมื่อเราสร้างหน้าต่าง และ message loop แล้ว แอพพลิเคชันของเราสามารถตอบสนองต่อการใช้งานของผู้ใช้ เมื่อผู้ใช้ทำงานกับชิ้นส่วนต่างๆ ชิ้นส่วนเหล่านั้นจะส่งข่าวสารไปยังคิวข่าวสารของระบบปฏิบัติการ จากนั้นระบบปฏิบัติจะสื่อสารกับแอพพลิเคชันของเราผ่าน callback function ซึ่งเราไปขึ้นทะเบียนไว้ 

ภายใน callback function เราจะต้องทำการเลือกฟังก์ชั่นที่เหมาะสมมารองรับข่าวสารที่ระบบปฏิบัติการส่งมาให้ ถ้าข่าวสารนั้น ไม่ตรงกับที่เราต้องการ ก็หมายความว่าอาจจะเป็นข่าวสารสำหรับแอพพลิเคชั่นตัวอื่น เราก็จะต้องส่งข่าวสารนั้นกลับคืนไปให้ระบบปฏิบัติการ 
```C
54 LRESULT CALLBACK WndProc (HWND hwnd, UINT message, WPARAM wParam,
55  LPARAM lParam)
56 {
57  PAINTSTRUCT ps;
58  HDC hdc;
59
60  switch (message) {
61
62  case WM_PAINT:
63      hdc = BeginPaint (hwnd, &ps);
64      Ellipse (hdc, 10, 10, 200, 100);
65      EndPaint (hwnd, &ps);
66      return 0;
67
68  case WM_DESTROY:
69      PostQuitMessage (0);
70      return 0;
71  }
72  return DefWindowProc (hwnd, message, wParam, lParam);
73 }
```
