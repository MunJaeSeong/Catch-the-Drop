<Game Description>
This game is about clicking on falling circles to increase your score.
You can start with the start button, and if you lose, you can restart with the start button.
Each time you pass a certain score, the number of circles increases.(100,300,1000)
You have a total of 5 lives, and you can save your score at the end of the game.

이 게임은 떨어지는 원을 클릭하여 점수를 올리는 게임입니다.
시작 버튼으로 시작할 수 있으며, 지면 시작 버튼으로 다시 시작할 수 있습니다.
특정 점수를 넘을 때마다 원의 개수가 증가합니다.(100, 300, 1000)
총 5개의 생명이 주어지며, 게임 종료 시 점수를 저장할 수 있습니다.

<Game Screen>
<img width="1920" height="919" alt="1" src="https://github.com/user-attachments/assets/b27cd611-f36c-4856-8e2d-4f5fac8bb5b1" />

<Code Organization>
/
ㄴcss
  ㄴstyle.css - 레이아웃·비주얼·반응형 스타일
ㄴjs
  ㄴgame.js - 엔진(루프), 상태관리, 입력·충돌·점수 로직
index.html - DOM 구조(캔버스, lifeBadge, scoreBoard)
  
<Architecture>
                          +-----------------------+
                          |      index.html       |
                          |  (DOM: canvas, UI)    |
                          +----------+------------+
                                     |
                                     v
                          +-----------------------+
                          |     css/style.css     |
                          |  (레이아웃·비주얼)     |
                          +----------+------------+
                                     |
                                     v
                          +-----------------------+
                          |      js/game.js       |
                          |   (게임 엔진)         |
                          +----------+------------+
                                     |
         +---------------------------+---------------------------+
         |                           |                           |
         v                           v                           v
+----------------+        +------------------------+    +---------------------+
| 입력 핸들러    |        | 게임 상태 / 모델       |    | 퍼시스턴스 / UI     |
| (캔버스 클릭)  |        | - score, lifes         |    | - renderScores()    |
| -> 좌표 계산   |        | - circles[]            |    | - localStorage 저장 |
| -> 충돌 판정   |        | - NUM_CIRCLES, flags   |    | - scoreBoard DOM     |
+-------+--------+        +-----------+------------+    +----------+----------+
        |                            |                             ^
        | 히트 이벤트                | 업데이트                      |
        v                            v                             |
+----------------+           +----------------------+              |
| 충돌 체크 및   | <--루프--- | 게임 루프 (RAF)     | ------------+
| 보상 처리      |           | - 위치 업데이트      |
| - 점수 부여    |           | - 화면 하단 확인     |
| - 도트 리셋    |           | - 목숨 차감 처리     |
+-------+--------+           | - 난이도 조정       |
        |                    +-----------+----------+
        v                                |
+----------------------+                 v
| 엔티티 매니저        |           +--------------------+
| - 도트 생성/리셋     |           | 렌더러 (canvas)    |
| - 스폰 / 재사용      |           | - 도트 그리기      |
+----------------------+          | - UI 오버레이 그리기|
                                  +--------------------+

시퀀스(간단 요약):
1. startBtn -> startGame(): 상태 초기화, 도트 생성, requestAnimationFrame(gameLoop) 호출
2. gameLoop(): 위치 업데이트 -> 화면 밖으로 나간 도트는 lifes 감소 및 리셋 -> 렌더 실행
3. 사용자 클릭 -> 입력 핸들러 -> 충돌 검사 -> 점수 증가 및 해당 도트 리셋
4. lifes == 0 -> endGame(): RAF 취소, 점수 저장 유도 -> 로컬스토리지에 저장 -> renderScores()
