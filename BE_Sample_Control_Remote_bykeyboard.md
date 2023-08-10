# BE_Sample_Control_Remote_bykeyboard 例程使用说明
## 1. 例程功能概括
该例程展示了如何 ***如何创建远程连接实例远程连接仿生眼设备*** 并通过按键 ***更改运动设置和控制仿生眼运动***  。

## 2 程序使用方法
根据 *path_to_workspace/BionicEyes/README.md* 中的流程成功编译仿生眼workspace后，所有sample程序的可执行文件保存在 *path_to_workspace/BionicEyes/bin/* 目录中，根据 *ubuntu* 操作系统的版本，程序的可执行文件被命名为：

>evo_be_Sample_Control_Remote_bykeyboard_2004  
>evo_be_Sample_Control_Remote_bykeyboard_1804  
>...

### 2.1 开启远端服务
要对仿生眼设备进行远程连接,需要确保已经有一台*PC*设备直连仿生眼设备并开启了仿生眼服务。具体方法如下:

在直连*PC*的 *path_to_workspace/BionicEyes/bin/* 目录下，根据对应设备不同，运行相应程序：
|设备        |可执行文件                     | 设备依赖库    |
|:--:       |:--:                         | :--:    |
|仿生眼III   |evo_be_Device_*               | libevo_be_Device_shared_*.so|
|仿生眼V     |evo_be_Device_5_*             | libevo_be_Device_5_shared_*.so|
|仿生鹰眼I   |evo_be_EEDevice_*             | libevo_be_EEDevice_shared_*.so|
|三轴平台    |evo_be_Device_ThreeAxis_*     | libevo_be_Device_ThreeAxis_shared_*.so|

**直连PC除运行上述设备程序外，还需要在另一个终端运行 evo_be_Device_Service_\* 用于启动仿生眼服务后，才能供远端PC检测到仿生眼系列设备的存在。**

***\*代表对应系统平台，例如1804代表ubuntu18.04***

直连仿生眼设备需要将个人PC与仿生眼设备相连。具体连接方法根据仿生眼设备的型号决定。

### 2.2 运行例程
直连_PC_开启仿生眼服务后, _**与直连PC处于同一个局域网下的**_ 远程 _PC_ 设备即可检测到仿生眼存在并进行连接,在远程 _PC_ 设备终端中进行如下操作(* 对应ubuntu系统版本):
1. `cd path_to_workspace/BionicEyes/bin/`
2. `./evo_be_Sample_Control_Remote_bykeyboard_*`

#### 2.2.1 选择连接设备
执行完毕上述操作后，远程PC将检测处于同一局域网下的所有仿生眼设备，并将其列出，供用户自行选择连接的设备：

**检测到多台设备时, 终端输出为：**
```
[2023-08-03 10:41:23.483] [EthernetSpec_Console] [info] EthComSpec: <<requestBeIpAddress>> Request to get be_device ip address...
[2023-08-03 10:41:26.319] [BE_Console] [info] BionicEyes: BionicEye device detected!!!
[2023-08-03 10:41:26.319] [BE_Console] [critical] BionicEyes: 0: BinoSense_BE150A28210000  192.168.31.88
[2023-08-03 10:41:26.319] [BE_Console] [critical] BionicEyes: 1: BinoSense_BE150A28210010  192.168.31.35
[2023-08-03 10:41:26.319] [BE_Console] [critical] BionicEyes: Please input the device Id(0,1...):

```
上述终端输出中，显示了远程连接例程检测到了两台仿生眼设备, 并将设备信息进行了输出，输出格式为：  
|         输出信息            |  含义   |
|:-------------------------:|:-------:|
|             0/1/2...      | 可选设备编号 |
| BinoSense_BE150A28210000  | 设备编号    |
|        192.168.31.88      | 设备IP地址  |

根据输出的可选设备编号，以及设备编号信息，在终端输入0或1、2...即可选择想要连接的设备。  
例如选择0号设备，在终端中输入0即可成功连接，如下所示：  
```
[2023-08-03 10:41:23.483] [EthernetSpec_Console] [info] EthComSpec: <<requestBeIpAddress>> Request to get be_device ip address...
[2023-08-03 10:41:26.319] [BE_Console] [info] BionicEyes: BionicEye device detected!!!
[2023-08-03 10:41:26.319] [BE_Console] [critical] BionicEyes: 0: BinoSense_BE150A28210000  192.168.31.88
[2023-08-03 10:41:26.319] [BE_Console] [critical] BionicEyes: 1: BinoSense_BE150A28210010  192.168.31.35
[2023-08-03 10:41:26.319] [BE_Console] [critical] BionicEyes: Please input the device Id(0,1...):
0 
[2023-08-03 11:00:05.724] [BE_Console] [info] BionicEyes: Ready to connect be device server(Data)....
[2023-08-03 11:00:05.726] [BE_Console] [info] BionicEyes: Ready to connect be device server(Control)...
[2023-08-03 11:00:05.726] [BE_Console] [info] BionicEyes: Ready to get remote config file....
[2023-08-03 11:00:05.727] [BEService_Console] [info] BEService: Request to download config file...
[2023-08-03 11:00:05.776] [BEService_Console] [info] BEService: BionicEyes config File have got!

```
==*注意：每次输出的可选设备编号并不固定，选择想要连接的设备的依据是设备编号（例如 BinoSense_BE150A28210000)*==

#### 2.2.2 输入参数选择

 - 选择运动基准:    
 仿生眼SDK共提供三种运动控制函数,其原型分别如下:    
 
	```c++
	virtual void setAbsolutePosition(bool activeFlag[],  
                                 float angle[],  
                                 BE_MovePatternType moveType = enumMovePattern_Saccade,  
                                 BE_MoveBaseType movebaseType = enumMoveBase_Independent);
	```
	```c++
	virtual void setRelativePosition(bool activeFlag[],  
                                 float angle[],  
                                 BE_MovePatternType moveType = enumMovePattern_Saccade,  
                                 BE_MoveBaseType movebaseType = enumMoveBase_Independent)
	```
	```c++
	virtual void setSpaceAbsPosition(float motorAngle[],  
                                 float motorMoveAngle[],  
                                 float imuAngle[],  
                                 BE_MovePatternType moveType = enumMovePattern_Saccade,  
                                 BE_MoveBaseType movebaseType = enumMoveBase_Independent)
	```
	***选择运动基准即在使用运动控制函数时选择参数BE_MoveBaseType movebaseType的值***  
	此参数的默认值为enumMoveBase_Independent(仿生眼各自独立运动).  
	2.2.1节中成功连接后,终端将有如下输出:
	```
	---------------------请选择仿生眼运动基准------------------------
	0:enumMoveBase_Independent->仿生眼各自独立运动
	1:enumMoveBase_LeftEye----->仿生眼基于左眼运动,右眼运动与左眼相同
	2:enumMoveBase_RightEye---->仿生眼基于右眼运动,左眼运动与右眼相同
	-----------------------------------------------------------------
	```
	根据输出信息,在终端中 **输入0或1或2** 即可选择参数movebaseType.
	
- 选择是否开启VOR:  
如前所述,仿生眼SDK中提供了三类运动控制函数, ***但可供选择的运动控制函数取决于VOR功能的启停状态***, ***当VOR功能开启时, setSpaceAbsPosition() 为唯一运动控制函数.且setSpaceAbsPosition() 只在VOR功能开启时起作用.***
故选择完毕movebaseType后,请选择是否开启VOR功能.
终端输出为:
	```
	-------------仿生眼SDK中提供了三类运动控制函数--------------
          0:setAbsolutePosition()
          1:setRelativePosition()
          2:setSpaceAbsPosition()
          注:VOR功能启动时, 2:setSpaceAbsPosition() 为唯一运动控制函数
   ------------------------------------------------------------
   **可供选择的运动控制函数取决于VOR功能的启停状态**
   请选择VOR工作状态:
   0:VOR关闭  1:VOR启动
   ```
   
   
   根据输出信息,在终端中**输入0或1**即可选择是否开启VOR功能.
   
#### 2.2.2.1 VOR功能关闭时  
终端将输出:  
 vor（维稳）功能关闭，运动控制函数 0:setAbsolutePosition() 1:setRelativePosition() 可供使用,请选择控制方式  
此时,在终端中输入**0或1**即可选择需要使用的控制函数.  
其中 ***setAbsolutePosition()*** 的原型为:
	
	```c++
	virtual void setAbsolutePosition(bool activeFlag[],  
                                 float angle[],  
                                 BE_MovePatternType moveType = enumMovePattern_Saccade,  
                                 BE_MoveBaseType movebaseType = enumMoveBase_Independent)
     ```
 

1. 参数bool activeFlag[]:  
	  bool 类型数组, 数组大小为6.  
	  以双目仿生眼为例,左右目两边各三个电机,共6个电机.传入参数activeFlag即可选择想要控制的电机.  
	  **activeFlag中6个元素分别控制的电机为:  
	  {左Pitch, 左Roll,左Yaw,右Pitch,右Roll,右Yaw}**  
	  例: activeFlag = {1,0,0,0,0,0}. 即激活左目控制俯仰角(Pitch)的电机.
2. 参数:float angle[]:  
	  float类型数组,数组大小为6.  
	  此参数为想要控制电机运动到的 **绝对位置** .  
	  例: activeFlag = {1,0,0,1,0,0} angle = {20,0,0,10,0,0}.即运动到左Pitch电机角度值为20度, 右Pitch电机角度值10度的所对应的位置.  
	  **注: 电机的角度值以仿生眼初始位置为基准.**  
	  
3. 参数BE_MovePatternType moveType  
	  仿生眼运动模式  
	  此参数选择为enumMovePattern_Saccade时, 为 **快速运动模式.**  
	  选择为enumMovePattern_SmoothPursuit时, 为 **平滑运动模式.**  
	  
4. 参数BE_MoveBaseType movebaseType  
	  仿生眼运动基准.   
	  此参数选择为: enumMoveBase_Independent时,**仿生眼将各自独立运动.**  
	  选择为enumMoveBase_LeftEye时, **仿生眼将基于左眼运动, 右眼将与左眼协同.**  
	  选择为enumMoveBase_RightEye时, **仿生眼将基于右眼运动,左眼将与右眼协同.**  
	 
***setRelativePosition()*** 的原型为:    
```c++
	virtual void setRelativePosition(bool activeFlag[],  
                                 float angle[],  
                                 BE_MovePatternType moveType = enumMovePattern_Saccade,  
                                 BE_MoveBaseType movebaseType = enumMoveBase_Independent)
```
	 
  此函数与setAbsolutePosition()的区别在于输入参数 float angle[] 的意义不同.  
	 **setAbsolutePosition() 会使仿生眼运动到给定的电机值所对应的绝对位置.  
	 setRelativePosition()会以当前位置为起点,运动角度为给定的电机运动角度.**  
	 例:若当前电机角度数据为:{20,10,10,10,10,10}, angle = {10,10,0,0,0,0}.  
	 仿生眼将运动到电机角度数据为{30,20,10,10,10,10}所对应的位置  
	 
	
#### 2.2.2.2 VOR功能开启时
运动控制函数将被自动确定为 ***setSpaceAbsPosition()*** ,此函数的原型为:    
```c++
	virtual void setSpaceAbsPosition(float motorAngle[],  
                                 float motorMoveAngle[],  
                                 float imuAngle[],  
                                 BE_MovePatternType moveType = enumMovePattern_Saccade,  
                                 BE_MoveBaseType movebaseType = enumMoveBase_Independent)
```
 **当VOR功能开启时, 电机将持续运动以维持VOR功能, 要控制仿生眼运动需要传入电机当前的角度(参数一:motorAngle[])与当前IMU数据(参数三:imuAngle[]).**  
1. 参数float motorAngle[]:  
电机当前角度值,此数据可通过:  
`BE_GeneralData data = device->getBeData();`  
获得.当前电机角度为: `data.motorData`.  
2. 参数float motorMoveAngle[]:  
相对于当前位置, 每个电机想要移动的角度.  
3. 参数float imuAngle[]:  
当前IMU数据,此数据可通过:  
`BE_GeneralData data = device->getBeData();`  
获得.当前电机角度为: `data.imuData[enumEuler]`.  
  
## 3 带注释代码一览
```c++  
#include <evo_be_Common.h>  
#include <opencv2/core.hpp>  
#include <opencv2/highgui.hpp>  
#include <opencv2/imgproc.hpp>  
#include <cmath>  
#include <X11/Xlib.h>  
#include <X11/keysym.h>  
#include <iomanip>  
using namespace evo_be;  
  
//for contorl by keyboard  
//XK_a XK_w XK_s XK_d  
static Display *be_keypress_dpy = XOpenDisplay(NULL);  
void printHelpMessage();  
void print_message(BE_GeneralData data);  
void move_control(CBionicEyes *device,bool VOR_flag, BE_MoveBaseType moveBaseType, int move_fuction);  
void move_fuc(CBionicEyes *device,bool VOR_flag, BE_MoveBaseType moveBaseType, int move_fuction, bool* active_flag,float* move_angle,BE_GeneralData data);  
void print_suc_mes(int movebase_type, bool VOR_flag,int move_fuction);  
  
  
  
static bool KEY_DOWN(KeySym keyValue_Linux)  
{  
    char keys_return[32];  
    XQueryKeymap(be_keypress_dpy, keys_return);  
    KeyCode kc2 = XKeysymToKeycode(be_keypress_dpy, keyValue_Linux);  
    return !!(keys_return[kc2 >> 3] & (1 << (kc2 & 7)));  
}  
  
int main()  
{  
    CBionicEyes *device = device->create(enumConnect_ImageControl);  //创建远程连接实例  
    // vor and sv function can affect eye_movement control  
    device->onoff_VOR(false);       //维持稳定 开启维稳功能 setRelativePosition 无法使用  
    device->onoff_SV(false);        //对齐功能需要在使用运动函数的时候，将BE_MoveBaseType设置为1或2,1是根据左眼运动，2是根据右眼运动。在进行slam的过程中，不需要一定使用对齐功能  
    device->goInitPosition(enumAllMotor);  
    //// 等待回零  
    msleep(1000);  
    int i = 0;  
    while (1){  
        std::cout<<"---------------------请选择仿生眼运动基准------------------------"<<std::endl;  
        std::cout<<"0:enumMoveBase_Independent->仿生眼各自独立运动"<<std::endl;  
        std::cout<<"1:enumMoveBase_LeftEye----->仿生眼基于左眼运动,右眼运动与左眼相同"<<std::endl;  
        std::cout<<"2:enumMoveBase_RightEye---->仿生眼基于右眼运动,左眼运动与右眼相同"<<std::endl;  
        std::cout<<"-----------------------------------------------------------------"<<std::endl;  
        int movebase_type;  
        //scanf("%d",&movebase_type);  
        cin>>movebase_type;  
        std::cout<<std::endl;  
  
        std::cout<<"-------------仿生眼SDK中提供了三类运动控制函数--------------"<<std::endl;  
        std::cout<<"          0:setAbsolutePosition()"<<std::endl;  
        std::cout<<"          1:setRelativePosition()"<<std::endl;  
        std::cout<<"          2:setSpaceAbsPosition()"<<std::endl;  
        std::cout<<"注:VOR功能启动时, 2:setSpaceAbsPosition() 为唯一运动控制函数"<<std::endl;  
        std::cout<<"------------------------------------------------------------"<<std::endl;  
        std::cout<<"**可供选择的运动控制函数取决于VOR功能的启停状态**"<<std::endl;  
        std::cout<<"请选择VOR工作状态:"<<std::endl;  
        std::cout<<"0:VOR关闭  1:VOR启动"<<std::endl;ls  
        bool VOR_flag;  
        cin>>VOR_flag;  
  
        int move_fuction;  
        i+=1;  
        ///当vor（维稳）功能打开时，运动控制函数setSpaceAbsPosition()可供使用  
        if(VOR_flag==1) {  
            std::cout<<"vor（维稳）功能打开，运动控制函数2:setSpaceAbsPosition()可供使用"<<std::endl;  
            move_fuction = 2;  
            device->onoff_VOR(true);  
  
            if (movebase_type == 0)   //enumMoveBase_Independent  
            {  
                print_suc_mes(movebase_type,VOR_flag,move_fuction);  
                move_control(device, VOR_flag, enumMoveBase_Independent,move_fuction);  
            }  
            if (movebase_type == 1) { //enumMoveBase_RightEye  
                print_suc_mes(movebase_type,VOR_flag,move_fuction);  
                move_control(device, VOR_flag, enumMoveBase_RightEye,move_fuction);  
            }  
            if(movebase_type == 2){   //enumMoveBase_LeftEye  
                print_suc_mes(movebase_type,VOR_flag,move_fuction);  
                move_control(device, VOR_flag, enumMoveBase_LeftEye,move_fuction);  
            }  
        }  
            ///当vor（维稳）功能关闭时，运动控制函数setAbsolutePosition()与setRelativePosition()可供使用  
        else{  
            std::cout<<"vor（维稳）功能关闭，运动控制函数 0:setAbsolutePosition() 1:setRelativePosition() 可供使用"<<std::endl;  
            std::cout<<"请选择控制方式"<<std::endl;  
            cin>>move_fuction;  
  
            if (movebase_type == 0)  
            {  
                print_suc_mes(movebase_type,VOR_flag,move_fuction);  
                move_control(device, VOR_flag, enumMoveBase_Independent,move_fuction);  
            }  
            if (movebase_type == 1) {  
                print_suc_mes(movebase_type,VOR_flag,move_fuction);  
                move_control(device, VOR_flag, enumMoveBase_RightEye,move_fuction);  
            }  
            if(movebase_type == 2){  
                print_suc_mes(movebase_type,VOR_flag,move_fuction);  
                move_control(device, VOR_flag, enumMoveBase_LeftEye,move_fuction);  
            }  
        }  
    }  
  
    return 0;  
}  
  
void printHelpMessage()  
{  
    printf("\  
usage: evo_be_App_Visualizer_1*04 \  
[--connect <mode>] [--server <mode>] [--receiverIP <ip>] [--serverIP <ip>]\n\  
options:\n\  
  -h --help      \tShow this help message\n\  -c --connect   \tConnect mode: i, c, ic\n\  -s --server    \tServer mode: lo, lf, do, df\n\  -rip --receiverIP\tIP of receiving device: xxx.xxx.xxx.xxx\n\  -sip --serverIP  \tIP of server device: xxx.xxx.xxx.xxx\n");  
};  
  
  
void move_control(CBionicEyes *device,bool VOR_flag, BE_MoveBaseType moveBaseType, int move_fuction)  
{  
    while (1) {  
        if (!(device->isBeDataReady())) {  
            msleep(100);  
            cout << "waiting..." << endl;  
            continue;        }  
        BE_GeneralData data = device->getBeData();  
        /// 左Pitch  
        if (KEY_DOWN(XK_w)) {  
            float move_angle[6] = {10, 0, 0, 0, 0, 0};  
            bool active_flag[6] = {1,0,0,0,0,0};  
            move_fuc(device, VOR_flag, moveBaseType,move_fuction,active_flag,move_angle, data);  
  
        }  
        if (KEY_DOWN(XK_s)) {  
            float move_angle[6] = {-10, 0, 0, 0, 0, 0};  
            bool active_flag[6] = {1,0,0,0,0,0};  
            move_fuc(device, VOR_flag, moveBaseType,move_fuction,active_flag,move_angle, data);  
        }  
        ///左Yaw  
        if (KEY_DOWN(XK_a)) {  
            float move_angle[6] = {0, 0, -10, 0, 0, 0};  
            bool active_flag[6] = {0,0,1,0,0,0};  
            move_fuc(device, VOR_flag, moveBaseType,move_fuction,active_flag,move_angle, data);  
  
        }  
        if (KEY_DOWN(XK_d)) {  
            float move_angle[6] = {0, 0, 10, 0, 0, 0};  
            bool active_flag[6] = {0,0,1,0,0,0};  
            move_fuc(device, VOR_flag, moveBaseType,move_fuction,active_flag,move_angle,data);  
  
        }  
        /// 右Pitch  
        if (KEY_DOWN(XK_Up)) {  
            float move_angle[6] = {0, 0, 0, 10, 0, 0};  
            bool active_flag[6] = {0,0,0,1,0,0};  
            move_fuc(device, VOR_flag, moveBaseType,move_fuction,active_flag,move_angle,data);  
  
        }  
        if (KEY_DOWN(XK_Down)) {  
            float move_angle[6] = {0, 0, 0, -10, 0, 0};  
            bool active_flag[6] = {0,0,0,1,0,0};  
            move_fuc(device, VOR_flag, moveBaseType,move_fuction,active_flag,move_angle,data);  
  
        }  
        /// 右Yaw  
        if(KEY_DOWN(XK_Left)){  
  
            float move_angle[6] = {0, 0, 0, 0, 0, -10};  
            bool active_flag[6] = {0,0,0,0,0,1};  
            move_fuc(device, VOR_flag, moveBaseType,move_fuction,active_flag,move_angle,data);  
  
        }  
        if(KEY_DOWN(XK_Right)){  
            float move_angle[6] = {0, 0, 0, 0, 0, 10};  
            bool active_flag[6] = {0,0,0,0,0,1};  
            move_fuc(device, VOR_flag, moveBaseType,move_fuction,active_flag,move_angle,data);  
  
        }  
        /// 回零  
        if (KEY_DOWN(XK_q)) {  
            bool active_flag[6] = {0,0,0,0,0,0};  
            float move_angle[6] = {0, 0, 0, 0, 0, 0};  
            move_fuc(device, VOR_flag, moveBaseType,3,active_flag,move_angle,data);  
  
        }  
        /// 重新选择控制方式. 按键c  
        if (KEY_DOWN(XK_c)) {  
            std::cout<<"请按照提示重新选择控制方式"<<std::endl;  
            std::cout<<std::endl;  
            return;        }  
    }  
}  
  
void move_fuc(CBionicEyes *device,bool VOR_flag, BE_MoveBaseType moveBaseType, int move_fuction, bool* active_flag,float* move_angle,BE_GeneralData data)  
{  
    /// 如果选择的是回零位功能, VOR打开时,goInitPosition()函数无法使用，如果想要回到默认零位，可以使用下面的方法  
    if(move_fuction==3)  
    {  
        if(VOR_flag)  
        {  
            float init_position[6] = {-data.motorData[0], -data.motorData[1], -data.motorData[2],  
                                      -data.motorData[3], -data.motorData[4], -data.motorData[5]};  
            device->setSpaceAbsPosition(data.motorData, init_position, data.imuData[enumEuler],  
                                        enumMovePattern_Saccade, moveBaseType);  
            msleep(200);  
            if(device->isBeDataReady())  
            {  
                print_message(data);  
            }  
  
  
        }  
        else  
        {  
            device->goInitPosition(enumAllMotor);  
            msleep(200);  
            BE_GeneralData data = device->getBeData();  
            if(device->isBeDataReady())  
            {  
                BE_GeneralData data = device->getBeData();  
                print_message(data);  
            }  
        }  
    }  
    /// 如果选择的是其他运动控制功能.  
    else  
    {  
        if(VOR_flag)  
        {  
  
            /// VOR 开启状态下, 仅setSpaceAbsPosition()可供使用, 仿生眼将运动到相对当前电机位置的给定位置.  
            //  传入电机的绝对位置的原因是:开启了维稳功能，需要通过电机imu数据 ,判断当前位置到底在哪里。  
            device->setSpaceAbsPosition(data.motorData, move_angle, data.imuData[enumEuler],  
                                        enumMovePattern_Saccade, moveBaseType);  
            msleep(200);  
            if(device->isBeDataReady())  
            {  
                BE_GeneralData data = device->getBeData();  
                print_message(data);  
            }  
        }  
        else  
        {  
            /// VOR关闭状态下, setAbsolutePosition() 与 setRelativePosition() 可供使用  
  
            /// 1.选择运动函数为setAbsolutePosition(), 仿生眼将运动到所给定的绝对位置.  
            if(move_fuction==0)  
            {  
                device->setAbsolutePosition(active_flag, move_angle, enumMovePattern_Saccade, moveBaseType);  
                msleep(200);  
                if(device->isBeDataReady())  
                {  
                    BE_GeneralData data = device->getBeData();  
                    print_message(data);  
                }  
            }  
            /// 2.选择运动函数为setRelativePosition(), 每次触发此函数,仿生眼将运动到相对当前电机位置的给定位置.  
            // 例如若当前位置为: {10,0,0,0,0,0}, 给定move_angle为{10,0,0,0,0,0},仿生眼将运动到 {20,0,0,0,0,0}            if(move_fuction==1)  
            {  
                device->setRelativePosition(active_flag,move_angle, enumMovePattern_Saccade, moveBaseType);  
                msleep(200);  
                if(device->isBeDataReady())  
                {  
                    BE_GeneralData data = device->getBeData();  
                    print_message(data);  
                }  
            }  
        }  
    }  
}  
  
void print_message(BE_GeneralData data)  
{  
    std::cout<<std::endl;  
    std::vector<std::vector<std::string>> table = {  
            {"lefe_Pitch", "left_Yaw", "left_Row","right_Pitch","right_Yaw","right_Row"},  
            {std::to_string(data.motorData[0]), std::to_string(data.motorData[1]), std::to_string(data.motorData[2]),std::to_string(data.motorData[3]),std::to_string(data.motorData[4]),std::to_string(data.motorData[5])},  
    };  
  
//    printTable(table);  
    cout << "------------------------当前电机位置----------------------------------"<<endl;  
    for (int i = 0; i < 6; i++)  
        cout <<setw(10)<<data.motorData[i];  
    cout<<endl;  
    cout << "----------------------------------------------------------------------";  
    cout << endl;  
    cout << "当前IMU数据:";  
    for (int i = 0; i < 3; i++)  
        cout << data.imuData[enumEuler][i] << " ";  
    std::cout<<std::endl;  
}  
  
void print_suc_mes(int movebase_type, bool VOR_flag,int move_fuction)  
{  
    std::vector<std::string> movebase_types = {"仿生眼各自独立运动","仿生眼基于左眼运动, 右眼运动与左眼运动相同","仿生眼基于右眼运动, 左眼运动与右眼运动相同"};  
    std::vector<std::string>  VOR_states = {"关闭","启动"};  
    std::vector<std::string>  move_fuctions = {"setAbsolutePosition() ,仿生眼将运动到设定的绝对位置","setRelativePosition(), 仿生眼将运动到当前位置的相对位置","setSpaceAbsPosition(), 仿生眼将维持VOR功能的前提下, 运动到当前位置的相对位置"};  
    std::vector<std::string>  control_note = {" wasd 以及 上下左右 键盘方向键分别控制左右眼单独运动","wasd控制左眼运动且右眼将同步运动","上下左右控制右眼运动且左眼将同步运动"};  
    std::cout<<"当前参数选择为:"<<std::endl;  
    std::cout<<"仿生眼运动基准:    "<<movebase_types[movebase_type]<<std::endl;  
    std::cout<<"VOR启停状态:       "<<VOR_states[static_cast<int>(VOR_flag)]<<std::endl;  
    std::cout<<"使用运动控制函数:  "<<move_fuctions[move_fuction]<<std::endl;  
    std::cout<<"当前状态下,可使用"<<control_note[movebase_type]<<std::endl;  
    std::cout<<"按q仿生眼将恢复零位, 按c可重新选择控制方式."<<std::endl;  
}
```






