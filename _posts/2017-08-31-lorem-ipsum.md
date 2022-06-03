---
layout: default
title: "prompt"
tags: tag1 tag2 tag3 tag4
---

linux 장비의 프롬프트(Prompt)를 내 마음대로 바꾸어 보자.
기본적으로 프롬프트의 설정은 PS1 이라는 값에 의해 결정이 된다.
<br><br>

# 1. 프롬프트 변경하기

쉘변수 기호 | 의미
:-:|:-
\t | 24시간의 단위로 현재시각을 HH:MM:SS 로 표시
\T | 12시간의 단위로 현재시각을 HH:MM:SS 로 표시
\@ | 12시간의 단위로 현재시각을 오전/오후 로 표시
\d | 현재 날짜를 나타냄. 요일, 월, 일 형식으로
\s | 현재 사용중인 쉘의 이름을 나타냄
\w | 현재 디렉토리의 전체 절대경로를 모두 표시
\W | 현재 디렉토리의 전체 절대경로명 중 현재디렉토리만 표시
\u | 사용자명을 표시
\h | 서버의 호스트명을 표시
\H | 서버의 도메인명을 표시
\# | 접속한 순간부터 사용한 명령어의 번호를 1번부터 차례대로 표시
\! | 사용한 명령어의 history 번호를 표시
\\$ | 현재 사용자가 root(uid 가 0 )이면 # 을 표시하고 아니면 $ 를 시
\\ | \' 문자 자체를 표시
\a | ASCII 종소리 문자 (07)
\e | ASCII 의 escape 문자 (033)
\n | 개행문자 (줄바꿈)
\v | 사용중인 bash 의 버전
\V | 사용중인 bash 의 배포, 버전+패치수준으로 버전을 상세히 표시
\r | Carrage retrun
\nnn | 8진수 nnn 에 해당하는 문자


<br><br>
기본적인 프롬프트 변경에 사용되는 기호들은 위와 같고
아래와 같은 형식으로 표현할 수 있다.

ex)<br>
export PS1="[\t-\u]\h:\w#"<br>
-> [10:23:10-root] testserver01:/usr/temp#
<br><br>

# 2. 재기동 이후에도 프롬프트 변경 반영하기

항상 같은 프롬프트를 설정하여 유지를 하고자 한다면
해당 export PS1 라인을 /etc/profile 에 넣어주면 된다.

또한 각 유저마다 프롬프트 라인 설정 값을 다르게 해주고 싶다면, 각 유저의 home 설정의 .bash_profile 파일에 해당 export PS1 라인을 추가해주면 신규세션 접속시 변경된 프롬프트를 확인 할 수 있다.

전체 설정의 경우 /etc/bashrc 또는 각 유저 설정시에 .bashrc에 해당 설정을 넣어 주는 것으로 가이드가 되어 있는데, 이는 기호에 따라 반영하면 된다.



이는 유저가 세션을 접속할 경우에 profile 을 읽어오는 순서에 따라 반영이 되기 때문인데

**/etc/profile -> /etc/bashrc -> ~/.bash_profile -> ~/.bashrc**

위와 같은 순서로 읽어 오기 때문이다.

때문에 export PS1 설정은 덮어 씌우는 순서로 되어 가장 마지막의 설정값이 반영이 된다고 생각하면 된다.
<br><br>

# 3. 프롬프트에 색깔을 넣어 보기

색을 표현하기 위해 추가된 부분을 ASCII 에의한 ANSI color 구분하여 표현한 것이고, 색상표는 아래와 같다.

Intensity | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 9
:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:
Normal | Black | Red | Green | Yellow | Blue | Magenta | Cyan | White | reset
Bright | Black | Red | Green | Yellow | Blue | Magenta | Cyan | White | -


Code | Effect
:-:|:-
0 |Reset / Normal
1 | Intensity: Bold
2 | Intensity: Faint
3 | Italic: on
4 | Underline: Single
5 | Blink: Slow
6 | Blink: Rapid
7 | Image: Negative
8 | Conceal
21 | Underline: Double
22 | Intensity: Normal
24 | Underline: None
25 | Blink: off
27 | Image: Positive
28 | Reveal
30–39 | Set foreground color, normal intensity
40–49 | Set background color, normal intensity
90–99 | Set foreground color, high intensity
100–109 | Set background color, high intensity

<br>
ex)<br>
export PS1="\e[41;37;4m[\t-\u]\h:\w#\e[0m"<br>
->  <U><span style='color:#808080;background-color:#ff0000'>[10:23:10-root] testserver01:/usr/temp#</span></U>

<br>
41 -> 배경색을 붉게<br>
37 -> 글자 색을 회색으로<br>
4 -> 및줄을 넣어서<br>
와 같이 표현하여 넣을 수 있다.

0 의 경우 리셋을 해주는 부분으로 들어있지 않을경우 내가 입력하는 인풋마저도 해당 설정을 따라가게 된다.
<br><br>

# 4. ksh 등의 PS1 에서의 쉘 변수 기호가 되지 않을 경우


ksh 의 경우 위의 프롬프트 설정이 반영이 되지 않고

<br>
ex)<br>
export PS1="\e[41;37;4m[\t-\u]\h:\w#\e[0m"<br>
-> e[41;37;4m[t-u]h:w#e[0m<br>
<br>
위와 같이 반영이 전혀 되지 않은 설정으로 표현이 될 수가 있다.

이러한 경우
<br>

색상  표현식은 ^[[41;37;4m 으로 구현 할수 있으며,<br>
^[ 는 insert 환경에서 ctrl+v, ctrl+[ 를 입력하면 된다.

hostname 을 표현 할떄는 \`hostname\`<br>
username 을 표현 할때는 \`whoami\`<br>
현재 경로를 표현할 때는 \`pwd\` 와 같은 방식으로 가능하다.

<br>
ex)<br>
export PS1="^[[41;37;4m["``whoami``"]"``hostname``":"``pwd``"#^[[0m"<br>
->  <U><span style='color:#808080;background-color:#ff0000'>[root] testserver01:/usr/temp#</span></U>
