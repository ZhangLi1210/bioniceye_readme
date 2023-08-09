# BE_Sample_SaveData 例程使用说明
## 1. 例程功能概括
该例程展示了如何 ***在创建连接实例后快速自动保存仿生眼数据*** ,  ***间隔帧保存数据*** 以及 ***保存某一帧数据***。

## 2 程序使用方法
根据 *path_to_workspace/BionicEyes/README.md* 中的流程成功编译仿生眼workspace后，所有sample程序的可执行文件保存在 *path_to_workspace/BionicEyes/bin/* 目录中，根据 *ubuntu* 操作系统的版本，程序的可执行文件被命名为：

>evo_be_Sample_SaveData_2004  
>evo_be_Sample_SaveData_1804  
>...

### 2.1 开启远端服务
SaveData 例程中创建的为远程连接实例，故需要确保已经有一台*PC*设备直连仿生眼设备并开启了仿生眼服务。具体方法如下:

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

### 2.2 运行savedata例程
直连_PC_开启仿生眼服务后, _**与直连PC处于同一个局域网下的**_ 远程 _PC_ 设备即可检测到仿生眼存在并进行连接,在远程 _PC_ 设备终端中进行如下操作(* 对应ubuntu系统版本):
1. `cd path_to_workspace/BionicEyes/bin/`
2. `./evo_be_Sample_SaveData__*`

执行完毕上述操作后，远程PC首先将检测处于同一局域网下的所有仿生眼设备，并将其列出，供用户自行选择连接的设备（检测到单台设备时，程序将直接连此单台设备）：

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


连接成功后，例程将开始保存仿生眼设备数据。数据的默认保存地址为：
/usr/Evo_BionicEyes/io_path/

### 2.3 BE_Sample_SaveData 程序详解
#### 2.3.1 创建连接实例
```c++
int main(int argc, char *argv[])  
{  
    CBionicEyes *device = device->create(enumConnect_ImageControl, enumDeviceServer_First);
   ...
   ...
}  
```
此处创建的为远程连接实例。
***[注]: 若创建直连实例以使用保存数据功能***
例: `CBionicEyes *device = device->create();`
参数: ***openNetworkService 请选择为默认值 true.*** 此参数开启时, 开启直连实例的电脑将被允许可以开启远端服务供远程设备连接, 但若没有远程PC连接时, 并不会提高CPU的使用.


#### 2.3.2 设置传输帧率
通过类 CBionicEyes 创建连接实例后，可以调用类CBionicEyes中包含的所有方法，包括获取仿生眼数据，设置仿生眼各项参数， 控制仿生眼运动等。此处通过调用设置传输帧率的函数以设置传输图像时的帧率。
```c++
int main(int argc, char *argv[])  
{  
   ...
   ...
   // 通过更改参数,即可更改传输图像时的帧率，此时的帧率为5.
   // 远程连接时，帧率的稳定性取决于网络传输速度。
   device->setDataRate_Transfer(5);
   ...
   ...
}  
```
#### 2.3.3 快速保存数据（saveBeData）
使用 **saveBeData（）** 函数保存数据是一种**快速保存**的方式，其保存数据时将按照设定的帧率，将每一帧数据都进行保存（远程连接时帧率的稳定性取决于网络传输速度）。
```c++
int main()  
{  
    ...
    ...
    //// start save be_data stream, using default folder  
    device->saveBeData();  
    ...
    ...
    //// stop saving be_data stream  
    device->saveBeData(0);  
    ...
    ... 
}
```
设置完传输图像时的帧率后，即可通过调用saveBeData()函数开始保存数据。saveBeData函数原型如下：
 ```c++
virtual void saveBeData(bool startStop = 1, string folderPath = Be_Io_Path + "fastSave") = 0;
```
 - 参数startStop:
 **使用saveBeData()保存数据时，saveBeData函数需要被调用两次:**
第一次调用：参数startStop须为**true** (1), 调用后，程序开始保存数据流。
第二次调用：参数startStop须为**false** (0),调用后，程序结束保存。
**数据开始保存与结束之间的时间，取决于两次调用此函数的时间间隔。**

 - 参数folderPath
 folderPath的默认值为:/usr/Evo_BionicEyes/io_path/fastSave, 此参数若不设置，接收的数据将保存在此默认文件夹中，若设置
 例：`device->saveBeData(1,“/xxx/myfastsave”);  `
 程序将在设置的保存路径中（/xxx/），尝试新建文件夹 myfastsave（如果myfastsave文件夹存在则不新建），并将接受的数据保存在此文件夹下。

使用saveBeData（）函数保存数据的文件夹下存在如下内容：
| 文件或文件夹名称 |          保存内容            |
|:--:           |                        :--:|
| Cam_0         |   仿生鹰眼四图之一   |
| Cam_1         |   仿生鹰眼四图之一 |
| Cam_2         |   仿生鹰眼四图之一或双目仿生眼左右合并图像 |
| Cam_3         | 仿生鹰眼四图之一 |
| 0_data.csv    | 仿生眼除图像外的各种数据：图像ID、设备编号、时间戳、IMU数据等 |
***注意：***
/////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
使用
`device->setImageResolution_Transfer(cv::Size(2880,1080));`
设定使用saveBeData函数获取的双目图像的分辨率时，cv::Size(2880,1080)是左右图合并之后的分辨率，即想要得到单个图像的分辨率是（1440, 1080）时，传入的参数应为（2880,1080）。
//////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

#### 2.3.4 间隔帧提取数据((snapBeData)
当需要在当前帧率下间隔提取帧数据时，可以使snapBeData（）。
具体方法如下：
```c++
int main()  
{  
    ...
    ...
		///// snap data example  
		int i = 0;  
		while(i < 50)  
		{  
		    // Snap be_data every 0.2 second, using default folder
		    // 下列代码将当前时刻，帧的图像与设备数据进行保存
		    device->snapBeData("This is snap Sample", Be_Io_Path + "snapSave");
		    // 等待0.2秒后，snapBeData再一次被执行，0.2秒后的帧的图像与设备数据被保存
		    msleep(200);  
		    i++;  
		} 
	    ///// snap data example in new folder  
	    //想要在另外一个文件夹下保存间隔帧图像时
	    //需要执行device->stopSnapBeData(); 
	    //并更改文件夹地址，重新执行snapBeData(）
		device->stopSnapBeData();  
		msleep(50);  
		while(i < 100)  
		{  
		    // Snap be_data every 0.2 second, using default folder  
		    device->snapBeData("This is snap Sample", Be_Io_Path + "newSnapSave");  
		    msleep(200);  
		    i++;  
		}	
    ...
    ... 
}
```
SnapBeData函数原型如下：
```c++
virtual bool snapBeData(string description="", string folderPath = Be_Io_Path + "snapSave") = 0;
```
 - 参数description:
 使用snapBeData间隔提取帧数据时的说明，在0_data.csv中进行了保存。

 - 参数folderPath
 folderPath的默认值为:/usr/Evo_BionicEyes/io_path/fastSave, 此参数若不设置，接收的数据将保存在此默认文件夹中，若设置
 例：`device->snapBeData("",“/xxx/mysnapsave”);  `
 程序将在设置的保存路径中（/xxx/），尝试新建文件夹 mysnapsave（如果mysnapsave文件夹存在则不新建），并将接受的数据保存在此文件夹下。

使用snapBeData(）函数保存数据的文件夹下存在的内容格式与saveBeData（）相同。


#### 2.3.5 提取保存某一帧数据（recordBeData）
有时在某一帧数据满足一定的条件时，需要将此帧的所有数据进行保存，此时可以使用recordBeData（）。具体方法如下：
```c++
int main()  
{  
    ...
    ...
    //// record data example  
    while(i < 150)  
    {  
        if (device->isBeDataReady()) {
          
            BE_GeneralData data = device->getBeData();  
			// 在这里可以判断当前帧数据是否满足一定的条件，如果满足
			// 可以使用recordBeData（）将当前帧所有数据进行保存
		   // 保存格式与saveBeData和snapBeData相同。
            if(i % 5 == 0)  
                device->recordBeData(data, "This is record Sample", Be_Io_Path + "recordSave");  
        }  
        msleep(100);  
        i++;  
    }  
    ///// record data example in new folder
    //想要在另外一个文件夹下保存满足条件的帧数据
	  //需要执行device->stopRecordBeData(); 
	  //并更改文件夹地址，重新执行recordBeData 
    device->stopRecordBeData();  
    msleep(50);  
    while(i < 200)  
    {  
        if (device->isBeDataReady()) {  
            BE_GeneralData data = device->getBeData();  
  
            if(i % 5 == 0)  
                device->recordBeData(data, "This is record Sample", Be_Io_Path + "NewRecordSave");  
        }  
        msleep(100);  
        i++;  
    }
    ...
    ...     
}
```
recordBeData函数原型如下：
 ```c++
virtual bool recordBeData(BE_GeneralData &data, string description="", string folderPath = Be_Io_Path + "recordSave") = 0;;
```
 - 参数data:
使用 *BE_GeneralData* 结构体创建的结构体变量，传输函数时，其保存了使用 *device->getBeData()* 获取的当前帧的仿生眼数据。

 - 参数description:
 使用recordBeData 保存某一帧数据时的说明，在0_data.csv中进行了保存。
 
 - 参数folderPath
 folderPath的默认值为:/usr/Evo_BionicEyes/io_path/fastSave, 此参数若不设置，接收的数据将保存在此默认文件夹中，若设置
 例：`device->RecordBeData(data,"", “/xxx/myrecordsave”);  `
 程序将在设置的保存路径中（/xxx/），尝试新建文件夹 myrecordsave（如果myrecordsave文件夹存在则不新建），并将接受的数据保存在此文件夹下。
