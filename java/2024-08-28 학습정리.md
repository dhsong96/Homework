### DCL

Row 수정, 삽입, 삭제 확정처리

### DML

SELECT, INSERT, UPDATE, DELETE

### DDL

CREATE TABLE, DROP TABLE, ALTER TABLE, ALTER COLUMN, ALTER USER

### TRANSACTION

테이블에 새로운 Row를 추가하면 테이블을 잠금(TABLE LOCK)

⇒ 아직까지 확정되지 않아서(잘못된 데이터를 입력했을 수 있어서)

### COMMIT

데이터의 수정을 확정 짓는다.

### ROLLBACK

데이터의 수정을 취소한다.

## JDBC 연동

JDBC(Java DataBase Connector)를 통해 Oracle Database와 연동(API)

- 구조

![image](https://github.com/user-attachments/assets/1e39980d-c7ad-4717-a25b-4be134f5eb86)


- Connection

데이터베이스에 연결을 시켜주는 인터페이스

- Statement

연결 된 데이터베이스에 SELECT INSERT 등의 쿼리를 실행시켜주는 인터페이스

- Result Set

연결 된 데이터베이스의 조회 결과를 가져오는 인터페이스

### 주요 API

- Connection
    - 데이터베이스와 연결
- Prepared Statement
    - 쿼리를 Oracle로 전송해 실행할 준비를 함
    - 쿼리에 파라미터를 ?(물음표)를 통해 추가할 수 있음
    - INSERT, UPDATE, DELETE
        - stmt.execute(), stmt.executeUpdate()…
    - SELECT
        - stmt.executeQuery()
- ResultSet
    - Prepared Statement를 통해 실행한 결과를 ResultSet을 통해 얻어옴
    - SELECT 쿼리에서만 사용

---

### 데이터 SELECT

1. Oracle Database에 연결

```java
// 1. Oracle Driver Loading
		//    Oracle Database 에 연결하기 위한 준비작업
		//    ojdb11.jar > oracle.jdbc.driver.OracleDriver.class
		try {
			Class.forName("oracle.jdbc.driver.OracleDriver"); // 해당 클래스를 로딩한다.
		}
		catch (ClassNotFoundException e) {
			System.out.println("오라클 드라이버를 찾을 수 없습니다.");
			System.out.println("드라이버의 이름 또는 ojdbc11.jar 파일이 연결되었는지 확인해 주세요.");
			return;
		}
		
		// 2. Oracle Database 에 연결 : Session 생성
		final String JDBC_URL = "jdbc:oracle:thin:@localhost:1521:XE";
		final String SCHEMA_NAME = "LOTTE_CINEMA";
		final String SCHEMA_PASSWORD = "LOTTE_CINEMA";
		// Connection : Database 에 연결한 Session
		try {
			Connection connection = DriverManager.getConnection(JDBC_URL, SCHEMA_NAME, SCHEMA_PASSWORD);
		}
		catch (SQLException sqle) {
			// Database 에 연결하다가 예외가 발생한 상황
			// 연결에 실패하면 쿼리를 실행할 수 없음
			System.out.println("데이터베이스에 연결할 수 없습니다.");
			System.out.println("URL, SCHEMA, PASSWORD 가 올바른지 확인하세요.");
			return;
		}
```

---

1. 쿼리 준비 및 실행

```java
// 3. 쿼리 준비 및 실행
		// DB로 가서 먼저 실행해봄
		// 정상적으로 작동한다면 세미콜론을 빼고 복사해서 붙여넣음
		// 행마다 구분하기 위해 앞 뒤로 띄어쓰기 작성
		StringBuffer query = new StringBuffer();
		query.append(" SELECT M.MV_ID                                 ");
		query.append("      , M.MV_TTL                                ");
		query.append("      , TO_CHAR(M.OPN_DT, 'YYYY-MM-DD') OPN_DT  ");
		query.append("      , M.SCRNG_TM                              ");
		query.append("      , AL.AGE_LMT_NM                           ");
		query.append("      , M.PLT                                   ");
		query.append("      , G.GNR_NM                                ");
		query.append("   FROM MV_INFO M                               ");
		query.append("  INNER JOIN MV_GNR MG                          ");
		query.append("     ON M.MV_ID = MG.MV_ID                      ");
		query.append("  INNER JOIN GNR G                              ");
		query.append("     ON MG.GNR_ID = G.GNR_ID                    ");
		query.append("  INNER JOIN AGE_LMT AL                         ");
		query.append("     ON M.AGE_LMT_ID = AL.AGE_LMT_ID            ");
		
		try {
			PreparedStatement pstmt = connection.prepareStatement(query.toString());
			// Select 의 결과를 받아옴
			ResultSet rs = pstmt.executeQuery();
		}
		catch(SQLException sqle) {
			// 쿼리가 잘못된 경우에 실행
			System.out.println("쿼리를 실행할 수 없습니다.");
			System.out.println(sqle.getMessage());
			// 에러가 나면 닫아줌
			if (pstmt != null) {
				try {
					pstmt.close();
				}
				catch (SQLException sqle2) {}
			}
			try {
				connection.close();
			}
			catch (SQLException sqle2){}
			return;
		}
```

---

1. 결과 출력

```java
// 4. 결과 출력
		try {
			while ( rs.next() ) {
				// 컬럼의 타입에 맞춰서 작성
				// SELECT 한 것만 가져올 수 있음
				int mvId = rs.getInt("MV_ID");
				String mvTtl = rs.getString("MV_TTL");
				String opnDt = rs.getString("OPN_DT");
				int scrngTm = rs.getInt("SCRNG_TM");
				String ageLmtNm = rs.getString("AGE_LMT_NM");
				String plt = rs.getString("PLT");
				String gnrNm = rs.getString("GNR_NM");
				
				System.out.println(mvId + " / " + mvTtl + " / "
								 + opnDt + " / " + scrngTm + " / "
								 + ageLmtNm + " / " + plt + " / "
								 + gnrNm);			
			}
		} 
		catch (SQLException sqle) {
			System.out.println("결과를 가져올 수 없습니다.");
			System.out.println(sqle.getMessage());
		}
```

ResultSet의 Cursor

rs.next() ⇒ 커서를 이동

- 커서의 이동은 현재 가리키고 있는 Row를 한칸 아래로 이동하겠다는 의미
- 다음 Row가 있을 경우 true / 아닐경우 false를 반환



1. Oracle Database Session 닫기

```java
// 5. Oracle Database Session 닫음
		//    Session 이 안닫아지면 메모리누수가 발생
		//    메모리누수가 지속해서 발생할 경우 --> Out Of Memory! 예외가 발생하면서 애플리케이션이 종료
		//    호출한 클래스의 역순으로 닫아줌
		
		try {
			rs.close();
		} 
		catch (SQLException sqle) {}
		
		try {
			pstmt.close();
		} 
		catch (SQLException sqle) {}
		
		try {
			connection.close();
		} 
		catch (SQLException sqle) {}
}
```
