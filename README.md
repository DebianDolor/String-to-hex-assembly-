# String to hex 

## I. Các thanh ghi trong kiến trúc Intel x86, x64 và ý nghĩa của chúng:  
### 1. General register

#### a, Data register:
- Có 4 thanh ghi dữ liệu 32 bit đc dùng cho số học logic và hoạt động khác theo nhiều cách:
    + Ghi dữ liệu: EAX,EBX,ECD,EDX.
    + Nửa thấp của các thanh ghi 32 bit có thể dùng như thanh ghi 16 bit: AX,BX,CX,DX 
        - AX: là bộ phận tích lũy đầu tiên, nó dùng trong nhập xuất và hầu hết các instruction số học. Trong phép nhân, toán hạng được lưu vào EAX,AX hoặc AL tùy theo kích cỡ của toán hạng đó.<br>
        - BX: Thanh ghi cơ sở dùng để đánh số địa chỉ.<br>
        - CX: Thanh ghi đếm được dùng như đếm số vòng lặp.<br>
        - DX: Thanh ghi dữ liệu.<br>
    + Nửa thấp của các thanh ghi 16 bit có thể dùng như thanh ghi 8 bit AH,AL,BH,BL,CH,CL,DH,DL.<br>
- Ở kiến trúc x64, bổ sung thêm các thanh ghi:
    + RAX, RBX, RCX, RDX, RSI, RDI, RBP, RSP
    + R8 -> R15
#### b, Pointer register:
- Intruction Pointer (IP):  Là những thanh ghi 16 bit lưu trữ address của next instruction. 
- Stack Pointer (SP):       Là những thanh ghi 16 bit cung cấp giá trị offset nằm trong ngăn xếp. 
- Base pointer (BS):        Là những thanh ghi 16 bit chủ yếu hỗ trợ trong việc tham chiếu biến tham số truyền tới chương trình con. 

#### c, Index Pointer:
- Thanh ghi 32 bit ESI và EDI (tương ứng thanh ghi 16 bit SI và DI) được dùng để đánh số địa chỉ và dùng trong phép toán cộng trừ.  
- Source Index (SI):        Được dùng đánh số của nguồn cho chuỗi operations.
- Destination Index (DI):   Ngược lại với SI.
    
### 2. Control Register  
- Nhiều lệnh liên quan đến so sánh và tính toán toán học và một số lệnh có điều kiện khác kiểm tra giá trị của các cờ trạng thái này để đưa luồng điều khiển đến vị trí khác.  
- Các cờ phổ biến: <br>
    - Overflow flag(OF):    tràn bit cao ngoài cùng bên trái của dữ liệu sau 1 tín hiệu của thuật toán.<br>
    - Direction Flag(DF):   xác định hướng trái phải cho việc di chuyển hoặc so sánh chuỗi dữ liệu. Khi DF là 0 chuỗi hoạt động lấy từ trái qua phải và ngược lại.<br>
    - Interrupt Flag(IF):   cho phép thiết lập hoạt động của bộ nhớ xử lý trong chế độ đơn bước. Chương trình debug dùng thiết lập cờ mật thám vì thế từng bước thực thi tại một điểm.<br>
    - Sign Flag(SF):        hiện tín hiệu kết quả của 1 phép toán số học. Dấu hiệu địa chỉ bởi bit cao ngoài cùng bên trái. Kết quả khẳng định thiết lập giá trị SF là 0 và ngược lại là 1.<br>
    - Carry flag(CF):       xuất hiện bit nhớ bit mượn => CF = 1.<br>
    - Zero flag (ZF):       cho thấy kết quả phép toán số học hay so sánh keeys quả bằng 0 thì ZF = 1.<br>
    - Auxilliarry Carry Flag(AF):   chứa bit 3 tới bit 4 theo phép toán số học, chỉ số cụ thể phép toán.  <br>


