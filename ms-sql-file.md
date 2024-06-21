### 10일 만에 마스터하는 MS SQL 실무 내용을 통해서 공부


## 1. 기본 쿼리
### 1.1. SELECT 문
```sql
쿼리문: SELECT * FROM study.dbo.companyinfo
```
설명: companyinfo 테이블의 모든 열과 행을 선택합니다.

### 1.2. DISTINCT
```sql

쿼리문: SELECT DISTINCT InclnCtryCode FROM companyinfo
```
설명: companyinfo 테이블에서 InclnCtryCode 열의 고유한 값만 선택합니다.

### 1.3. WHERE 절
```sql

쿼리문: SELECT * FROM companyinfo WHERE InclnCtryCode='kor'
```
설명: companyinfo 테이블에서 InclnCtryCode가 'kor'인 행만 선택합니다.

### 1.4. AND, OR, NOT 연산자
```sql
쿼리문: SELECT * FROM companyinfo WHERE InclnCtryCode='kor' AND Employees > 50000
```
설명: companyinfo 테이블에서 InclnCtryCode가 'kor'이고 Employees가 50000보다 큰 행을 선택합니다.

### 1.5. LIKE와 와일드카드
```sql
쿼리문: SELECT * FROM companyinfo WHERE name LIKE 'a%'
```
설명: companyinfo 테이블에서 name이 'a'로 시작하는 행을 선택합니다.

### 1.6. ORDER BY
```sql
쿼리문: SELECT InclnCtryCode, Employees, Name FROM companyinfo WHERE InclnCtryCode IS NOT NULL ORDER BY InclnCtryCode, Employees DESC
```
설명: companyinfo 테이블에서 InclnCtryCode가 NULL이 아닌 행을 선택하고, InclnCtryCode로 오름차순 정렬한 후 Employees로 내림차순 정렬합니다.

## 2. 집계 함수
### 2.1. 집계 함수 사용
```sql
쿼리문: SELECT MAX(Close_) AS 최고가, MIN(Close_) AS 최소가, AVG(Close_) AS 평균가 FROM StockPrice WHERE ID=40853
```
설명: StockPrice 테이블에서 ID가 40853인 행의 Close_ 열에 대해 최댓값, 최솟값, 평균값을 구합니다.

### 2.2. GROUP BY
```sql
쿼리문: SELECT City, SUM(Employees) AS 고용인, MAX(Employees) AS 최대고용, COUNT(*) AS 회사수 FROM CompanyInfo GROUP BY City ORDER BY 고용인 DESC
```
설명: CompanyInfo 테이블에서 City별로 그룹화하여 고용인 수의 합계, 최대 고용인 수, 회사 수를 구하고 고용인 수를 기준으로 내림차순 정렬합니다.

### 2.3. HAVING
```sql
쿼리문: SELECT City, SUM(Employees) AS 고용인, MAX(Employees) AS 최대고용, COUNT(*) AS 회사수 FROM CompanyInfo GROUP BY City HAVING SUM(Employees) >= 2000000 ORDER BY 고용인 DESC
```
설명: CompanyInfo 테이블에서 City별로 그룹화하여 고용인 수의 합계, 최대 고용인 수, 회사 수를 구하고, 고용인 수의 합계가 2,000,000 이상인 그룹만 선택한 후 고용인 수를 기준으로 내림차순 정렬합니다.

## 3. 순위 및 분석 함수
### 3.1. ROW_NUMBER
```sql
쿼리문: SELECT Name, Employees, ROW_NUMBER() OVER (ORDER BY Employees DESC) AS 순위 FROM companyinfo ORDER BY 순위
```
설명: companyinfo 테이블에서 Employees를 기준으로 내림차순 순위를 매기고, 순위를 기준으로 정렬합니다.

