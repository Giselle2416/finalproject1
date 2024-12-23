module finalproject(  
output reg \[7:0\] ledr, ledg,ledb,  
output reg \[1:0\] comg,coms,  
output reg \[2:0\] com,  
output reg \[6:0\] seg,  
output EN,  
input clk,restart,left,right,  
input reg \[1:0\]color);

 wire clk\_div, clk\_sec, clk\_mv;  
   wire A2, B2, C2, D2, E2, F2, G2;  
   wire A3, B3, C3, D3, E3, F3, G3;  
   wire A0, B0, C0, D0, E0, F0, G0;  
   wire A1, B1, C1, D1, E1, F1, G1;  
 byte line,count,timeout;  
 reg \[3:0\] a, b, c;  
 reg \[3:0\] speed;  
 reg \[7:0\] people \[7:0\];  
 reg \[7:0\] plate \[7:0\];  
 reg \[7:0\] re \[7:0\];  
 reg \[3:0\] grade1,grade2,sec1,sec2;  
 reg \[2:0\] random01, random02, random03, r1, r2, r3;  
 reg \[1:0\] count1;  
 reg \[6:0\] seg1,seg2,seg3,seg4;  
   
 divfreq div0(clk, clk\_div);  
 divfreq1 div1(clk, clk\_sec);  
 divfreq2 div2(clk,speed, clk\_mv);  
 divfreq3 div3(clk, clk\_pmv);  
   
 segment7 SEG0(grade1, A2,B2,C2,D2,E2,F2,G2);  
 segment7 SEG1(grade2, A3,B3,C3,D3,E3,F3,G3);  
 segment7 SEG2(sec1, A0,B0,C0,D0,E0,F0,G0);  
 segment7 SEG3(sec2, A1,B1,C1,D1,E1,F1,G1);  
   
 result Chang1(grade1,grade2,re);  
   
//初始化  
 initial  
  begin  
   timeout=0;                     
   speed=0;  
   line=3;               //人物初始位置  
   random01 \= (5\*random01 \+ 3)%16;  
   r1 \= random01 % 8;  
   random02 \= (5\*(random02+1) \+ 3)%16;  
   r2 \= random02 % 8;  
   random03= (5\*(random03+2) \+ 3)%16;  
   r3 \= random03 % 8;  
   a \= 0;  
   b \= 0;  
   c \= 0;  
   ledr=8'b11111111;  
   ledg=8'b11111111;  
ledb=8'b11111111;  
   grade1=0;  
   grade2=0;  
   sec1=4'b0110;   
   sec2=4'b0000;  
   count1=2'b00;  
re\[0\] \= 8'b11111111;          //分數  
    re\[1\] \= 8'b11111111;  
    re\[2\] \= 8'b11111111;  
    re\[3\] \= 8'b11111111;  
    re\[4\] \= 8'b11111111;  
    re\[5\] \= 8'b11111111;  
    re\[6\] \= 8'b11111111;  
    re\[7\] \= 8'b11111111;  
    plate\[0\] \= 8'b11111111;   //掉落物  
    plate\[1\] \= 8'b11111111;  
    plate\[2\] \= 8'b11111111;  
    plate\[3\] \= 8'b11111111;  
    plate\[4\] \= 8'b11111111;  
    plate\[5\] \= 8'b11111111;  
    plate\[6\] \= 8'b11111111;  
    plate\[7\] \= 8'b11111111;  
    people\[0\] \= 8'b11111111;     //人物  
    people\[1\] \= 8'b11111111;  
    people\[2\] \= 8'b11111111;  
    people\[3\] \= 8'b11111111;  
    people\[4\] \= 8'b11111111;  
    people\[5\] \= 8'b11111111;  
    people\[6\] \= 8'b11111111;  
    people\[7\] \= 8'b11111111;  
    people\[line\]\[7\] \= 1'b0;  
  end  
    
