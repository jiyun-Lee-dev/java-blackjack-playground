## [NEXTSTEP 플레이그라운드의 미션 진행 과정](https://github.com/next-step/nextstep-docs/blob/master/playground/README.md)

---
## 학습 효과를 높이기 위해 추천하는 미션 진행 방법

---
1. 피드백 강의 전까지 미션 진행 
> 피드백 강의 전까지 혼자 힘으로 미션 진행. 미션을 진행하면서 하나의 작업이 끝날 때 마다 add, commit
> 예를 들어 다음 숫자 야구 게임의 경우 0, 1, 2단계까지 구현을 완료한 후 push

![mission baseball](https://raw.githubusercontent.com/next-step/nextstep-docs/master/playground/images/mission_baseball.png)

---
2. 피드백 앞 단계까지 미션 구현을 완료한 후 피드백 강의를 학습한다.

---
3. Git 브랜치를 master 또는 main으로 변경한 후 피드백을 반영하기 위한 새로운 브랜치를 생성한 후 처음부터 다시 미션 구현을 도전한다.

```
git branch -a // 모든 로컬 브랜치 확인
git checkout master // 기본 브랜치가 master인 경우
git checkout main // 기본 브랜치가 main인 경우

git checkout -b 브랜치이름
ex) git checkout -b apply-feedback
```
---
4. 요구사항 정리
- 우선 싱글덱으로 가정.
  > 싱글덱이 52장이라고 하는 것 같음.  
  > 스페이드,하트,다이아몬드,클럽 (모양 4개)  
  > ace, 2~10, jack, queen, king (숫자/글자 13개)  
  > 1~52까지의 숫자 배열을 셔플한 다음에 순차적으로 숫자를 뽑고, 해당하는 카드를 플레이어와 딜러에게 주려고 함.
###
- 카드의 숫자 계산은 카드 숫자를 기본으로 하며, ace는 1 또는 11, king/queen/jack은 각각 10으로 계산한다
  > ace를 1 또는 11로 계산하는 건 현재 가지고 있는 카드의 합이 21이 넘지 않도록, 21에 가장 가까워지도록 하는 조건으로 자동 계산해준다.
###
- 게임 시작 전, 플레이어는 각자의 베팅 금액을 입력한다.
###
- 게임이 시작되면 딜러와 플레이어 모두 2장의 카드를 받는다.
###
- 딜러와 플레이어의 카드들에 변경이 생기면, 그 시점에서 딜러와 플레이어가 갖고 있는 카드들의 정보를 출력한다.
###
- 카드 지급 후 딜러와 플레이어가 가지고 있는 카드들의 정보를 통해 게임 종료 여부에 대해 판단한다.

  > -  게임 종료 기준 변경
  >   1) 일단 딜러가 21 초과할 경우
  >   2) 참여자가 한명이라도 21 달성할 경우
  >   3) 카드가 다 소진될 경우

  > - 카드 지급하고 나서 매번 다음의 사항들을 확인하기
  >   1) 딜러가 21 초과하는지 확인
  >   2) 참여자 카드 덱 총합이 21이 되는지 확인
  >   3) dealing shoe에서 갖고 있는 카드 다 소진됐는지 확인
  >   4) 21을 초과한 플레이어가 몇 명인지 확인

  > - playResult 다음과 같이 변경  
  >   1) Blackjack, 참여자 이름  
  >   2) Bust, 참여자 이름  
  >   3) Win (21 달성 또는 카드 소진 시 21에 가장 가까운 참여자), 참여자 이름  
  >   * playstatus, particiapnt 을 가지고 있음

  > - 참여자 별로 최종 수익을 계산해서 출력  
  >   최종 수익 계산법은 다음과 같이 정리(내 맘대로.. 실제로 어떻게 하는지 모르겠음..)  
  >   1) 플레이어, 블랙잭으로 이기면 1.5배
  >   2) 플레이어, 21로 이기면 1배
  >   3) 플레이어, 게임에서 지면 -1배
  >   4) 딜러, 진 플레이어의 베팅금액만큼 +, 이긴 플레이어의 베팅금액만큼 - 하는걸로 하자

###
- 카드 지급 후 게임이 바로 종료되지 못하는 상태라면, 카드 추가 지급을 진행한다.
> - 카드 추가 지급 시, 플레이어는 카드를 더 뽑을 것인지 선택해야 한다.
> ####
> - 카드 추가 지급 시, 딜러가 가지고 있는 카드의 합이 16 이하라면, 한 장의 카드를 무조건 받는다
> ####
> - 카드 추가 지급 시, 딜러가 가지고 있는 카드의 합이 17 이상이라면, 카드를 더이상 받을 수 없다.
> ####
> - 카드 추가 지급 후, 위에 나와있는 것처럼 게임 종료 여부에 대해 판단한다.
###
- 게임이 종료가 결정되면, 최종 수익에 대한 정보가 출력되고 게임이 종료된다.  

---

5. 객체 설계  
> ###1)  Card 
> - model
> - 트럼프 카드 한 장.
> ####
> - 필드  
    : 숫자(number), 무늬(suit)
>####
> - 메서드  
   : 숫자가 ace일 때 1로 계산할 건지, 11로 계산할 건지 판단하기  
   : 카드에 대한 정보(숫자 + 무늬)를 문자열로 반환하기

> ###2)  Cards
> - model
> - 참여자가 가지고 있는 트럼프 카드 뭉치  
> ####
> - 필드  
   : 카드 리스트(cards)  
> ####
> - 메서드  
   : 가지고 있는 카드 총합 구하기

> ###3) Deck
> - model
> - 게임에서 사용하는 싱글 덱  
> ####
> - 핃드  
>   : 52장의 카드 뭉치 (cards)
> ####
> - 메서드
>   : 전달 받은 숫자에 해당하는 카드 반환하기