## II. Cấu trúc cơ bản của lệnh assembly trên kiến trúc Intel  
### 1. Các kiểu bộ nhớ : Độ lớn mã và số liệu trong một chương trình được quy định bởi chỉ dẫn MODEL  
        .MODEL memory_model  
        (memory_model: Small, Medium, Compact, Large, Huge)
### 2. Đoạn số liệu: chứa các biến, khai báo hằng,..  
        .DATA  
            word1 DW 1  
            msg DB 'This is message'  
### 3. Đoạn ngăn xếp: dành một vùn nhớ để lưu trữ cho stack  
        .STACK size  
### 4. Đoạn mã: Chứa lệnh của chương trình  
        .CODE  
        - Bên trong đoạn mã chứa lệnh thường được tổ chức thành thủ tục mà cấu trúc của thủ tục như sau:  
            name PROC  
            ; body of the procedure  
            name ENDP  
## III. Cách viết hàm và gọi tham số trong assembly trên kiến trúc Intel  
### 1. Produce  
        - Một chương trình assembly có thể xây dựng bằng các procedure.  
        - Cú pháp:  
                name PROC type  
                ; body of procedure  
                RET  
                name ENDP  
        - Trong đó:  
            + name : do người dùng định nghĩa tên thủ tục.  
            + Type : NEAR or FAR.  
            + RET : trả điều kiển cho thủ tục gọi.  

### 2. CALL và RETURN  
- Lệnh CALL được dùng để gọi một thủ tục. Có 2 cách gọi một thủ tục:  
    + Gọi trực tiếp: CALL name.  
    + Gọi gián tiếp: CALL address-expression.  
- Khi lệnh CALL đã thi hành:   
    + Địa chỉ quay về của thủ tục gọi được cấp cao stack. Địa chỉ này là offset của lệnh tiếp theo lệnh CALL.  
    + IP lấy địa chỉ offset của lệnh đầu tiên trên thủ tục được gọi, có nghĩa là điều khiển được chuyển đến thủ tục.  
- Lệnh RET sẽ lấy giá trị trong SP đưa và IP:
    + Nếu pop-value là một số N thì IP = SP + N . 
    + Trong cả 2 trường hợp thì CS:IP chứa địa chỉ trở về chương trình gọi và điều khiển được trả cho chương trình gọi.  

## IV. Lập trình giao diện trong assembly trên kiến trúc Intel  
### Các bước:  
- 1. Lấy instance handle chương trình của bạn (cần thiết).<br> 
- 2. Lấy command line (ko cần thiết trừ khi chương trình muốn tiến hành 1 command line).<br>  
- 3. Register (đăng ký) window class (cần thiết, trừ khi bạn dùng lọai window được định nghĩa trước. VD: Messagebox hay 1 dialog box).<br>   
- 4. Cài đặt window (cần thiết).<br>   
- 5. Show (hiển thị) window trên desktop ( cần thiết trừ khi bạn ko muốn show window ngay).<br>   
- 6. Refesh (làm tươi) vùng client của window.<br>   
- 7. Enter (nhập vào) 1 vòng lặp vô hạn để kiểm tra những messages (thông điệp) từ window.<br>   
- 8. Nếu message xảy ra, chúng sẽ được thực thi bởi hàm chuyên trách chịu trách nhiệm của window.<br>   
- 9. Thoát chương trình nếu người dùng close window.<br>   

***
### VD: (http://www.interq.or.jp/chubu/r6/masm32/tute/tute003.html)
.386                        ; MASM sử dụng cấu trúc 80386 để set trong chương trình.  
.model flat,stdcall         ; MASM model định địa chỉ theo flat memory  
option casemap:none         ; case sensitive

include     \masm32\include\windows.inc  
include     \masm32\include\user32.inc  
includelib  \masm32\lib\user32.lib          ; calls to functions in user32.lib and kernel32.lib  
include     \masm32\include\kernel32.inc  
includelib  \masm32\lib\kernel32.lib  

