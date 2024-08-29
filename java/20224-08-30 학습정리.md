### CRUD
- 각 테이블 당 5개의 CRUD가 가능하다.

- Create : Insert

- ReadAll(모든정보) : Select

- ReadOne(상세정보) : Select

- Update : Update

- Delete : Update

### VO  Value Object(데이터 클래스)
- 테이블에 존재하는 모든 컬럼들을 멤버변수로 정의
- 변하지 않는 값을 관리하는 객체
- 다대다 관계를 해소했던 테이블 관계테이블에서는 VO가 별도로 필요하지않다
- 생성자 만들지 않는다 : 외부 솔루션에서는 기본(default) 생성자만 호출

### DTO
- 값을 전달할 때 사용하는 객체
- VO와 DTO를 실무에서는 구분없이 사용

### DAO 
- 데이터에 접근하는 객체
- 데이터베이스와 연동하는 일을 한다.
- SELECT, INSERT, UPDATE, DELETE
- 다대다 관계를 해소했던 테이블들은 필요한 컬럼만 받아서 Dao 구현

### SERVICE
- DAO 를 호출 
- 트랜잭션을 관리한다.

### Secure coding
- SQL injection 방어 방법 sql문에 값을 직접 넣지 않고(하드코딩) 
- 바인딩 기법을 사용 (?와 PreparedStatement.setString|setInt...)이용
