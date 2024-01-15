---
layout: single
title: "부트캠프 미니 프로젝트 5 UltimateS"
tag: [bootcamp, miniproject]
---

# Mini Project(UltimateS)

## 소개

### 팀장

최호준

### 팀원

김정훈, 박경리, 강동희

### 주제

게임 전적 검색 서비스

<br>

## 목차

1. 프로젝트 진행 방식
2. 프로젝트 목적
3. Game Record System (이하 GRS)
   1. API
   2. JPQL 최적화
   3. DB 초기화
4. Record Search Service (이하 RSS)
   1. 인터페이스 설계
   2. API 사용
   3. 예외 처리
5. 시연

<br>

## 프로젝트 진행 방식

![프로젝트 구조](../../../images/ultimates/프로젝트%20구조.png)

<br>

## 프로젝트 목적

1. GRS
   - DB 테이블 및 엔티티 설계 학습
   - Spring Data JPA 야무지게 사용해보기
   - API 만들어보기

2. RSS
   - 외부 API를 사용해서 데이터를 정제하고 뷰로 응답하는 것을 학습
   - 예외 처리 기능 적절하게 사용해보기
   - Thymeleaf 숙달
   - 코드 리팩토링 연습

<br>

## GRS

### API

### JPQL 최적화

> 리팩토링 전

```java
@Service
public class GameDataService {
    
    private final GameRepository gameRepository;
    private final UserRepository userRepository;

    @Autowired
    public GameDataService(GameRepository gameRepository, UserRepository userRepository) {
        this.gameRepository = gameRepository;
        this.userRepository = userRepository;
    }

    public ResponseEntity<List<GameDataDto>> getAllGameData() {
        List<GameData> gameDataList = gameRepository.findAll();
        List<GameDataDto> gameDataDtoList = new ArrayList<>();

        for (GameData gameData : gameDataList) {
            GameDataDto gameDataDto = new GameDataDto();
            gameDataDto.setIdx(gameData.getIdx());
            gameDataDto.setUserName(gameData.getUserName());
            gameDataDto.setGameNumber(gameData.getGameNumber());
            gameDataDto.setStartTime(gameData.getStartTime());
            gameDataDto.setEndTime(gameData.getEndTime());
            gameDataDto.setPlayChamp(gameData.getPlayChamp());
            gameDataDto.setKillCnt(gameData.getKillCnt());
            gameDataDto.setDeathCnt(gameData.getDeathCnt());
            gameDataDto.setAssistCnt(gameData.getAssistCnt());
            gameDataDto.setWin(gameData.isWin());
            gameDataDto.setAtkDmg(gameData.getAtkDmg());
            gameDataDto.setRecDmg(gameData.getRecDmg());

            gameDataDtoList.add(gameDataDto);
        }
        
        return new ResponseEntity<>(gameDataDtoList, HttpStatus.OK);
    }
}
```

<br>

> 리팩토링 후

```java
public interface GameRepository extends JpaRepository<GameData, Long> {
    
    @Query("SELECT new com.ultimates.grs.data.dto.GameDataDto(gd) FROM GameData gd")
    List<GameDataDto> findGameDataDtoList();
}



@Getter
@Setter
@ToString
@NoArgsConstructor
public class GameDataDto {

   private Long idx;
   private String userName;
   private int gameNumber;
   private LocalDateTime startTime;
   private LocalDateTime endTime;
   private String playChamp;
   private int killCnt;
   private int deathCnt;
   private int assistCnt;
   private boolean win;
   private int atkDmg;
   private int recDmg;

   public GameDataDto(GameData gd) {
      this.idx = gd.getIdx();
      this.userName = gd.getUserName();
      this.gameNumber = gd.getGameNumber();
      this.startTime = gd.getStartTime();
      this.endTime = gd.getEndTime();
      this.playChamp = gd.getPlayChamp();
      this.killCnt = gd.getKillCnt();
      this.deathCnt = gd.getDeathCnt();
      this.assistCnt = gd.getAssistCnt();
      this.win = gd.isWin();
      this.atkDmg = gd.getAtkDmg();
      this.recDmg = gd.getRecDmg();
   }
}
```

### DB 초기화

> resources/application.yml

```yaml
spring:
  jpa:
    show-sql: true
    properties:
      hibernate:
        ddl-auto: update
        format_sql: true
    defer-datasource-initialization: true

  sql:
    init:
      mode: always
```

<br>

> resources/data.sql

