렌터카 리뷰 분석을 통한 감정 분석 & <br>
딥러닝을 활용하여 렌터카 고객 맞춤형 추천 서비스 제작
========================================
본 프로젝트는 Kaggle에서 제공한 데이터 셋을 활용하여 진행되었습니다. (csv파일 데이터 참고)


I. 프로젝트 개요
----------------
1. 프로젝트 배경 및 목적
리뷰를 통해 고객의 감정을 분석하고 어떠한 상품을 원하는지 의도를 파악하고자 한다. 
딥러닝을 통해 고객에게 맞춤형 상품 추천 서비스를 제작해보고자 한다. 
  
2. 데이터셋 소개
 ### Train data 80%와 Test data 20%를 통해 시행 
  x는 리뷰(review_text), y는 감정 점수(sentiment_Score)로 시행
  ##### Train data: 58 / Test data: 15
  
3. EDA

![image](https://github.com/user-attachments/assets/88f7ec43-5885-43f6-abab-11cda475e6a2)


![image](https://github.com/user-attachments/assets/5f46431d-f26e-4322-ba86-b1cc6cf62667)


![image](https://github.com/user-attachments/assets/233ecf7d-ed50-45d3-bf1e-ae3e0aa3c2aa)


![image](https://github.com/user-attachments/assets/3b869321-1292-4bce-9f86-6afdd318c086)<br>
![image](https://github.com/user-attachments/assets/dd4fb044-1dc1-4a19-8993-6e8d78bae9e0)

✅ 감정분석
산출 방법: Python Transformers 자연어 처리 모델<br>
review1, review2, review3를 review_text로 전처리 후 감정 분석 수행<br>
리뷰 길이 제한: 텍스트 길이 chunk로 나누어 최대 텍스트 길이 200자로 한정(제한된 길이로 설정해야 딥러닝 작동..?)<br>
감정분석 실행: 감정 분석 모델 텍스트를 긍정적인지 부정적인지 판단하는 작업 수행 후 긍정 부정적인 감정이 나타난 비율을 계산하여 최종 감정 점수를 산출

✅ 긍정과 부정을 단순히 특정 단어만 보고 판단하는 것이 아님 <br>-> 리뷰 텍스트 전체를 사전 학습된 감정 분석 모델 Bert 계열에 넣고 모델이 해당 문장이 긍정일 확률이 높은지 부정적인 확률이 높은지 출력해 확률에 따라 나눔 <br>
예1) I love how smooth and quiet the car drives <br>
-> 모델 예측: 긍정 Positive 확률 0.92 <br>
예2) The car looks good, but the engine has serious problems <br>
-> 모델 예측: 부정 Negative 확률 0.81 <br>
* 예시와 같이 두번째 문장은 good 같은 긍정 단어가 있음에도 불구하고 Problems라는 단어와 부정적인 맥락이 포함되어 있어서 모델이 전체 문맥을 보고 부정으로 분류 <br>
* 따라서 좋은 단어의 리스트 나쁜 단어의 리스트로 단어 자체 기준 (긍/부정) 기준으로 감정을 나누는 것이 아니라, 문장의 의미와 구조 전체를 이해하는 모델을 사용해 감정을 분류 <br>
* Word Cloud(분류된 리뷰들을 시각화)에서 확인할 수 있듯이 긍정 리뷰에는 'great', 'drive', 'smooth'같은 단어가 많이 등장했고 부정 리뷰에는 'problem', 'dealer', 'issue'같은 단어가 많이 나타남. <br>


II. 전처리
-----------
1. 적용 모델에 따라 전처리를 다르게 진행하거나, 아예 전처리를 하지 않은 부분도 있으나 기본적인 전처리는 아래와 같은 프로세스로 진행 되었음:
- 특수문자 제거(자체 함수 사용)
- 문장 분리(kss)
- 띄어쓰기(pykospacing)
- 중복 제거(soynlp.normalize)

2. 토크나이저
- Khaiii
- Okt
- Mecab

III. 데이터 분석 모델링
----------------
1. 모델 생성
![image](https://github.com/user-attachments/assets/7a239a18-efeb-4a6e-8a80-2740b544b213)

2. 분석 결과
![image](https://github.com/user-attachments/assets/8d503597-be8d-4214-9538-637fcd9baa93)
결과: 긍정 집단의 리뷰 점수와 부정 점수보다 많은 것을 확인할 수 있음
차종별 평균 리뷰 점수 순위: BMW>AUDI>MERCEDES>HYUNDAI>FORD
평균 감정 분석 순위: BMW>AUDI>TOYOTA>MERCEDES>HYUNDAI>FORD

<!DOCTYPE html>
<html lang="ko">
<head>
   <h1>최종적으로 예측 모델링을 통해 도출된 상품 추천 순위 </h1>
</head>
<body>
    <table border="1">
        <tr>
            <th>순위</th>
            <th>차종</th>
            <th>예측점수</th>
        </tr>
        <tr>
            <td>1</td>
            <td>HYUNDAI IONIQ</td>
            <td>0.5666</td>
        </tr>
        <tr>
            <td>2</td>
            <td>BMW3 SERIES</td>
            <td>0.559</td>
        </tr>
        <tr>
            <td>3</td>
            <td>BMW x7</td>
            <td>0.54987</td>
        </tr>
        <tr>
            <td>4</td>
            <td>BMW M5</td>
            <td>0.52397</td>
        </tr>
    </table>
</body>
</html>

