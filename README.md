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
    직원평균보수현황 = pd.read_excel('/content/drive/MyDrive/코사다마 2023 coco/데이터/7.직원평균보수현황.xlsx', header=1)

### 
    직원평균보수현황.drop(['기관유형','주무부처','2017년','2018년','2019년','2020년','2021년','상위기관'], axis=1,inplace=True)

### 
    신규채용현황_sum = 신규채용현황_drop.groupby(['기관명']).sum().reset_index()
    직원평균보수현황_일반정규직 = 직원평균보수현황[직원평균보수현황['구분'].str.contains('일반정규직')]
    직원평균보수현황_최종 = 직원평균보수현황_일반정규직[직원평균보수현황_일반정규직['항목'].str.contains('1인당 평균보수액')]
    직원평균보수현황_최종.drop(['구분', '항목'], axis=1, inplace = True)

# 상여, 성과급, 기타수당, 기본급 
# 기본급 + 성과상여금 + 실적수당 + 고정수당 + 급여성 복리후생비
### 
    신규채용현황_sum.sort_values(by=['2022년'], ascending=False).head(30)

###
    신규채용현황_병원삭제 = 신규채용현황_sum[~신규채용현황_sum['기관명'].str.contains('병원')]
    신규채용현황_최종 = 신규채용현황_병원삭제.sort_values(by=['2022년'], ascending=False).head(20)

## 휴직 제도
#### 원본데이터명 : 그밖의_복리후생제도
#### 복리후생제도가 모두 휴직제도라서 이름 변경

### 데이터 불러오기
    휴직제도 = pd.read_excel('/content/drive/MyDrive/코사다마 2023 coco/데이터/10.그밖의_복리후생제도_등의_운영현황.xlsx', header=1)


### 필요없는 열 제거
    휴직제도.drop(['기관유형','주무부처','2017년','2018년','2019년','2020년','상위기관','구분'], axis=1,inplace=True)

### 문과 직렬만 남기기 위해 기관명 확인하고 이과 직렬 drop
    휴직제도['기관명'].unique()

    휴직제도.drop(휴직제도[휴직제도['기관명'].str.contains('가스|방역|병원|과학|해양|원자력|나노|에너지|체육|전력|전기', na = False)].index, axis = 0,inplace=True)

### 피벗테이블 만들기
    휴직제도_pivot = pd.pivot_table(data = 휴직제도, index = '기관명', values = '2021년', columns = '항목')

### 수치가 유의미한 데이터만 남기기
#### 결측값 0으로 대체
    휴직제도_pivot.fillna(0, inplace = True)
#### 데이터가 무의미한 column 삭제
#### 삭제한 대부분의 열의 값이 0    ex. 휴직제도_pivot[휴직제도_pivot['해외 동반 휴직']!=0]
    휴직제도_filtered = 휴직제도_pivot.drop(['가족 간호 휴직','고용 휴직','기관 고유지급금액','병역 휴직','해외 동반 휴직'], axis = 1)
#### 남은 것 중 모든 데이터가 0 인 열 삭제
    new_drop = 휴직제도_filtered[(휴직제도_filtered['업무상 공상']==0)&(휴직제도_filtered['업무외 질병']==0)&(휴직제도_filtered['연수 휴직']==0)&(휴직제도_filtered['유학 휴직']==0)&(휴직제도_filtered['육아 휴직']==0)]
    new_drop_list = new_drop.index
    휴직제도_최종 = 휴직제도_filtered.drop(new_drop_list, axis = 0)

### 수치가 유의미한 순으로 컬럼 재정렬
    휴직제도_최종.columns = ['업무외 질병','업무상 공상','육아 휴직','연수 휴직','유학 휴직']