### 3.2. LAG, LEAD
```sql
쿼리문: SELECT Date_, Close_ AS 종가, LAG(Close_, 1) OVER (ORDER BY Date_) AS 어제종가, LEAD(Close_, 1) OVER (ORDER BY Date_) AS 내일종가 FROM StockPrice WHERE ID = 40853 ORDER BY Date_
```
설명: StockPrice 테이블에서 ID가 40853인 행의 Date_를 기준으로 정렬하고, 각 행의 전날 종가와 다음날 종가를 함께 출력합니다.
```sql
쿼리문: SELECT Date_, Close_/LAG(Close_, 1) OVER (ORDER BY Date_) - 1 AS 오늘수익률, LEAD(Close_, 1) OVER (ORDER BY Date_) / Close_ - 1 AS 내일수익률 FROM StockPrice WHERE ID = 40853 ORDER BY Date_
```
설명: StockPrice 테이블에서 ID가 40853인 행의 Date_를 기준으로 정렬하고, 각 행의 오늘 수익률과 내일 수익률을 계산하여 출력합니다.

### 3.3. PARTITION BY
```sql
쿼리문: SELECT Name, InclnCtryCode, RANK() OVER (PARTITION BY InclnCtryCode ORDER BY Employees DESC) AS 순위 FROM companyinfo WHERE InclnCtryCode IS NOT NULL ORDER BY 순위
```
설명: companyinfo 테이블에서 InclnCtryCode로 파티션을 나누고, 각 파티션 내에서 Employees를 기준으로 순위를 매긴 후, 순위를 기준으로 정렬합니다.
```sql
쿼리문: SELECT Date_, ID, Close_/LAG(Close_, 1) OVER (PARTITION BY ID ORDER BY Date_) - 1.0 AS 수익률 FROM StockPrice
```
설명: StockPrice 테이블에서 ID별로 파티션을 나누고, 각 파티션 내에서 Date_를 기준으로 정렬한 후, 각 행의 수익률을 계산하여 출력합니다.
```sql
쿼리문: SELECT Date_, ID, Close_, AVG(Close_) OVER (PARTITION BY ID ORDER BY DATE_ ROWS BETWEEN 2 PRECEDING AND 0 PRECEDING) AS SMA3 FROM StockPrice
```
설명: StockPrice 테이블에서 ID별로 파티션을 나누고, 각 파티션 내에서 Date_를 기준으로 정렬한 후, 현재 행을 포함하여 이전 2개 행까지의 Close_의 평균을 계산하여 SMA3으로 출력합니다.

## 4. 조인(JOIN)
### 4.1. INNER JOIN
```sql
쿼리문: SELECT c.Name, s.Date_, s.Close_ FROM companyinfo c JOIN StockPrice s ON c.ID = s.ID
```
설명: companyinfo 테이블과 StockPrice 테이블을 ID 열을 기준으로 내부 조인하여 회사 이름, 날짜, 종가를 출력합니다.

### 4.2. LEFT JOIN
```sql
쿼리문: SELECT c.Name, c.Ind_ID, i.Ind_Name FROM companyinfo c LEFT JOIN industryinfo i ON c.Ind_ID = i.Ind_ID
```
설명: companyinfo 테이블을 기준으로 industryinfo 테이블을 Ind_ID 열을 기준으로 왼쪽 외부 조인하여 회사 이름, 산업 ID, 산업 이름을 출력합니다.

### 4.3. FULL OUTER JOIN
```sql
쿼리문: SELECT c.Name, c.Ind_ID, i.Ind_Name FROM companyinfo c FULL OUTER JOIN industryinfo i ON c.Ind_ID = i.Ind_ID
```
설명: companyinfo 테이블과 industryinfo 테이블을 Ind_ID 열을 기준으로 완전 외부 조인하여 회사 이름, 산업 ID, 산업 이름을 출력하고, 일치하지 않는 부분은 NULL로 채웁니다.

