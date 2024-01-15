---
layout: single
title: "부트캠프 10일차"
tag: bootcamp
---

### 실습 코드

&nbsp;&nbsp; 어제 배우고 실습했던 내용을 복습하면서 오늘도 새로운 과제를 내주셨다. 이번에는 혼자 만드는 것이 아니라 팀을 짜주셨고 우리 팀은 4명이서 한 팀이 되어 하나의 프로그램을 만들어보았다. 프로그램은 다음과 같이 시작된다.

```
==========================================================================================================================================
원하는 작업 번호를 입력해주세요.
1. 전체 레코드 조회 | 2. 성별이 기타인 사람 성별 수정 | 3. 멤버 추가 | 4. 평균 방문횟수 보다 많이 방문한 사람 명단 조회 | 5. 나이가 비공개인 사람들의 방문 횟수 조회 | 6. 작업 종료
```

&nbsp;&nbsp; 데이터베이스는 다음과 같이 초기화 되어있다.

![DB init](/images/day10/DB init.png)

&nbsp;&nbsp; 프로젝트는 전체적인 틀과 비교적 쉬운 1, 5, 6번 메뉴는 내가 담당하기로 하고 각자 한 메뉴씩 담당해서 취합하는 것으로 진행되었다.

#### team.best

##### Main.class

```java
package team.best;

/**
 * @author 허찬
 * @author 이경승
 * @author 이준희
 * @author 강동희
 */
public class Main {
    public static void main(String[] args) {
        Program.start();
    }
}
```

##### Program.class

```java
package team.best;

import com.chocola.db.MySQLConnector;

import java.util.Scanner;

public class Program {

    private Program() {
    }

    public static void start() {
        String menu = "==========================================================================================================================================\n" +
                "원하는 작업 번호를 입력해주세요.\n" +
                "1. 전체 레코드 조회 | 2. 성별이 기타인 사람 성별 수정 | 3. 멤버 추가 | 4. 평균 방문횟수 보다 많이 방문한 사람 명단 조회 | 5. 나이가 비공개인 사람들의 방문 횟수 조회 | 6. 작업 종료";
        boolean run = true;
        MySQLConnector mysql = new MySQLConnector();
        Scanner sc = new Scanner(System.in);

        while (run) {
            System.out.println(menu);
            System.out.print("==> ");

            int input = sc.nextInt();
            switch (input) {
                case 1 : {
                    mysql.selectAll();
                    break;
                }
                case 2 : {
                    mysql.updateCGender();
                    break;
                }
                case 3 : {
                    mysql.insert();
                    break;
                }
                case 4 : {
                    mysql.findOverVisitAvg();
                    break;
                }
                case 5 : {
                    mysql.selectAgePrivateMember();
                    break;
                }
                default : {
                    System.out.println("프로그램을 종료합니다.");
                    run = false;
                    break;
                }
            }
        }
    }
}
```

#### team.best.db

##### MySQLConnector.class

