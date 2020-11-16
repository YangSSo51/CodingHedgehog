# 도치약국

## CodingHedgehog (코딩하는 고슴도치)

### __안현진,배선영,이상현,양소영__

#### 문서 관리 : slack
#### 일정 관리 : trello

### 0. 화면 이미지
<img src="https://user-images.githubusercontent.com/48993188/78004974-df1fea00-7375-11ea-85e7-32cc78626c27.jpg" width="30%" height="450"><img src="https://user-images.githubusercontent.com/48993188/78005333-61a8a980-7376-11ea-8711-4f311b5aa713.jpg" width="30%" height="450"><img src="https://user-images.githubusercontent.com/48993188/78005422-80a73b80-7376-11ea-8e7e-06777d5756f7.jpg" width="30%" height="450">
<img src="https://user-images.githubusercontent.com/48993188/78005462-8f8dee00-7376-11ea-85a4-8f92f909a535.jpg" width="30%" height="450"><img src="https://user-images.githubusercontent.com/48993188/78005498-9d437380-7376-11ea-9823-bc60a3493793.jpg" width="30%" height="450"><img src="https://user-images.githubusercontent.com/48993188/78005522-a7657200-7376-11ea-8957-78549ab9fb71.jpg" width="30%" height="450">


## 데이터
----------------------------
### 1. 데이터 수집
- 기본 데이터
  - 공공 API를 통해 데이터 수집
  - public_api 폴더의 코드 사용
- 테스트 데이터
  - public_api/download_test.py 참고
- 상세 데이터
  - 약의 효능,복용방법에 대한 데이터 수집
- 상호 작용 데이터
  - 함께 복용하면 안되는 알약 데이터 

### 2. 데이터 라벨링
- 딥러닝
  - api 데이터에서 내용 탐색 후 자동 라벨링
- 객체 탐지
  - object 영역 labelImg 도구로 라벨링
  - [labelImg](https://github.com/tzutalin/labelImg)

### 3. 이미지 편집
1. 객체 탐지를 통해 알약 객체 찾기
2. 객체 영역의 좌표값 저장
3. openCV를 통해 해당 좌표값을 기준으로 사진 자르기

## 딥러닝
----------------------------
### 0. 이미지 전처리
1. OpenCV의 Bilateral Filter를 기반으로 이미지 화질을 개선함.
2. 알약 이미지에서 Canny Edge 알고리즘과 LAB 색상 공간, Adaptive Threadhold를 적용해  그림자를 제거함.

### 1. 모양 학습
- 알약 이미지에 대해 Zernike Moment를 이용하여 알약의 모양을 추출함.

### 2. 색상 학습
- 알약이 탐지된 이미지에서 OpenCV를 활용한 색상 평균 추출

### 3. 문자 학습
- 알약 이미지에 대해 OpenCV를 활용해 윤곽(Contour)을 추출하고 CNN Deep Learning 모델로 학습 및 OCR을 이용하여 알약의 글자를 추출함

### 4. 객체 탐지
1. 학습
    - [Darkflow](https://junyoung-jamong.github.io/deep/learning/2019/01/22/Darkflow%EB%A5%BC-%ED%99%9C%EC%9A%A9%ED%95%B4-YOLO%EB%AA%A8%EB%8D%B8-%EC%9D%B4%EB%AF%B8%EC%A7%80-%EB%94%94%ED%85%8D%EC%85%98-%EA%B5%AC%ED%98%84-in-windows.html)
    - predefined_classes.txt,labels.txt 내용 Pill로 변경
    - tiny-yolo.cfg 내용 중 filters=(5+classes)*5=30/classes=1로 변경
    - data 디렉토리 아래에 annotations,dataset 디렉토리를 생성하고 라벨링된 데이터를 넣어줌
    - 명령어를 통해 학습시켜주고 TensorBoard를 통해 loss의 수렴정도를 확인함
2. detection
    - [Object Detection](https://junyoung-jamong.github.io/machine/learning/2019/01/25/Android%EC%97%90%EC%84%9C-%EB%82%B4-YOLO%EB%AA%A8%EB%8D%B8-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0.html)
    - 학습 데이터의 *.pb파일를 assets에 추가해줌
    - DetectorActivity.java에서 파일 경로와 DetectorMode를 변경
    - TensorFlowYoloDetector.java에서 클래스 개수와 label명을 변경
3. 성능평가
    - testdata에 대한 이진분류결과표를 작성
    - sklearn 라이브러리를 이용하여 학습모델에 대한 평가점수(정확도, 정밀도, 재현율, f점수)를 계산한다.
    
## 웹서버
----------------------------
### 1. DB 설계
### 2. DB 구축
### 3. 서버 구축
- nodejs 웹 서버 구축
### 4. 서버 통신
- express 모듈을 활용한 안드로이드 앱과의 통신
- sequelize 모듈을 활용한 mysql 데이터베이스 통신
- multer 모듈을 활용 안드로이드 앱으로부터 서버로 사진 전송
- JSON 파싱을 통한 데이터 전송

## 애플리케이션
----------------------------
### 1. UI 제작
- Material UI 사용
- PowerMockUp으로 스토리보드 작성

### 2. 기본기능 제작
- 객체 탐지 카메라 기능
- 기본 정보 제공
- 복약 알림 : 복용횟수에 따라 알림 발생
- 복약리스트 : 자주 검색하는 알약을 즐겨찾기에 저장가능

### 3. 웹서버 통신
- 기본 검색 기능(텍스트로) : 알약이름,식별표시,색상,모양 을 선택하면 해당하는 알약리스트를 반환

### 4. 사진 검색 기능
- 객체 탐지 카메라를 사용하여 알약 객체 인지
- 알약 객체에서 정보를 추출하여 검색 결과 반환

### 5. 기본 검색 기능
- 알약 식별 정보(색상,모양,각인문자)를 바탕으로 알약 검색
- 해당하는 알약에 대한 상세 정보 반환(기본정보,복약방법,주의사항 효능)
- TTS를 통해 상세 정보를 음성으로 들을 수 있음

### 6. 상호작용 리스트 
- 함께 복용하면 안되는 알약들에 대한 리스트와 경고문구 보여줌

