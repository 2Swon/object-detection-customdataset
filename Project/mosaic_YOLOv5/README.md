# car number mosaic(개인 프로젝트)
뉴스, 드라마, 유튜브등 동영상들을 촬영할때 차량번호가 나오는 것을 방지하기 위해<br>
차량 번호판을 모자이크 처리해주는 을 만들었습니다.

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
#데이터셋 다운로드
!pip install roboflow

from roboflow import Roboflow
rf = Roboflow(api_key="krcQlWTnG5PUWdI1yzWt")
project = rf.workspace("license-plate-mhig5").project("license-plate-7egee")
dataset = project.version(5).download("yolov5")
```

데이터셋 다운로드

```
names: ["LP"]
nc: 1
train: ./license-plate-5/train/images'
val: ./license-plate-5/valid/images
test: ./license-plate-5/test/images
```

data.yaml 파일 설정



### 2. detect.py

```py
		blurratio = 40 # blur강도 설정
            if len(det):
                # Rescale boxes from img_size to im0 size
                det[:, :4] = scale_boxes(im.shape[2:], det[:, :4], im0.shape).round()

                # Print results

                for c in det[:, 5].unique():  # 객체 개수 출력
                    n = (det[:, 5] == c).sum()  # detections per class
                    s += f"{n} {names[int(c)]}{'s' * (n > 1)}, "  # add to string

                    # Write results

                    for *xyxy, conf, cls in reversed(det):
                        ##모자이크 처리부분
                        crop_obj = im0[int(xyxy[1]):int(xyxy[3]), int(xyxy[0]):int(xyxy[2])]
                        blur = cv2.blur(crop_obj, (blurratio, blurratio))
                        im0[int(xyxy[1]):int(xyxy[3]), int(xyxy[0]):int(xyxy[2])] = blur

                    if save_txt:  # Write to file 각 객체의 클래스 정보 좌표 정보 저장
                        xywh = (xyxy2xywh(torch.tensor(xyxy).view(1, 4)) / gn).view(-1).tolist()  # normalized xywh
                        line = (cls, *xywh, conf) if save_conf else (cls, *xywh)  # label format
                        with open(f'{txt_path}.txt', 'a') as f:
                            f.write(('%g ' * len(line)).rstrip() % line + '\n')
                    if save_img or save_crop or view_img:  # Add bbox to image
                        c = int(cls)  # integer class
                        label = None if hide_labels else (names[c] if hide_conf else f'{names[c]}{conf:.2f}')  # 바운딩박스
                        annotator.box_label(xyxy, label, color=colors(c, True))  # 바운딩 박스 표시
                    if save_crop:
                        save_one_box(xyxy, imc, file=save_dir / 'crops' / names[c] / f'{p.stem}.jpg', BGR=True)
```

원본이미지를 카피한 후 추출된 xyxy좌표를 blur처리해줌



### 3. train

```python
!python detect.py --weights /best.pt --img 416 --conf 0.4 --source (이미지 or 동영상 경로)
```



#### 4. 결과



<img src="https://github.com/2Swon/DeepLearing/blob/main/Project/mosaic_YOLOv5/img/2.png" alt="1">