### 4.4. 다중 조인
```sql
쿼리문: SELECT c.Name, c.ID, d.Fin_ID, d.Description FROM companyinfo c JOIN idmap i ON c.ID = i.ID JOIN descriptions d ON i.Fin_ID = d.Fin_ID
```
설명: companyinfo 테이블, idmap 테이블, descriptions 테이블을 다중 조인하여 회사 이름, ID, 재무 ID, 설명을 출력합니다.

## 5. 서브쿼리
### 5.1. 서브쿼리를 이용한 데이터 필터링
```sql
쿼리문:
sqlCopySELECT seoul.Name, seoul.Close_
FROM (
    SELECT a.Name, s.Date_, s.Close_
    FROM companyinfo a
    JOIN stockprice s ON a.ID = s.ID
    WHERE City = 'Seoul' AND s.Date_ = '20201012'
) seoul
WHERE Close_ >= 500000
```
설명: 서울 소재 기업의 2020년 10월 12일자 주식 가격 중 500,000원 이상인 데이터를 출력합니다.

### 5.2. 단일 값 서브쿼리
```sql
쿼리문: SELECT * FROM stockprice WHERE ID = 40853 AND Close_ = (SELECT MAX(Close_) FROM stockprice WHERE ID = 40853)
```
설명: stockprice 테이블에서 ID가 40853이고 Close_가 해당 ID의 최댓값과 같은 행을 선택합니다.

### 5.3. 단일 열 서브쿼리
```sql
쿼리문:
sqlCopySELECT Name
FROM companyinfo
WHERE ID IN (
    SELECT ID
    FROM stockprice s
    GROUP BY ID
    HAVING MAX(Close_) >= 500000
)
```
설명: stockprice 테이블에서 최고가가 500,000원 이상인 ID를 서브쿼리로 구하고, 해당 ID에 해당하는 회사 이름을 companyinfo 테이블에서 선택합니다.

## 6. 뷰(VIEW)
### 6.1. 뷰 생성
```sql
쿼리문:
sqlCopyCREATE VIEW vw_stockpricewithname AS
SELECT a.Name, a.ID, s.Date_, s.Close_
FROM companyinfo a
JOIN stockprice s ON a.ID = s.ID
```
설명: companyinfo 테이블과 stockprice 테이블을 조인하여 회사 이름, ID, 날짜, 종가를 포함하는 vw_stockpricewithname 뷰를 생성합니다.

### 6.2. 뷰 사용
```sql
쿼리문: SELECT * FROM vw_stockpricewithname WHERE Name = 'NVIDIA' ORDER BY Date_
```
설명: vw_stockpricewithname 뷰에서 회사 이름이 'NVIDIA'인 데이터를 날짜순으로 정렬하여 선택합니다.

### 6.3. 뷰 제거
```sql
쿼리문: DROP VIEW vw_stockpricewithname
```
설명: vw_stockpricewithname 뷰를 제거합니다.

## 7. WITH 구문
### 7.1. WITH를 이용한 서브쿼리 재활용
```sql
쿼리문:
sqlCopyWITH cte_price AS (
    SELECT a.Name, a.ID, s.Date_, s.Close_
    FROM companyinfo a
    JOIN stockprice s ON a.ID = s.ID
)
SELECT * FROM cte_price WHERE Name = 'NVIDIA'
```
설명: companyinfo 테이블과 stockprice 테이블을 조인한 결과를 cte_price라는 CTE(Common Table Expression)로 정의하고, 해당 CTE에서 회사 이름이 'NVIDIA'인 데이터를 선택합니다.

## 8. 집계 함수 종류

합계: SUM
평균: AVG
최댓값: MAX
최솟값: MIN
개수: COUNT
```sql
쿼리문:
sqlCopySELECT SUM(Employees) AS 합계,
       AVG(Employees) AS 평균,
       MAX(Employees) AS 최대,
       MIN(Employees) AS 최소,
       COUNT(Employees) AS 개수
FROM companyinfo
```
설명: companyinfo 테이블의 Employees 열에 대해 합계, 평균, 최댓값, 최솟값, 개수를 계산하여 출력합니다.

