DACON 태양광 발전량 예측 AI 경진대회
=============
설명
-------------
DACON 태양광 발전향 예측 AI 경진대회 Private Score 기준 9위를 달성했습니다.  
대회 링크: https://dacon.io/competitions/official/235680/overview/

|파일이름|설명|
|---|---|
|Analysis_Output|LSTM 출력 경향을 분석한 파일|
|Apply_Grid_Search|Grid search를 이용한 xgboost 파라미터 튜닝|
|cnn_model|CNN을 활용한 예측 모델|
|eda|EDA 파일|
|lgbm_model|LGBM을 활용한 예측 모델|
|LSTM_CNN_MLP_Stacking|3개의 모델을 staking한 모델|
|lstm_model|LSTM을 활용한 예측 모델|
|MLP|Multi Layer Perceptron을 이용한 예측 모델|
|seq2seq_model|Seq2Seq를 이용한 예측 모델|
|Stat_Model|statsmodels을 적용한 예측모델|
|xgb_model|XGB를 활용한 예측 모델|
|XGB_try|Loss Function Customizing을 통한 XGB예측모델|
|XGB+LGBM+MLP_Staking|3개의 모델을 staking한 모델|

접근
------------
## EDA  
![image](https://user-images.githubusercontent.com/50457791/106358949-98629e00-6352-11eb-96d3-9bf8261e999a.png)  
EDA 결과 계절(분기)별로 발전량의 변화가 뚜렷하게 나타남을 관찰했습니다.  
Test 데이터는 7일간의 데이터만 입력하기에 계절성을 제거하는 것이 성능에 유리하다고 판단했습니다.  

![image](https://user-images.githubusercontent.com/50457791/106358958-a57f8d00-6352-11eb-96c1-7c82bb820df5.png)  
EDA를 통해 계절성을 제거하였으며 성능 향상을 이루었습니다.  

![image](https://user-images.githubusercontent.com/50457791/106358991-cf38b400-6352-11eb-8eb8-c0c42371217e.png)  
또한 Feature Engineering을 통해 새롭게 생성한 주당 최대 발전량과 날씨 요소별 발전량을 나타냈습니다.  
그래프를 통해 최대 발전량과 날씨에 따라 발전량 양상이 다르게 나타나며 두 피쳐가 유의미함을 확인했습니다.  

## Feature Engineering  
EDA와 레퍼런스를 통해 다음의 추가적인 피쳐를 생성했습니다.
- max_week_*  
각 피쳐들의 주 단위 최대 값입니다.  
계절과 분기에 따른 오차를 보정하는데 이용합니다.
- peak_diff  
주 단위 최대 발전량에서 매 타임마다 발전량의 차이를 나타냅니다.  
해당 주차에서 발전할것으로 기대되는 최대 발전량에 못미친 정도를 나타냅니다.
- GHI  
ref: https://doi.org/10.8080/1020150067066  


- RDHNI  

- Lower_Cloud, Middle_Cloud  
ref: http://koreascience.or.kr/article/JAKO201303840303693.page  

- DD  
ref: https://nvlpubs.nist.gov/nistpubs/jres/74c/jresv74cn3-4p117_a1b.pdf  



## PreProcess  
3년치의 Train 데이터를 활용하기에 각 년도별로 데이터를 나누어 train/valid set을 나누었습니다.  
이후 세 모델의 평균으로 결과를 구해 과적합을 줄였습니다.  
|Model Number|Train|Test|
|---|---|---|
|Model 1|year_1, year_2|year_3|
|Model 2|year_1, year_3|year_2|
|Model 3|year_2, year_2|year_1|


## Model  
![image](https://user-images.githubusercontent.com/50457791/106359942-293c7800-6359-11eb-8d58-1bdc19ccc8ec.png)  
7일간의 데이터인 336 Time-step을 단일 모델에 입력하면 긴 step과 반복되는 값 (밤에는 발전량이 0)으로 데이터 소실이 클것으로 예상했습니다.  
하루마다 다른 GRU 모델에 입력하고 출력 결과를 이어붙여 다시 LSTM에 입력하는 것으로 성능 향상을 이루었습니다.


## Result  
![image](https://user-images.githubusercontent.com/50457791/106360041-b8499000-6359-11eb-9156-936b2d0a5fa3.png)  
quantile마다 다른 스케일로 의도한대로 값을 예측할 수 있었습니다.  
public: 1.80359   private: 2.01757 를 기록하며 private 기준 9위를 달성했습니다.

---
## Author
- 박지홍 (qkrwlghddlek@gmail.com)
- [정성헌](https://github.com/EavnJung) (jshoney15@gmail.com)
