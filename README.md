# dacon-cancer-classification-competition
* 2024 생명연구자원 AI활용 경진대회 소스 코드
* **상위 13%**

---

1. Baseline_model_comparision (1).ipynb
이거는 처음에 만든 파일으로 기본적인 모델들을 단순한 인코딩(레이블 인코딩)을 이용해서 모델끼리의 성능 비교를 해본 것
   + 여기서 알아낸 것
     + 특정 모델이 특정 클래스를 잘 잡아냄
     + 하나의 feature에 띄어쓰기를 기준으로 multi feature를 갖는 경우가 있음
2. automl (1).ipynb
autogluon이라는 GPU를 사용하는 모델을 통해서 구해본 것, 여기 있는 input 데이터는 X_encoded_pca라는 건데, raw data에서 multi_label을 MultiBinaizer이라는 걸로 구분해서 총 feature을 완전 분리해냄.
마치 ohe처럼 (4385 features -> 20만 얼마로 늘어남)
따라서 이렇게 늘어난 것을 줄이고자 pca로 특징을 3천개 정도로 줄이고 학습을 돌림(이유는 시간 절약 및 여러가지 이유가 있음)
현재 성능 0.26~으로 나온 모델이 이 모델로 돌린 것임
시간은 약 : 1시간정도

추가 정보
* joblib이라는 파일은 모델을 학습하거나, 인코딩할때 시간이 굉장히 많이 들기 때문에 시간을 아끼고 이를 저장하는 용도로 사용되는 폴더임
모델 학습 : 2시간정도
데이터 인코딩 : 4시간 넘게 걸렸음 
* 다만 지금 X_encoded_pca.csv는 너무 파일이 커서 push하지 않은 상태

---

data : train.csv, test.csv
data shape : train.csv(6201, 4385), test.csv(2546, 4384)
학습 방법 :
1. 데이터를 read_csv로 가져오기. index=ID로 설정
2. target값이 될 'SUBCLASS' 열을 분리하고 보관.
3. train data의 열이 범주형 multi value를 갖고 있기 때문에 인코딩이 필요하다. 따라서 범주형과 숫자형 열을 구분해서, 범주형 열을 각기 인코딩. MultiLabelBinarizer를 이용해서 열마다 인코딩을 하고 그에 대한 MultiLabelBinarizer객체를 저장. 추가로 multi value는 띄어쓰기로 구분되어 있으며, 분리하고 나서 숫자형 값으로 나오는 것도 범주형으로 바꿔서 진행. 인코딩된 값은 새로운 데이터프레임에 저장
4. 이전에 인코딩한 데이터프레임과 'SUBCLASS'열을 갖고 train_test_split으로 분리를 하는데 stratify=y으로 설정
5. random forest모델을 이용해서 학습하고 검증 데이터를 평가
6. 학습된 모델은 joblib을 이용해서 저장
7. 테스트 데이터의 각 열을 저장해놓은 인코딩하고 저장된 모델을 사용해서 inference하고 [ID, SUBCLASS]이 있는 submisiion.csv를 생성
