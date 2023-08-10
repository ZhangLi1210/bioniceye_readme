# BE_Sample_GrabImage_Remote 例程使用说明
## 1. 例程功能概括
该例程展示了如何 ***如何创建远程连接实例远程连接仿生眼设备*** 并 ***获取仿生眼数据.*** 。

## 2 程序使用方法
根据 *path_to_workspace/BionicEyes/README.md* 中的流程成功编译仿生眼workspace后，所有sample程序的可执行文件保存在 *path_to_workspace/BionicEyes/bin/* 目录中，根据 *ubuntu* 操作系统的版本，程序的可执行文件被命名为：

>evo_be_Sample_GrabImage_Remote_2004  
>evo_be_Sample_GrabImage_Remote_1804  
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

### 2.2 运行远程连接例程
直连_PC_开启仿生眼服务后, _**与直连PC处于同一个局域网下的**_ 远程 _PC_ 设备即可检测到仿生眼存在并进行连接,在远程 _PC_ 设备终端中进行如下操作(* 对应ubuntu系统版本):
1. `cd path_to_workspace/BionicEyes/bin/`
2. `./evo_be_Sample_GrabImage_Remote_*`

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


**检测到单台设备时, 终端输出为：**
```
[2023-08-03 11:07:30.606] [BE_Console] [info] BionicEyes: BionicEye device detected!!!class EVO_BIONICEYES_API CBionicEyes
[2023-08-03 11:07:30.607] [BE_Console] [critical] BionicEyes: 0: BinoSense_BE150A28210000  192.168.31.88
[2023-08-03 11:07:30.611] [BE_Console] [info] BionicEyes: Ready to connect be device server(Data)....
[2023-08-03 11:07:30.616] [BE_Console] [info] BionicEyes: Ready to connect be device server(Control)...
[2023-08-03 11:07:30.617] [BE_Console] [info] BionicEyes: Ready to get remote config file....
[2023-08-03 11:07:30.618] [BEService_Console] [info] BEService: Request to download config file...
[2023-08-03 11:07:30.709] [BEService_Console] [info] BEService: BionicEyes config File have got!
```
根据终端输出，检测到单台设备时，例程将直接连接此设备。

连接成功后，仿生眼获取的图像将以窗口的形式显示。

### 2.3 BE_Sample_GrabImage_Remote程序详解
#### 2.3.1 创建连接实例
```c++
int main(int argc, char *argv[])  
{  
    //类CBionicEyes中，提供了创建连接实例的接口，以及创建连接实例后可以
    //通过实例调用的所有方法。见第3节: 类 CBionicEyes 一览。
    CBionicEyes *device = device->create(enumConnect_ImageControl, evo_be::enumDeviceServer_Only);
   ...
   ...
}  
```
在仿生眼的SDK中，提供了三种创建连接实例的方法。

**创建直连实例的方法原型为:**
```c++
static CBionicEyes *create(bool openNetworkService = true, int dataDefaultFrameRate = 25, void *logger_ptr = NULL);
```

**两种创建远程实例的方法原型分别为：**

1.  
```c++
static CBionicEyes *create(BE_Connect_Type type,  
                           BE_Connect_DataServerType dataServerType = enumDeviceServer_Only,  
                           BE_Data_TransmissionType dataTransmissionType = enumDataTransmission_ASPAP,  
                           void *logger_ptr = NULL);
```
2. 
```c++
static CBionicEyes *create(string ipAddr,  
                           BE_Connect_Type type = enumdisConnect,  
                           BE_Connect_DataServerType dataServerType = enumDeviceServer_Only,  
                           BE_Data_TransmissionType dataTransmissionType = enumDataTransmission_ASPAP,  
                           void *logger_ptr = NULL);
```
***创建连接实例的create函数为重载函数，它会根据传入参数的类型自动选择创建连接实例的方法。***
 以 *BE_Sample_GrabImage_Remote*  例程为例，创建远程连接实例的代码为：
 ```c++
 CBionicEyes *device = device->create(enumConnect_ImageControl, evo_be::enumDeviceServer_Only);
 ```
 根据传入参数的情况，调用的函数原型为：
 ```c++
static CBionicEyes *create(BE_Connect_Type type,  
                           BE_Connect_DataServerType dataServerType = enumDeviceServer_Only,  
                           BE_Data_TransmissionType dataTransmissionType = enumDataTransmission_ASPAP,  
                           void *logger_ptr = NULL);
```
使用此函数原型在创建创建远程连接实例后，程序运行过程中将检测同一局域网下的所有可连接的仿生眼设备供用户自行选择设备连接（如2.2节所示）。
三种创建连接实例方法的各参数意义与选择方式见 evo_be_common.h。

#### 2.3.2 通过连接实例进行初始化
通过类 CBionicEyes 创建的连接实例，可以调用类CBionicEyes中包含的所有方法，包括获取仿生眼数据，设置仿生眼各项参数， 控制仿生眼运动等。具体信息见第3节: **类 CBionicEyes 一览**。
sample中下列部分简单显示了如何调用这些方法：
```c++
int main(int argc, char *argv[])  
{  
	...
	...
	//    device->setImageResolution_Transfer(cv::Size(1920,540));  
	//    device->setImageResolution(cv::Size(3840,1080));  
	//    device->setImageColor_Transfer(enumColor);  
	//    device->setImageCompressionQuality(95);  
	//    device->setImageColor(enumMono);  
	//    //cout<< device->getMaxImageFrameRate()<<endl;  
	//    device->setDataRate_Transfer(25);
   ...
   ...
}  
```
#### 2.3.3 获取图像数据
```c++
int main()  
{  
    ...
    ...
    while (1)  
    {  
        // 调用isBeDataReady()方法判断设备数据是否准备完毕
        // 在调用getBeData()获取数据时需要执行此方法
        if (device->isBeDataReady())  
        {  
            // BE_GeneralData 为保存数据的结构体
            // 仿生眼SDK中将所有接收数据的格式与种类集成在
            // BE_GeneralData 结构体中
            // 通过 BE_GeneralData 结构体创建结构体变量data
            // 并执行device->getBeData()即可获取当前仿生眼数据;
            // 并存储在结构体变量data的各项结构体成员中
            // 包括图像数据、数据、图像ID，时间戳等等
            BE_GeneralData data = device->getBeData();  
            for(int i = 0; i< MAXCAMERASInDEVICE; i++)  
            {  
                if(data.imageFlag[i])   // MAXCAMERASInDEVICE 4  
              {  
                    if(i == 3)  
                    {  
                        cv::Mat infraImage, tempImage;  
  
                        if(device->infraImageTrans(data, infraImage, tempImage))  
                        {  
                            cv::imshow("BE_Image_" + to_string(i), infraImage);  
                        }  
                    } else  
                    {  
                        cv::Mat temp;  
                        cv::resize(data.image[i], temp, cv::Size(data.image[i].cols >> 1, data.image[i].rows >> 1));  
                        cv::imshow("BE_Image_" + to_string(i), temp);  
                    }  
                }  
            }  
        }  
        cv::waitKey(40);  
    }  
    return 0;  
} 
```
**BE_GeneralData 结构体说明:**  
***BE_GeneralData*** 是定义在头文件 ***evo_be.h*** 的结构体。该结构体中包含了所有仿生眼基础同步数据以及一些读写的内联函数。  
`结构体中的数据成员如下表所示。`
| 类型 | 成员 | 含义 |
| :---:|:---:  |:----:|
| cv::Mat | image[*MAXCAMERASInDEVICE*] |  Mat类数组，用于存放仿生眼设备获取的图像  |
| uint32_t | id |   唯一全局id |
| uint32_t | timeStamp |   时间戳（100 us）   |
| BE_IpInfo| be_device_info |   仿生眼设备信息，详细请参见结构体 *BE_IpInfo*  |
| bool | imageFlag[*MAXCAMERASInDEVICE*] |   仿生眼图像存在标志  |
| BE_ImageInfo|  imageInfo[*MAXCAMERASInDEVICE*] |  仿生眼图像信息，详细请参见结构体 *BE_ImageInfo*|
| float | motorData[6] |   电机编码器数据 |
| float | imuData[*MAXIMUBUFFER*][4] | IMU数据 |
| double | gpsData[4] |  GPS数据（经度、纬度、高度、GMT） |
| bool |   isMovingFastly |  获取这些数据时眼睛是否快速移动   |

`表格中定义的常量 *MAXCAMERASInDEVICE* = 4，*MAXIMUBUFFER* = 16。`

**注：Mat 类的数组 *image* 定义为包含四个元素的数组，在连接双目仿生眼时，从仿生眼设备中获取的两张图像会左右拼接为一张图像并存储在 *image[2]* 中。 如果连接的是仿生鹰眼，则从设备中获取的四个图像会分别存储在*image* 数组的四个元素中。同理，*imageFlag* 和 *imageInfo* 的存储逻辑相似。**

## 3 类 CBionicEyes 一览
```c++
class EVO_BIONICEYES_API CBionicEyes  
{  
public:  
       /**************************************************  
        * Creating BinoSense device instances for direct connection use.        
        * @brief Creating BinoSense device instances        
        * @param openNetworkService:          
        * Set #True# to use network service and support remote connect, 
        * or set false to   decrease CPU usage.        
        * @param dataDefaultFrameRate:          Set default frame(including image， motor coder， IMU data, and so on..)        
        * acquisition rate. You can still change it by calling function setDataRate. Max accepted value is 200. In some device        
        * and some image resolution setting, this value may be less than 200.\n        
        * For example CBionicEyes *device = device->create(false, 50);        * @note When setting openNetworkService true, you still need to start evo_be_Device_Service_**** program to support        * remote user.        
        ***************************************************/   
        static CBionicEyes *create(bool openNetworkService = true, int dataDefaultFrameRate = 25, void *logger_ptr = NULL);  
  
  
       /**************************************************  
        * Creating BinoSense device instances for remote connection use. When use this to create instance, all        
        * devices in local network will be detected and listed, you can choose which device you want to use.        
        * @brief Creating BinoSense device instances.        
        * @note One instance can only connect one BinoSense device. If you have more BinoSense to connect, more        
        * create functions should be called.        
        * @param type:             Device connect type, for example, enumConnect_ImageControl means you can        
        * control device and get its data including image info.        
        * @param dataServerType:   You can choose connect local server to get device data or connect device to        
        * get data directly. When local server is chosen, a local server must be started, or you can't get any data.        
        * @param dataTransmissionType:     You can decide to get newest frame as soon as possible, or get every frame        
        * with any frame lost.\n        
        * For example CBionicEyes *device = device->create(enumConnect_ImageControl);        
        * **************************************************/  
         
        static CBionicEyes *create(BE_Connect_Type type,  
                                   BE_Connect_DataServerType dataServerType = enumDeviceServer_Only,  
                                   BE_Data_TransmissionType dataTransmissionType = enumDataTransmission_ASPAP,  
                                   void *logger_ptr = NULL);  
  
       /**************************************************  
        * Creating BinoSense device instances for remote connection use. This function will connect device which IP        
        * address is same to given IP address.        
        * @brief Creating BinoSense device instances.        
        * @note One instance can only connect one BinoSense device. If you have more BinoSense to connect, more        
        * create functions should be called.        
        * @param type:             Device connect type, for example, enumConnect_ImageControl means you can        
        * control device and get its data including image info.        
        * @param dataServerType:   You can choose connect local server to get device data or connect device to        
        * get data directly. When local server is chosen, a local server must be started, or you can't get any data.        
        * @param dataTransmissionType:     You can decide to get newest frame as soon as possible, or get every frame        
        * with any frame lost.\n        
        * For example CBionicEyes *device = device->create("192.168.1.20", enumConnect_ImageControl);        
        **************************************************/   
        static CBionicEyes *create(string ipAddr,  
                                   BE_Connect_Type type = enumdisConnect,  
                                   BE_Connect_DataServerType dataServerType = enumDeviceServer_Only,  
                                   BE_Data_TransmissionType dataTransmissionType = enumDataTransmission_ASPAP,  
                                   void *logger_ptr = NULL);  
  
  
       /**************************************************  
        * @brief Destructor        
        ***************************************************/   
        virtual ~CBionicEyes();  
  
  
       /**************************************************  
        * @brief Get connected BinoSense device IP address(32 bits integer).        
        ***************************************************/   
        * virtual uint32_t getBeDevice_Ip() = 0;  
  
       /**************************************************  
        * @brief Get connected BinoSense device type.        
        ***************************************************/       
        virtual BE_Device_Type getBeDeviceType() = 0;  
  
       /**************************************************  
        * @brief Get connected BinoSense device IP address(string type, like 192.168.1.1).       
        ***************************************************/   
        virtual string getBeDevice_Ip_str() = 0;  
  
       /**************************************************  
        * Set image resolution(SideBySide size!)(BE_ImageGeneralInfo.image, only for remote use)        
        * @brief Creating Set image resolution.        
        * @note This will affect function "getBeData" returning image size, but don't affect network transform image size.        
        * @param size:     set image size, default (3840, 1080)        
        * @param cameraId:     set specified camera, see  !!!!(有问题)      
        ***************************************************/   
        virtual void setImageResolution(cv::Size size = cv::Size(1920, 1080)) = 0;  
  
       /**************************************************  
        * Set network transfer image resolution(SideBySide size!), if network isn't ideal, you could down sample the transfer image        
        * @brief Creating Set network transfer image resolution.        
        * @note This won't affect function "getBeData" returning image size.        
        * @param size:     set image size, default (1920, 540)        
        ***************************************************/  
        virtual void setImageResolution_Transfer(cv::Size size = cv::Size(960, 540)) = 0;  
  
       /**************************************************  
        * Set image color (BE_ImageGeneralInfo.colorType)        
        * @brief Set image color        
        * @note This will affect function "getBeData" returning image color type.        
        * @param type:     set image color type, default enumColor        
        ***************************************************/  
        virtual void setImageColor(ImageColorType type = enumColor) = 0;  
  
       /**************************************************  
        * @brief Set network transfer image color        
        * @note This won't affect function "getBeData" returning image color type.        
        * @param type:     set image color type, default enumColor        
        ***************************************************/   
        virtual void setImageColor_Transfer(ImageColorType type = enumColor) = 0;  
  
       /**************************************************  
        * @brief Set network transfer frame rate        
        * @param rate:     set transfer frame rate, default 25.0        
        ***************************************************/   
        virtual void setDataRate_Transfer(float rate = 25.0f) = 0;  
  
       /**************************************************  
        * @brief Get origin camera image resolution        
        ***************************************************/   
        virtual cv::Size getOriginImageResolution() = 0;  
  
       /**************************************************  
        * @brief Get max image frame rate        
        **************************************************/   
        virtual float getMaxImageFrameRate() = 0;  
  
       /**************************************************  
        * @brief Set image loseless compression or lossy compression (only for network transfer)        
        * @note loseless compression: imgQual = 100; lossy compression: imgQual (0 = worst, 99 = best)        
        * **************************************************/   
        virtual void setImageCompressionQuality(int imgQual = 95) = 0;  
  
       /**************************************************  
        * @brief Determine if the data is ready.        
        * @note Before calling "getBeData", you should ensure that this function return True.        
        ***************************************************/   
        virtual bool isBeDataReady() = 0;  
  
       /**************************************************  
        * @brief Get bedata.        
        * @note Thread safe and returned data has be copied.(Deep copy)        
        * @note Before calling this function, you should call        
        * isBeDataReady first and ensure function isBeDataReady return True.        **************************************************/       
        virtual void getBeData(BE_GeneralData &data) = 0;  
  
       /**************************************************  
        * @brief Get bedata.        
        * @note Thread safe and returned data has be copied.(Deep copy)        
        * @note Before calling this function, you should call        
        * isBeDataReady first and ensure function isBeDataReady return True.        **************************************************/       
        virtual BE_GeneralData getBeData() = 0;  
  
       /**************************************************  
        * @brief Get bedata.        
        * @note Thread safe and returned data has be copied.(Deep copy)        
        * @note Before calling this function, you should call        * 
        * isBeDataReady first and ensure function isBeDataReady return True.        **************************************************/       
        virtual bool getBeData(BE_GeneralData &data, uint id) = 0;  
  
       /**************************************************  
        * @brief Get Bionic Eye Calibration Data        
        * @note Caller is responsible for freeing the returned buffer.        
        ***************************************************/   
        virtual uchar *getCalibrationInfo() = 0;  
  
       /**************************************************  
        * @brief Get motor up and down limit position(For eye).        
        * @note Type(enumAllMotor) is not supported.        
        * @param type:     Choose which motor you want to get up and down limit.        
        * @param up:       Float data reference, return up limit value        * 
        * @param down:     Float data reference, return down limit value        
        * **************************************************/   
        virtual bool getUpDownLimit(MotorType type, float &up, float &down) = 0;  
  
       /**************************************************  
        * @brief Get motor up and down limit position(For neck)        
        * @note Type(enumNeckAllMotor) is not supported.        
        * @param type:     Choose which motor you want to get up and down limit.        
        * @param up:       Float data reference,        
        * @param down:     Float data reference,        
        ***************************************************/   
        virtual bool getUpDownLimit_Neck(MotorType_Neck type, float &up, float &down) = 0;  
  
       /**************************************************  
        * @brief Set eye absolute speed(deg/s).        
       * @param activeFlag:     Bool data array(size = 6). Choose which motor or motors you want to set speed. If you set        
       * activeFlag[id] = false; the no.id motor will auto set speed to 0.        
       * @param speed:    Float data array(size = 6). Only for chosen motor, the value will work.        
       ***************************************************/       
       virtual void setAbsoluteSpeed(bool activeFlag[], float speed[]) = 0;  
  
       /**************************************************  
        * @brief Set eye absolute position        
        * @param type:     Choose which motor or all motors you want to send move cmd        
        * @param angle:    Float data array(size = 6). For chosen motor, the value will work.        
        * @param moveType:     Set move type        
        * @param movebaseType: Set move base Type        
        ***************************************************/      
        virtual void setAbsolutePosition(MotorType type,  
                                        float angle[],  
                                        BE_MovePatternType moveType = enumMovePattern_Saccade,  
                                        BE_MoveBaseType movebaseType = enumMoveBase_Independent) = 0;  
  
       /**************************************************  
        * @brief Set Eye relative Position        
        * @param type:     Choose which motor or all motors you want to send move cmd        
        * @param angle:    Float data array(size = 6). For chosen motor, the value will work.        
        * @param moveType:     Set move type        
        * @param movebaseType: Set move base Type        
        ***************************************************/       
        virtual void setRelativePosition(MotorType type,  
                                        float angle[],  
                                        BE_MovePatternType moveType = enumMovePattern_Saccade,  
                                        BE_MoveBaseType movebaseType = enumMoveBase_Independent) = 0;  
  
       /**************************************************  
        * @brief Set eye absolute position        
        * @param activeFlag:     Bool data array(size = 6). Choose which motor or motors you want to send move cmd        
        * @param angle:    Float data array(size = 6). Only for chosen motor, the value will work.        
        * @param moveType:     Set move type        
        * @param movebaseType: Set move base Type        
        * **************************************************/   
        virtual void setAbsolutePosition(bool activeFlag[],  
                                  float angle[],  
                                  BE_MovePatternType moveType = enumMovePattern_Saccade,  
                                  BE_MoveBaseType movebaseType = enumMoveBase_Independent) = 0;  
  
       /**************************************************  
        * @brief Set eye relative position.        
        * @param activeFlag:     Bool data array(size = 6). Choose which motor or motors you want to send move cmd        
        * @param angle:    Float data array(size = 6). Only for chosen motor, the value will work.        
        * @param moveType:     Set move type        
        * @param movebaseType: Set move base Type        
        ***************************************************/   
        virtual void setRelativePosition(bool activeFlag[],  
                                  float angle[],  
                                  BE_MovePatternType moveType = enumMovePattern_Saccade,  
                                  BE_MoveBaseType movebaseType = enumMoveBase_Independent) = 0;  
  
       /**************************************************  
        * Set space Eye absolute Position （Absolute space）  
        * @brief Set space Eye absolute Position        
        * @note Only when device is on VOR mode, this function can work!        
        * @param motorAngle:     Float data array(size = 6). Input current all motor value.        
        * @param motorMoveAngle:    Float data array(size = 6). Set motor move angle value.        
        * @param imuAngle:    Float data array(size = 3). Input current all IMU value.        
        * @param moveType:     Set move type        
        * @param movebaseType: Set move base Type        
        ***************************************************/   
        virtual void setSpaceAbsPosition(float motorAngle[],  
                                  float motorMoveAngle[],  
                                  float imuAngle[],  
                                  BE_MovePatternType moveType = enumMovePattern_Saccade,  
                                  BE_MoveBaseType movebaseType = enumMoveBase_Independent) = 0;  
                                  
  
       /**************************************************        
       * @brief Set neck absolute position (If neck exists)        
       * @note Type(enumNeckAllMotor) is not supported.        
       * @param activeFlag:   Bool data array(size = 3). Choose which motor or motors you want to send move cmd        
       * @param angle:        Float data array(size = 3). Only for chosen motor, the value will work.        
       * @param moveType:     Set move type        
       ***************************************************/   
       virtual void setAbsolutePosition_Neck(bool activeFlag[],  
                                       float angle[],  
                                       BE_MovePatternType moveType = enumMovePattern_Saccade) = 0;  
  
       /**************************************************  
        * Set neck relative position (If neck exists) (Array Size = 3)        
        * @brief Set neck space eye relative position        
        * @param activeFlag:   Bool data array(size = 3). Choose which motor or motors you want to send move cmd        
        * @param angle:        Float data array(size = 3). Only for chosen motor, the value will work.        
        * @param moveType:     Set move type        
        ***************************************************/   
        virtual void setRelativePosition_Neck(bool activeFlag[],  
                                       float angle[],  
                                       BE_MovePatternType moveType = enumMovePattern_Saccade) = 0;  
  
  
       /**************************************************        
       * @brief Set neck & eye union position （Absolute space）  
       * @param activeFlag:   Bool data array(size = 3). Choose which motor or motors you want to send move cmd        
       * @param angle:        Float data array(size = 3). Only for chosen motor, the value will work.        
       * @param moveType:     Set move type        
       ***************************************************/   
       virtual void setUnionAbsolutePosition_NeckEye(bool activeFlag[], float eyeAngle[], float neckAngle[], BE_MovePatternType moveType = enumMovePattern_Saccade) = 0;  
  
       /**************************************************  
        * Set neck relative position (If neck exists) (Array Size = 3)        * @brief Set neck space eye relative position        
        * @param activeFlag:   Bool data array(size = 3). Choose which motor or motors you want to send move cmd        
        * @param angle:        Float data array(size = 3). Only for chosen motor, the value will work.        
        * @param moveType:     Set move type        
        ***************************************************/   
        virtual void setUnionRelativePosition_NeckEye(bool activeFlag[], float eyeAngle[], float neckAngle[], BE_MovePatternType moveType = enumMovePattern_Saccade) = 0; // For future  
  
       /**************************************************        
       * Set current position as init position command. Current motor value will turn 0 after restart.        
       * @brief Set current position as init position command        
       * @note Current postion will be initial position. Effective after restart.       
       **************************************************/      
       virtual void setNowPositionAsInitPos() = 0;  
  
       /**************************************************  
        * @brief Chosen motor or motor will go to init position.        * 
        * @param type: Set chosen eye motor.        
        ***************************************************/   
       virtual void goInitPosition(MotorType type) = 0;  
  
       /**************************************************  
        * @brief Get chosen motor or motors init position value;        
        * * @param type: Set chosen eye motor.        
        * * @param angle: return motor initial position value        
        * **************************************************/   
        virtual void getInitPosition(MotorType type, float angle[]) = 0;  
  
       /**************************************************  
        * @brief Chosen neck motor or motor will go to init position.        * 
        * @param type: Set chosen neck motor.        
        * **************************************************/ 
          virtual void goInitPosition_Neck(MotorType_Neck type) = 0;  
  
       /**************************************************  
        * @brief Get chosen neck motor or motors init position value.        * 
        * @param type: Set chosen neck motor.        
        * * @param angle: return neck motor initial position value        
        * **************************************************/   
         virtual void getInitPosition_Neck(MotorType_Neck type, float angle[]) = 0;  
  
       /**************************************************  
        * @brief return true if neck existed        
        * **************************************************/  
          virtual bool haveNeckLinked() = 0;  
  
       /**************************************************  
        * @brief Open or close VOR function       
        * @param vor_eye on/off eye vor       
        * @param vor_neck on/off neck vor        
        ***************************************************/  
         virtual void onoff_VOR(bool vor_eye = true, bool vor_neck = false) = 0;  
  
       /**************************************************  
        * @brief Open or close SV function        
        * @param onoff on/off SV function        
        ***************************************************/   
        virtual void onoff_SV(bool onoff) = 0;  
  
       /**************************************************  
        * @brief Set sv work mode        
        * @param type: set sv work mode type (see SVType)        
        * @param rect: set work area        
        * @param size: set origin image size        
        ***************************************************/  
         virtual void setSvModel(SVType type = enumGlobalSV, cv::Rect rect = cv::Rect(0, 0, 1920, 1080), cv::Size size = cv::Size(1920, 1080)) = 0;  
  
       /**************************************************  
        * Set be-device synchronizing signal source        
        * @brief Set be-device synchronizing signal source      
        * @param flag: True: send signal, False: only recv signal        
        * **************************************************/  
         virtual void setSyncSignalSource(bool flag = true) = 0;  
  
       /**************************************************  
        * @brief Get camera exposure time set range       
        * @param up: return exposure time up_limit       
        * @param down: return exposure time down_limit        
        ***************************************************/   
        virtual void getCameraExposureTimeRange(float &up, float &down) = 0;  
  
       /**************************************************  
        * @brief Get camera exposure time        
        * @param type: Specify camera to change parameter, see BE_ImageSensorType and CameraIndex  (-1 means all camera!!!)        
        ***************************************************/  
         virtual float getCameraExposureTime(int type) = 0;  
  
       /**************************************************  
        * @brief Set camera exposure time       
        * @param type: Specify camera to change parameter, see BE_ImageSensorType and CameraIndex  (-1 means all camera!!!)      
        * @param autoExposure: True: set auto exposure mode, False: set manual exposure mode        
        * @param exposureTime: set exposure time. Only works when autoExposure = False;        
        ***************************************************/   
        virtual void setCameraExposure(int type, bool autoExposure, float exposureTime = 0) = 0;  
  
       /**************************************************  
        * @brief Get Camera WhiteBalance Temperature set range        
        * @param up: return WhiteBalance Temperature up_limit        
        * @param down: return WhiteBalance Temperature down_limit        
        ***************************************************/      
        virtual void getCameraWhiteBalanceTemperatureRange(int &up, int &down) = 0;  
  
       /**************************************************  
        * @brief Get Camera WhiteBalance Temperature        
        * @param type: Specify camera to change parameter, see BE_ImageSensorType and CameraIndex  (-1 means all camera!!!)        
        ***************************************************/       
        virtual float getCameraWhiteBalanceTemperature(int type) = 0;  
  
       /**************************************************  
        * @brief Set Camera WhiteBalance Temperature        
        * @param type: Specify camera to change parameter, see BE_ImageSensorType and CameraIndex  (-1 means all camera!!!)       
       * @param autoWhitebalance: True: set whitebalance temperature mode, False: set manual whitebalance temperature mode        
       * @param WhiteBalanceTemperature: set whitebalance temperature. Only works when autoExposure = False;        
       ***************************************************/       
       virtual void setCameraWhiteBalanceTemperature(int type, bool autoWhitebalance, float WhiteBalanceTemperature = 6600) = 0;  
  
       /**************************************************  
        * @brief Get camera exposure auto reference range        
        * @param up: Return up limit        
        * @param down: Return down limit        
        * **************************************************/       
        virtual void getCameraExposureAutoReferenceRange(float &up, float &down) = 0;  
  
       /**************************************************  
        * @brief Set camera exposure auto reference        
        * @note This function only supports some specified BinoSense Device. For example, BinoSense V3 is not        
        * supported.        
        * @param type: Specify camera to change parameter, see BE_ImageSensorType and CameraIndex  (-1 means all camera!!!)        
        * @param value: set exposure exposure auto reference value        
        ***************************************************/       
        virtual void setCameraExposureAutoReference(int type, int value = 70) = 0;  
  
       /**************************************************  
        * @brief Get camera gain range        
        * @param up: Return up limit        
        * @param down: Return down limit        
        ***************************************************/       
        virtual void getCameraGainRange(float &up, float &down) = 0;  
  
       /**************************************************  
        * @brief Set camera Gain work mode        
        * @note This function only supports some specified BinoSense Device. For example, BinoSense V3 is not        
        * supported.        
        * @param type: Specify camera to change parameter, see BE_ImageSensorType and CameraIndex  (-1 means all camera!!!)        
        * @param autoGain: Set camera gain set work in auto mode or manually mode.        
        * @param value: set gain value(Only works when autoGain = False)        
        ***************************************************/       
        virtual void setCameraGain(int type, bool autoGain = true, int value = 370) = 0;  
  
       /**************************************************  
        * Start to save all data when calling this function.        
        * @brief Save all be_data        
        * @note If you have chosen high image resolution, note the disk capacity.        
        * You can change saving image size by calling setImageResolution_Transfer        
        * @param startStop: 0, stop; 1, start;        
        * @param folderPath: set data_saving path. If path exists, this function will auto generate a new path        
        * named path_T.        
        ***************************************************/   
        virtual void saveBeData(bool startStop = 1, string folderPath = Be_Io_Path + "fastSave") = 0;  
  
       /**************************************************  
        * Snap be_data when calling this function. May have some time delay(ms).        
        * @brief Snap be_data when calling this function.        
        * @note You can change saving image size by calling setImageResolution_Transfer        
        * @note After calling snapBeData in the first time, the param folderPath will not work until        
        * function stopSnapBeData is called.        
        * @param description: You can give this snaped data a description. It will be written        
        * in CSV file.        
        * @param folderPath: set data saving path. If path exists, this function will auto        
        * generate a new path named path_T.        
        ***************************************************/   
        virtual bool snapBeData(string description="", string folderPath = Be_Io_Path + "snapSave") = 0;  
  
       /**************************************************  
        * Stop snapping be_data when calling this function. May have some time delay(ms).        
        * @brief Stop snapping be_data when calling this function.        
        * @note If you want to change a folder to save snaped data, call this function first        
        * and then recall snapBeData.        
        * **************************************************/       
        virtual void stopSnapBeData() = 0;  
  
       /**************************************************  
        * Record be_data when calling this function. May have some time delay(ms).        
        * @brief Record be_data when calling this function.        
        * @note You can change saving image size by calling setImageResolution_Transfer        
        * @note After calling recordBeData in the first time, the param folderPath will not work until        
        * function stopRecordBeData is called.        
        * @param data: The data will be recorded.        
        * @param description: You can give this recorded data a description. It will be written        
        * in CSV file.        
        * @param folderPath: set data saving path. If path exists, this function will auto        
        * generate a new path named path_T.        
        ***************************************************/       
        virtual bool recordBeData(BE_GeneralData &data, string description="", string folderPath = Be_Io_Path + "recordSave") = 0;  
  
       /**************************************************  
        * Stop recording be_data when calling this function.        
        * @brief Stop recording be_data when calling this function.        
        * @note If you want to change a folder to save recorded data, call this function        
        * first and then recall recordBeData. It's similar to snapBeData & stopSnapBeData.        
        ***************************************************/       
        virtual void stopRecordBeData() = 0;  
  
       /**************************************************  
        * Give a trigger signal to data server to send next data(Only for remote connect use,        
        * and parameter dataTransmissionType must be enumDataTransmission_OneByOne)        
        * @brief Give a trigger signal to data server to send a next data        
        * **************************************************/     
        virtual void triggerDataTransmission() = 0;  
  
       /*! @brief Set grab data rate, Max 200 fps.*/  
       virtual void setBeDataRate(int rate = 25) = 0;  
  
       /*! @brief Set sync camera delay time: max 30000 us.*/  
       virtual void setSyncCameraDelayTime(int timeDelay = 0) = 0;  
  
       /*! @brief Transform infrared image. Only for bionic device which has infrared camera.*/  
       virtual bool infraImageTrans(BE_GeneralData &data, cv::Mat& infraImage, cv::Mat& tempImage) = 0;  
  
       /*! @brief Calculate temp for point value of infrared image.*/  
       virtual float infraTempCalc(uint value) = 0;  
  
       /**************************************************  
        * On/Off camera image grab or network transform. It will help to save system resource or bandwidth resources        
        * @brief On/Off camera image grab or network transform        
        * @param flag: Bool data array(size = MAXCAMERASInDEVICE). Set 1 means ON and set 0 means OFF        
        * @param NetworkType: 1 means only affect network transform, 0 means affect both network and local image grab        
        ***************************************************/       
        virtual void onOffCameraImage(bool flag[], bool onlyNetworkType = true) = 0;  
  
       /**************************************************  
        * @brief On/Off Laser ranging       
        ***************************************************/       
        virtual void onOffLaserRanging(bool onOff = true) = 0;  
  
       /**************************************************  
        * @brief On/Off Infrared Light       
        ***************************************************/       
        virtual void onOffInfraredFillingLamp(bool onOff = false, uchar focalLength = 0) = 0;  
  
       /**************************************************  
        * @brief Set zoom camera module focal length, range: 1~30       
        ***************************************************/       
        virtual void setZoomFocalLength(uchar focalLength = 1) = 0;  
  
       /**************************************************  
        * @brief Set wipers module(Only for eagle eye device),0: close ,1~255 :open, different speed       
        ***************************************************/       
        virtual void setWipers(uchar value = 0) = 0;  
};
```

 
 