WinMain proto :DWORD,:DWORD,:DWORD,:DWORD  

.DATA   
ClassName db "SimpleWinClass",0    
AppName db "Our First Window",0  

.DATA?                              ; Uninitialized data  
hInstance HINSTANCE ?               ; Instance handle của chương trình
CommandLine LPSTR ?                 ; command line của chương trình  

.CODE   
start:  
invoke GetModuleHandle, NULL        ;gọi hàm GetModuleHandle để lấy instance handle của chương trình 
                                    ; Under Win32, hmodule==hinstance mov hInstance,eax
mov hInstance,eax  
invoke GetCommandLine   
mov CommandLine,eax  
invoke WinMain, hInstance,NULL,CommandLine, SW_SHOWDEFAULT  
                                    ; gọi hàm WinMain  
invoke ExitProcess, eax             ;thoát khỏi chương trình code trả về eax của WinMain  

WinMain proc hInst:HINSTANCE,hPrevInst:HINSTANCE,CmdLine:LPSTR,CmdShow:DWORD  
LOCAL wc:WNDCLASSEX                 ; tạo local variables trên stack  
LOCAL msg:MSG  
LOCAL hwnd:HWND  

mov wc.cbSize,SIZEOF WNDCLASSEX     ; fill values in members of wc  
mov wc.style, CS_HREDRAW or CS_VREDRAW  
mov wc.lpfnWndProc, OFFSET WndProc  
mov wc.cbClsExtra,NULL  
mov wc.cbWndExtra,NULL  
push hInstance  
pop wc.hInstance  
mov wc.hbrBackground,COLOR_WINDOW+1                 ; định nghĩa những đặc trưng quan trọng riêng biệt của một window  
mov wc.lpszMenuName,NULL
mov wc.lpszClassName,OFFSET ClassName  
invoke LoadIcon,NULL,IDI_APPLICATION  
mov wc.hIcon,eax  
mov wc.hIconSm,eax  
invoke LoadCursor,NULL,IDC_ARROW  
mov wc.hCursor,eax  
invoke RegisterClassEx, addr wc                     ; đăng ký window class  


invoke CreateWindowEx,NULL,\  
ADDR ClassName,\  
ADDR AppName,\  
WS_OVERLAPPEDWINDOW,\  
CW_USEDEFAULT,\     
CW_USEDEFAULT,\                                     ;CreateWindowEx để cài đặt window nền tảng window class đã tính toán  
CW_USEDEFAULT,\  
CW_USEDEFAULT,\  
NULL,\  
NULL,\  
hInst,\  
NULL  
mov hwnd,eax  
invoke ShowWindow, hwnd,CmdShow   
invoke UpdateWindow, hwnd   

.WHILE TRUE    
invoke GetMessage, ADDR msg,NULL,0,0  
.BREAK .IF (!eax)
invoke TranslateMessage, ADDR msg     ; Hàm GetMessage sẽ ko trả về cho đến khi có một thông điệp cho một cửa sổ trong module  
invoke DispatchMessage, ADDR msg      ;TranslateMessage là một hàm tiện ích bắt lấy input từ keyboard và sinh ra 1 thông điệp mới  
.ENDW  
mov eax,msg.wParam ; Nếu vòng lặp thông điệp kết thúc, exit code được cài nạp vào trong thành phần wParam của cấu trúc MSG  
ret  
WinMain endp  
 
WndProc proc hWnd:HWND, uMsg:UINT, wParam:WPARAM, lParam:LPARAM  
.IF uMsg==WM_DESTROY                    ; nếu user đóng window  
invoke PostQuitMessage,NULL             ; thoát  
.ELSE
invoke DefWindowProc,hWnd,uMsg,wParam,lParam   
ret  
.ENDIF   
xor eax,eax  
ret  
WndProc endp  
  
end start  

***
