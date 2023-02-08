# COCO 보고서
## 전공계열별 취업 현황
### 데이터 불러오기
    employee = pd.read_csv("/content/drive/MyDrive/코사다마 2023 coco/데이터/직업별_전공계열별_취업자_20230121215640.csv", encoding='cp949')

### 보기 편하게 데이터 수정
employee_pivot = pd.pivot(data=employee, values=['2021.2/2', '2022.1/2'], index='전공계열', columns='직업')
employee_pivot.head(10)
## 직업 보수 현황
## 휴직 제도