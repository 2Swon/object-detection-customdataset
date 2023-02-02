# mosaic project_v1(개인 프로젝트)

뉴스, 유튜브, 드라마 등등 동영상 촬영을 할 때 차번호가 노출 되는 것을 방지하고자 

차번호판만 인식하여 모자이크 처리하는 시스템을 만들었습니다.

객체 탐지 모델로는 YOLOv5모델을 사용하였습니다.

### 1. 사용 방법 yolo_carnum.ipynb

```python
#yolo 깃허브 clone
%cd /content
!git clone https://github.com/ultralytics/yolov5.git
%cd yolov5
!pip install -qr requirements.txt
```



```python
#차 번호판 데이터셋 다운로드
!pip install roboflow
from roboflow import Roboflow
rf = Roboflow(api_key="krcQlWTnG5PUWdI1yzWt")
project = rf.workspace("license-plate-mhig5").project("license-plate-7egee")
dataset = project.version(5).download("yolov5")
```

### 2. detect.py

```python
 if len(det):
   # Rescale boxes from img_size to im0 size
   det[:, :4] = scale_boxes(im.shape[2:], det[:, :4], im0.shape).round()

    # Print results
                

   for c in det[:, 5].unique(): #객체 개수 출력
    n = (det[:, 5] == c).sum()  # detections per class
    s += f"{n} {names[int(c)]}{'s' * (n > 1)}, "  # add to string

   # Write results

	blurratio = 40 #blur 강도 설정                
    for *xyxy, conf, cls in reversed(det):
        ##모자이크 처리부분
		crop_obj = im0[int(xyxy[1]):int(xyxy[3]), int(xyxy[0]):int(xyxy[2])]
        blur = cv2.blur(crop_obj,(blurratio, blurratio))
        im0[int(xyxy[1]):int(xyxy[3]), int(xyxy[0]):int(xyxy[2])] = blur
        
        if save_txt:  # Write to file 각 객체의 클래스 정보 좌표 정보 저장
            xywh = (xyxy2xywh(torch.tensor(xyxy).view(1, 4)) / gn).view(-1).tolist()  # normalized xywh
            line = (cls, *xywh, conf) if save_conf else (cls, *xywh)  # label format
            with open(f'{txt_path}.txt', 'a') as f:
            f.write(('%g ' * len(line)).rstrip() % line + '\n')
        if save_img or save_crop or view_img:  # Add bbox to image
            c = int(cls)  # integer class
            label = None if hide_labels else (names[c] if hide_conf else f'{names[c]}{conf:.2f}') #바운딩박스 
			annotator.box_label(xyxy, label, color=colors(c, True))# 바운딩 박스 표시
        if save_crop:
			save_one_box(xyxy, imc, file=save_dir / 'crops' / names[c] / f'{p.stem}.jpg', BGR=True)
```

검출된 객체의 xyxy 좌표를 cv2.blur 처리를 해주었습니다.

blurratio는 40으로 설정해주었습니다.



### 3. train

```
!python detect.py --weights best.pt --img 416 --conf 0.4 --source (원하는 동영상 경로)
```

train된 모델을 불러와서 학습시켜주면 됩니다.



## 결과

<img src="https://github.com/2Swon/DeepLearing/blob/main/Project/mosaic_YOLOv5/img/2.png" alt="1">