## 9. 문자열 함수

문자열 길이: LEN
왼쪽 공백 제거: LTRIM
오른쪽 공백 제거: RTRIM
대문자로 변환: UPPER
소문자로 변환: LOWER
왼쪽에서 문자열 추출: LEFT
오른쪽에서 문자열 추출: RIGHT
문자열 역순 변환: REVERSE
문자열 대체: REPLACE
문자열 반복: REPLICATE
숫자를 문자열로 변환: STR
문자열 부분 추출: SUBSTRING
문자열에서 특정 문자 위치 찾기: CHARINDEX

``` sql
예시:
sqlCopyPRINT LEN('Abc DEF Fed CBa')
PRINT LTRIM('   Hello')
PRINT RTRIM('Hello   ')
PRINT UPPER('Hello')
PRINT LOWER('HELLO')
PRINT LEFT('Hello', 2)
PRINT RIGHT('Hello', 3)
PRINT REVERSE('Hello')
PRINT REPLACE('Hello World', 'World', 'Universe')
PRINT REPLICATE('Hi', 3)
PRINT STR(12345) + '6789'
PRINT SUBSTRING('Hello World', 1, 5)
PRINT CHARINDEX('o', 'Hello World')
```
## 10. 날짜 함수
- 현재 날짜 반환: `GETDATE()`
- 년도 추출: `YEAR()`
- 월 추출: `MONTH()`
- 일 추출: `DAY()`
- 날짜 간 일수 차이 계산: `DATEDIFF()`
- 날짜에 지정한 기간 더하기: `DATEADD()`

- 예시:
```sql
PRINT GETDATE()
PRINT YEAR(GETDATE())
PRINT MONTH(GETDATE())
PRINT DAY(GETDATE())
PRINT DATEDIFF(DAY, GETDATE(), '2024-12-25')
PRINT DATEADD(MONTH, 3, GETDATE())


```


## 11. 형식 변환 함수

명시적 형변환: CAST
암시적 형변환: CONVERT

예시:
```sql

sqlCopyPRINT CAST(2023 AS VARCHAR) + N'년'
PRINT CONVERT(VARCHAR, 2023) + N'년'
PRINT CAST(GETDATE() AS VARCHAR)
PRINT CONVERT(VARCHAR(8), GETDATE(), 112)
```

## 12. 테이블 생성 및 수정
### 12.1. 테이블 생성
```sql
쿼리문:
sqlCopyCREATE TABLE Persons (
    PersonID INT,
    LastName VARCHAR(255),
    FirstName VARCHAR(255),
    Address VARCHAR(255),
    City VARCHAR(255)
)
```
설명: Persons 테이블을 생성하고 PersonID, LastName, FirstName, Address, City 열을 정의합니다.

### 12.2. 테이블 열 추가
```sql
쿼리문: ALTER TABLE Persons ADD NewCol INT
```
설명: Persons 테이블에 NewCol이라는 새로운 열을 추가합니다.

### 12.3. 테이블 열 삭제

```sql
쿼리문: ALTER TABLE Persons DROP COLUMN NewCol
```

설명: Persons 테이블에서 NewCol 열을 삭제합니다.

### 12.4. 테이블 열 데이터 타입 변경
```sql
쿼리문: ALTER TABLE Persons ALTER COLUMN PersonID VARCHAR(255)
```
설명: Persons 테이블의 PersonID 열의 데이터 타입을 VARCHAR(255)로 변경합니다.

## 13. 데이터 조작(DML)
### 13.1. 데이터 삽입(INSERT)
```sql
쿼리문:
sqlCopyINSERT INTO Persons (LastName, FirstName)
VALUES ('Hitchcock', 'Alfred')
```