```java
package team.best.db;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

import team.best.dto.AgePrivateMemberDto;
import team.best.dto.InsertMemberDto;
import team.best.dto.MemberDto;

public class MySQLConnector {

    private static final String url = "jdbc:mysql://localhost:3306/db_bootcamp?useSSL=false&serverTimezone=UTC&allowPublicKeyRetrieval=true";
    private static final String id = "root";
    private static final String pw = "1234";

    public MySQLConnector() {
    }

    private Connection getConnection() {
        try {
            Connection conn = DriverManager.getConnection(url, id, pw);
            System.out.println("MySQL connect : SUCCESS!");
            return conn;
        } catch (SQLException e) {
            e.printStackTrace();
            throw new IllegalStateException(e);
        }
    }

    private void close(Connection conn, Statement stmt, ResultSet rs) {
        if (rs != null) {
            try {
                rs.close();
                System.out.println("ResultSet close");
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }

        if (stmt != null) {
            try {
                stmt.close();
                System.out.println("Statement close");
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }

        if (conn != null) {
            try {
                conn.close();
                System.out.println("Connection close");
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
    
    public void selectAll() { // 강동희
        String query = "select * from bootcamp2";
        Connection con = null;
        Statement stmt = null;
        ResultSet rs = null;

        try {
            con = getConnection();
            stmt = con.createStatement();
            rs = stmt.executeQuery(query);

            StringBuilder sb = new StringBuilder();
            while (rs.next()) {
                MemberDto member = new MemberDto(rs);
                sb.append(member);
            }

            System.out.println(sb);
        } catch (SQLException e) {
            System.out.println("selectAll() Err : " + e.getMessage());
        } finally {
            close(con, stmt, rs);
        }
    }

    public void updateCGender() { // 허  찬
        String cGenderUpdateQuery1 = "update bootcamp2 set cGender = '여' where cName = '김효경'";
        String cGenderUpdateQuery2 = "update bootcamp2 set cGender = '남' where cGender = '기타'";
        Connection conn = null;
        Statement stmt = null;

        try {
            conn = getConnection();
            stmt = conn.createStatement();
            stmt.executeUpdate(cGenderUpdateQuery1);
            stmt.executeUpdate(cGenderUpdateQuery2);
            System.out.println("성별이 기타인 사람 수정 완료!");
        } catch (SQLException e) {
            System.out.println("updateCGender() ERR : " + e.getMessage());
        } finally {
            close(conn, stmt, null);
        }
    }

    public void insert() { // 이경승
        Connection conn = null;
        PreparedStatement pstmt = null;
        String queryInsert = "insert into bootcamp2 (cName, cRating, cJoinDate, cLastDate, cVisitNo, cPostNo, cCommentNo, cGender, cAge) "
                + "values (?,?,?,?,?,?,?,?,?)";
        InsertMemberDto member = new InsertMemberDto("전준형", "멤버", "2023.06.22.", "2023.07.05.", 56, 0, 0, "남", "20대 후반");
        int n = 0;

        try {
            conn = getConnection();
            pstmt = conn.prepareStatement(queryInsert);
            pstmt.setString(1,  member.getCName());
            pstmt.setString(2,  member.getCRating());
            pstmt.setString(3,  member.getCJoinDate());
            pstmt.setString(4,  member.getCLastDate());
            pstmt.setInt(5,  member.getCVisitNo());
            pstmt.setInt(6,  member.getCPostNo());
            pstmt.setInt(7,  member.getCCommentNo());
            pstmt.setString(8,  member.getCGender());
            pstmt.setString(9,  member.getCAge());
            n = pstmt.executeUpdate();

            if(n > 0) {
                System.out.println(n + " 개의 레코드 삽입 성공");
            }
        } catch (SQLException e) {
            System.out.println("insert()  ERR : " + e.getMessage());
        } finally {
            close(conn, pstmt, null);
        }
    }

    public void findOverVisitAvg() { // 이준희
        String sql = "select cName from bootcamp2 where cVisitNo > (select avg(cVisitNo) from bootcamp2)";

        Connection conn = null;
        Statement stmt = null;
        ResultSet rs = null;

        try {
            conn = getConnection();
            stmt = conn.createStatement();
            rs = stmt.executeQuery(sql);

            StringBuilder sb = new StringBuilder();
            while(rs.next()) {
                sb.append(rs.getString(1)).append("\n");
            }

            System.out.println(sb);
        } catch (SQLException e) {
            System.out.println(sql+ " Err: " + e.getMessage());
        } finally {
            close(conn, stmt, rs);
        }
    }

    public void selectAgePrivateMember() { // 강동희
        String query = "select cSeqNo, cName, cVisitNo from bootcamp2 where cAge = '비공개'";
        Connection con = null;
        Statement stmt = null;
        ResultSet rs = null;

        try {
            con = getConnection();
            stmt = con.createStatement();
            rs = stmt.executeQuery(query);

            StringBuilder sb = new StringBuilder();
            while (rs.next()) {
                AgePrivateMemberDto member = new AgePrivateMemberDto(rs);
                sb.append(member);
            }

            System.out.println(sb);
        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            close(con, stmt, rs);
        }
    }
}
```

#### team.best.dto

##### AgePrivateMemberDto.class

```java
package team.best.dto;

import java.sql.ResultSet;
import java.sql.SQLException;

public class AgePrivateMemberDto {

    private final int cSeqNo;
    private final String cName;
    private final int cVisitNo;

    public AgePrivateMemberDto(ResultSet rs) throws SQLException {
        this.cSeqNo = rs.getInt("cSeqNo");
        this.cName = rs.getString("cName");
        this.cVisitNo = rs.getInt("cVisitNo");
    }

    @Override
    public String toString() {
        return "Member{" +
                "cSeqNo=" + cSeqNo +
                ", cName='" + cName + '\'' +
                ", cVisitNo=" + cVisitNo +
                "}\n";
    }
}
```

##### InsertMemberDto.class

```java
package team.best.dto;

public class InsertMemberDto {

    private final String cName;
    private final String cRating;
    private final String cJoinDate;
    private final String cLastDate;
    private final int cVisitNo;
    private final int cPostNo;
    private final int cCommentNo;
    private final String cGender;
    private final String cAge;

    public InsertMemberDto(String cName, String cRating, String cJoinDate, String cLastDate, int cVisitNo, int cPostNo, int cCommentNo, String cGender, String cAge) {
        this.cName = cName;
        this.cRating = cRating;
        this.cJoinDate = cJoinDate;
        this.cLastDate = cLastDate;
        this.cVisitNo = cVisitNo;
        this.cPostNo = cPostNo;
        this.cCommentNo = cCommentNo;
        this.cGender = cGender;
        this.cAge = cAge;
    }

    public String getCName() {
        return cName;
    }

    public String getCRating() {
        return cRating;
    }

    public String getCJoinDate() {
        return cJoinDate;
    }

    public String getCLastDate() {
        return cLastDate;
    }

    public int getCVisitNo() {
        return cVisitNo;
    }

    public int getCPostNo() {
        return cPostNo;
    }

    public int getCCommentNo() {
        return cCommentNo;
    }

    public String getCGender() {
        return cGender;
    }

    public String getCAge() {
        return cAge;
    }
}
```

