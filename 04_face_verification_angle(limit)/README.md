# Face Verification(angle) limit

## Task
```
이 문제에서는 모델 아키텍쳐에 사용 할 수 있는 파라메타가 제한됩니다. (500만개)

얼굴 이미지_1(측면 얼굴)과 얼굴 이미지_2(정면 얼굴)의 동일인 여부를 분류(True/False)하는 문제
측면 조건: -90, -75, -60도 / 60, 75, 90도
정면 조건: -15, 0, 15도
상하 조건: 0도 (정면만 사용)
조도 조건 : 1000, 400, 200, 150, 100 Lux (조명 전체 켜짐)
악세사리 조건 : 미착용
```

## Dataset
#### Image Resolution : 112 X 112

<img width=900 src="images_for_desc/image.png"/>

| Phase | front | side | total |
| - | - | - | - |
| train | 22,050 | 44,100 | 66,150 |

```
train data로 정면과 측면 얼굴 이미지를 다양하게 제공합니다. 
학습 시간 등을 고려하여 dataloader로 부터 자유롭게 로드하여 사용하시면 됩니다.
```

| Phase | True | False | total |
| - | - | - | - |
| validate | 5,000 | 5,000 | 10,000 |
| test | 10,000 | 10,000 | 20,000 |

```
validate와 test data는 정면과 측면 이미지 한 쌍을 Pair로 제공합니다.
```

## Data Directory
```
\_data
    \_ train
        \_ ??????_S001_L??_E??_C??_cropped.jpg (images)
        \_ train_meta.csv
        \_ train_label.csv (train 시 dataloader 코드에서 생성되어 저장됨)
    \_ validate
        \_ ??????_S001_L??_E??_C??_cropped.jpg (images)
        \_ validate_label.csv
    \_ test
        \_ ??????_S001_L??_E??_C??_cropped.jpg (images)
        \_ test_label.csv (dummy labels)

image_name = "Person"_S001_"Illumination"_"Expression"_"Camera_Angle"_cropped.jpg
```

## Data Sample
<img width=400 src="images_for_desc/image2.png"/>　　　<img width=400 src="images_for_desc/image3.png"/>

## Label
```
# train_meta.txt
(image_name)                         (face_id)  (ang_option)
19082212_S001_L1_E01_C8_cropped.jpg  19082212   front

# *****_label.txt
(none_image_name)                    (acc_image_name)                    (label)
19082212_S001_L1_E01_C8_cropped.jpg  19082212_S001_L5_E02_C3_cropped.jpg  0

```

## Metric
```
F1 score
```

## Description
```
측면 얼굴(side) 이미지로 정면 얼굴(front) 이미지와의 동일인 여부를 판단하는 문제입니다.
정면 1장과 측면 1장이 한 pair로 주어집니다.(이 때, 조명 밝기와 표정은 랜덤으로 주어짐)
label은 동일인일 때 0, 동일인이 아닐 때 1로 예측하시면 됩니다.

Baseline code는 siamese network로 모델링되어 있으며 이는 자유롭게 변경하여 작성하시면 됩니다.
train data 기준으로 만들 수 있는 최대 Pair의 수는 약 10억 쌍 정도이나 
모든 쌍을 학습에 사용하지 않고 train 실행 시 학습에 사용할 pair를 만들도록 작성되어 있습니다.
이 부분 역시 자유롭게 변경하여 사용하실 수 있습니다.
다만, 정확한 evaluation을 위해 label file 양식은 변경하실 수 없습니다.
```

## Commands
```
# train
python main.py --lr=0.001 --cuda=True --num_epochs=10 --print_iter=10 --model_name="model.pth" --prediction_file="prediction.txt" --batch=16 --mode="train"

# test (for submission)
python main.py --batch=16 --model_name="1.pth" --prediction_dir="prediction" --mode="test" 


All options in example commands are default value.
```

## Submission
```
prediction.txt 파일을 제출하시면 됩니다.

prediction.txt 파일은 front image, side image, label 형태입니다.
label 부분을 예측 값으로 작성하시어 제출하시면 됩니다.
```

## Notice

'''
!!!!!!!!!!!!!!!!!!!!! 필독!!!!!!!!!!!!!!!!!!!!!!!!!!!

** 컨테이너 내 기본 제공 폴더
- /datasets : read only 폴더 (각 태스크를 위한 데이터셋 제공)
- /tf/notebooks :  read/write 폴더 (참가자가 Wirte 용도로 사용할 폴더)
1. 참가자는 /datasets 폴더에 주어진 데이터셋을 적절한 폴더(/tf/notebooks) 내에 복사/압축해제 등을 진행한 뒤 사용해야합니다.
   예시> Jpyter Notebook 환경에서 압축 해제 예시 : !bash -c "unzip /datasets/objstrgzip/18_NLP_comments.zip -d /tf/notebooks/
   예시> Terminal(Vs Code) 환경에서 압축 해제 예시 : bash -c "unzip /datasets/objstrgzip/18_NLP_comments.zip -d /tf/notebooks/
   
2. 참가자는 각 문제별로 데이터를 로드하기 위해 적절한 path를 코드에 입력해야합니다. (main.py 참조)
3. 참가자는 모델의 결과 파일(Ex> prediction.txt)을 write가 가능한 폴더에 저장되도록 적절 한 path를 입력해야합니다. (main.py 참조)
4. 세션/컨테이너 등 재시작시 위에 명시된 폴더(datasets, notebooks) 외에는 삭제될 수 있으니 
   참가자는 적절한 폴더에 Dataset, Source code, 결과 파일 등을 저장한 뒤 활용해야합니다.
   
!!!!!!!!!!!!!!!!!!!!! 필독!!!!!!!!!!!!!!!!!!!!!!!!!!!
'''
