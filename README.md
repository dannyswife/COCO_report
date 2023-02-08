# COCO 보고서
## 전공계열별 취업 현황
### 데이터 불러오기
    employee = pd.read_csv("/content/drive/MyDrive/코사다마 2023 coco/데이터/직업별_전공계열별_취업자_20230121215640.csv", encoding='cp949')

### 보기 편하게 데이터 수정
    employee_pivot = pd.pivot(data=employee, values=['2021.2/2', '2022.1/2'], index='전공계열', columns='직업')

### 2022 데이터와 2021 데이터로 나누기
    #2022
    employee_2022 = pd.pivot(data=employee, values='2022.1/2', index='전공계열', columns='직업')

    #2021
    employee_2021 = pd.pivot(data=employee, values='2021.2/2', index='전공계열', columns='직업')


### 데이터 전처리(직업 행 & 전공계열 행 삭제)
    employee_2022.drop('직업', axis=1, inplace=True)
    employee_2021.drop('직업', axis=1, inplace=True)

    employee_2022.drop('전공계열', axis=0, inplace=True)
    employee_2021.drop('전공계열', axis=0, inplace=True)



## 직업 보수 현황
### 데이터 불러오기
    신규채용현황 = pd.read_excel('/content/drive/MyDrive/코사다마 2023 coco/데이터/4.신규채용현황.xlsx', header=1)

### 항목에서 상임임원총신규채용, 고졸인력 제외 & 2017~2021년 컬럼, 상위기관 컬럼 삭제
    신규채용현황_drop = 신규채용현황[~신규채용현황.항목.isin(['상임임원총신규채용','고졸인력','청년','여성','장애인','비수도권지역인재','이전지역지역인재'])].drop(['기관유형','주무부처','2017년','2018년','2019년','2020년','2021년','상위기관'],axis=1)

### 
    신규채용현황_sum = 신규채용현황_drop.groupby(['기관명']).sum().reset_index()

### 
    신규채용현황_sum.sort_values(by=['2022년'], ascending=False).head(30)

###
    신규채용현황_병원삭제 = 신규채용현황_sum[~신규채용현황_sum['기관명'].str.contains('병원')]
    신규채용현황_최종 = 신규채용현황_병원삭제.sort_values(by=['2022년'], ascending=False).head(20)

## 휴직 제도