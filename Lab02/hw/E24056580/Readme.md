﻿# Homework Lab2 
## Member


E24056580
E24045173
E24053037

##  Program 1

這次的作業需要做兩個對應的紅綠燈出來，且要可以控制綠燈長短以及用按鈕控制紅綠燈，因此我們想出來的設計是：
 1. 按下按鈕可以使正常運作中的紅綠燈轉變成閃黃(紅)燈
 2. 調整開關可以改變綠燈秒數(甚至黃燈)，其關係如下表

|開關|綠燈/黃燈/紅燈|  
|--|--|
|00|15 / 2 / 17|
|01|10 / 2 / 12  |
|10|5 / 5 / 10  |
|11|3 / 7 / 10  |


在這個程式中，運用到Lab2-1的除頻器，與Lab2-3的Decoder、RGB_LED及控制IO和clk的constraint，另外我們還加入了一個新建的time_control.v，並在最後用一個top.v包起來。

RGB_LED.v本身就是拿來控制LED閃爍的程式，他只要負責接收PWM值，輸出RGB訊號即可，因此不用更改。

在constraint的程式碼中，我們幾乎沒有更改程式碼，除了打開一些必須使用的IO以及增加clk_div的constraint。

在time_control.v裡，就是用來控制綠燈時間的程式。我們使用組合電路直接判斷sw，並將預設好的時間長短輸出給Decoder。

最後比較麻煩的程式是Decoder，相較於Lab2-3我們給了更多的input/output，並運用狀態機的概念加入四個State，分別表示第一顆亮綠燈、第一顆亮黃燈、第二顆亮綠燈、第二顆亮黃燈，這四種狀態。
我們用上述四種狀態，以及time_control給的輸入"紅綠燈秒數"，配合counter的計數，以邏輯判斷的現在需亮哪一種燈，輸出相對應的PWM值。
且在這支程式的最後，我們會用組合電路的方式直接判斷btn[1]是否有被按下，若被按下則強制更新PWM值，且用fliker來控制閃爍(fliker會隨著每個clk更新，在1.0之間變換)。

最後在TOP的地方，比較特別的是，為了使閃爍時間很快，所以RGB_LED吃的是原本的clk；而為了讓紅綠燈轉換時間拉長，及讓LED倒數的慢一點，所以Decoder吃的是除頻後的clk_div。

##  Program 2.


這個作業需要用按紐調整PWM值，但LED只有四顆，所以我們每次加減PWM都是16為單位，也就是

> PWM=LED[4:0]*16

舉例：XXOX 就是2*16=32，PWM=32

LED不夠的問題解決了，但是在做的時候沒想到怎麼讀"按一下按鈕"，所以我們用較笨的方法：我們會每個clk判斷按鈕是否有被按下。
所以只會在clk來的時候才會接收按鈕訊號，而且按紐常按會隨著時間慢慢地把PWM加上去。
(事後發現好像直接讀取btn的正緣觸發訊號就好)

但是用這種方式每個clk來都讀取的話，會讀得太快，因此我們又使用了除頻器。所以最後Decoder吃的依舊是clk_div。

最後一個問題是，如何讓LED跟RGB_LED在正確的狀態下亮出相對應的值?
為了解決這個問題，我們在Decoder的最後，用組合電路判斷現在sw及btn的訊號，並直接在這裏面控制LED的輸出！

詳細的操作結果可看DEMO影片


