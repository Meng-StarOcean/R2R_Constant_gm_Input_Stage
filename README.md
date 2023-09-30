A new Rail-to-Rail constant gm input stage using feedforward canceling technique

开源协议：Apache License 2.0 
## 采用前馈抵消技术改出来的一个新结构
论文Constant-gm Constant-Slew-Rate High-Bandwidth Low-Voltage Rail-to-Rail CMOS Input Stage for VLSI Cell Libraries首次提出了采用前馈抵消（feedforward canceling）技术实现跨导恒定的电路，但是该电路需要采用源随器实现浮动电压源来实现运放的轨到轨操作，而且该运放的地轨不一定能够达到GND，而电源轨却能超出VDD。其电路原理图如下：
![image](https://github.com/Meng-StarOcean/R2R_Constant_gm_Input_Stage/assets/77794246/59348820-b286-400c-a10f-cac8fdd96ebe)

为了扩展轨到轨的范围且保证良好的跨导恒定效果，采用该论文提出的前馈抵消技术实现了一个新的电路结构：
![image](https://github.com/Meng-StarOcean/R2R_Constant_gm_Input_Stage/assets/77794246/bea18251-f58b-4bfb-9b00-bfe0e1858182)
### 电路工作原理

M1P、M2P、M1PA、M2PA、M1PB和M2PB尺寸相同，M1N、M2N、M1NA、M2NA、M1NB和M2NB尺寸相同
注意M1NA、M2NA、M1PA和M2PA和其他输入差分对漏极接的位置。该结构没有像采用前馈抵消技术那篇论文那样采用一个相同尺寸的NMOS输入差分对来探测电流，而是通过电流溢出的方式主动的让输入差分对电流减小，然后让溢出电流流过另外一路尾电流源来实现一对NMOS输入差分对和一对PMOS输入差分对电流相等。详细电路原理见下面分析：

* 当输入共模电压处在中间部分时，M8P和M8N尾电流中的电流全部流过M1PB、M2PB以及M1NB、M2NB输入差分对，M11N和M11P中没有电流流过，此时M1NA、M2NA和M1NB、M2NB的小信号电流直接抵消。对于另外一边的PMOS差分对也是同样的道理。此时的小信号电流仅由M1N、M2N、M1P和M2P决定。
* 当输入共模电压靠近VDD时，M11P流过大部分电流，M11N不会流过电流。对PMOS来说，M1P、M2P、M1PA和M2PA产生的小信号电流直接抵消。而M1PB、M2PB、M1NA和M2NA的小信号电流也能直接抵消。此时的小信号电流仅由M1N、M2N、M1NB和M2NB决定。
* 同理输入共模电压靠近VSS时，小信号电流仅由M1P、M2P、M1PB和M2PB决定。
* 电源电压比较低时，就输入共模在中间，但是还是由部分电流往M11P和M11N流过时同样能保证跨导恒定的。

对M11P和M11N来说，他们的宽长比的要求是当输入共模到VDD时，尾电流管所有的电流都得能从M11P流过，不能使得M8P进入线性区，对M11N也是几乎相同的要求。另外M11P和M11N漏极需要接到M5N和M5P的源极，这里必须加上这个cascode管，以防止输入共模导致M11P或M11N进入线性区。最后由于输入差分对为了低失调一般取的面积都很大，所以M11P和M11N接到M6N和M6P的漏极不会特别影响这一点的阻抗。

这个电路相对原论文的一个好处就是输入共模能够扩展到VDD和VSS以外，同时还能保证不错的跨导恒定效果，也取消了源随器，功耗会小一点（结合GBW和功耗来看）。缺点也很明显，原论文只用了NMOS输入差分对，因此不用管PMOS和NMOS由于工艺导致的参数变化，而该结构要求NMOS和PMOS的跨导比较匹配，所以由于工艺变化导致的跨导变化就不好处理了。

## 实际电路及仿真结果
![image](https://github.com/Meng-StarOcean/R2R_Constant_gm_Input_Stage/assets/77794246/058a74c0-eb4d-43af-a0c1-9dbc82c76912)
测试电路如下：

![image](https://github.com/Meng-StarOcean/R2R_Constant_gm_Input_Stage/assets/77794246/47923f0f-e0ac-4689-8a06-aa4794b6a171)

在不同输入电压下（电源电压3.3V）看了哈环路稳定性分析结果，结果看起来还不错（只看了tt corner，这个电路由于是在之前的基础上直接改了哈，所以相位没太管）

![image](https://github.com/Meng-StarOcean/R2R_Constant_gm_Input_Stage/assets/77794246/13783f67-0e22-4d59-9c3d-d43ff9a4b085)

然后开环扫了哈，看了看整体跨导随输入共模的变化，效果还行。
![image](https://github.com/Meng-StarOcean/R2R_Constant_gm_Input_Stage/assets/77794246/2e4b2ec1-0c6d-4394-b857-c1fc87d9e873)

输入共模可以扩展到VSS-1V ~ VDD+1V。

### 主要参考到的几篇文献
* A High Bandwidth Constant gm and Slew-Rate Rail-to-Rail CMOS Input Circuit and its Application to Analog Cells for Low Voltage VLSI Systems
* Constant-gm Constant-Slew-Rate High-Bandwidth Low-Voltage Rail-to-Rail CMOS Input Stage for VLSI Cell Libraries
* A 1.5-V, Constant-Gm, Rail-to-Rail Input Stage  Operational Amplifier
