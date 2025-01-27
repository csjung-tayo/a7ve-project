# 리뷰 분석기반 고객맞춤형 제주도 호텔 추천 시스템

## 1. 문제 인식 및 정의
호텔이나 숙박시설을 예약할 때 고객들은 리뷰를 읽고 결정을 하는 경우가 많고, 원하는 호텔을 찾기위해 고객은 리뷰를 다 찾아 읽고 걸러내는 데에 있어 시간이 많이 소요됩니다. 현재 여러 호텔예약사이트를 시장조사한 결과 호텔 별점, 가격대, 위치등으로 호텔을 필터하는 기능은 있으나 실제 리뷰를 반영하여 개인화된 맞춤형 호텔을 추천해주지는 못했습니다. 이렇게 정의한 문제점을 바탕으로 가장 인기있는 여행 플랫폼인 야놀자의 제주도내 호텔의 리뷰 분석을 이용하여 고객맞춤형 호텔 추천 시스템을 개발하였습니다. 

## 2. 데이터 수집
- 채널 : 야놀자의 제주 지역 호텔의 리뷰 웹 크롤링
- 기간 : 2019.06.01 ~ 2021.08.07

## 3. 분석 프로세스
1. 크롤링
    1. 리뷰 200개 이상인 호텔 필터링
    2. 호텔 코드, 이름, 위치(특징) 가져오기
    3. 크롤링 함수 : 특수문자 제외, 띄어쓰기 포함 15글자 이상 500글자 이하, 기간 설정(2019.06 이후), 리뷰 개수가 200개 이상인 호텔의 모든 리뷰 가져오기
    → data : 12920개
2. 전처리
    1. Sampling : 크롤링 후, 호텔별 리뷰 200개 랜덤 추출 → final data : 31*200 = 6200개
    2. 자음, 모음 남발 제외
    3. 이모티콘 제외
    4. 띄어쓰기 제외 (적용 X)
    5. 맞춤법 검사(네이버 맞춤법 검사 라이브러리 hanspell 사용)
    6. 형태소 분석을 위한 추가적인 전처리 (가성비, 뷰 -> 구성비, 경치)
    - +) 가성비 -> 구성비 : 가성+비로 단어가 분리되어 의미를 잃는 것을 막고자, 구성비로 변경 후 형태소 분석 이후 다시 가성비로 교체
3. 형태소 분석
    - 불용어 제거
    - 한 글자 단어 제거
    - 다빈도 단어 추출 (호텔별 50개)
4. 키워드 추출 (상위 키워드 7개)
    - 청결 : 깨끗, 깔끔, 청결, 청소 ...
    - 서비스 : 친절, 직원, 서비스, 프런트, 응대 ...
    - 가격 : 가격, (가성비, 가격 대비 → 구성비), 저렴 ...
    - 위치 : 위치, 공항, 접근성, 시장, 주변, 근처, 바다, 뷰→경치 ...
    - 시설 : 시설, 객실, 수영장, 조식, 화장실, 식당, 샤워, 수압, 욕조, 칫솔, 에어컨, 침구, 수건 ...
    - 접근성 : 주차, 주차장, 편의점, 리조트 ...
    - 소음 : 소리, 방음, 조용 ...
5. Bubble Chart : 다빈도 단어의 시각화
6. 유용한 리뷰 필터링
    - 키워드 여부와 리뷰 길이에 따라 리뷰에 유용한 정보가 있다고 판단하여 최종 추천된 호텔의 유용한 리뷰를 제공하여 호텔을 파악하는 데 도움
    - 기준 : 1. 키워드 개수 2개 이상, 2. 키워드 1개 + 글자 수 42자(리뷰 길이의 중간값) 이상
7. 감성분석 (LSTM)
    - 약 13,000개 데이터를 직접 읽어 본 결과, 별점 1점과 5점의 리뷰는 각각 부정/긍정이 대체로 확실하나 별점 2\~4점의 리뷰에는 긍정과 부정 내용이 혼재되어 있음
      따라서, 별점 2~4점이 실제 리뷰 내용을 정확하게 반영하지 못하므로 실제 리뷰 기반의 긍•부정 라벨링 재진행
    - 별점 기반 감성 분석 정확도 약 81% → 실제 리뷰 기반 감성 분석 정확도 약 91%
8. 유사도 분석 (tf-idf)
    - 긍정인 리뷰 데이터를 바탕으로 호텔별 리뷰를 하나의 문서(총 31개)로 합친 후, 호텔별 7개 키워드의 TF-IDF 지표 확인
9. 최종 시스템
    → 사용자의 키워드 3개 입력
    → 유사도 기반 가중치(3,2,1) 조정 후 상위 호텔 3개 출력
    → 상위 3개 지도(folium) 시각화, Radar Chart, 호텔별 긍/부정 WordCloud, 호텔별 유용한 리뷰 출력
    
## Collaborators
    지다영 | dys621124@gmail.com 
    정새롬 | saerom.celiajung@gmail.com 
    
© 2021 DaYeong Ji <dys621124@gmail.com>, Saerom Jung <saerom.celiajung@gmail.com>
