# khu_sw_capstone

## 1. 연구 배경

#### 다중 토픽별 문서 요약
- 리뷰에서 핵심 단어를 추출하면 어떨까?라는 호기심을 가지게 됨
- 기존 LDA로는 한계가 있어, 성능이 좋은 논문은 적용해보고자 함.

#### 한국어 데이터에 맞는 aspect 추출 연구의 부재
- 영어 데이터에 비해 한국어에 맞는 aspect 연구의 부재
- Labeling 되어 있는 데이터셋이 없어 비지도학습 기반 연구 진행

#### 소비자에게 더 빠르고 직관적인 리뷰 정보 제공
- aspect 단위로 리뷰를 요약한다거나, 감정 분석을 진행한다면 좀 더 직관적인 정보를 제공 가능함

## 2. 연구 과정

### 데이터 수집
- 올리브영 스킨/토너 데이터 20,000개 수집
- 화장품 도메인의 리뷰가 피부/사용후기/제품정보 등 카테고리가 뚜렷하게 소비자들이 작성하는 경향이 있음
- 그 중 스킨, 토너 제품이 보편적으로 리뷰를 작성하며 특징(aspect)가 뚜렷하게 추출될 것이라고 생각해서 선정

### 데이터 전처리
- 특수문자 제거 : 크롤링을 통해 수집된 html 태그 및 불필요한 특수문자 제거
- 띄어쓰기 및 맞춤법 교정 : 한국어 리뷰 특성상 맞춤법과 띄어쓰기가 틀리는 경우가 많아 교정해 줄 필요가 있음.
- 형태소 분석기(mecab)을 활용한 명사 추출 : aspect의 후보가 될 수 있는 형태소는 동사나 형용사보다, 명사가 더 적합하다고 생각해서 선정
- kss 한국어 분장 분리기 : review 단위로 attention을 진행하면 여러 aspect를 갖는 문장은 적절하게 학습될 수 없음. review를 최소 문장 단위로 분류하여 aspect 추출 진행.

### Word Embedding
aspect 단위로 추출하기 위해 전체 vocab을 대변하는 word embedding matrix가 필요함.
- Word2Vec CBOW : 주변에 있는 단어들을 통해 중간에 있는 단어를 예측하는 방법

### Aspect를 위한 초기 군집 선정
- kmeans clustering : 각 클러스터와 거리 차이의 분산을 최소화하는 방식으로 작동. 초기 군집의 centroid point를 통해 뒤에 나올 model 학습

### Modeling(ABAE)
- 임베딩 공간에서 각 속성을 나타내는 대표 단어를 통해 해석 가능한 aspect embedding을 학습하는 것이 목표
- non-aspect 단어를 걸러내고, attention 가중치를 계산하여 문장 임베딩 도출
- 입력 문장이 어떤 aspect에 속하는지 확률 계산
- aspect 임베딩 행렬을 이용하여 선형 결합으로 문장 임베딩 재구성

### 연구 결과


### 기대효과 및 