```roomsql
INSERT INTO champ_data (champ_name, q_skill, w_skill, e_skill, r_skill)
VALUES
    ('리신', '충무공', '명량해전', '거북선 소환', '학익진'),
    ('아리', '대차륜', '권각술', '무영각', '경공술'),
    ('트페', '격려', '회전창', '화염찌르기', '바위부수기'),
    ('말파', '뇌전탄', '화살비', '추적자의 화살', '용사냥꾼'),
    ('사일', '흩뿌리기', '난치기', '해그리기', '범가르기'),
    ('알리', '약초', '진찰', '명의', '지혈'),
    ('자야', '일도양단', '당파', '역습', '무촉전'),
    ('라칸', '매화검술', '연막탄', '점혈', '매화난무'),
    ('이즈', '일검참전', '이검참산', '삼검참해', '사검참허'),
    ('그브', '초전박살', '선풍', '물아일체', '호접');

INSERT INTO game_data (user_name, game_number, start_time, end_time, play_champ, kill_cnt, death_cnt, assist_cnt, win, atk_dmg, rec_dmg)
VALUES
    ('AAA', 1, '2023-09-21 10:00:00', '2023-09-21 10:30:00', '리신', 10, 5, 7, 1, 2000, 1800),
    ('BBB', 1, '2023-09-21 10:00:00', '2023-09-21 10:30:00', '자야', 14, 3, 9, 1, 2300, 2100),
    ('CCC', 1, '2023-09-21 10:00:00', '2023-09-21 10:30:00', '트페', 12, 4, 8, 1, 2200, 2000),
    ('DDD', 1, '2023-09-21 10:00:00', '2023-09-21 10:30:00', '이즈', 15, 1, 8, 0, 2500, 2200),
    ('EEE', 1, '2023-09-21 10:00:00', '2023-09-21 10:30:00', '사일', 9, 8, 5, 0, 1900, 1800),
    ('FFF', 1, '2023-09-21 10:00:00', '2023-09-21 10:30:00', '말파', 10, 5, 7, 0, 2000, 1800),
    ('AAA', 2, '2023-09-21 11:30:00', '2023-09-21 12:10:00', '리신', 8, 6, 10, 1, 1800, 1600),
    ('BBB', 2, '2023-09-21 11:30:00', '2023-09-21 12:10:00', '라칸', 13, 2, 10, 1, 2100, 1900),
    ('CCC', 2, '2023-09-21 11:30:00', '2023-09-21 12:10:00', '말파', 11, 7, 6, 0, 2400, 2200),
    ('DDD', 2, '2023-09-21 11:30:00', '2023-09-21 12:10:00', '그브', 7, 7, 7, 0, 2000, 1800),
    ('EEE', 2, '2023-09-21 11:30:00', '2023-09-21 12:10:00', '알리', 6, 9, 7, 1, 1600, 1500),
    ('FFF', 2, '2023-09-21 11:30:00', '2023-09-21 12:10:00', '아리', 8, 6, 10, 0, 1800, 1600),
    ('AAA', 3, '2023-09-21 13:00:00', '2023-09-21 13:32:00', '트페', 12, 4, 8, 1, 2200, 2000),
    ('BBB', 3, '2023-09-21 13:00:00', '2023-09-21 13:32:00', '이즈', 15, 1, 8, 0, 2500, 2200),
    ('CCC', 3, '2023-09-21 13:00:00', '2023-09-21 13:32:00', '사일', 9, 8, 5, 1, 1900, 1800),
    ('DDD', 3, '2023-09-21 13:00:00', '2023-09-21 13:32:00', '리신', 10, 5, 7, 0, 2000, 1800),
    ('EEE', 3, '2023-09-21 13:00:00', '2023-09-21 13:32:00', '자야', 14, 3, 9, 1, 2300, 2100),
    ('FFF', 3, '2023-09-21 13:00:00', '2023-09-21 13:32:00', '알리', 12, 4, 8, 0, 2200, 2000),
    ('AAA', 4, '2023-09-21 14:30:00', '2023-09-21 14:50:00', '말파', 11, 7, 6, 0, 2400, 2200),
    ('BBB', 4, '2023-09-21 14:30:00', '2023-09-21 14:50:00', '그브', 7, 7, 7, 1, 2000, 1800),
    ('CCC', 4, '2023-09-21 14:30:00', '2023-09-21 14:50:00', '알리', 6, 9, 7, 1, 1600, 1500),
    ('DDD', 4, '2023-09-21 14:30:00', '2023-09-21 14:50:00', '아리', 8, 6, 10, 0, 1800, 1600),
    ('EEE', 4, '2023-09-21 14:30:00', '2023-09-21 14:50:00', '라칸', 13, 2, 10, 1, 2100, 1900),
    ('FFF', 4, '2023-09-21 14:30:00', '2023-09-21 14:50:00', '자야', 11, 7, 6, 0, 2400, 2200),
    ('AAA', 5, '2023-09-21 16:00:00', '2023-09-21 16:20:00', '사일', 9, 8, 5, 1, 1900, 1800),
    ('BBB', 5, '2023-09-21 16:00:00', '2023-09-21 16:20:00', '리신', 10, 5, 7, 0, 2000, 1800),
    ('CCC', 5, '2023-09-21 16:00:00', '2023-09-21 16:20:00', '자야', 14, 3, 9, 0, 2300, 2100),
    ('DDD', 5, '2023-09-21 16:00:00', '2023-09-21 16:20:00', '트페', 12, 4, 8, 0, 2200, 2000),
    ('EEE', 5, '2023-09-21 16:00:00', '2023-09-21 16:20:00', '이즈', 15, 1, 8, 1, 2500, 2200),
    ('FFF', 5, '2023-09-21 16:00:00', '2023-09-21 16:20:00', '그브', 9, 8, 5, 1, 1900, 1800),
    ('AAA', 6, '2023-09-21 17:30:00', '2023-09-21 17:58:00', '알리', 6, 9, 7, 0, 1600, 1500),
    ('BBB', 6, '2023-09-21 17:30:00', '2023-09-21 17:58:00', '아리', 8, 6, 10, 0, 1800, 1600),
    ('CCC', 6, '2023-09-21 17:30:00', '2023-09-21 17:58:00', '라칸', 13, 2, 10, 1, 2100, 1900),
    ('DDD', 6, '2023-09-21 17:30:00', '2023-09-21 17:58:00', '말파', 11, 7, 6, 1, 2400, 2200),
    ('EEE', 6, '2023-09-21 17:30:00', '2023-09-21 17:58:00', '그브', 7, 7, 7, 1, 2000, 1800),
    ('FFF', 6, '2023-09-21 17:30:00', '2023-09-21 17:58:00', '이즈', 6, 9, 7, 0, 1600, 1500);

INSERT INTO user_data (lv, game_num, user_name, tier)
VALUES
    (10, 1, 'AAA', 5),
    (15, 2, 'BBB', 3),
    (8, 3, 'CCC', 2),
    (12, 4, 'DDD', 4),
    (9, 5, 'EEE', 2),
    (11, 6, 'FFF', 3),
    (7, 7, 'User7', 1),
    (13, 8, 'User8', 4),
    (6, 9, 'User9', 1),
    (14, 10, 'User10', 5);
```

