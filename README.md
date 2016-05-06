# dissect-dctracking

### 

### Break down steps
(1) line 26: opti=readDCOptions(optfile);
> - % function opt=readDCOptions(inifile) from `dctracking/readDCOptions.m
> - % parse configuration for DCO Tracking

> - default to `config/default2d.ini` if not provided
> - `ini=IniConfig();` from motutils/external/iniconfig/IniConfig.m, by (c) 2009-2010 Evgeny Prilepin aka Iroln
>    - %IniConfig - The class for working with configurations of settings and INI-files. 
> - config.ini sections
>    - [Parameters]
>    - [General]
>    - [Hypothesis Space]
>    - [Initialization]
>    - [Detections]
>    - [Functions]
>    - [Discrete Optimization]
>    - [Continuous Optimization]
>    - [Miscellaneous]

(2) line 37: sceneInfo=getSceneInfo(scenario,opti);
> - % function sceneInfo=getSceneInfo(scenario, opt) from motutils/getSceneInfo.m
> - % if string, read from file, 
    
```matlab
if ischar(scenario)
  sceneInfo=readSceneOptions(scenario);
  sceneInfo.yshift=0;
  return;
end
```
> - function sceneInfo=readSceneOptions(inifile) from motutils/readSceneOptions.m
> - defaults to `scenes/scene2D.ini`
> - ini sections:
>   - [Source]
>   - [Miscellaneous]

(3) sceneInfo=getSceneInfo(scenario,opti); ---> sceneInfo=readSceneOptions(scenario); ---> sceneInfo=checkScene(sceneInfo);

(4) sceneInfo=checkScene(sceneInfo); %   from motutils/getSceneInfo.m
> - Required field
>    - imgFolder
>    - frameRate
> - from imgFolder ---> sceneInfo.imgFolder, works out:
>    - sceneInfo.imgFileFormat
>    - sceneInfo.imgExt
>    - sceneInfo.frameNums
>    - sceneInfo.imgHeight
>    - sceneInfo.imgWidth 
> - by function [imgFormat, imgExt, frameNums]=getImgFormat(folder,ext) from motutils/getImgFormat.m
>    - % try to determine the image format for a sequence
> - if _sceneInfo.dataset_, _sceneInfo.sequence_ not available, set to **_unknown_**
> - if **sceneInfo.gtFile** available, convert ground truth file by `gtInfo=convertTXTToStruct(sceneInfo.gtFile);`
> - if **sceneInfo.camFile** available, load sceneInfo.camPar by motutils/camera/parseCameraParameters.m
>    - camera calibration file (Tsai Model)
>    - convert to 3D ground coordinates by motutils/projectToGroundPlane.m
> - if **detfile** NOT available, run detector to get detections, by `detfile=detectPeople(sceneInfo);`
>    - from `motutils/external/dollar/toolbox/detector/detectPeople.m`
>    - *detFile* save to: `detFile = fullfile(sceneInfo.imgFolder,'det.txt');`
>    - by default, this is **P. Dollar's pedestrian detector.**, from https://github.com/pdollar/toolbox, which is from **Piotr's Computer Vision Matlab Toolbox**
>    - http://vision.ucsd.edu/~pdollar/toolbox/doc/ not working anymore!!
>    - Aggregate Channel Features (ACF) object detection
> -  **WILL SAVE DETECTIONS TO `sceneInfo.imgFolder/det.txt`**

(3) Converting detections from CP_MCF to DCO_X format
> - comment out the following lines from `getDetectionsFromDPMCF_DCOX.m`, namely `motutils/parseDetections.m`
```matlab
    if scenario<190
        detections=rescaleConfidence(detections,opt);
    end
```


