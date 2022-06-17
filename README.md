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

#### 데이터 수집
- 올리브영 스킨/토너 데이터 20,000개 수집
- 화장품 도메인의 리뷰가 피부/사용후기/제품정보 등 카테고리가 뚜렷하게 소비자들이 작성하는 경향이 있음
- 그 중 스킨, 토너 제품이 보편적으로 리뷰를 작성하며 특징(aspect)가 뚜렷하게 추출될 것이라고 생각해서 선정

#### 데이터 전처리
- 특수문자 제거 : 크롤링을 통해 수집된 html 태그 및 불필요한 특수문자 제거
- 띄어쓰기 및 맞춤법 교정 : 한국어 리뷰 특성상 맞춤법과 띄어쓰기가 틀리는 경우가 많아 교정해 줄 필요가 있음.
- 형태소 분석기(mecab)을 활용한 명사 추출 : aspect의 후보가 될 수 있는 형태소는 동사나 형용사보다, 명사가 더 적합하다고 생각해서 선정
- kss 한국어 분장 분리기 : review 단위로 attention을 진행하면 여러 aspect를 갖는 문장은 적절하게 학습될 수 없음. review를 최소 문장 단위로 분류하여 aspect 추출 진행.

#### Word Embedding
aspect 단위로 추출하기 위해 전체 vocab을 대변하는 word embedding matrix가 필요함.
- Word2Vec CBOW : 주변에 있는 단어들을 통해 중간에 있는 단어를 예측하는 방법
- GloVe : 말뭉치 전체를 대상으로 두 단어 간 동시 발생 수를 학습에 이용
- FastText : 단어를 글자의 n-gram으로 표현하는 skip-gram 기반 분산 표상 방식

#### Aspect를 위한 초기 군집 선정
- kmeans clustering : 각 클러스터와 거리 차이의 분산을 최소화하는 방식으로 작동. 초기 군집의 centroid point를 통해 뒤에 나올 model 학습

#### Modeling - Attention Based Aspect Extraction
- 임베딩 공간에서 각 속성을 나타내는 대표 단어를 통해 해석 가능한 aspect embedding을 학습하는 것이 목표
- non-aspect 단어를 걸러내고, attention 가중치를 계산하여 문장 임베딩 도출
- 입력 문장이 어떤 aspect에 속하는지 확률 계산
- aspect 임베딩 행렬을 이용하여 선형 결합으로 문장 임베딩 재구성

### 3. 연구 결과
Perplexity와 coherence score를 고려하여 aspect 군집을 5로 선정하여 진행.

#### aspect 추출 결과
[1] 넘버 증인 올리브 채소 마켓 브링 상품 클렌징 배송 기획 할인 입점 그린 구성 증정<br>
[2] 올리브 채소 넘버 마켓 증인 할인 가격 기획 세일 미번 브링 구성 대비 상품 용량<br>
[3] 각질 유분 저녁 아침 지성 피지 복합 건성 건조 타입 제거 느낌 겨울 세안 수북<br>
[4] 친구 남자 올리브 선물 상품 배송 채소 유튜브 마켓 추천 선점 할인 체험 무료 고민<br>
[5] 제형 아침 저녁 겨울 흡수 느낌 충전 건조 수분 유분 세안 알갱이 화장 부족 토로<br>

#### aspect 매핑
1 - 추가제품 및 기타정보<br>
2 - 이벤트<br>
3 - 피부타입 및 후기<br>
4 - 상품관련정보<br>
결과로 나온 단어들을 화장품 스킨/토너 도메인에 맞게 aspect를 매핑하였다.

### 4. 기대효과 및 활용방안
- 현재 이커머스 사이트에서 리뷰 관련 연구가 많이 진행되고 있다. 특히 네이버 쇼핑에서는 카테고리별로 리뷰를 나눠 요약 모델을 구현하고 있고, 화해 기업에서는 카테고리별 sentiment analysis를 진행하고 있다. 이에 따라 aspect를 추출하는 것은 기존에 있는 요약 모델과 ABSA모델에 좀 더 정교한 모델을 구혀하고자 도움을 준다.
- 특히 화장품 도메인에서는 사용자의 리뷰를 보는 경향이 강한데, 중요한 aspect를 추출하여 사용자가 직관적인 리뷰를 볼 수 있도록 도움을 줄 수 있다.

#### 결론 및 제언

##### 추가 데이터셋 수집
- aspect는 vocab을 기준으로 뽑기 때문에, 데이터의 양이 매우 중요

##### 전처리 개선
- n-gram 기법 추가 : 단일 단어 외 N개의 단어가 이어져 있는 것들도 aspect 후보가 될 수 있음.
- BPE 추가 : 한국어는 복합명사 및 파생명사가 많아, subword 단위로 토크나이징을 할 필요가 있음.

##### 분석 결과 성능 평가
- Labeling이 되어 있지 않은 크롤링 데이터이기 때문에 성능 측정지표가 필요함.
- 비지도학습 기반으로, 추후 평가(사람)을 구해 정성평가 진행.