##### MemberDto.class

```java
package team.best.dto;

import java.sql.ResultSet;
import java.sql.SQLException;

public class MemberDto {

    private final int cSeqNo;
    private final String cName;
    private final String cRating;
    private final String cJoinDate;
    private final String cLastDate;
    private final int cVisitNo;
    private final int cPostNo;
    private final int cCommentNo;
    private final String cGender;
    private final String cAge;

    public MemberDto(ResultSet rs) throws SQLException {
        this.cSeqNo = rs.getInt("cSeqNo");
        this.cName = rs.getString("cName");
        this.cRating = rs.getString("cRating");
        this.cJoinDate = rs.getString("cJoinDate");
        this.cLastDate = rs.getString("cLastDate");
        this.cVisitNo = rs.getInt("cVisitNo");
        this.cPostNo = rs.getInt("cPostNo");
        this.cCommentNo = rs.getInt("cCommentNo");
        this.cGender = rs.getString("cGender");
        this.cAge = rs.getString("cAge");
    }

    @Override
    public String toString() {
        return "Member{" +
                "cSeqNo=" + cSeqNo +
                ", cName='" + cName + '\'' +
                ", cRating='" + cRating + '\'' +
                ", cJoinDate='" + cJoinDate + '\'' +
                ", cLastDate='" + cLastDate + '\'' +
                ", cVisitNo=" + cVisitNo +
                ", cPostNo=" + cPostNo +
                ", cCommentNo=" + cCommentNo +
                ", cGender='" + cGender + '\'' +
                ", cAge='" + cAge + '\'' +
                "}\n";
    }
}
```

<br>

### 결과

#### 1. 전체 레코드 조회

- ##### 출력

![selectAll()](/images/day10/selectAll().png)

#### 2. 성별이 기타인 사람 성별 수정

- ##### 출력

![updateCGender()](/images/day10/updateCGender().png)

- ##### 변경 결과(DB)

|                변경 전                 |                                 변경 후                                  |
|:-----------------------------------:|:---------------------------------------------------------------------:|
| ![DB init](/images/day10/DB init.png) | ![DB after updateCGender()](/images/day10/DB after updateCGender().png) |

#### 3. 멤버 추가

- ##### 출력

![insert()](/images/day10/insert().png)

- ##### 변경 결과(DB)

![DB after insert()](/images/day10/DB after insert().png)

#### 4. 평균 방문횟수 보다 많이 방문한 사람 명단 조회

- ##### 출력

![findOverVisitAvg()](/images/day10/findOverVisitAvg().png)

#### 5. 나이가 비공개인 사람들의 방문 횟수 조회

- ##### 출력

![selectAgePrivateMember()](/images/day10/selectAgePrivateMember().png)

#### 6. 작업 종료

- ##### 출력

![shutdown](/images/day10/shutdown.png)

<br>

### 회고

#### 느낀 점

&nbsp;&nbsp; 즐겁다. 협업을 많이 해보고 싶었는데 오늘은 생각지 못한 기회였다. 다들 배우신대로 코드를 잘 짰고 별다른 수정 사항 없이 원하는 결과를 출력하였다. Slack 앱을 통해서 코드를 나누고 소통 했는데 간편한 느낌을 받았다. 생각보다 훨씬 수월하게 마무리 되어서 다행이라고 생각했다.

#### 아쉬웠던 점

&nbsp;&nbsp; 다들 어색해서 그런지 프로젝트 준비 단계에서 소통이 원활하게 이뤄지지 않았다. 각자 기능(메서드) 하나씩 맡아서 취합하는 방향으로 진행을 했는데 메서드명이나 변수명, 변수를 사용하는 방법(ex. Connection 객체) 등을 사전에 합의하지 못했다. 때문에 내가 취합을 하는 과정에서 일일이 수정을 거쳐야 했다.

#### 보완할 점

&nbsp;&nbsp; 사전에 협의되지 않는 코드들은 합치는 데에 불필요한 작업이 많이 들어간다는 것을 몸소 체험했다! 다음에 협업을 할 때에는 충분한 토의를 거친 후에 진행하는 것이 좋겠다고 생각했다.

---
[<== 부트캠프 9일차](../bootcamp-day9) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; [부트캠프 11일차 ==>](../bootcamp-miniproject-kktalk)