> ###4) Dealing Shoe  
> - model
> - 게임에 쓰이는 카드들을 보관/제공하고 있는 곳  
> ####
> - 필드  
>  : 싱글 덱 (singleDeck)  
>  : 다음으로 꺼내지는 카드의 인덱스 (nextCardIndex)  
>  : 0~51까지 숫자 배열 (randomNumbers)
> ####
>  - 메서드  
>  : 생성자에서 randomNumbers 셔플하기  
>  : 덱에서 카드 한 장 뽑기  
>   ( nextCardIndex에 해당하는 카드 반환하고, nextCardIndex + 1 하기)  
>  : 가지고 있는 카드를 모두 소진했는지 판별하기  
  

> ###5) Participant
> - model
> - 플레이어와 딜러를 포함하는 게임에 참여하고 있는 사람을 뜻하는 추상 클래스
> ####
> - 필드  
   : 카드 리스트(cards)  
   : 이름(name)  
   : 베팅 금액(bettingMoney)
> ####
> - 메서드  
   : 전달받은 playResult와 현재 가지고 있는 카드의 총합을 토대로 최종 수익 계산하기  
   : 가지고 있는 카드들의 정보를 문자열로 반환하기  
   : 전달받은 카드 한 장을 자신의 카드 리스트에 넣기  
   : 최종 수익에 대한 정보를 문자열로 반환하기  
>  : 카드를 추가로 지급받을 수 있는 상태인지 조회하기(선언만)  

> ###6) Player
> - model
> - participant를 상속받은 플레이어
> ####
> - 필드  
> ####
> - 메서드  
>   : 카드를 추가로 지급받을 수 있는 상태인지 조회하기 (카드 총합이 21을 초과했는지를 기준으로)  

> ###7) Dealer
> - model
> - participant를 상속받은 딜러
> ####
> - 필드
> ####
> - 메서드  
    : 현재 가지고 있는 카드가 16 이하인지 아닌지 판별하기  
>   : 카드를 추가로 지급받을 수 있는 상태인지 조회하기 (카드 총합이 16을 초과했는지를 기준으로)

> ###8) Manager
> - model
> - 게임 종료 여부를 판별하는 가상의 객체  
> ( 현실에서는 딜러가 해당 역할을 하는 것 같음  
>   근데 그러면 딜러에 너무 많은 책임을 주게 될 것 같고,  
>   딜러랑 플레이어를 같은 레벨로 두는게 좋을 것 같아서.. )
> ####
> - 필드
> ####
> - 메서드  
>   : 게임 종료 여부 판별하기  
>     1) 딜러가 21을 초과하는지 확인  
>     2) 모든 플레이어가 21을 초과하는지 확인  
>     3) 참여자들 중에 21을 달성하는 참여자가 있는지 확인    
>     4) dealing shoe에서 갖고 있는 카드가 다 소진되었는지 확인  
> 

> ###9) Game
> - controller
> - 블랙잭 게임을 실행하는 가상의 객체
> ####
> - 필드  
>   : 지금이 첫 번째 라운드인지 아닌지(isFirstRound)  
>   : 플레이어 리스트(players)  
>   : 딜러(dealer)
> ####
> - 메서드  
>   : 딜러와 플레이어를 초기화하기  
>   : 첫 번째 라운드에서 딜러와 플레이어에게 모두 2장씩 카드를 지급하기  
>   : 카드를 추가로 지급받을 수 있는 상태의 참여자들에게 카드 지급하기  
>   > 플레이어의 경우,  
    추가로 지급받을 것인지 물어보고, 받는다고 하면 dealing shoe에서 카드 한 장을 뽑아 추가 지급해준다.  
    지급받을 때마다, 플레이어가 가지고 있는 카드들의 정보를 출력해준다.  
    플레어이가 추가로 지급받지 않겠다고 할 때까지 물어본다.
    
>   > 딜러의 경우,  
    추가로 지급받을 수 있는 상황인지 아닌지에 따라서 추가 지급하거나/하지 않고 안내 메시지를 출력해준다. 
    
>   : 현재까지 참여자들이 갖고 있는 카드들의 정보를 모두 오픈(출력)하기  
>   : manager를 통해 게임 종료 여부를 판단하고,  
>   - 게임이 종료되지 않으면 다시 참여자들에게 카드를 추가 지급한다.  
>   - 게임이 종료되면 카드 추가 지급을 멈추고, 참여자들의 카드 정보와 함께 최종 결과를 출력한다.  
> 
>   : 각 참여자 별로 최종 수익을 출력하고, 게임을 종료하기

> ###10) PlayResult
> - 게임에 대한 결과 정보를 가지고 있는 객체
> ####
> - 필드  
>   : 게임 결과 상태 (playStatus - blackjack, bust, win, continue)  
>   : 승리한 참여자 유형 (winnerType - player, dealer, none)

> ###11) Name
> - 참여자 이름 정보를 가지고 있는 원시값 포장 객체
> - 필드  
>   : 이름(name: string)  
> - 메서드  
>   : 생성자에서 예외처리 (공백 등)

> ###12) BettingMoeny
> - 베팅 금액 정보를 가지고 있는 원시값 포장 객체
> - 필드  
>   : 금액(bettingMoney: int)  
> - 메서드  
>   : 생성자에서 예외처리 (10000원 이상, 100000원 이하만 가능)

> ###13) PlayStatus
> - 게임 결과 상태를 나타내는 enum 객체
> - 필드  
>   : blackjack, bust, win, continue

> ###14) WinnerType
> - 승리한 참여자 유형을 나타내는 enum 객체
> - 필드  
>   : player, dealer, none
#
#
#

