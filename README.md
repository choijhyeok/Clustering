# Clustering 분석 프로젝트
&nbsp;
## 1. 사용할 데이터
    - Facebook Live Sellers in Thailand Data Set : https://archive.ics.uci.edu/ml/datasets/Facebook+Live+Sellers+in+Thailand#
    - 태국의 패션 및 화장품 소매업체 10곳의 페이스북 페이지의 데이터, 게시물(동영상, 사진, 상태 및 링크), 참여 메트릭은 댓글, 공유 및 반응으로 구성
   
## 2. 과정 및 목적
  - 비지도 학습의 Clustering에 대한 깊은 이해를 위해 수행
 - 기존의 PCA만으로 차원축소를 할 때 주성분이 너무 많이 나와서 제대로 된 시각화, 해석이 어려움
    - PCA만 사용했을 때는 정확하게 성분들이 구별되지 않는 경우가 많음 (np.argmax(cumsum >= 0.97)를 통해서 97% 설명력 기준)
    - 또한 주성분이 많이 나오면 시각화와 이해하기가 어려움 (3차원 이상일 경우 해석이 어렵고 시각화가 난해함)
    - 이를 해결하기 위해서 PCA 설명력 95% 기준으로 PCA 수행 후 나온 결과를 tsne 주성분 2로 다시 차원축소 수행
    - 비교 예시를 위해서 3D,2D 이미지 시각화
 - k-means, dbscan, Agglomerative의 작동방식 이해와 군집이 제대로 이루어져 있는지 파악
    - 최종적으로 전처리 - 정규화 - 차원축소를 거친 데이터를 통해서 Clustering 수행 (k-means,Dbscan,Agglomerative)
&nbsp;
## 3. 사용할 clustering 
 1. k-means
    - 과정
        1. 일단 k개의 임의의 중심점을 배치
        2. 각 데이터를 가장 가까운 중심점으로 할당
        3. 군집으로 지정된 데이터를 기반으로 해당 군집의 중심점을 업데이트
        4. 2번,3번 단계를 그려서 수렴될 때까지 진행, 더이상 중심점이 업데이트되지 않을 때까지 반복
        
    - 장점
        - 쉽고 빠르게 연산이 가능
    - 단점
    - 
        - 휘발성이라서 다시 실행했을 때 같은 결과 못 얻을 수 있음
        - 임의로 k라는 군집 수를 잡아야 하고, 처음 정한 k가 cluster의 영향을 많이 줌.
        - outlier에 민감하고 아웃라이어까지 반영하며 중심을 정하는 문제
        - 원,구 모양이 아닌 다른 모양에서는 좋은 결과를 기대하기 어려움
        - 데이터 노이즈에 민감함
  &nbsp;
  2. dbscan
     - 과정
        1. eps,min_sample을 통해서 수행 (eps = 데이터 간 최대거리, min_sample = 중심객체에서 최대거리까지 포함되는 객체 개수)
        2. 임의의 데이터를 선정하고 eps거리안에서 min_sample에 해당하는 점을 중심객체
        3. min_sampled의 수를 만족하지 못하면 외각으로
        4. 겹치는 객체는 동일 군집으로 파악
        5. 위 과정을 계속 수행하면서 어디에서 속하지 않는 객체는 노이즈 처리
         
     - 장점
        - 클러스터 수 사전 정의 불필요
        - 노이츠 처리에 강함
        - 데이터의 모양이 특이한 상황에서 좋은 결과를 나타냄(U,Z 같은 모양)
        - 재현성이 있어서 다시 실행해도 같은 결과 반환
         
     - 단점
        - 많은 연산 때문에 결과처리 속도가 느림
        - 고차원 데이터로 갈수록 연산 길어짐
        - 주변 거리인 eps를 정의하기 어려움
        - 원,구 같은 데이터에서는 k-means가 더 좋음



&nbsp; 

   3. Agglomerative 
      - 과정
         1. 가장 거리가 가까운 데이터를 찾고 이들을 묶음
         2. 점점 하나로 묶어가면서 최종적으로 하나의 묶음이 될때까지 수행
         3. 모두가 하나에 묶이면 덴드로그램으로 쉽게 파악
         4. 원하는 클러스터는 그림을 몇번에서 자르면 되는지 파악하기 쉬움
          
          - 군집을 묶을때 4가지 거리척도가 존제함
             1. single linkage
                - 서로 다른 군집의 모든 데이터 간 거리 중 최소값
             2. complete linkage
                - 서로 다른 군집의 모든 데이터 간 거리 중 최대값
             3. ward linkage
                - 군집 내 편차들의 제곱합을 최소화 하도록 군집을 만들고
                - 군집 간 정보의 손실을 최소화 하는 방법
             4. Average linkage
                - 서로 다른 군집의 모든 데이터 간 거리의 평균
      - 장점
          - 사전에 클러스터 수 정의 불필요
          - 덴드로그램을 통해서 원하는 클러스터 수, 클러스터 간의 거리를 확인가능
          - 2D가 아닌 상황에서도 시각화가 용이
      - 단점
          - 새로운 데이터 포인트에 대해서 예측하기 어려움
          - 크기가 커지면 속도가 매우 느려짐
          - 한 번 병합이 되거나 분리된 군집은 되돌릴수 없다.


&nbsp;
## 4. 군집 평가척도

 - silhouette score
     - 0~1 사이의 값을 가지고 1에 가까울수록 좋음
     - 전체 군집의 실루엣 스코어를 시각화 했을때 전체가 고르게 크기가 분포된게 좋음
     - 실루엣 스코어가 음수가 있거나 전체 평균을 넘지 못한 군집이 없도록 하는게 좋음

 - calinski harabasz score
     - 값이 클수록 군집이 잘되어있다고 판단
     
 - davies bouldin score
     - 값이 작을수록 군집이 잘 형성되어있다고 판단
     
     
&nbsp;
## 5. 결과 산정
    - silhouette score가 음수가 없고, 모든 군집이 평균이상이며 각 군집이 균등한 군집선택
    - 그 중에서 calinski harabasz score가 높은 값을 선택
    - 최종적으로 davies bouldin score 까지 제일 낮은 군집을 각각의 알고리즘에서 선택

    
&nbsp;
## 2D vs 3D 이미지


<img src='http://drive.google.com/uc?export=view&id=18jCJtVeybybFd3eNkOVm2UatzVbAg1UJ' width="500">
<img src='http://drive.google.com/uc?export=view&id=13A3VT-ZW18Al5fgXpSn5kNkCVJoWaHg_' width="500">