설명: Persons 테이블의 LastName과 FirstName 열에 'Hitchcock'과 'Alfred' 값을 삽입합니다.

### 13.2. 데이터 삭제(DELETE)

```sql
쿼리문: DELETE FROM Persons WHERE PersonID IS NULL
```

설명: Persons 테이블에서 PersonID가 NULL인 행을 삭제합니다.

### 13.3. 데이터 수정(UPDATE)
```sql
쿼리문:
sqlCopyUPDATE Persons
SET Address = 'Jagalchi-ro 112', City = 'Busan'
WHERE PersonID = 20
```
설명: Persons 테이블에서 PersonID가 20인 행의 Address를 'Jagalchi-ro 112'로, City를 'Busan'으로 수정합니다.

## 14. 제약 조건(Constraint)
### 14.1. NOT NULL 제약 조건
```sql
쿼리문:
sqlCopyCREATE TABLE Customers (
    ID INT NOT NULL,
    LastName VARCHAR(255) NOT NULL,
    FirstName VARCHAR(255) NOT NULL,
    Age INT
)
```
설명: Customers 테이블을 생성하고 ID, LastName, FirstName 열에 NOT NULL 제약 조건을 설정합니다.

### 14.2. UNIQUE 제약 조건
```sql
쿼리문:
sqlCopyALTER TABLE Customers
ADD CONSTRAINT UQ_Customers_ID UNIQUE (ID)
```
설명: Customers 테이블의 ID 열에 UNIQUE 제약 조건을 추가합니다.

### 14.3. PRIMARY KEY 제약 조건
```sql
쿼리문:
sqlCopyALTER TABLE Customers
ADD CONSTRAINT PK_Customers PRIMARY KEY (ID)
```
설명: Customers 테이블의 ID 열을 PRIMARY KEY로 설정합니다.

### 14.4. FOREIGN KEY 제약 조건
```sql
쿼리문:
sqlCopyCREATE TABLE Orders (
    OrderID INT NOT NULL PRIMARY KEY,
    OrderNumber INT NOT NULL,
    PersonID INT FOREIGN KEY REFERENCES Customers(ID)
)
```
설명: Orders 테이블을 생성하고 PersonID 열을 Customers 테이블의 ID 열을 참조하는 FOREIGN KEY로 설정합니다.

## 15. 스칼라 함수
### 15.1. 사용자 정의 함수 생성
```sql
쿼리문:
sqlCopyCREATE FUNCTION f_plus (
    @value1 INT,
    @value2 INT
)
RETURNS INT
AS
BEGIN
    DECLARE @sum_value INT
    SET @sum_value = (@value1 + @value2)
    RETURN @sum_value
END
```
설명: 두 개의 정수 값을 입력받아 합계를 반환하는 f_plus 함수를 생성합니다.

### 15.2. 테이블 반환 함수 생성
```sql
쿼리문:
sqlCopyCREATE FUNCTION f_info (@id INT)
RETURNS TABLE
AS
RETURN
(
    SELECT R.FIN_NAME, R.FIN_VAL, R.FDATE, R.FPRD, A.Name
    FROM companyinfo A
    JOIN IDMap I ON A.ID = I.ID
    JOIN Ratios R ON I.FIN_ID = R.FIN_ID
    WHERE A.ID = @id
)
```
설명: 기업 ID를 입력받아 해당 기업의 재무 정보를 반환하는 f_info 함수를 생성합니다.

## 16. 저장 프로시저(Stored Procedure)
### 16.1. 저장 프로시저 생성
```sql
쿼리문:
sqlCopyCREATE PROCEDURE SP_SELECT_COMPANYINFO
AS
BEGIN
    SELECT * FROM companyinfo
END
```
설명: companyinfo 테이블의 모든 데이터를 조회하는 SP_SELECT_COMPANYINFO 저장 프로시저를 생성합니다.