<br>

> resources/schema.sql

```roomsql
DROP TABLE IF EXISTS game_data;
CREATE TABLE game_data (
    idx bigint NOT NULL AUTO_INCREMENT,
    user_name varchar(255) DEFAULT NULL,
    game_number int DEFAULT NULL,
    start_time datetime DEFAULT NULL,
    end_time datetime DEFAULT NULL,
    play_champ varchar(255) DEFAULT NULL,
    kill_cnt int DEFAULT NULL,
    death_cnt int DEFAULT NULL,
    assist_cnt int DEFAULT NULL,
    win tinyint(1) DEFAULT NULL,
    atk_dmg int DEFAULT NULL,
    rec_dmg int DEFAULT NULL,
    PRIMARY KEY (idx)
);

DROP TABLE IF EXISTS champ_data;
CREATE TABLE champ_data (
    idx BIGINT NOT NULL AUTO_INCREMENT,
    champ_name VARCHAR(255) NOT NULL,
    q_skill VARCHAR(255) NOT NULL,
    w_skill VARCHAR(255) NOT NULL,
    e_skill VARCHAR(255) NOT NULL,
    r_skill VARCHAR(255) NOT NULL,
    PRIMARY KEY (idx)
);

DROP TABLE IF EXISTS user_data;
CREATE TABLE user_data (
    idx BIGINT AUTO_INCREMENT PRIMARY KEY,
    lv INT NOT NULL,
    game_num INT NOT NULL,
    user_name VARCHAR(255) NOT NULL,
    tier INT
);
```

<br>

## RSS

### 인터페이스 설계

```java
public interface ChampService {

    /**
     * 해당 챔프의 상세 정보를 반환하는 메서드
     *
     * @param champName 챔프의 이름
     * @return 챔프의 상세 정보를 담고 있는 Champ 객체
     * @throws NonExistChampException {@code champName}에 해당하는 챔프가 존재하지 않으면 이 예외가 발생
     * @author chocolaggibbiddori
     */
    Champ getChampDetail(String champName);

    /**
     * 해당 유저의 모스트 챔프 정보를 반환하는 메서드
     * <p>판수(playCount) 기준으로 내림차순 정렬
     *
     * @param username 유저의 닉네임
     * @return 유저의 모스트 챔프 정보를 담고 있는 MostChamp 객체들을 리스트에 담아서 반환
     * @throws IllegalUserException {@code username}이 존재하지 않는 유저라면 이 예외가 발생
     * @author chocolaggibbiddori, hojun
     */
    List<MostChamp> getMostChampList(String username);
}
```

### API 사용 & 예외 처리

```java
public int getChampPlayCount(String champName) {
     URI uri = UriComponentsBuilder
             .fromUriString(uriString)
             .path("/grs/gamedata/champodds/{champName}")
             .encode()
             .build()
             .expand(champName)
             .toUri();

     List<ChampData> champDataList = restTemplate.exchange(uri, HttpMethod.GET, null, new ParameterizedTypeReference<List<ChampData>>() {
     }).getBody();
     
     if (champDataList == null || champDataList.isEmpty()) {
         log.error("[getChampPlayCount] 존재하지 않는 챔프입니다.");
         throw new NonExistChampException("존재하지 않는 챔프입니다.");
     }

     return champDataList.size();
}
```

![예외 처리](../../../images/ultimates/예외처리%20클래스%20구조.png)

![예외 페이지](../../../images/ultimates/예외%20페이지.png)

___

[<== 부트캠프 55일차](/bootcamp-day55)