//7段顯示器的視覺暫留  
always@(posedge clk\_div)  
 begin  
   
  seg1\[0\] \= A0;    //由sec1(十位）轉成7段  
  seg1\[1\] \= B0;  
  seg1\[2\] \= C0;  
  seg1\[3\] \= D0;  
  seg1\[4\] \= E0;  
  seg1\[5\] \= F0;  
  seg1\[6\] \= G0;  
    
  seg2\[0\] \= A1;  
  seg2\[1\] \= B1;   //由sec2（個位）轉成7段  
  seg2\[2\] \= C1;  
  seg2\[3\] \= D1;  
  seg2\[4\] \= E1;  
  seg2\[5\] \= F1;  
  seg2\[6\] \= G1;  
    
  seg3\[0\] \= A2;   ////由grade1（十位）轉成7段  
  seg3\[1\] \= B2;  
  seg3\[2\] \= C2;  
  seg3\[3\] \= D2;  
  seg3\[4\] \= E2;  
  seg3\[5\] \= F2;  
  seg3\[6\] \= G2;  
    
  seg4\[0\] \= A3;   ////由grade2（個位）轉成7段  
  seg4\[1\] \= B3;  
  seg4\[2\] \= C3;  
  seg4\[3\] \= D3;  
  seg4\[4\] \= E3;  
  seg4\[5\] \= F3;  
  seg4\[6\] \= G3;  
 //每次只顯示ㄧ個顯示器的內容，通過快速輪流更新來實現顯示器同時顯示的效果  
  if(count1 \== 2'b00)     
   begin  
    seg \<= seg2;  
    coms \<= 2'b01;     
    comg \<= 2'b11;  
    count1=count1+1'b1;  
   end  
  else if(count1 \== 2'b01)  
   begin  
    seg \<= seg1;  
    coms \<= 2'b10;  
    comg \<= 2'b11;  
    count1=count1+1'b1;  
   end  
  else if(count1 \== 2'b10)  
   begin  
    seg \<= seg3;  
    coms \<= 2'b11;  
    comg \<= 2'b10;  
    count1=count1+1'b1;  
   end  
  else if(count1 \== 2'b11)  
   begin  
    seg \<= seg4;  
    coms \<= 2'b11;  
    comg \<= 2'b01;  
    count1 \<= 2'b00;  
   end  
 end  
//計時  
always@(posedge clk\_sec)  
 begin  
  if(restart==1)      //初始化  
   begin  
    speed=0;  
    timeout=0;  
    sec1=4'b0110;      //9  
    sec2=4'b0000;      //0  
   end  
  else if(timeout\!=1)  
   begin     //掉計時  
    if (sec1 \== 4'b0000 && sec2 \== 4'b0000)    //當秒數十位與個位數皆等於0  
     begin  
      timeout \= 1; // 計時結束  
     end   
    else if (sec2 \== 4'b0000)  
     begin  
      sec1 \<= sec1 \- 1'b1;    //十位數減1  
      sec2 \<= 4'b1001;         //9  
      speed \<= speed \+ 1; // 每十秒加速  
     end  
    else  
     begin  
      sec2 \<= sec2 \- 1'b1;  //個位數減1  
     end  
   end  
else   
begin  
sec1=4'b0000;    //timeout \== 1，超時  
sec2=4'b0000;  
end  
 end  
//8x8led 視覺暫留  
always@(posedge clk\_div)  
 begin  
  if(count \>= 7\)    //column，達到7則回到最左側  
   count \<= 0;  
  else  
   count \<= count \+ 1;    //column,沒到7之前，一直往右移  
   com \= count;  
   EN \= 1'b1;  
if(timeout==1)  
begin  
ledr\<=re\[count\];           //分數  
ledb \<=8'b11111111;  
ledg \<=8'b11111111;  
end  
  if(timeout==0)  
   begin  
//掉落物與人物顏色改變  
if(color==2'b00)  
begin  
    ledg \<= plate\[count\];          
    ledr \<= people\[count\];  
ledb \<=8'b11111111;  
end  
else if(color==2'b01)  
begin  
    ledb \<= plate\[count\];  
    ledg \<= people\[count\];  
ledr \<=8'b11111111;  
end  
else if(color==2'b10)  
begin  
    ledr \<= plate\[count\];  
    ledb \<= people\[count\];  
ledg \<=8'b11111111;  
end  
else if(color==2'b11)  
begin  
    ledg \<= plate\[count\];  
    ledb \<= people\[count\];  
ledr \<=8'b11111111;  
end  
   end  
 end

   
   
//人物移動  
always@(posedge clk\_pmv)  
 begin  
   if(restart==1 || timeout==1)  
begin  
line \= 3;          //起始點  
people\[0\] \= 8'b11111111;  
    people\[1\] \= 8'b11111111;  
    people\[2\] \= 8'b11111111;  
    people\[3\] \= 8'b11111111;  
    people\[4\] \= 8'b11111111;  
    people\[5\] \= 8'b11111111;  
    people\[6\] \= 8'b11111111;  
    people\[7\] \= 8'b11111111;  
end  
if(timeout==0)  
begin  
   if((right \== 1\) && (line \!= 7))     //7是邊界，如果line==7還能向右移動會出界  
     begin  
      people\[line\]\[7\] \= 1'b1;    //暗  
      line \= line \+ 1;    //右移  
     end  
   if((left \== 1\) && (line \!= 0))   //0是邊界，如果line==0還能向左移動會出界  
     begin  
      people\[line\]\[7\] \= 1'b1;   //暗  
      line \= line \- 1;    //左移  
     end  
people\[line\]\[7\] \= 1'b0;  
end  
 end  
   
   
 //掉落物移動  
always@(posedge clk\_mv)  
 begin  
  if(restart==1)    //初始化  
   begin  
    grade1=0;  
    grade2=0;  
    a \= 0;  
    b \= 0;  
    c \= 0;  
    random01 \= (5\*random01 \+ 3)%16;      //隨機生成一個位置當掉落物的初始位置  
    r1 \= random01 % 8;  
    random02 \= (5\*(random02+1) \+ 3)%16;  
    r2 \= random02 % 8;  
    random03= (5\*(random03+2) \+ 3)%16;  
    r3 \= random03 % 8;  
    plate\[0\] \= 8'b11111111;  
    plate\[1\] \= 8'b11111111;  
    plate\[2\] \= 8'b11111111;  
    plate\[3\] \= 8'b11111111;  
    plate\[4\] \= 8'b11111111;  
    plate\[5\] \= 8'b11111111;  
    plate\[6\] \= 8'b11111111;  
    plate\[7\] \= 8'b11111111;  
   end  
     
  else if(timeout==0)   
  begin  
  if(grade2\>=0)//掉落物a  
   begin  
   if(a \== 0\)  
     begin  
      plate\[r1\]\[a\] \= 1'b0;  
      a \= a+1;      
     end  
    else if (a \> 0 && a \<= 7\)   //0\~7之間下落  
      begin  
       plate\[r1\]\[a-1\] \= 1'b1;  
       plate\[r1\]\[a\] \= 1'b0;  
       a \= a+1;  
      end  
    else if(a \== 8\)   
     begin  
      plate\[r1\]\[a-1\] \= 1'b1;   //最底部，暗  
      random01= (5\*(random01+2) \+ 3)%16;   //重新選一個位置生成  
      r1 \= random01 % 8;  
      a \= 0;     //回到頂點  
     end  
   end  
  if(grade1\>=1)//掉落物b    //得分\>=10分，掉落物加一  
   begin  
   if(b \== 0\)  
     begin  
      plate\[r2\]\[b\] \= 1'b0;  
      b \= b+1;  
     end  
    else if (b \> 0 && b \<= 7\)    
      begin  
       plate\[r2\]\[b-1\] \= 1'b1;  
       plate\[r2\]\[b\] \= 1'b0;  
       b \= b+1;  
      end  
    else if(b \== 8\)   
     begin  
      plate\[r2\]\[b-1\] \= 1'b1;  
      random02= (5\*(random01+2) \+ 3)%16;  
      r2 \= random02 % 8;  
      b \= 0;  
     end  
   end  
  if(grade1\>=2)//掉落物c   //得分\>=20分，掉落物加一  
   begin  
   if(c \== 0\)  
     begin  
      plate\[r3\]\[c\] \= 1'b0;  
      c \= c+1;  
     end  
    else if (c \> 0 && c \<= 7\)  
      begin  
       plate\[r3\]\[c-1\] \= 1'b1;  
       plate\[r3\]\[c\] \= 1'b0;  
       c \= c+1;  
      end  
    else if(c \== 8\)   
     begin  
      plate\[r3\]\[c-1\] \= 1'b1;  
      random03= (5\*(random01+2) \+ 3)%16;  
      r3 \= random03 % 8;  
      c \= 0;  
     end  
   end

    //計分  
    if (plate\[line\]\[7\] \== 0\)     //如果掉落物與人物碰撞  
     begin  
      if(grade2==9)   //個位數==9  
       begin  
        grade1\<=grade1+1;  
        grade2\<=0;  
       end  
      else grade2=grade2 \+ 1;  
        
      plate\[line\]\[7\] \= 1'b1;  
      if(line==r1) a=8;     //人物與a掉落物碰撞  
      else if(line==r2) b=8; //人物與b掉落物碰撞  
      else if(line==r3) c=8; //人物與c掉落物碰撞  
     end  
    end 

  end  
endmodule  
//timeout後分數顯示  
module result(input \[4:0\]grade1,grade2,output reg \[7:0\] r \[7:0\]);  
always@(grade1)  
begin  
if(grade1==0)  
begin  
r\[0\] \= 8'b00000000;  
    r\[1\] \= 8'b01111110;  
    r\[2\] \= 8'b01111110;  
    r\[3\] \= 8'b00000000;  
end  
else if(grade1==1)  
begin  
r\[0\] \= 8'b11111111;  
    r\[1\] \= 8'b11111111;  
    r\[2\] \= 8'b00000000;  
    r\[3\] \= 8'b11111111;  
end  
else if(grade1==2)  
begin  
r\[0\] \= 8'b00000110;  
    r\[1\] \= 8'b01110110;  
    r\[2\] \= 8'b01110110;  
    r\[3\] \= 8'b01110000;  
end  
else if(grade1==3)  
begin  
r\[0\] \= 8'b01101110;  
    r\[1\] \= 8'b01101110;  
    r\[2\] \= 8'b01101110;  
    r\[3\] \= 8'b00000000;  
end  
else if(grade1==4)  
begin  
r\[0\] \= 8'b11110000;  
    r\[1\] \= 8'b11110111;  
    r\[2\] \= 8'b11110111;  
    r\[3\] \= 8'b00000000;  
end  
else if(grade1==5)  
begin  
r\[0\] \= 8'b01110000;  
    r\[1\] \= 8'b01110110;  
    r\[2\] \= 8'b01110110;  
    r\[3\] \= 8'b00000110;  
end  
else if(grade1==6)  
begin  
r\[0\] \= 8'b00000000;  
    r\[1\] \= 8'b01101110;  
    r\[2\] \= 8'b01101110;  
    r\[3\] \= 8'b00001110;  
end  
else if(grade1==7)  
begin  
r\[0\] \= 8'b11111110;  
    r\[1\] \= 8'b11111110;  
    r\[2\] \= 8'b11111110;  
    r\[3\] \= 8'b00000000;  
end  
else if(grade1==8)  
begin  
r\[0\] \= 8'b00000000;  
    r\[1\] \= 8'b01110110;  
    r\[2\] \= 8'b01110110;  
    r\[3\] \= 8'b00000000;  
end  
else if(grade1==9)  
begin  
r\[0\] \= 8'b01110000;  
    r\[1\] \= 8'b01110110;  
    r\[2\] \= 8'b01110110;  
    r\[3\] \= 8'b00000000;  
end  
end  
always@(grade2)  
begin   
  if(grade2==0)  
begin  
r\[0+4\] \= 8'b00000000;  
    r\[1+4\] \= 8'b01111110;  
    r\[2+4\] \= 8'b01111110;  
    r\[3+4\] \= 8'b00000000;  
end  
else if(grade2==1)  
begin  
r\[0+4\] \= 8'b11111111;  
    r\[1+4\] \= 8'b11111111;  
    r\[2+4\] \= 8'b00000000;  
    r\[3+4\] \= 8'b11111111;  
end  
else if(grade2==2)  
begin  
r\[0+4\] \= 8'b00000110;  
    r\[1+4\] \= 8'b01110110;  
    r\[2+4\] \= 8'b01110110;  
    r\[3+4\] \= 8'b01110000;  
end  
else if(grade2==3)  
begin  
r\[0+4\] \= 8'b01101110;  
    r\[1+4\] \= 8'b01101110;  
    r\[2+4\] \= 8'b01101110;  
    r\[3+4\] \= 8'b00000000;  
end  
else if(grade2==4)  
begin  
r\[0+4\] \= 8'b11110000;  
    r\[1+4\] \= 8'b11110111;  
    r\[2+4\] \= 8'b11110111;  
    r\[3+4\] \= 8'b00000000;  
end  
else if(grade2==5)  
begin  
r\[0+4\] \= 8'b01110000;  
    r\[1+4\] \= 8'b01110110;  
    r\[2+4\] \= 8'b01110110;  
    r\[3+4\] \= 8'b00000110;  
end  
else if(grade2==6)  
begin  
r\[0+4\] \= 8'b00000000;  
    r\[1+4\] \= 8'b01101110;  
    r\[2+4\] \= 8'b01101110;  
    r\[3+4\] \= 8'b00001110;  
end  
else if(grade2==7)  
begin  
r\[0+4\] \= 8'b11111110;  
    r\[1+4\] \= 8'b11111110;  
    r\[2+4\] \= 8'b11111110;  
    r\[3+4\] \= 8'b00000000;  
end  
else if(grade2==8)  
begin  
r\[0+4\] \= 8'b00000000;  
    r\[1+4\] \= 8'b01110110;  
    r\[2+4\] \= 8'b01110110;  
    r\[3+4\] \= 8'b00000000;  
end  
else if(grade2==9)  
begin  
r\[0+4\] \= 8'b01110000;  
    r\[1+4\] \= 8'b01110110;  
    r\[2+4\] \= 8'b01110110;  
    r\[3+4\] \= 8'b00000000;  
end  
end  
endmodule

//整數轉七段顯示器  
module segment7(input \[0:3\] a, output A,B,C,D,E,F,G);  
 assign A \= \~(a\[0\]&\~a\[1\]&\~a\[2\] | \~a\[0\]\&a\[2\] | \~a\[1\]&\~a\[2\]&\~a\[3\] | \~a\[0\]\&a\[1\]\&a\[3\]),  
        B \= \~(\~a\[0\]&\~a\[1\] | \~a\[1\]&\~a\[2\] | \~a\[0\]&\~a\[2\]&\~a\[3\] | \~a\[0\]\&a\[2\]\&a\[3\]),  
    C \= \~(\~a\[0\]\&a\[1\] | \~a\[1\]&\~a\[2\] | \~a\[0\]\&a\[3\]),  
    D \= \~(a\[0\]&\~a\[1\]&\~a\[2\] | \~a\[0\]&\~a\[1\]\&a\[2\] | \~a\[0\]\&a\[2\]&\~a\[3\] | \~a\[0\]\&a\[1\]&\~a\[2\]\&a\[3\] | \~a\[1\]&\~a\[2\]&\~a\[3\]),  
    E \= \~(\~a\[1\]&\~a\[2\]&\~a\[3\] | \~a\[0\]\&a\[2\]&\~a\[3\]),  
    F \= \~(\~a\[0\]\&a\[1\]&\~a\[2\] | \~a\[0\]\&a\[1\]&\~a\[3\] | a\[0\]&\~a\[1\]&\~a\[2\] | \~a\[1\]&\~a\[2\]&\~a\[3\]),  
    G \= \~(a\[0\]&\~a\[1\]&\~a\[2\] | \~a\[0\]&\~a\[1\]\&a\[2\] | \~a\[0\]\&a\[1\]&\~a\[2\] | \~a\[0\]\&a\[2\]&\~a\[3\]);  
      
endmodule

//顯示除頻器  
module divfreq(input clk, output reg clk\_div);  
  reg \[24:0\] count;  
  always @(posedge clk)  
    begin  
      if(count \> 2500\)  
        begin  
          count \<= 25'b0;  
          clk\_div \<= \~clk\_div;  
        end  
      else  
        count \<= count \+ 1'b1;  
    end  
endmodule  
//計時除頻器  
module divfreq1(input clk, output reg clk\_sec);  
  reg \[24:0\] count;  
  always @(posedge clk)  
    begin  
      if(count \> 25000000\)  
        begin  
          count \<= 25'b0;  
          clk\_sec \<= \~clk\_sec;  
        end  
      else  
        count \<= count \+ 1'b1;  
    end  
endmodule  
//掉落物除頻器  
module divfreq2(input clk,speed, output reg clk\_mv);  
  reg \[35:0\] Count;  
  initial  
    begin  
      clk\_mv \= 0;  
    end   
  always @(posedge clk)  
  begin  
  if(speed==0)  
   begin  
   if(Count \> 4500000\)  
   begin  
    Count \<= 45'b0;  
    clk\_mv \<= \~clk\_mv;  
   end  
   else  
    Count \<= Count \+ 1'b1;  
 end  
 else  
 begin  
   if(Count \> 4500000/speed)  
   begin  
    Count \<= 45'b0;  
    clk\_mv \<= \~clk\_mv;  
   end  
   else  
    Count \<= Count \+ 1'b1;  
 end  
 end  
endmodule

//人物除頻器  
module divfreq3(input clk, output reg clk\_pmv);  
  reg \[35:0\] Count;  
  initial  
    begin  
      clk\_pmv \= 0;  
    end   
  always @(posedge clk)  
  begin  
   if(Count \> 4500000\)  
   begin  
    Count \<= 45'b0;  
    clk\_pmv \<= \~clk\_pmv;  
   end  
   else  
    Count \<= Count \+ 1'b1;  
  end  
endmodulemodule finalproject(  
output reg \[7:0\] ledr, ledg,ledb,  
output reg \[1:0\] comg,coms,  
output reg \[2:0\] com,  
output reg \[6:0\] seg,  
output EN,  
input clk,restart,left,right,  
input reg \[1:0\]color);

 wire clk\_div, clk\_sec, clk\_mv;  
   wire A2, B2, C2, D2, E2, F2, G2;  
   wire A3, B3, C3, D3, E3, F3, G3;  
   wire A0, B0, C0, D0, E0, F0, G0;  
   wire A1, B1, C1, D1, E1, F1, G1;  
 byte line,count,timeout;  
 reg \[3:0\] a, b, c;  
 reg \[3:0\] speed;  
 reg \[7:0\] people \[7:0\];  
 reg \[7:0\] plate \[7:0\];  
 reg \[7:0\] re \[7:0\];  
 reg \[3:0\] grade1,grade2,sec1,sec2;  
 reg \[2:0\] random01, random02, random03, r1, r2, r3;  
 reg \[1:0\] count1;  
 reg \[6:0\] seg1,seg2,seg3,seg4;  
   
 divfreq div0(clk, clk\_div);  
 divfreq1 div1(clk, clk\_sec);  
 divfreq2 div2(clk,speed, clk\_mv);  
 divfreq3 div3(clk, clk\_pmv);  
   
 segment7 SEG0(grade1, A2,B2,C2,D2,E2,F2,G2);  
 segment7 SEG1(grade2, A3,B3,C3,D3,E3,F3,G3);  
 segment7 SEG2(sec1, A0,B0,C0,D0,E0,F0,G0);  
 segment7 SEG3(sec2, A1,B1,C1,D1,E1,F1,G1);  
   
 result Chang1(grade1,grade2,re);  
   
//初始化  
 initial  
  begin  
   timeout=0;  
   speed=0;  
   line=3; //人物初始位置  
   random01 \= (5\*random01 \+ 3)%16;  
   r1 \= random01 % 8;  
   random02 \= (5\*(random02+1) \+ 3)%16;  
   r2 \= random02 % 8;  
   random03= (5\*(random03+2) \+ 3)%16;  
   r3 \= random03 % 8;  
   a \= 0;  
   b \= 0;  
   c \= 0;  
   ledr=8'b11111111;  
   ledg=8'b11111111;  
ledb=8'b11111111;  
   grade1=0;  
   grade2=0;  
   sec1=4'b0110;   
   sec2=4'b0000;  
   count1=2'b00;  
re\[0\] \= 8'b11111111; //分數  
    re\[1\] \= 8'b11111111;  
    re\[2\] \= 8'b11111111;  
    re\[3\] \= 8'b11111111;  
    re\[4\] \= 8'b11111111;  
    re\[5\] \= 8'b11111111;  
    re\[6\] \= 8'b11111111;  
    re\[7\] \= 8'b11111111;  
    plate\[0\] \= 8'b11111111; //掉落物  
    plate\[1\] \= 8'b11111111;  
    plate\[2\] \= 8'b11111111;  
    plate\[3\] \= 8'b11111111;  
    plate\[4\] \= 8'b11111111;  
    plate\[5\] \= 8'b11111111;  
    plate\[6\] \= 8'b11111111;  
    plate\[7\] \= 8'b11111111;  
    people\[0\] \= 8'b11111111; //人物  
    people\[1\] \= 8'b11111111;  
    people\[2\] \= 8'b11111111;  
    people\[3\] \= 8'b11111111;  
    people\[4\] \= 8'b11111111;  
    people\[5\] \= 8'b11111111;  
    people\[6\] \= 8'b11111111;  
    people\[7\] \= 8'b11111111;  
    people\[line\]\[7\] \= 1'b0;  
  end  
    
//7段顯示器的視覺暫留  
always@(posedge clk\_div)  
 begin  
   
  seg1\[0\] \= A0; //sec1(十位數）轉成七段  
  seg1\[1\] \= B0;  
  seg1\[2\] \= C0;  
  seg1\[3\] \= D0;  
  seg1\[4\] \= E0;  
  seg1\[5\] \= F0;  
  seg1\[6\] \= G0;  
    
  seg2\[0\] \= A1; //sec2(個位數）轉成七段  
  seg2\[1\] \= B1;  
  seg2\[2\] \= C1;  
  seg2\[3\] \= D1;  
  seg2\[4\] \= E1;  
  seg2\[5\] \= F1;  
  seg2\[6\] \= G1;  
    
  seg3\[0\] \= A2; //grade1(十位數）轉成七段  
  seg3\[1\] \= B2;  
  seg3\[2\] \= C2;  
  seg3\[3\] \= D2;  
  seg3\[4\] \= E2;  
  seg3\[5\] \= F2;  
  seg3\[6\] \= G2;  
    
  seg4\[0\] \= A3; //grade2(個位數）轉成七段  
  seg4\[1\] \= B3;  
  seg4\[2\] \= C3;  
  seg4\[3\] \= D3;  
  seg4\[4\] \= E3;  
  seg4\[5\] \= F3;  
  seg4\[6\] \= G3;  
 //通過快速輪流更新，達到顯示器同時顯示的效果  
  if(count1 \== 2'b00)  
   begin  
    seg \<= seg2;  
    coms \<= 2'b01;  
    comg \<= 2'b11;  
    count1=count1+1'b1;  
   end  
  else if(count1 \== 2'b01)  
   begin  
    seg \<= seg1;  
    coms \<= 2'b10;  
    comg \<= 2'b11;  
    count1=count1+1'b1;  
   end  
  else if(count1 \== 2'b10)  
   begin  
    seg \<= seg3;  
    coms \<= 2'b11;  
    comg \<= 2'b10;  
    count1=count1+1'b1;  
   end  
  else if(count1 \== 2'b11)  
   begin  
    seg \<= seg4;  
    coms \<= 2'b11;  
    comg \<= 2'b01;  
    count1 \<= 2'b00;  
   end  
 end  
//計時  
always@(posedge clk\_sec)  
 begin  
  if(restart==1). //initial  
   begin  
    speed=0;  
    timeout=0;  
    sec1=4'b0110;  //9  
    sec2=4'b0000; //0  
   end  
  else if(timeout\!=1)  
   begin  
    if (sec1 \== 4'b0000 && sec2 \== 4'b0000)  //當秒數十位與個位數皆等於0  
     begin  
      timeout \= 1; // 計時結束  
     end   
    else if (sec2 \== 4'b0000)  
     begin  
      sec1 \<= sec1 \- 1'b1; //十位數減1  
      sec2 \<= 4'b1001;  
      speed \<= speed \+ 1; // 每十秒加速  
     end  
    else  
     begin  
      sec2 \<= sec2 \- 1'b1; //個位數減1  
     end  
   end  
else   
begin  
sec1=4'b0000; //timeout==1，超時  
sec2=4'b0000;  
end  
 end  
//8x8led 視覺暫留  
always@(posedge clk\_div)  
 begin  
  if(count \>= 7\) //column,到達最右側，回到最左側  
   count \<= 0;  
  else  
   count \<= count \+ 1; //column,一直向右移  
   com \= count;  
   EN \= 1'b1;  
if(timeout==1)  
begin  
ledr\<=re\[count\]; //分數  
ledb \<=8'b11111111;  
ledg \<=8'b11111111;  
end  
  if(timeout==0)  
   begin  
//掉落物與人物顏色改變  
if(color==2'b00)  
begin  
    ledg \<= plate\[count\];  
    ledr \<= people\[count\];  
ledb \<=8'b11111111;  
end  
else if(color==2'b01)  
begin  
    ledb \<= plate\[count\];  
    ledg \<= people\[count\];  
ledr \<=8'b11111111;  
end  
else if(color==2'b10)  
begin  
    ledr \<= plate\[count\];  
    ledb \<= people\[count\];  
ledg \<=8'b11111111;  
end  
else if(color==2'b11)  
begin  
    ledg \<= plate\[count\];  
    ledb \<= people\[count\];  
ledr \<=8'b11111111;  
end  
   end  
 end

   
   
//人物移動  
always@(posedge clk\_pmv)  
 begin  
   if(restart==1 || timeout==1)  
begin  
line \= 3; //人物起始column  
people\[0\] \= 8'b11111111;  
    people\[1\] \= 8'b11111111;  
    people\[2\] \= 8'b11111111;  
    people\[3\] \= 8'b11111111;  
    people\[4\] \= 8'b11111111;  
    people\[5\] \= 8'b11111111;  
    people\[6\] \= 8'b11111111;  
    people\[7\] \= 8'b11111111;  
end  
if(timeout==0)  
begin  
   if((right \== 1\) && (line \!= 7)) //7是邊界，如果line==7還能向左移動會出界  
     begin  
      people\[line\]\[7\] \= 1'b1; //暗  
      line \= line \+ 1; //右移  
     end  
   if((left \== 1\) && (line \!= 0)) //0是邊界，如果line==0還能向左移動會出界  
     begin  
      people\[line\]\[7\] \= 1'b1; //暗  
      line \= line \- 1; //左移  
     end  
people\[line\]\[7\] \= 1'b0;  
end  
 end  
   
   
 //掉落物移動  
always@(posedge clk\_mv)  
 begin  
  if(restart==1)  
   begin  
    grade1=0;  
    grade2=0;  
    a \= 0;  
    b \= 0;  
    c \= 0;  
//隨機生成一個掉落物的初始位置  
    random01 \= (5\*random01 \+ 3)%16;  
    r1 \= random01 % 8;  
    random02 \= (5\*(random02+1) \+ 3)%16;  
    r2 \= random02 % 8;  
    random03= (5\*(random03+2) \+ 3)%16;  
    r3 \= random03 % 8;  
    plate\[0\] \= 8'b11111111;  
    plate\[1\] \= 8'b11111111;  
    plate\[2\] \= 8'b11111111;  
    plate\[3\] \= 8'b11111111;  
    plate\[4\] \= 8'b11111111;  
    plate\[5\] \= 8'b11111111;  
    plate\[6\] \= 8'b11111111;  
    plate\[7\] \= 8'b11111111;  
   end  
     
  else if(timeout==0)   
  begin  
  if(grade2\>=0)//掉落物a  
   begin  
   if(a \== 0\)  
     begin  
      plate\[r1\]\[a\] \= 1'b0;  
      a \= a+1;  
     end  
    else if (a \> 0 && a \<= 7\) //0\~7之間  
      begin  
       plate\[r1\]\[a-1\] \= 1'b1;  
       plate\[r1\]\[a\] \= 1'b0;  
       a \= a+1;  
      end  
    else if(a \== 8\)   
     begin  
      plate\[r1\]\[a-1\] \= 1'b1; //最底部，暗  
      random01= (5\*(random01+2) \+ 3)%16; //重新選一個位置生成  
      r1 \= random01 % 8;  
      a \= 0;  
     end  
   end  
  if(grade1==1)//掉落物b，得分\>=10掉落物加一  
   begin  
   if(b \== 0\)  
     begin  
      plate\[r2\]\[b\] \= 1'b0;  
      b \= b+1;  
     end  
    else if (b \> 0 && b \<= 7\)  
      begin  
       plate\[r2\]\[b-1\] \= 1'b1;  
       plate\[r2\]\[b\] \= 1'b0;  
       b \= b+1;  
      end  
    else if(b \== 8\)   
     begin  
      plate\[r2\]\[b-1\] \= 1'b1;  
      random02= (5\*(random01+2) \+ 3)%16;  
      r2 \= random02 % 8;  
      b \= 0;  
     end  
   end  
  if(grade1\>=2)//掉落物c.,得分\>=20，掉落物加一  
   begin  
   if(c \== 0\)  
     begin  
      plate\[r3\]\[c\] \= 1'b0;  
      c \= c+1;  
     end  
    else if (c \> 0 && c \<= 7\)  
      begin  
       plate\[r3\]\[c-1\] \= 1'b1;  
       plate\[r3\]\[c\] \= 1'b0;  
       c \= c+1;  
      end  
    else if(c \== 8\)   
     begin  
      plate\[r3\]\[c-1\] \= 1'b1;  
      random03= (5\*(random01+2) \+ 3)%16;  
      r3 \= random03 % 8;  
      c \= 0;  
     end  
   end

    //計分  
    if (plate\[line\]\[7\] \== 0\)  
     begin  
      if(grade2==9)  //個位數==9  
       begin  
        grade1\<=grade1+1;  
        grade2\<=0;  
       end  
      else grade2=grade2 \+ 1;  
        
      plate\[line\]\[7\] \= 1'b1;  
//掉落物與人物碰撞  
      if(line==r1) a=8;  
      else if(line==r2) b=8;  
      else if(line==r3) c=8;  
     end  
    end 

  end  
endmodule  
//timeout後分數顯示  
module result(input \[4:0\]grade1,grade2,output reg \[7:0\] r \[7:0\]);  
always@(grade1)  
begin  
if(grade1==0)  
begin  
r\[0\] \= 8'b00000000;  
    r\[1\] \= 8'b01111110;  
    r\[2\] \= 8'b01111110;  
    r\[3\] \= 8'b00000000;  
end  
else if(grade1==1)  
begin  
r\[0\] \= 8'b11111111;  
    r\[1\] \= 8'b11111111;  
    r\[2\] \= 8'b00000000;  
    r\[3\] \= 8'b11111111;  
end  
else if(grade1==2)  
begin  
r\[0\] \= 8'b00000110;  
    r\[1\] \= 8'b01110110;  
    r\[2\] \= 8'b01110110;  
    r\[3\] \= 8'b01110000;  
end  
else if(grade1==3)  
begin  
r\[0\] \= 8'b01101110;  
    r\[1\] \= 8'b01101110;  
    r\[2\] \= 8'b01101110;  
    r\[3\] \= 8'b00000000;  
end  
else if(grade1==4)  
begin  
r\[0\] \= 8'b11110000;  
    r\[1\] \= 8'b11110111;  
    r\[2\] \= 8'b11110111;  
    r\[3\] \= 8'b00000000;  
end  
else if(grade1==5)  
begin  
r\[0\] \= 8'b01110000;  
    r\[1\] \= 8'b01110110;  
    r\[2\] \= 8'b01110110;  
    r\[3\] \= 8'b00000110;  
end  
else if(grade1==6)  
begin  
r\[0\] \= 8'b00000000;  
    r\[1\] \= 8'b01101110;  
    r\[2\] \= 8'b01101110;  
    r\[3\] \= 8'b00001110;  
end  
else if(grade1==7)  
begin  
r\[0\] \= 8'b11111110;  
    r\[1\] \= 8'b11111110;  
    r\[2\] \= 8'b11111110;  
    r\[3\] \= 8'b00000000;  
end  
else if(grade1==8)  
begin  
r\[0\] \= 8'b00000000;  
    r\[1\] \= 8'b01110110;  
    r\[2\] \= 8'b01110110;  
    r\[3\] \= 8'b00000000;  
end  
else if(grade1==9)  
begin  
r\[0\] \= 8'b01110000;  
    r\[1\] \= 8'b01110110;  
    r\[2\] \= 8'b01110110;  
    r\[3\] \= 8'b00000000;  
end  
end  
always@(grade2)  
begin   
  if(grade2==0)  
begin  
r\[0+4\] \= 8'b00000000;  
    r\[1+4\] \= 8'b01111110;  
    r\[2+4\] \= 8'b01111110;  
    r\[3+4\] \= 8'b00000000;  
end  
else if(grade2==1)  
begin  
r\[0+4\] \= 8'b11111111;  
    r\[1+4\] \= 8'b11111111;  
    r\[2+4\] \= 8'b00000000;  
    r\[3+4\] \= 8'b11111111;  
end  
else if(grade2==2)  
begin  
r\[0+4\] \= 8'b00000110;  
    r\[1+4\] \= 8'b01110110;  
    r\[2+4\] \= 8'b01110110;  
    r\[3+4\] \= 8'b01110000;  
end  
else if(grade2==3)  
begin  
r\[0+4\] \= 8'b01101110;  
    r\[1+4\] \= 8'b01101110;  
    r\[2+4\] \= 8'b01101110;  
    r\[3+4\] \= 8'b00000000;  
end  
else if(grade2==4)  
begin  
r\[0+4\] \= 8'b11110000;  
    r\[1+4\] \= 8'b11110111;  
    r\[2+4\] \= 8'b11110111;  
    r\[3+4\] \= 8'b00000000;  
end  
else if(grade2==5)  
begin  
r\[0+4\] \= 8'b01110000;  
    r\[1+4\] \= 8'b01110110;  
    r\[2+4\] \= 8'b01110110;  
    r\[3+4\] \= 8'b00000110;  
end  
else if(grade2==6)  
begin  
r\[0+4\] \= 8'b00000000;  
    r\[1+4\] \= 8'b01101110;  
    r\[2+4\] \= 8'b01101110;  
    r\[3+4\] \= 8'b00001110;  
end  
else if(grade2==7)  
begin  
r\[0+4\] \= 8'b11111110;  
    r\[1+4\] \= 8'b11111110;  
    r\[2+4\] \= 8'b11111110;  
    r\[3+4\] \= 8'b00000000;  
end  
else if(grade2==8)  
begin  
r\[0+4\] \= 8'b00000000;  
    r\[1+4\] \= 8'b01110110;  
    r\[2+4\] \= 8'b01110110;  
    r\[3+4\] \= 8'b00000000;  
end  
else if(grade2==9)  
begin  
r\[0+4\] \= 8'b01110000;  
    r\[1+4\] \= 8'b01110110;  
    r\[2+4\] \= 8'b01110110;  
    r\[3+4\] \= 8'b00000000;  
end  
end  
endmodule

//整數轉七段顯示器  
module segment7(input \[0:3\] a, output A,B,C,D,E,F,G);  
 assign A \= \~(a\[0\]&\~a\[1\]&\~a\[2\] | \~a\[0\]\&a\[2\] | \~a\[1\]&\~a\[2\]&\~a\[3\] | \~a\[0\]\&a\[1\]\&a\[3\]),  
        B \= \~(\~a\[0\]&\~a\[1\] | \~a\[1\]&\~a\[2\] | \~a\[0\]&\~a\[2\]&\~a\[3\] | \~a\[0\]\&a\[2\]\&a\[3\]),  
    C \= \~(\~a\[0\]\&a\[1\] | \~a\[1\]&\~a\[2\] | \~a\[0\]\&a\[3\]),  
    D \= \~(a\[0\]&\~a\[1\]&\~a\[2\] | \~a\[0\]&\~a\[1\]\&a\[2\] | \~a\[0\]\&a\[2\]&\~a\[3\] | \~a\[0\]\&a\[1\]&\~a\[2\]\&a\[3\] | \~a\[1\]&\~a\[2\]&\~a\[3\]),  
    E \= \~(\~a\[1\]&\~a\[2\]&\~a\[3\] | \~a\[0\]\&a\[2\]&\~a\[3\]),  
    F \= \~(\~a\[0\]\&a\[1\]&\~a\[2\] | \~a\[0\]\&a\[1\]&\~a\[3\] | a\[0\]&\~a\[1\]&\~a\[2\] | \~a\[1\]&\~a\[2\]&\~a\[3\]),  
    G \= \~(a\[0\]&\~a\[1\]&\~a\[2\] | \~a\[0\]&\~a\[1\]\&a\[2\] | \~a\[0\]\&a\[1\]&\~a\[2\] | \~a\[0\]\&a\[2\]&\~a\[3\]);  
      
endmodule

//顯示除頻器  
module divfreq(input clk, output reg clk\_div);  
  reg \[24:0\] count;  
  always @(posedge clk)  
    begin  
      if(count \> 2500\)  
        begin  
          count \<= 25'b0;  
          clk\_div \<= \~clk\_div;  
        end  
      else  
        count \<= count \+ 1'b1;  
    end  
endmodule  
//計時除頻器  
module divfreq1(input clk, output reg clk\_sec);  
  reg \[24:0\] count;  
  always @(posedge clk)  
    begin  
      if(count \> 25000000\)  
        begin  
          count \<= 25'b0;  
          clk\_sec \<= \~clk\_sec;  
        end  
      else  
        count \<= count \+ 1'b1;  
    end  
endmodule  
//掉落物除頻器  
module divfreq2(input clk,speed, output reg clk\_mv);  
  reg \[35:0\] Count;  
  initial  
    begin  
      clk\_mv \= 0;  
    end   
  always @(posedge clk)  
  begin  
  if(speed==0)  
   begin  
   if(Count \> 4500000\)  
   begin  
    Count \<= 45'b0;  
    clk\_mv \<= \~clk\_mv;  
   end  
   else  
    Count \<= Count \+ 1'b1;  
 end  
 else  
 begin  
   if(Count \> 4500000/speed)  
   begin  
    Count \<= 45'b0;  
    clk\_mv \<= \~clk\_mv;  
   end  
   else  
    Count \<= Count \+ 1'b1;  
 end  
 end  
endmodule

//人物除頻器  
module divfreq3(input clk, output reg clk\_pmv);  
  reg \[35:0\] Count;  
  initial  
    begin  
      clk\_pmv \= 0;  
    end   
  always @(posedge clk)  
  begin  
   if(Count \> 4500000\)  
   begin  
    Count \<= 45'b0;  
    clk\_pmv \<= \~clk\_pmv;  
   end  
   else  
    Count \<= Count \+ 1'b1;  
  end  
endmodule
