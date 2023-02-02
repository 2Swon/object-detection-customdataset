# mosaic_realtime_project(개인 프로젝트)

실생활에서 모자이크를 할만한 객체들 (담배, 얼굴, 흉기, 혈흔,  자동차 번호판 등등)을

마음껏 데이터셋을 넣고 mosaic 기능을 class별로 on/off 할 수 있는

실시간 모자이크 시스템을 만들었습니다.



class : 0 = knife, 1 = smoking, 2 = face

### 1. 사용 방법 yolo_carnum.ipynb

```python
#yolo 깃허브 clone
%cd /content
!git clone https://github.com/ultralytics/yolov5.git
%cd yolov5
!pip install -qr requirements.txt
```

깃허브 clone

```python
!pip install roboflow
from roboflow import Roboflow
#칼 데이터셋 다운로드
rf = Roboflow(api_key="krcQlWTnG5PUWdI1yzWt")
project = rf.workspace("augustus").project("knife_dataset-kysbg")
dataset = project.version(2).download("yolov5")
#담배 데이터셋 다운로드
project = rf.workspace("cigarettesmokingdetection").project("cigarette-detection-uyqvc")
dataset = project.version(1).download("yolov5")

#얼굴 데이터셋 다운로드
project = rf.workspace("mohamed-traore-2ekkp").project("face-detection-mik1i")
dataset = project.version(20).download("yolov5")
```

다양한 데이터셋 다운로드 후 데이터셋 통합



### 2. detect.py

##### 2.1 파라미터 

```python


def run(
        ...
        hide_annotation=False,
        ...
        option=[]
):


def parse_opt():
    ...
    parser.add_argument('--hide-annotation', default=False, action='store_true', help='hide annotation')#바운딩박스 출력 여부 결정 (False=출력)   
    ...
    parser.add_argument('--option', type=arg_as_list, default=[], help='mosaic object list([]=mosaic all,[-1]=nothing)')#모자이크 옵션 설정
    
    opt = parser.parse_args()
    opt.imgsz *= 2 if len(opt.imgsz) == 1 else 1  # expand
    print_args(vars(opt))
    return opt
```

##### 2.2 run 함수

```
mosaic_list = [] #각 객체별모자이크 처리 여부

    for i in range(len(names)):
        mosaic_list.append(0)
    
    if len(option)==0:
        for i in range(len(mosaic_list)):
            mosaic_list[i] = 1
    else :
        for i in range(len(option)):
            if (0 <= option[i] <len(names)):
                mosaic_list[option[i]] = 1
```

모자이크를 처리할 객체들의 정보를 저장

```py
		coord = [] #각 객체에 대한 좌표 x1,y1,x2,y2
		blurratio = 40 # blur강도 설정
		if len(det):
                # Rescale boxes from img_size to im0 size
                det[:, :4] = scale_boxes(im.shape[2:], det[:, :4], im0.shape).round()

                # Print results
                for c in det[:, 5].unique():
                    n = (det[:, 5] == c).sum()  # detections per class
                    s += f"{n} {names[int(c)]}{'s' * (n > 1)}, "  # add to string

                m = im0.copy()  ##모자이크 이미지를 만들어주기 위해 이미지를 줄이고 키움
                blur = cv2.blur(m, (blurratio, blurratio))
                m = blur

                # Write results
                for *xyxy, conf, cls in reversed(det):
                    if save_txt:  # Write to file
                        xywh = (xyxy2xywh(torch.tensor(xyxy).view(1, 4)) / gn).view(-1).tolist()  # normalized xywh
                        line = (cls, *xywh, conf) if save_conf else (cls, *xywh)  # label format
                        with open(f'{txt_path}.txt', 'a') as f:
                            f.write(('%g ' * len(line)).rstrip() % line + '\n')

                    if save_img or save_crop or view_img:  # Add bbox to image
                        c = int(cls)  # integer class
                        if mosaic_list[c]==1:
                            coord.append(xyxy)
                        label = None if hide_labels else (names[c] if hide_conf else f'{names[c]} {conf:.2f}')
                        if hide_annotation == False:
                            annotator.box_label(xyxy, label, color=colors(c, True))  # 바운딩 박스 표시
                    if save_crop:
                        save_one_box(xyxy, imc, file=save_dir / 'crops' / names[c] / f'{p.stem}.jpg', BGR=True)
```

mosaic_list 가 1인 index의 좌표값만 coor[]에 저장

hide_annotation이 False이면 label과 바운딩박스 표시

```python
            #blur 처리 
            for i in range(len(coord)):
                for k in range(len(coord[i])):
                    coord[i][k] = int(coord[i][k])
                im0[coord[i][1]:coord[i][3], coord[i][0]:coord[i][2], :] = m[coord[i][1]:coord[i][3], coord[i][0]:coord[i][2], :]
```

mosaic처리를 해야하는 좌표값을 불러온 후

blur처리된 좌표값 전달

### 3. 결과 사진

##### 3.1 option [] (모두 모자이크처리)

```python
!python detect.py --weights best.pt --conf 0.4 --source 0 --option []
```

<img src="https://github.com/2Swon/DeepLearing/blob/main/Project/mosaic_realtime/img/option%5B%5D.png">

##### 3.2 option[0, 1] (흉기, 담배 모자이크 처리)

```python
!python detect.py --weights best.pt --conf 0.4 --source 0 --option [0,1]
```
<img src="https://github.com/2Swon/DeepLearing/blob/main/Project/mosaic_realtime/img/option%5B0%2C1%5D.png">


##### 3.3 hide-annotation (바운딩박스 정보 숨기기)

```python
!python detect.py --weights best.pt --conf 0.4 --source 0 --option [] --hide-annotation
```
<img src="https://github.com/2Swon/DeepLearing/blob/main/Project/mosaic_realtime/img/hide-annotation.png">



