# 立体矫正程序（StereoRectify）使用说明
## 1. 立体矫正程序功能概括
仿生眼系列设备的标定数据储存在仿生眼配置文件中，该例程展示了如何 ***获取标定数据***, ***使用标定数据进行立体矫正*** 并 ***立体匹配获取视差图与深度图***。

## 2 立体矫正程序使用方法

根据 *path_to_workspace/BionicEyes/README.md* 中的流程成功编译仿生眼workspace后，所有sample程序的可执行文件保存在 *path_to_workspace/BionicEyes/bin/* 目录中，根据 *ubuntu* 操作系统的版本，程序的可执行文件被命名为：


>evo_be_Sample_StereoRectify_2004  
>evo_be_Sample_StereoRectify_1804  
>...
### 2.1 开启远端服务 
立体矫正例程中创建的是远程连接实例 (创建直连实例同样可以获得标定数据), 故在使用时请确保已经有一台*PC*设备直连仿生眼设备并开启了仿生眼服务。具体方法如下:

在直连*PC*的 *path_to_workspace/BionicEyes/bin/* 目录下，根据对应设备不同，运行相应程序：
|设备        |可执行文件                     | 设备依赖库    |
|:--:       |:--:                         | :--:    |
|仿生眼III   |evo_be_Device_*               | libevo_be_Device_shared_*.so|
|仿生眼V     |evo_be_Device_5_*             | libevo_be_Device_5_shared_*.so|
|仿生鹰眼I   |evo_be_EEDevice_*             | libevo_be_EEDevice_shared_*.so|
|三轴平台    |evo_be_Device_ThreeAxis_*     | libevo_be_Device_ThreeAxis_shared_*.so|

**直连PC除运行上述设备程序外，还需要在另一个终端运行 evo_be_Device_Service_\* 用于启动仿生眼服务后，才能供远端PC检测到仿生眼系列设备的存在。**

***\*代表对应系统平台，例如1804代表ubuntu18.04***

### 2.2 运行立体矫正程序
直连*PC*开启仿生眼服务后, ***与直连PC处于同一个局域网下的*** 远程 *PC* 设备即可检测到仿生眼存在并进行连接,在远程 *PC* 设备终端中进行如下操作:

#### 2.2.1 进入bin目录
 `cd  path_to_workspace/BionicEyes/bin/`
#### 2.2.2 运行可执行文件查看help
 `./evo_be_Sample_StereoRectify_* -h`
 上述命令的输出结果为：
 usage: evo_be_App_Visualizer_1*04 [--connect `<mode>`] [--server `<mode>`] [--receiverIP `<ip>`] [--serverIP `<ip>`]
options:
  -h --help             Show this help message
  -c --connect          Connect mode: i, c, ic
  -s --server           Server mode: lo, lf, do, df
  -rip --receiverIP     IP of receiving device: xxx.xxx.xxx.xxx
  -sip --serverIP       IP of server device: xxx.xxx.xxx.xxx
	  
输出结果中显示了运行此程序时可选的各种选项与参数。仿生眼SDK中提供了两种创建远程连接实例的方法，其函数原型如下，分别对应 ***自动识别所有远程设备供用户自行选择设备连接*** 功能 与 ***通过IP地址连接*** 功能：
```c++
static CBionicEyes *create(BE_Connect_Type type,  
                           BE_Connect_DataServerType dataServerType = enumDeviceServer_Only,  
                           BE_Data_TransmissionType dataTransmissionType = enumDataTransmission_ASPAP,  
                           void *logger_ptr = NULL);		
```

```c++
static CBionicEyes *create(string ipAddr,  
                           BE_Connect_Type type = enumdisConnect,  
                           BE_Connect_DataServerType dataServerType = enumDeviceServer_Only,  
                           BE_Data_TransmissionType dataTransmissionType = enumDataTransmission_ASPAP,  
                           void *logger_ptr = NULL);
```
远程连接实例的具体使用方法与参数意义在*BE_Sample_Control_Remote* 例程中进行了介绍。
对于立体矫正例程, -c（或--connect）对应创建远程连接实例时选择参数 BE_Connect_Type type, -s对应创建远程连接实例时选择参数BE_Connect_DataServerType dataServerType, 详细信息如下：

| -c | 可选参数 |对应远程连接实例的参数选择|
|:---:|:----:|:---:|
|    | i  | type = enumConnect_Image |
|    |  c  | type = enumConnect_Control  |
|    | ic |type = enumConnect_ImageControl||

 **不传入-c选项时, 程序默认type = enumConnect_ImageControl**
 
| -s | 可选参数 |对应远程连接实例的参数选择|
|:---:|:----:|:---:|
|    | lo  | dataServerType =  enumLocalServer_Only |
|    |  lf  | dataServerType =  enumLocalServer_First  |
|    | do |dataServerType = enumDeviceServer_Only|
|    | df |dataServerType = enumDeviceServer_First|

 **不传入-s选项时, 程序默认dataServerType = enumLocalServer_First**
 
#### 2.2.3 根据使用场景运行例程

 * 不传入 -sip(或--serverIP) 选项参数时, 程序将自动识别同一局域网下所有远程设备供用户自行选择设备连接  
 例：`./evo_be_Sample_StereoRectify_* -c i -s do`  
 * 传入-sip(或--serverIP)选项参数时，程序将直接连接传入IP所对应的仿生眼设备.  
 例: `./evo_be_Sample_StereoRectify_* -c i -s do -sip 192.168.31.88 `  
 注: 如何获取仿生眼设备服务器IP,见 ***evo_be_Sample_GetDeviceList_2004*** 例程。  
 
### 2.3 立体矫正Sample程序详解
#### 2.3.1 创建远程连接实例
```c++
int main(int argc, char *argv[])  
{  
    ...
    if (cmdOptionParser(argc, argv, be_fsm))  
        // 传入服务器IP,创建的远程连接实例直接连接IP对应的设备
        be_fsm.device = be_fsm.device->create(be_fsm.server_ipAddr, be_fsm.connect_type, be_fsm.connect_dataServerType, be_fsm.data_transmissionType);  
    else
        // 不传入IP,创建的远程连接实例将自动识别同一局域网下所有远程设备供用户自行选择设备连接       
        be_fsm.device = be_fsm.device->create(be_fsm.connect_type, be_fsm.connect_dataServerType, be_fsm.data_transmissionType);
   ...
}

```
#### 2.3.2 初始化设备
```c++
int main(int argc, char *argv[])  
{  
    ...
		if (!initDevice(be_fsm, canvas0))  
		    std::exit(EXIT_FAILURE);
   ...
}   
```

立体矫正例程中定义的*initDevice()*函数原型如下：

```c++
bool initDevice(BE_FSMState &be_fsm, cv::Mat &canvas)  
{  
   //通过创建的远程连接或直连实例,可以调用控制设备使用的方法
   //例如控制设备的运动、获取设备数据或者设置设备参数等
   //所有方法的原型集成在类 CBionicEyes 中，见evo_be_Common.h
   //  be_fsm.device->onoff_SV(true);  
   //  be_fsm.device->onoff_VOR(false);
    	
    be_fsm.device->setImageColor_Transfer(evo_be::enumColor);  
    be_fsm.device->setImageColor(evo_be::enumColor);
    // 获取相机的原始分辨率。 
    auto originalSize = be_fsm.device->getOriginImageResolution();  
    be_fsm.imgMaxWidth = originalSize.width;   //2048  
    be_fsm.imgMaxHeight = originalSize.height; //1536
    
    // scale参数对应的是使用仿生眼获取的图像的分辨率,相对于相机原始分辨率
    // 的缩放程度.例如若得到的相机的原始分辨率为2048*1536，而想要获取的图
    // 像分辨率为1024*768,则scale=0.5。 此参数与获取动态标定数据有关。
    be_fsm.scale = 0.5;                        // set whatever imagevR1, vR2, vP1, vP2, vQ; resolution that you need
	
    //定义了scale=0.5, 假设相机的原始分辨率2048*1536, scale = 0.5代表 
    //想要获取的图像的分辨率是1024×768。可以使用
    //setImageResolution_Transfer方法与setImageResolution方法
    //对获取的图像的分辨率进行设置。
    cv::Size be_res(be_fsm.imgMaxWidth * 2 * be_fsm.scale, be_fsm.imgMaxHeight * be_fsm.scale);

    // 由于传输时，左右两幅图像是合并在一起发送
    // 使用setImageResolution_Transfer与setImageResolution 设置想 
    // 要获取的单个图像的分辨率时,需要将宽×2
    // 即想要左右单个图像的分辨率为（1024,768）时，
    // be_res应为(2048, 768）
    // 将be_res传入 setImageResolution_Transfer与
    // setImageResolution方法时,获取的合并图像的分辨率将为（2048,768）
    // 左右单个图像的分辨率为（1024,768）
    be_fsm.device->setImageResolution_Transfer(be_res);  
    be_fsm.device->setImageResolution(be_res);

    //上述两种设置分辨率函数的区别在于：
    //setImageResolution_Transfer 设置的分辨率不会影响到
    //使用getbedata（）获取的图像数据， 但会影响到会影响到savebedata()
    //保存数据时候的分辨率。
    // setImageResolution会影响到getbedata（）获取的图像数据的分辨
    // 率,该函数只能在远程连接时起作用。直连时使用该函数无法改变获取的
    // 图像分辨率。

    // 设置图片传输质量。 
    be_fsm.device->setImageCompressionQuality(95);  
    // 设置图片传输帧率
    be_fsm.device->setDataRate_Transfer(25);  
    msleep(250);
    // 判断设备是否准备完毕  
    while (!(be_fsm.device->isBeDataReady()))  
    {  
        msleep(40);  
        std::cout << "Waiting for data..." << std::endl;  
        continue;    }  
		// be_fsm.device->setAbsolutePosition(evo_be::enumAllMotor, std::vector<float>(6, 0.0f).data());
	    if (be_fsm.data_transmissionType == evo_be::BE_Data_TransmissionType::enumDataTransmission_OneByOne)      
        // 如果创建远程实例时，选择参数 dataTransmissionType    
        // =enumDataTransmission_OneByOne,则下面是一个依次获取图像
        // 数据的简单例程
        for (int k = 0; k < 5; k++)  
        {
            // 每次接受下一个图像数据时，需要
            // 使用triggerDataTransmission()方法，触发接受下一个图像
            // 的信号。  
            be_fsm.device->triggerDataTransmission();  
            be_fsm.device->getBeData();  
            msleep(20);  
       }  
  
    be_fsm.device->getBeData(be_fsm.beData);  
    canvas = cv::Mat(1080, 1920, CV_8UC3);  
    canvas.setTo(cv::Scalar(0, 0, 0));  
    return true;
}
```

#### 2.3.3 准备接受数据
```c++
int main(int argc, char *argv[])  
{  
    ...
    ...
    auto device = be_fsm.device;  
    //device->onoff_SV(false);  
   // device->onoff_VOR(false);
   // 根据结构体BE_GeneralData 可创建接受设备数据的结构体变量
   // 例：evo_be::BE_GeneralData beData；
    evo_be::BE_GeneralData &beData = be_fsm.beData;  
    const double scale = be_fsm.scale; 
    // 使用getBeData()方法获取设备数据后。
    // 例: beData = device->getBeData();
    // 获取的设备数据将存储在结构体变量的各个成员中
    // 如beData.image、beData.motorData、beData.imuData等

   //这里获取了单个左右目图像的宽高，用于后续的图像矫正。
    const int w = beData.image[enumBoth].cols / 2;  
    const int h = beData.image[enumBoth].rows;
    ...
    ...
}
```

#### 2.3.4 动态获取相机内参与畸变标定数据
```c++
int main(int argc, char *argv[])  
{  
    ...
    ...
	// dynamic calibration pointer  
	// 创建获取标定数据的接口
	std::shared_ptr<evo_be::CBE_Calibrator> calib;  
	uchar *encoder = device->getCalibrationInfo();  
	calib = calib->create(encoder);  
	// 获取标定数据接口后，调用方需要负责释放返回的缓冲区
	delete[] encoder;  
  
        // opencv camera and distort matrices. See opencv 	documentation. 
        // 使用接口根据获得图像的分辨率动态获取相机内参矩阵：
        // 由于相机内参与图像分辨率相关。
        // 此时，定义的scale参数为动态获取相机内参的依据。
        // 若相机原始分辨率2048*1536, 图像的分辨率是1024*768
        // 则要获取图像分辨率为1024*768时的相机内参矩阵
        // 则令scale为0.5，依此类推。
	cv::Matx33d K1 = calib->K(calib->eyeId("Left"), scale);  
	auto D1 = calib->D(calib->eyeId("Left"));  
	cv::Matx33d K2 = calib->K(calib->eyeId("Right"), scale);  
	auto D2 = calib->D(calib->eyeId("Right"));

	// K1,K2为左右相机的内参矩阵，D1,D2为左右相机的畸变矩阵
	// beData 获取的双目图像存储在beData.image[2]中，
	// 且左右两幅图合并在一起，故需要进行切割获得左右目图像
	cv::Mat imgL_raw = beData.image[enumBoth].colRange(0, w), imgR_raw = beData.image[enumBoth].colRange(w, 2 * w);
    ...
    ...
}
```

#### 2.3.5 动态获取左右相机相对旋转矩阵与平移矩阵（R、T)与立体矫正
在双目相机的立体矫正中，所需的不仅是相机内参（K1,K2）和畸变矩阵（D1,D2），还有左右相机之间的旋转和平移矩阵， **仿生眼设备的一大特定是能够根据相机的位置动态的返回当前的左右相机的相对R、T数据** , 具体方法如下：

```c++
int main()
{
   ...
   ...
    int beDataId_last = -1;  
    while (cv::waitKey(1) != 27)  
    {  
        auto t0 = GetSysTimeMicros();  
        //获取一次数据
        device->getBeData(beData);
        //判断根据获取数据判断相机在是否在快速运动，
        // 快速运动时不做立体矫正
        if (beData.isMovingFastly)    
        {  
            continue;  
        } 
        // 判断是否获取到了新的数据
        if (beData.id != beDataId_last)  
        {  
            // get relative pose between two eyes
            // 重点：这里通过传入当前获取的电机位置数据，调用				   
            // getRt(beData.motorData)方法,可动态解算出运动到
            // 当前位置时，左右相机的相对R，T
            evo_be::RtStereo Rt = calib->getRt(beData.motorData);  
            
            cv::Mat vR1, vR2, vP1, vP2, vQ;  //R1.R2,P1,P2. Q,是  
            cv::Rect vvalidRoi[2];  
            cv::Mat vrmap[2][2]; 
             
            // 仿生眼SDK中提供了立体矫正的接口函数:evo_be::BE_Utilities::virtualStereoRectify（），
            // 输入参数：
            // K1 K2：内参矩阵、D1，D2：畸变矩阵、左右相机相对位置R，T
            // 输出参数：
            // 矫正旋转矩阵（R1,R2），投影矩阵（P1,P2），深度映射矩阵（Q）
            evo_be::BE_Utilities::virtualStereoRectify(cv::Mat(K1), cv::Mat(K2), cv::Mat(D1), cv::Mat(D2),cv::Size(w, h),cv::Mat(Rt.R_rl), cv::Mat(Rt.t_rl), vR1,vR2,vP1, vP2, vQ,cv::CALIB_ZERO_DISPARITY,1,cv::Size(w, h), vvalidRoi[0], vvalidRoi[1],0,0);  
            
            //cv::initUndistortRectifyMap 函数根据变换矩阵和投影矩阵生成矫正图像到原始图像的像素坐标的映射，然后使用 remap 函数即可得到校正后的双目图像。  
            cv::initUndistortRectifyMap(cv::Mat(K1), cv::Mat(D1), cv::Mat(vR1), cv::Mat(vP1),cv::Size(w, h), CV_16SC2, vrmap[0][0], vrmap[0][1]);  
            cv::initUndistortRectifyMap(cv::Mat(K2), cv::Mat(D2), cv::Mat(vR2), cv::Mat(vP2), cv::Size(w, h), CV_16SC2, vrmap[1][0], vrmap[1][1]);  
            cv::Mat vimgL_rect,vimgR_rect;  
            cv::remap(imgL_raw, vimgL_rect, vrmap[0][0], vrmap[0][1], cv::INTER_LINEAR);  
            cv::remap(imgR_raw, vimgR_rect, vrmap[1][0], vrmap[1][1], cv::INTER_LINEAR);  
            cv::imshow("stereo rectify left image",vimgL_rect);  
            cv::imshow("stereo rectify right image",vimgR_rect);  
  
            //划线，对矫正效果进行可视化
            cv::Mat canvas;  
            canvas.create(h, w * 2, CV_8UC3);  
            cv::Mat canvasPart1 = canvas(cv::Rect(0, 0, w, h));  
            cv::Mat canvasPart2 = canvas(cv::Rect(w, 0, w, h));  
            cv::resize(vimgL_rect, canvasPart1, canvasPart1.size(), 0, 0, cv::INTER_AREA);  
            cv::resize(vimgR_rect, canvasPart2, canvasPart2.size(), 0, 0, cv::INTER_AREA);  
            for (int j = 0; j < canvas.rows; j += 20)  //这里在画对齐线  
               cv::line(canvas, cv::Point(0, j), cv::Point(canvas.cols, j),(j/20)%2==0?cv::Scalar(0, 255, 0):cv::Scalar(0, 0, 255), 1, 8);  
            cv::imshow("stereo rectify",canvas);  
            canvas.create(h, w * 2, CV_8UC3);
  ...
  ...
            beDataId_last = beData.id;  
            continue;        }  
        else  
            msleep(10);  
  
        //std::cout << "Loop  time  = " << (GetSysTimeMicros() - t0) * 0.1 << " ms" << std::endl;  
    }  
  
    return 0;  

}
```


#### 2.3.6 立体匹配获取视差图与深度图
```c++
int main(int argc, char *argv[])  
{  
	...
	...
    // stereomatching pointer     
    // 仿生眼SDK中提供了基于SGM方法的立体匹配的接口，见evo_be_ImageProcess_Core.h 的
    // CBE_StereoMatching 类
    // 接口的创建方法如下：
    std::shared_ptr<evo_be::CBE_StereoMatching> sgm = sgm->create(cv::Size(w, h)); 
    // w为图像宽，h为图像的高。 
    sgm->setWidth(w);  
    sgm->setHeight(h);  
    // P1 and P2 see Semi-global matching.  
    sgm->setUniqueness(0.97); //这里是设置了唯一性比值 
    sgm->setP1(10);  
    sgm->setP2(200);   //p1.p2是SGM方法里面的平滑性惩罚系数  
  
  
    int beDataId_last = -1;  
    while (cv::waitKey(1) != 27)  
    { 
		...
		...
            // do stereo matching 使用接口进行立体匹配  
            // vimgL_rect为矫正后的左目图像，vimgR_rect 为矫正后的右目图像
            // VQ为矫正时得到的深度映射矩阵， diap为得到的视差图,depth为得到的深度图。
            // mask是为没有匹配成功的区域创建的掩膜
            sgm->virtualCalc(vimgL_rect,vimgR_rect,vQ,disp,depth,mask);  
            // 为了将视察图进行可视化， 将视察图转化为COLORMAP。
            sgm->disp32fTo8u(disp,disp_8u);  
            disp_8u.convertTo(disp_8u, CV_8U, 1.0);  
            cv::applyColorMap(disp_8u, disp_color, cv::COLORMAP_JET);  
            cv::imshow("disp", disp_color);  
 
            beDataId_last = beData.id;  
            continue;        }  
        else  
            msleep(10);  
 
    }  
  
    return 0;  
}
```

## 3 CBE_StereoMatching 类 一览
```c++
class EVO_BE_IMAGEPROCESS_CORE_API CBE_StereoMatching  
{  
public:  
   /**  
    * @brief Allocate stereo-matching object for a fixed image size    
    * @param  size             Image size of a single camera    
    * @return std::shared_ptr<CBE_StereroMatching>    
    */   
    static std::shared_ptr<CBE_StereoMatching> create(cv::Size size = cv::Size(960, 540));  
    
   /**  
    * @brief Destroy the stereo-matching object    
    */   
    virtual ~CBE_StereoMatching();  
    
   /**  
    * @brief    
    * @param  left             Rectified left image  
    * @param  right            Rectified right image    
    * @param  disp             Disparity image    
    * */   
    virtual void calc(const cv::Mat &srcLeft, const cv::Mat &srcRight, cv::Mat &dstDisp, cv::Mat &mask) = 0;  
   
   /**  
    * @brief  Set the P1 parameter of SGM    
    * @param  P1_input         Recommended 10 to 80    
    */   
    virtual void setP1(int P1_input) = 0;  
   
   /**  
    * @brief  Set the P2 parameter of SGM    
    * @param  P2_input         Recommended 120 to 250    
    */   
    virtual void setP2(int P2_input) = 0;
      
   /**  
    * @brief Set the uniqueness parameter of SGM    
    * @param  uniqueness_input Between 0.0 and 1.0, recommended 0.75 to 0.97    
    */   
    virtual void setUniqueness(float uniqueness_input) = 0;  
   
   /**  
    * @brief Set the width of input image    * @param  width_input      960x540 (or 1024x769) for speed consideration   */   virtual void setWidth(int width_input) = 0;  
   /**  
    * @brief Set the height of input image    
    * @param  height_input     960x540 (or 1024x768) for speed consideration    
    */   
    virtual void setHeight(int height_input) = 0;  
   
   /**  
    * @brief Reset P1, P2, uniqueness to default values    
    */   
    virtual void reset() = 0;  
   
   /**  
    * @brief Convert disparity map from float to uchar for display purpose    
    * @param  src32FC1         Input disparity map of float type    
    * @param  dst8UC1          Output disparity map of uchar type    
    */   
    virtual void calc(const cv::Mat &srcLeft, const cv::Mat &srcRight,  
                 const cv::Matx33d &KLeft, const cv::Matx33d &RRectLeft, const cv::Matx34d &PRectLeft,  
                 const cv::Matx44d &Q, double scale,  
                 cv::Mat &dstXYZ, cv::Mat &mask8UC1) = 0;  
  
   virtual void disp32fTo8u(cv::Mat &src32FC1, cv::Mat &dst8UC1) = 0;  
   
   /**  
    * @brief Convert disparity map (float) to depth map (float) in real-world units    
    * @param  src32FC1_disp    Input disparity map (float)    
    * @param  Q32FC1           Input disparity-to-depth matrix (see CBE_Calibrator::rectifyUndistortRemap)    
    * @param  dst32FC1_depth   Output depth map (float) in millimeter (mm)    
    */   
    virtual void disp32fTo3D(cv::Mat &src32FC1_disp, cv::Matx44f Q32FC1, cv::Mat &dst32FC1_depth) = 0;  
   
   /**  
    * @brief Convert disparity map (float) to depth map (float) and colored point cloud in real-world units    
    * @param  src32FC1_disp    Input disparity map (float)    
    * @param  src8UC3_left     Input undistored, stereo-rectified image of left eye    
    * @param  Q32FC1           Input disparity-to-depth matrix (see CBE_Calibrator::rectifyUndistortRemap)    
    * @param  dst32FC1_depth   Output depth map (float) in millimeter (mm)    
    * @param  dst32FC3_pcd_xyz Output coordinate matrix of the point cloud, each "pixel" of which has    * three float channels, i.e., the (x,y,z) coordinates in millimeter (mm)    
    * @param  dst8UC3_pcd_rgb  Output color (channel order = RGB, NOT BGR!) matrix of the point cloud;    
    * there is an entry-wise correspondence between dst32FC3_pcd_xyz and dst32FC3_pcd_rgb;    
    */  
   virtual void disp32fTo3D(cv::Mat &src32FC1_disp, cv::Mat &src8UC3_left, cv::Matx44f Q32FC1,  
                      cv::Mat &dst32FC1_depth, cv::Mat &dst32FC3_pcd_xyz, cv::Mat &dst8UC3_pcd_rgb) = 0;  
  

   virtual void virtualCalc(const cv::Mat &srcLeft, const cv::Mat &srcRight,const cv::Matx44d &Q,cv::Mat& disp, cv::Mat &dstXYZ, cv::Mat &mask8UC1) = 0;  
};
```