### 16.2. 매개변수를 사용한 저장 프로시저 생성
```sql
쿼리문:
sqlCopyCREATE PROCEDURE SP_SELECT_STOCKPRICE
    @ID INT,
    @StartDate DATE,
    @EndDate DATE
AS
BEGIN
    SELECT Date_, Close_
    FROM StockPrice
    WHERE ID = @ID
      AND DATE_ BETWEEN @StartDate AND @EndDate
    ORDER BY DATE_ DESC
END
```
설명: 기업 코드와 기간을 입력받아 해당 기간 동안의 기업의 주가를 조회하는 SP_SELECT_STOCKPRICE 저장 프로시저를 생성합니다.

### 16.3. 저장 프로시저 실행
```sql
쿼리문: EXEC SP_SELECT_COMPANYINFO
```
설명: SP_SELECT_COMPANYINFO 저장 프로시저를 실행합니다.
```sql
쿼리문: EXEC SP_SELECT_STOCKPRICE 40853, '2020-10-01', '2020-10-13'
```
설명: SP_SELECT_STOCKPRICE 저장 프로시저를 실행하고 기업 코드 40853, 시작일 '2020-10-01', 종료일 '2020-10-13'을 매개변수로 전달합니다.

## 17. 흐름 제어(Flow Control)
### 17.1. IF 문
```sql
쿼리문:
sqlCopyDECLARE @i INT = 110
IF @i % 2 = 0
BEGIN
    PRINT N'짝수'
END
ELSE
BEGIN
    PRINT N'홀수'
END
```
설명: @i 변수가 짝수인지 홀수인지 판별하고 결과를 출력합니다.

### 17.2. WHILE 문
```sql
쿼리문:
sqlCopyDECLARE @i INT = 1
DECLARE @added BIGINT = 0
WHILE (@i <= 100)
BEGIN
    SET @added = @added + @i
    SET @i = @i + 1
END
PRINT @added
```
설명: 1부터 100까지의 합을 계산하여 출력합니다.

### 17.3. CONTINUE와 BREAK
```sql
쿼리문:
sqlCopyDECLARE @i INT = 1
DECLARE @added BIGINT = 0
WHILE (@i <= 100)
BEGIN
    IF @i % 9 = 0
    BEGIN
        PRINT CAST(@i AS VARCHAR) + N': 9의 배수입니다.'
        SET @i = @i + 1
        CONTINUE
    END
    
    IF @added >= 3000
    BEGIN
        PRINT N'@added가 ' + CAST(@added AS VARCHAR) + N'값이 되어 종료합니다.'
        BREAK
    END
    
    SET @added = @added + @i
    SET @i = @i + 1
END
```
설명: 1부터 100까지 반복하면서 9의 배수일 때는 출력하고 다음 반복으로 넘어가며, 합계가 3000 이상이 되면 반복을 종료합니다.

## 18. 동적 SQL(Dynamic SQL)
```sql
쿼리문:
sqlCopyCREATE PROCEDURE SP_SELECT_TABLE_INFO
    @TableName VARCHAR(3000)
AS
BEGIN
    DECLARE @sqlQuery VARCHAR(3000)
    SET @sqlQuery = 'SELECT * FROM ' + @TableName
    
    EXEC(@sqlQuery)
END
```
설명: 테이블 이름을 매개변수로 받아 해당 테이블의 데이터를 조회하는 SP_SELECT_TABLE_INFO 저장 프로시저를 생성합니다. 동적 SQL을 사용하여 쿼리를 문자열로 생성한 후 실행합니다.
```sql
쿼리문:
sqlCopyEXEC SP_SELECT_TABLE_INFO 'StockPrice'
EXEC SP_SELECT_TABLE_INFO 'Companyinfo'
```
설명: SP_SELECT_TABLE_INFO 저장 프로시저를 실행하고 'StockPrice'와 'Companyinfo' 테이블을 매개변수로 전달합니다.




