## Welcome to GitHub Pages





JANUARY, 2021





Gvim script 작성시 주의할점은 테스트대상 같은 파일을 여러창에 열어두고 설정파일을 바꿔가며 명령어를 테스트하는 경우 신뢰성이 없으므로 하나의 창만 열어 두고 테스트 하기 바랍니다.(아직 source 명령어는 script를 직접 읽어 들이는 경우 상당히 불안 한것 같습니다).이런 사례가 좀 있다고 합니다.




아래내용을 빔설정파일에 작성하신 경우에는 :set [no]list 하셔서 각라인 마지막 문자다음의
$ 앞에 다른 공백문자가 없는지 확인 바랍니다.때론 공백문자가 <Space>등으로 해석 된다네여..
전체 파일에 다 적용하려면 :%s/\v\s*$// 하시면 됩니다..( \는 backslash )

Gvim 7.0을 기준으로 설명한 것으로 몇몇(한글전환포함-vim 초보자는 모르면 그냥 넣고 쓰면 압니다)은 vim상이나 구버전에서 적용되지 않습니다.

아래내용은 텍스트 작성용으로 적합한 내용 입니다.
vim은 Esc키와 Caps_Lock 키를 서로 바꿔 사용하면 좋습니다..(xmodmap이나 console인 경우에도
xmodmap처럼 loadkey로 수정할수 있습니다)
-- 참조 http://marc.info/?l=vim&m=118070873505901&w=2 (Esc키 와 Caps_Lock )

-- 리눅스 초보자는 man xev 참조..
http://hmhm.net/entry/free-korean-font (major 공짜 폰트)
http://yoonfont.co.kr/help/download_list.asp?code=03(이 폰트는 윈도우에 설치후 리눅스로 복사)
(윤디자인 웹바탕,웹돋음- 괜찮은 폰트로(작은 글씨에 대단한 강점) 보며 윈도우 굴림,바탕보다 더 좋다고 봅니다

(특히 가독성 및 심미성을 고려할때 - 개인차 존재) fontconfig에서 영문과 한글폰트를 매칭시켜야 제대로 나옵니다)
예시...
vi /etc/Cmap -- Console 용

keycode 0x01 = Caps_Lock
keycode 0x3a = Escape

loadkeys /etc/Cmap

vi /etc/Xmap -- Xwindow 용

clear Lock
keycode 66 = Escape
keycode 9 = Caps_Lock
add Lock = Caps_Lock

xmodmap /etc/Xmap
키코드들은 showkey -t 3 나 xev 로 확인..

특별한거는 아니고 보통모드(구명령모드)에서 입력모드 진입시 한글입력대기 상태를 자동으로 설정해 줄수있는 script 입니다..
\ 는 Backslash 이며 아래 내용을 gvim(아래의 gui_running 부분)용은 gvimrc(없으면 생성) 설정파일에 vim 용은 vimrc 전역이나 개인설정 파일에 넣습니다..

추가로 이문서에서는 보통모드는 기존의 명령모드로 명령모드는 기존의 Ex모드로 명명합니다.
vim-doc 문서를 보아 이렇게 해석하는게 옳다고 봅니다...

즉 아래의 nnoremap , cnoremap 그리고 inoremap 은 각각 normal mode, cmdline mode 그리고 insert mode 를 나타내므로 위와 같은 용어를 본인은 사용합니다..

mapping 기본 설명..

nnoremap <옵션값> {lhs} {rhs}
{lhs}는 좌측값이고 {rhs}은 우측값 입니다. 이맵핑은 우측값은 vim원래 명령어를 사용하여 맵을 설정하고자 하는경우에 사용하는 겁니다..즉 개별적으로 만든 맵핑으로 재밉핑이 이루어지지 않는 경우 입니다(안정적)..

기타 맵핑에 사용할수 있는 키보드값은 help key-notation 하면 사용할수 있는 특수키가 나옵니다. 일반키는 소문자,대문자 (shift 값이 자동 포함)대로 써주면 됩니다..
기타맵과 관련하여 noremap 같은경우 보통모드,비주얼모드,아퍼레이터모드 모두에 적용할때 사용하는 맵인데 개별적으로 맵을 설정하는것 보다 다소 불안한점이 있으므로 아래는 전부 개별모드용으로 맵핑을 만들었습니다..

if has("gui_running")

winpos 200 50
"gvim 시작화면 Display상 위치지정

set lines=24
set columns=80
"gvim Default 화면크기 지정.


set guifont=Bitstream\ Vera\ Sans\ Mono\ 10
"영어문자 폰트 설정
set guifontwide=YDIDotum\ 10
"set guifontwide=Naver\ Dictionary\ 9
"한글문자 폰트 설정

"구 옵션 guifontset 이 설정이 존재하면 위 옵션에 우선합니다..
"그리고 아래 내용은 uff-8 환경하에서 잘 지원 됩니다..
"우리나라 한글폰트지정 문자크기(9등)앞에 \Roman,\Regular,\Bold등 지정가능 (trick) 위폰트들은 공짜로 구할수 있으며

폰트이름은 fc-list 로부터 구합니다.
"폰트이름내의 공백라인 앞에는 \를 추가하고 ,를 포함하는 폰트이름은 \\(즉 \\,)를 넣어주면 됩니다.영어는 단일폭 문자이고 한글은 이중폭 문자인데 기술적으로 양자는 동일한 폭으(즉 정확한 배수가 되어야)로 같이 표시될수 있어야 한다고 합니다.
set linespace=0 값을 조정하면 픽셀라인을 조정할수 있어.좀더 시원한 글자크기를로 조정할수있습니다..(해당값은 -1 등 에서 자연수) 글자 높이폭이 전체폭 다 사용하는 폰트의 경우 조정하면 좋습니다..

set formatoptions+=m 옵션을 주는 경우 한글문서등 작성시 textwidth등 처리에 좋다고 합니다..

hi MEa term=standout cterm=reverse ctermfg=9 ctermbg=7 guibg=LightGray
hi MEb term=underline ctermfg=4 guifg=Purple
"아래 함수에서 사용될 컬러그룹들을 개인적으로 미리 생성

hi ErrorMsg term=standout ctermfg=1 guibg=White guifg=Red

"에러메세지 색상을 백그라운드 컬러 없이 출력

set imak=Hangul
set iminsert=2
"위는 확인 설정(DeFault값,하지만 imak를 사용하려면 다른 값이면 안됩니다.)
set imsearch=0
"set imcmdline 을 설정하면 안되더군여.고로 위 3개 설정, imak는 보통 S-space 나 Hangul 둘중 하나로 설정 합니다.
이는 한글 입력전환 기능에 대한 키옵션 설정이 아니라 보통모드에서 입력모드 진입시등의 경우에 Default 문자 입력 상태를 설정할수있는 iminsert(입력모드),imsearch(명령모드중 찾기모드),imcmdline(명령모드)옵션등을 완전히 이용할수 있도록 사전에 해당 관련 키를 미리 지정해 주는 겁니다.
"이옵션들은 아래에 제가 만든 맵핑 함수를 사용하지 않으면 전혀 필요가 없습니다.입력모드 진입시 한글전환 문제 때문에 보통모드를 많이 사용하지 않는 기피 현상을 거의(100% ^-^) 제거시켜 줍니다..
"KoreaI은 입력모드 진입시 한글주입력 모드를 나타내고 EnglishI는 영문주입력 모드를 나타냅니다.
"KoreaN은 보통모드상 에서의 r,R,gR 명령어 사용시 한글주입력을 나타내고 EnglishN은 영문주입력을 나타냅니다.

물론 각모드에서 한영전환이 가능합니다. (아직까지 락관련현상 및 사이드효과 없음)
"F11은 보통모드상에서의 한영전환을 나타내고 F12는 입력모드상에서의 한영전환을 나타냅니다.

입력모드에서 보통모드로 나올때 표시되는 "한글-영어" 메세지는 보통모드는 한글이고 입력모드는 영어를 나타내며 "한글 모드"

메세지는 양자 모두 한글입력 대기 상태임을 나타냅니다.
"한영주모드 전환은 모두 보통모드상에서만 가능합니다(한영모드전환은 언제나 가능)


디폴트는 영문모드로 시작하도록 하였습니다..(모두 toggle 기능)

한글모드로 시작하고 싶으시면 함수밖의 call EnglishN(),EnglishI()를 KoreaN등으로 바꾸어 줍니다


다음 함수들 내에서 imak문자열은 모두 제거해도 됩니다(약간의 안정성 때문에 )..


fun KoreaI()
let where = getpos('.')
if exists("s:num_1_1")
unlet s:num_1_1
let s:num_1_0 = "True"
else
let s:num_1_0 = "True"
endif

if exists("s:num_2_0")
inoremap <Esc> <Esc>:set imi=0<CR>
\:set imak=""<CR>:norm! l<CR>:echo "한글 모드"<CR>
else
inoremap <Esc> <Esc>:set imi=0<CR>
\:set imak=""<CR>:norm! l<CR>:echo "영어-한글"<CR>
endif

nnoremap a :set imak=Hangul<CR>:set imi=2<CR>a
nnoremap A :set imak=Hangul<CR>:set imi=2<CR>A
nnoremap i :set imak=Hangul<CR>:set imi=2<CR>i
nnoremap I :set imak=Hangul<CR>:set imi=2<CR>I
nnoremap c :set imak=Hangul<CR>:set imi=2<CR>c
nnoremap C :set imak=Hangul<CR> :set imi=2<CR>C
nnoremap cc :set imak=Hangul<CR>:set imi=2<CR>cc
nnoremap o :set imak=Hangul<CR>:set imi=2<CR>o
nnoremap O :set imak=Hangul<CR>:set imi=2<CR>O
nnoremap s :set imak=Hangul<CR>:set imi=2<CR>s
nnoremap S :set imak=Hangul<CR>:set imi=2<CR>S
nnoremap <F12> :call EnglishI()<CR>

echohl MEa
echo "한글 전환"
echohl None
call setpos('.',where)
endfun


fun EnglishI()
let where = getpos('.')

if exists("s:num_1_0")
unlet s:num_1_0
let s:num_1_1 = "True"
else
let s:num_1_1 = "True"
endif


if exists("s:num_2_1")
inoremap <Esc> <Esc>:set imi=0<CR>
\:set imak=""<CR>:norm! l<CR>:echo "영어 모드"<CR>
else
inoremap <Esc> <Esc>:set imi=0<CR>
\:set imak=""<CR>:norm! l<CR>:echo "한글-영어"<CR>
endif



nnoremap a :set imak=""<CR>:set imi=0<CR>a
nnoremap A :set imak=""<CR>:set imi=0<CR>A
nnoremap i :set imak=""<CR>:set imi=0<CR>i
nnoremap I :set imak=""<CR>:set imi=0<CR>I
nnoremap c :set imak=""<CR>:set imi=0<CR>c
nnoremap C :set imak=""<CR>:set imi=0<CR>C
nnoremap cc :set imak=""<CR>:set imi=0<CR>cc
nnoremap o :set imak=""<CR>:set imi=0<CR>o
nnoremap O :set imak=""<CR>:set imi=0<CR>O
nnoremap s :set imak=""<CR>:set imi=0<CR>s
nnoremap S :set imak=""<CR>:set imi=0<CR>S
nnoremap <F12> :call KoreaI()<CR>

echohl MEa
echo "영어 전환"
echohl None
call setpos('.',where)
endfun
call EnglishI()


fun KoreaN()
let where = getpos('.')
if exists("s:num_2_1")
unlet s:num_2_1
let s:num_2_0 = "True"
else
let s:num_2_0 = "True"
endif

if exists("s:num_1_0")
inoremap <Esc> <Esc>:set imi=0<CR>
\:set imak=""<CR>:norm! l<CR>:echo "한글 모드"<CR>
else
inoremap <Esc> <Esc>:set imi=0<CR>
\:set imak=""<CR>:norm! l<CR>:echo "한글-영어"<CR>
endif

nnoremap r :set imak=""<CR>:set imi=0<CR>r "오류 아닙니다.!
nnoremap R :set imak=Hangul<CR>:set imi=2<CR>R
nnoremap gR :set imak=Hangul<CR>:set imi=2<CR>gR
nnoremap <F11> :call EnglishN()<CR>

echohl MEb
echo "한글 치환"
echohl None
call setpos('.',where)
endfun


fun EnglishN()
let where = getpos('.')
if exists("s:num_2_0")
unlet s:num_2_0
let s:num_2_1 = "True"
else
let s:num_2_1 = "True"
endif

if exists("s:num_1_1")
inoremap <Esc> <Esc>:set imi=0<CR>
\:set imak=""<CR>:norm! l<CR>:echo "영어 모드"<CR>
else
inoremap <Esc> <Esc>:set imi=0<CR>
\:set imak=""<CR>:norm! l<CR>:echo "영어-한글"<CR>
endif

nnoremap r :set imak=""<CR>:set imi=0<CR>r
nnoremap R :set imak=""<CR>:set imi=0<CR>R
nnoremap gR :set imak=""<CR>:set imi=0<CR>gR
nnoremap <F11> :call KoreaN()<CR>

echohl MEb
echo "영어 치환"
echohl None
call setpos('.',where)
endfun
call EnglishN()


else
"여기파트는 터미널에만 적용됩니다.
set nopaste
set pastetoggle=1(이것은 원하는 키로..)
"이옵션은 터미널에만 영향을 미친다.GUI 에서는 Disabled automatically

"아래 set paste 참조..



endif




아래 내용을 참고하시면 발생가능한 에러는 초보자도 충분히 다룰수 있다고 봅니다..


추가 설명을 드리면 iminsert 값이 0 이면 보통모드에서 입력모드 진입시 영문입력 상태를 2 이면 한글입력 가능상태를 지정합니다..

imsearch값은 명령모드중 탐색시(/, ?) 한영을 지정하는 것으로 영문을 지정하는 0 값으로

놓는게 좋습니다.. imcmdline 은 명령모드(콜론상태 - :)상태에서의 한영을 지정하는것 입니다.set imc는 영어를 set noimc 는 한글을 지정 합니다..

imak 는 위 옵션들이 잘 작동하게끔 사전에 설정해 주는 겁니다..

참고로 imdisable 은 XIM 입력기 자체를 아예 사용하지 않는 경우에 사용합니다..

따라서 이때 이 옵션값을 주면 한글입력기로는 어떤 경우이든지 한글을 입력할수 없습니다..

다른 방법을 사용하는것은 가능합니다(keymap 등인데 제가 잘모릅니다)..



기타 마이너 Tips



보통모드에서의 입력모드 진입없이 바로 공백라인 삽입..



nnoremap <silent> oo :call Loo()<CR>



fun Loo()
let line = line(".")
let column = col(".")
let line -= 1
let mylist = ""
call append(line,mylist)
let line += 2
call cursor(line,column)
endfun

"위 "oo"의 Loo() 함수는 Default o와 O기능과 달리 보통모드에서 공백 라인 생성후 입력모드로 진입하지 않도록한다.

공백라인은 현재라인 상위에서 생성되며 2oo 등과 같은 식으로 여러라인을 한꺼번에 생성할수도 있다.

" 22oo 이면 현재라인부터 22 라인까지 공백 텍스트를 넣는다는 의미입니다 사실 이 기능이 굉장히 유용합니다..!!

여기서의 count기능은 개별 생성 명령어로는 불가능한것 같습니다 (즉 range 로 해석 됩니다).

"커셔가 보기좋게 밑에 위치하도록 해 놨습니다(많은 공백라인은 yanking와 p를 사용하는게 좋아 보입니다..)







일단 화살표키를 집중적으로 살펴 봅니다.



MS 윈도우처럼 커셔키의 상하좌우 이동

(즉 스크린 라인,칼럼별 커셔키 이동, h,l 만으로 다음 라인 이동 가능)



nnoremap <silent> h :call Lhs()<CR>
nnoremap <silent> l :call Rhs()<CR>

"디폴트말고 이방법을 적용하고자 한다면 이후 언급되는 기타 맵들에 적용..





nnoremap - gm
nnoremap = g$
nnoremap 0 g0
nnoremap $ g$


nnoremap j gj
nnoremap k gk
vnoremap j gj
vnoremap k gk


nnoremap <Down> gj
nnoremap <Up> gk
vnoremap <Down> gj
vnoremap <Up> gk
inoremap <Down> <C-O>gj
inoremap <Up> <C-O>gk



기타필요하면 vnoremap이나 inoremap등으로도 만들어 줍니다.





fun Lhs()
if col(".") == 1
norm! -$
else
norm! h
endif
endfun

fun Rhs()
if col(".") >= col("$") - 1
norm! +0
else
norm! l
endif
endfun
(Rhs() 함수가 꽤 어려웠음)

경우에 따라 -1을 제거해야 할지도 모릅니다.



ps.

알고보니 vim 디폴트 상태의 좌우 커셔키 이동이 사실은 더 좋은것 같습니다.. 왜냐하면 공백문자 처리작업이 좀 더 좋기 때문 입니다.(set [no]list 로 확인) 그리고 textwidth 등도 0 의 값을 가지는게 좋다고 봅니다.



"보통모드에서 화살표 이동키 속도 가속화(accelerations)



nnoremap <C-H> <C-Left>
nnoremap <C-L> <C-Right>
nnoremap <C-K> <C-U>
nnoremap <C-J> <C-D>




입력모드상에서의 화살키 사용입니다(toogle 기능).. 텍스트 문서 작성용으로 최고의 효율성 보장 !

원하는키로 적정히 조정하여 쓰세여 (아래조합이면 좋다고 봅니다)..



fun Arrow0()


exe 'inoremap <silent> 2 <C-O>:call BS()<Esc>'
exe 'inoremap <silent> 3 <Space>'
exe 'inoremap <silent> 4 <C-J>'
exe 'inoremap <silent> 5 <Home>'
exe 'inoremap <silent> 6 <End>'
exe 'inoremap <silent> 7 <Left>'
exe 'inoremap <silent> 8 <C-O>gj'
exe 'inoremap <silent> 9 <C-O>gk'
exe 'inoremap <silent> 0 <Right>'

echohl WarningMsg
echo "Starting... "|sleep 1|redraw
echohl None
inoremap <Home> <C-O>:call Arrow1()<CR>
endfun
inoremap <Home> <C-O>:call Arrow0()<CR>

fun Arrow1()
exe 'iunmap 2'
exe 'iunmap 3'
exe 'iunmap 4'
exe 'iunmap 5'
exe 'iunmap 6'
exe 'iunmap 7'
exe 'iunmap 8'
exe 'iunmap 9'
exe 'iunmap 0'
inoremap <Home> <C-O>:call Arrow0()<CR>
echohl Comment
echo "Stopping... "|sleep 1|redraw
echohl None
endfun


써보시면 알겠지만 keypad 숫자를 사용하는게 큰숫자를 입력시 빠르다는건 다들 느끼실 겁니다..그리고 입력모드에서 키하나만 지정하면 타글기능으로 하부맵핑을 두배이상으로 만들수 있으므로 응용적용하면 아주 유용할 겁니다..

exe는 없어도 되는데 뽀대 나라고..^_^

혹시나 해서 보니 입력모드에서 직접 보통모드 명령어를 사용할수 있도록 하는 <C-O>를 통해 gj 나 gk 를 이용할수도 있는 방법이있군여.-완벽합니다. .vim!



cnoremap <kPoint> ,




명령모드(Ex모드)에서는 keypad "." 키를 " ," 로 두는게 재빠르죠.







보통모드에서의 Backspace 기능 활성화

--Tab 위에서 라인결합시 탭이 살아있는 단점이 있습니다.. 알고보니 기본명령어 J 도 동일 하므로 단점이 아니네여..^_^


nnoremap <silent> <S-H> :call BS()<CR>
nnoremap <silent><BS> :call BS()<CR>




fun BS() "보통모드에서의 Backspace의 사용
let test_l = line(".")
let test_c = col(".")
norm! X
if test_c == 1 && test_l != 1
-
norm! $
if col(".") == 1
delete
else
let test = getpos('.')
s/\v$.*\n.*^/ /
let test[2] += 2

call setpos('.',test)
endif
endif
endfun





"라인 결합시 추가로 한칸 안 넣으려면 / /와 2l을 적당히 조정하면 됩니다.

(라인결합시는 공백을 하나 넣는게 안정적입니다 - 보통모드 J 명령어와 입력모드상의 backspace의 장점 조합 )



보통모드에서 Space 기능 활성화



nnoremap <Space> i<Space><Esc>l






보통모드에서의 Enter 기능 활성화..



nnoremap <S-J> i<CR><Esc>2h

"위는 입력모드와 일관성을 유지 하도록 한다(동일한 기능).






"This is only applied in gui_mode as autoread


"set autoread<



"Delete is also applied -- 'confirm q'


외부프로그램에서(gvim등) 동일파일에 대한 수정이 있는 경우에 자동으로 해당 내용을 현재 버퍼에 업데이트 시킵니다..

위 해당 설정용 옵션들이 잘 안먹히는 경우에는 아래의 내용을 사용합니다..(vim FAQ 수정)



hi MoreMsg NONE
hi MoreMsg term=standout ctermfg=5 guibg=White guifg=Purple
MoreMsg 는 디폴트값 변경이 잘 안되더군여(겨우 잡음)..

아래 confirm()함수가 이 하이라이트 그룹을 사용합니다.



autocmd! FileChangedShell * call Update()

fun Update()
let choice = confirm("File has been changed outside of vim",
\"&Cancel\n&Load",1)
redraw

echohl WarningMsg
if choice == 1
echo "Preserved as it-is !"
sleep 1
else
edit! %
echo "Loading Sucessfully !"
syntax on

sleep 1
endif
echohl None
endfun




일단 confirm()함수는 input()함수나 inputdialog() 함수보다 간단하며 깔금하게 선택항목을 제공하므로 다른 여러 함수나 autocmd 등을 사용하고자 하는경우에 구미에 맞게 설정, 사용 할수 있습니다..
간단히 설명드리면 처음인자인 &Cancel을 선택하면 return값 1을 출력하고 다음순서인 &Load를 선택하시면 2를 출력합니다..마지막 1번은 그냥 엔터한 경우나 기타 등을 누른경우 디폴트값을 지정하는 겁니다..여기서는 1번으로 지정했습니다.

기타 하이라이트는 명령모드에서 hi 명령어를 내리시면 가능한 그룹과 컬러 설정목록이 해당 컬러화면과 함께 출력되므로 골라서 쓰세여..





편집용 문서 작성시 유용한 map들..

word 와 WORD 기능을 바꾼다...

(WORD는 공백문자를 간격으로한 단어를 구분하고 word 는 좀더 세부적으로 나눈 겁니다)



"아래 mapping은 Text 작업시 유용하다.


nnoremap w W
nnoremap yw yW
nnoremap dw dW
nnoremap W w
nnoremap yW yw
nnoremap dW dw
nnoremap e E
nnoremap E e
nnoremap b B
nnoremap - M
nnoremap = $




0 번이 ^ 를 나타내므로 가까운곳에 = 을 $ 로 map 처리합니다..



탭창 열고 닫기 및 이동 맵- 굉장히 유용..



nnoremap <silent> <F2> :tabnew<CR>
inoremap <silent> <F2> <Esc>:tabnew<CR>
nnoremap <silent> <S-F2> :close!<CR>
inoremap <silent> <S-F2> <Esc>:close!<CR>
nnoremap <silent> <F3> :tabn<CR>
inoremap <silent> <F3> <Esc>:tabn<CR>
nnoremap <silent> <S-F3> :tabp<CR>
inoremap <silent> <S-F3> <Esc>:tabp<CR>
nnoremap ,t :tabmove




tabmove 뒤에는 공백문자가 하나 있어야 합니다(이점 유의하시구여). 탭창 순서자체를 옮기는 겁니다. 현재탭을 맨앞에 위치하고자 할때에는 0 번을 두번째는 1번 순등입니다..


여기서 S 는 Shift 입니다..







현재 창내 커셔가 위치한 파일명을 새로운 탭창에서 보기..



nnoremap <F4> :tabedit <cfile><CR>
inoremap <F4> <Esc>:tabedit <cfile><CR>




"아래는 일종의 탭브라우징 이다.




nnoremap ,b :set isk+=,,.,/,?,;,:,~,@,#,$,%,&,*,-,=,+<CR>:!firefox '
\<cword>'<CR>:set isk-=,,.,/,?,;,:,~,@,#,$,%,&,*,-,=,+<CR>




현재 파일내에 커셔가 위치한 인터넷 주소를 웹브라우저에서 연계하여 열기 본인 웹브라우저 명칭을 넣으면 됩니다(이하 동일)



nnoremap ,B :set isk+=,,.,/,?,;,:,~,@,#,$,%,&,*,-,=,+<CR>:!firefox '
\<cfile>'<CR>:set isk-=,,.,/,?,;,:,~,@,#,$,%,&,*,-,=,+<CR>




위처럼 자세하게 할필요 없이 아래처럼 간단히 해도 됩니다..



nnoremap ,,B :!firefox %<CR>

즉 현재 파일내용(%)을 웹브라우저에서 보게합니다..

<cword><cfile>등은 현재커셔 아래에 위치한 것을 단어로 인식하거나 파일로 인식하는 기호로 자세한 것은 help expand() 하시면 볼수 있습니다.. !는 vim상에서 shell명령어를 사용하고자 하는경우 앞에 붙여 줍니다..

isk는 위 단어에 적용되거나 할 인식가능한 문자정의에 해당합니다..



버퍼를 창으로 불러내기(해당 번호 입력하면 됩니다) 버퍼란 파일이 메모리상에 올려진 것을 말합니다..

버퍼종류도 3가지가 있는데 자세한건 help 페이지 참조..



nnoremap <expr> <F8> ":files<CR>:buffer "
inoremap <expr> <F8> <Esc>":files<CR>:buffer "

buffer 다음에도 공백문자 하나가 와야 보기 좋은데 이때는 <expr>로 해주어야 되는군여..






"Emacs' 키맵 스타일 사용
"입력모드에서 <C-W>,<BS> or CTRL-H 는 원래 존재한다
"명령(Ex)모드에서의 q:, q/ 그리고 q? 기능등 때문에 필수 불가결 (완전한 편집모드 지원)

"위 화면내에서 CTRl-C 를 사용해 보세여..


cnoremap <C-A> <Home>
cnoremap <C-E> <End>
cnoremap <C-D> <BS>
cnoremap <C-X> <Delete>
cnoremap <C-H> <Left>
cnoremap <C-J> <Down>
cnoremap <C-K> <Up>
cnoremap <C-L> <Right>
cnoremap <C-W> <S-Left>
cnoremap <C-B> <S-Right>
"나머지키는 가능한 vim스타일을 사용한다.CTRL키때문에 손목무리가 오므로 본인은 사용하지 않는 맵..





help 페이지 관련...



help 페이지 단일창에서 열기..



nnoremap <F1> :help<CR>:only!<CR>



help 페이지 내에서 Enter 키로 해당 주제항목 세부 내용보기


au filetype help nnoremap <buffer><CR> <C-]>



help 페이지 내에서 백스페이스키로 종전 화면 돌아가기


au filetype help nnoremap <buffer><BS> <C-T>


"vim에서 사용가능한 한계 메모리량을 kbyte 단위로 설정..



"C언어 주석처리 /* 와 */ 를 넣고 지우기.. 프로그래머도 아닌데 어쩌다 만들게 됐네여..

(종전 논리로 복귀합니다.. 라인이 동일한 경우 커셔위치가 이동하는걸 깜빡하고)


"s/\v(\/\*[ ]{})(.*)([ ]{}\*\/)/\2/ -- 이건 그냥 참고하세여..

"magic 으로 하면 너무 혼란스러워 very magic(주로 사용) 으로 작성 하였습니다.


"magic은 BREs(기본정규 표현식)에 가깝고 very magic 는 여기에 성가신 backslash 를 넣지 않는 경우입니다

"즉 일반 알파벳이나 정수등을 제외한 모든 문자를 특수문자로 간주합니다(굉장히 편하죠.)

"다만 일반문자(알파벳문자)에서 backslash 추가로 특수문자로 정의된 \n, \s 등 같은 경우에는 당연 backslash 를

" 넣어야 합니다 (안 넣으면 어떻게 할겨?)





기타 프로그램밍시 주석처리 전세계 맵총모음 (아래 제시된 함수의 경우는 없습니다--터미널에서는 안됩니다,그리고 별로 추천하고 싶지는 않습니다.)..

http://www.vim.org/tips/tip.php?tip_id=271

"s/\v(.*)(\/\*[ ]{})/\1/ "in_Lhs
"s/\v([^[:blank:]]|)([ ]{}\*\/)/\1/ "in_Rhs


이거는 아래 out_Lhs등과 달리 한라인에 동일한 c 주석문자가 2개이상 존재시 안에 있는 것부터 제거할지 아니면 바깥쪽에 있는걸

먼저 제거할지를 선택할수 있습니다(일단 위문장 차후 수정 들어갑니다).원하시면 아래 꺼와 대체하세여..

보통모드, 입력모드에서는 단일라인 양외곽에 주석 /* 와 */ 가 쌍으로 존재하는 경우 언제나 해당 pair 가 제거 됩니다.

현시점은 라인앞에 #이들어가는 헤드파일 지정문구나 변수지정문구의 경우 상황별로 나누기 어려워서(달리말하면 코딩내에 특수문자가 안들어 가기 때문에 분류하기가 힘듭니다)일단 이부분은 유보합니다.

그리고 비주얼주석은 /*시작라인, 라인들,마지막라인 */ 식으로 이루어지며 /*(시작공백라인) ,*각라인,*/ (마지막공백라인) 식으로는 되지 않습니다(업데이트하여 후자의 경우 추가 하였습니다.그리고 단일라인내 여러 screen line들의 경우에도 <C-X>맵핑으로 /* * * * */ 식의 주석이 가능합니다 .물론 제거시(보통모드등에서도 가능)에는 모두 동시에 제거됩니다.이것은 비주얼모드에만 해당합니다. 보통모드등은 Range 로 해석되기 때문에 불가능합니다. 그리고 #로 시작하는 라인의 경우에는 경우의 수가 너무많아 일단 보류 상태입니다.)





fun C_Comment(Group,Action)
let b:where = getpos(".")
" try
if a:Group == "normal"

if a:Action == "remove"

if strpart(getline("."),0) =~ '\/\*'
\&& strpart(getline("."),0) !~ '\*\/'
s/\v(\/\*[ ]?)// "out_Lhs
elseif strpart(getline("."),0) !~ '\/\*'
\&& strpart(getline("."),0) =~ '\*\/'
s/\v(.*[^[:blank:]]|)([ ]?\*\/)/\1/ "out_Rhs
elseif strpart(getline("."),0) =~ '\/\*'
\&& strpart(getline("."),0) =~ '\*\/'
s/\v(\/\*[ ]?)// "out_Lhs
s/\v(.*[^[:blank:]]|)([ ]?\*\/)/\1/ "out_Rhs
endif

elseif a:Action == "add"
if strpart(getline("."),0) =~ '^[[:blank:]]*$'
return ""
endif
if strpart(getline("."),0) !~ '^[[:blank:]]*#'
if strpart(getline("."),0) =~ '.*[{}()",;/*][[:blank:]]*$'
\|| strpart(getline("."),0) =~ '^\s*[^(){};]*\s*$'
s/\v^(\s*)(.*)$/\1\/\* \2 \*\//
elseif strpart(getline("."),0) =~ '.*[{}()",;/*][[:blank:]]*
\[^{}()";,/*].*$'
s/\v(.*[{}()",;/*][[:blank:]]*)([^{}()";,/*].*)
\$/\1\/\* \2 \*\//
endif
elseif strpart(getline("."),0) =~ '^[[:blank:]]*#'
s/\v^([[:blank:]]*)(.*)$/\1\2 \/\* \*\//
endif
endif "(remove,add) 항목 끝

elseif a:Group == "visual"

if a:Action == "remove"

if line("'\<") == line("'\>")

if strpart(getline("."),col("'<")-2,col("'>")-col("'<")+3)
\=~ '[/*][/*][ ]*[*/][*/]'
if strpart(getline("."),col("'<")-2,col("'>")-col("'<")+3)
\=~ '\/\*[ ]*\*\/'
s/\v(\/%V[*][ ]?%V[*]\/)//
elseif strpart(getline("."),col("'<")-2,col("'>")-col("'<")+3)
\=~ '\*\/[ ]*\/\*'
s/\v(\*%V[/][ ]?%V[/]\*)//
elseif strpart(getline("."),col("'<")-2,col("'>")-col("'<")+3)
\=~ '\/\*[ ]*\/\*'
s/\v(\/%V[*][ ]?%V[/]\*)//
elseif strpart(getline("."),col("'<")-2,col("'>")-col("'<")+3)
\=~ '\*\/[ ]*\*\/'
s/\v(\*%V[/][ ]?%V[*]\/)//
endif
else

if strpart(getline("'<"),col("'<")-2,3) =~ '\/\*'
\&& strpart(getline("'>"),col("'>")-2,3) =~ '\*\/'
s/\v([/]%V[*][ ]?)(.*)([^[:blank:]+]\s{-})
\([ ]?%V[*][/])/\2\3/
elseif strpart(getline("'<"),col("'<")-2,3) =~ '\*\/'
\&& strpart(getline("'>"),col("'>")-2,3) =~ '\/\*'
s/\v([ ]?[*]?%V.[/]?)(.*)([^[:blank:]/]+\s{-})
\([/]?%V.[*]?[ ]?)/\2\3/
elseif strpart(getline("'<"),col("'<")-2,3) =~ '\/\*'
\&& strpart(getline("'>"),col("'>")-2,3) =~ '\/\*'
s/\v([/]?%V.[*]?[ ]?)(.*)([^[:blank:]/]+\s{-})
\([/]?%V.[*]?[ ]?)/\2\3/
elseif strpart(getline("'<"),col("'<")-2,3) =~ '\*\/'
\&& strpart(getline("'>"),col("'>")-2,3) =~ '\*\/'
s/\v([ ]?[*]?%V.[/]?)(.*)([^[:blank:]+]\s*)
\([ ]?[*]?%V.[/]?)/\2\3/
endif

if strpart(getline("."),0) =~ '\s\+\*\s\+'
s/\v(\s+)(%V[*])(\s+)/\1 \3/g
endif
endif
elseif line("'\<") != line("'\>")
norm! `<
if strpart(getline("'<"),col("'<")-2,3) =~ '\/\*'
s/\v([/]%V.[*]?[ ]?)(.*)/\2/
elseif strpart(getline("'<"),col("'<")-2,3) =~ '\*\/'
s/\v([ ]?[*]?%V.[/]?)(.*)/\2/
endif

norm! `>
if strpart(getline("'>"),0) =~ '^\s*\*\/\s*$'
s/\v^(\s*)(\*\/)(.*)$/\1\3/
else
if strpart(getline("'>"),col("'>")-2,3) =~ '\*\/'
s/\v(.*[^[:blank:]*]\s{-})([ ]?[*]?%V.[/]?)(.*)/\1\3/
elseif strpart(getline("'>"),col("'>")-2,3) =~ '\/\*'
s/\v(.*)([^[:blank:]/]\s{-})([ ]?[/]?%V.[*]?[ ]?)/\1\2/
endif
endif

if strpart(getline(line("'<")+1),0) =~'^\s\+\*\s\+'
\&& strpart(getline(line("'>")-1),0) =~'^\s\+\*\s\+'
'<,'>s/\v(\s+)([*])(\s+)/\1 \3/g
endif

endif
elseif a:Action == "add"

if line("'\<") == line("'\>") "각 Screen-Line 앞에 * 비삽입
s/\v%V.*%V./\/\* & \*\//
exe 'norm! `<g^'
if exists("b:addition") "각 Screen-Line 앞에 * 삽입되는 예외
unlet b:addition
while col(".") < col("$")
exe 'norm! gjli*'
exe 'norm! lx2h'
if line(".") > line("'\<")
exe 'norm! ^gkxi '
exe 'norm! gj^x'
break
endif
endwhile
endif
elseif line("'\<") != line("'\>") "모두 각 Screen-Line 앞에 * 삽입
if strpart(getline("'\<"),0) =~ '^[[:blank:]]*$'
\&& strpart(getline("'\>"),0) =~ '^[[:blank:]]*$'
exe 'norm! `<g0i/*'
exe 'norm! `>g0i */'
exe 'norm! `<g0'
while line(".") < line("'\>")-1
exe 'norm! gjg0i * '
exe 'norm! g0'
endwhile
elseif strpart(getline("'\<"),0) !~ '^[[:blank:]]*$'
\&& strpart(getline("'\>"),0) !~ '^[[:blank:]]*$'
exe 'norm! `<'
s/\v%V.*%V./\/\* &/
exe 'norm! `>'
s/\v%V.*%V./& \*\//
else
return "지원되지 않습니다!"
endif
endif
endif "remove,add 끝
endif

" catch /E486:/
" echo "Out of command!"
" endtry
setl nohls
call setpos(".",b:where)

if exists("b:invisual")
unlet b:invisual
start
endif

endfun

nnoremap <silent> <Delete> :call C_Comment("normal","add")<CR>
nnoremap <silent> <S-Delete> :call C_Comment("normal","remove")<CR>

inoremap <silent> <Delete> <C-O>:call C_Comment("normal","add")<CR>
inoremap <silent> <S-Delete> <C-O>:call C_Comment("normal","remove")<CR>

vnoremap <silent> <Delete> <Esc>:call C_Comment("visual","add")<CR>
vnoremap <silent> <C-X><Delete> <Esc>:let b:addition="on"<CR>
\:call C_Comment("visual","add")<CR>
vnoremap <silent> <S-Delete> <Esc>:call C_Comment("visual","remove")<CR>

inoremap <silent> <M-Delete> <Right><Esc>:let b:invisual=1<CR>v







3<Delete> 하면 3개의 라인에 각각 주석이 삽입되네여... 제거도 동일합니다.(입력모드와 비주얼모드는 당연 해당이 안됩니다.)

단 공백문자만 있는 라인은 주석이 삽입되지 않도록 하였습니다. .

제거시에는 예를들어 비주얼 시작점이 / 또는 * 에 위치한 경우에는 해당 /*문자가 인식제거 됩니다.

단점::

<M-Delete>는 alt+Delete 키로 입력모드에서 비주얼모드로 바로 시작하고 주석삽입,제거후 자동으로 입력모드로

돌아옵니다(위의 if exists("b:invisual") 파트조건문) 이때 입력모드에서 비주얼로 들어간후 주석작업을 하지않고

다른 작업을 한후 비주얼모드를 나오면(보통모드로) 다음번 보통모드에서 비주얼모드로 진입후 빠져나올때 한번은 입력모드로

들어간다는 겁니다..따라서 입력모드에서 바로 비주얼로 들어간 경우에는 가능한 주석작업만 해야 합니다(불필요하면 제거).





터미널에서는 Shift를 포함하여 특수키끼리 조합이 잘 먹히지 않습니다. 이유는 알수 없음..

- ㅋ Backspace 와 Delete키는 원래 말 잘 안듣는다네여..


"단어 자리 바꾸기(cf..문자치환 xph) - 라인간 가능




fun WordChange()
let b:where=getpos(".")
try
s/\v([^[:blank:]]*%#[^[:blank:]]+)([[:blank:]]+|
\[[:blank:]]*\n[[:blank:]]*)([^[:blank:]]+)/\3\2\1/
catch /E486:/
endtry
call setpos(".",b:where)
setl nohls
endfun
nnoremap <silent> gw :call WordChange()<CR>


탭을 포함한 공백문자를 기준으로 현재커서위치 단어로부터 다음 단어와 서로 자리를 바꾸도록 합니다.

code 작성시 작은 도움이 될겁니다. 주의 : 위맵은 set list 하셔서 $ 앞에 공백문자가 없어야 합니다.




예를들어



try

custom commands

catch /E486:/

echo "수행 불가능!"

endtry



하시면 해당 명령어 수행시 에러발생시 에러 메세지를 통제할수 있습니다. 예를들어 본인이 사용하는 함수나 명령어등 에러메세지가 E500 이라면 catch 란에 적어주면 됩니다.. vim 버전에 따라 에러 번호가 틀리다고 합니다(따라서 적절히 수정해야).





"gvim의 기본 메뉴창등 설정을 위한 것으로 Default 값으로 guioptions=agimrLtT 이 존재한다. 하지만 우선 명령라인에서 set guioptions(=go)?로 확인 하는게 좋다. set go-=mTe 등으로 옵션 값을 지정하지만 한꺼번에 설정시에는 위 값들의 순서대로 적어 주어야 하기 때문에 보통 개별적으로 다음과 같이 하나씩 적어준다..

set guioptions+=c
"각종 메세지창 출력시에 그래픽 화면보다 console 모드를 사용한다(더 깔금)

set guioptions-=m
"시작 메뉴바 제거

set guioptions-=T
"시작 툴바 제거




set guioptions-=e
"기억 안남 하지만 제거해야 좋은걸루 기억..



이제 깔끔한 화면을 보게 된다..

현재창에서 메뉴바등을 다시 보고싶으면 명령라인에서(Ex모드) set go+=m 또는 T 등을 내리면 된다.

추가로 <F10> 을 눌러도 됩니다.







set maxmemtot=51200


"vim에서 사용가능한 한계 메모리량을 kbyte 단위로 설정..



set path=/etc/**,/boot/**,/usr/fonts/**
"명령라인에서 find vimrc 를 내리면 e /etc/vim/vimrc를 한것과 동일하다. "**" 은 아래로 30개 디렉터리를 포함한다.






기본적인 인코딩,파일 포맷 설명(본인이 이해하는 한도내에서 설명)

set encoding=utf-8
기본적인 파일 읽기,쓰기,프로그램 내부처리 인코딩을 나타낸다.
개별적으로 다른 우선값이 있는 경우에는 vim내부의 프로그램 처리 인코딩 으로써 역활을 수행한다.이 값은 vim 시작후에는 수정하지 않는게 좋습니다.
이값은 보통 시스템 로케일 환경변수(MS등의 cp949가능)를 받아 디폴트로 부여된다. GTK+2인 경우에는 utf-8(만능은 아님)이 상당히 선호 되어야 한다고 한다.




터미널 로케일과 vim에서 사용하고자 하는 encoding 이 다른 경우..


let &termencoding = &encoding -- 터미널 로케일이 euc-kr,기타등
set encoding=utf-8 --vim에서 사용하고자 하는 인코딩
따라서 encoding 옵션의 다른 개별 우선값들이 없으면 파일읽기,저장등에 이 값(utf-8)이 사용된다..
보통 터미널 로케일은 시스템 로케일과 동일하고 이값은 vim상에서 이미 encoding값과 로케일이 마추어지기 때문에 고려되지 않는다.따라서 터미널 로케일이 시스템 로케일과 다르거나 vim이 시스템 로케일을 제대로 인식하지 못하는 경우등에 사용한다.






clipboard
^
|

V
+---------------+
| |
keyboard ----->| core |-----> display
| |
+---------------+
^
|
V
file




도해출처 - 빔소스






만약에 사용하고 있는 터미널 인코딩이 사용하고자 하는 encoding과 다른 경우에는 위와같이 해주면 터미널 로케일을 가능한

범위내에서 알아서 전환해준다. 터미널은 키보드 입력이나 화면출력과 관계된 것이고 최근에는 회면출력은 GUI인
경우에는 vim의 encoding이 담당하고 Gtk+2인 경우에는 강제적으로 utf-8에 설정되어있어 다른값으로 바꿀수 없다고 한다.
GUI가 내부적으로는 utf-8을 core 처리 인코딩으로 사용한다고 한다..
cf..let는 새로운 변수를 설정하는 것이며 &는 vim옵션변수명 앞에 사용된다.

set fencs=ucs-bom,utf-8,cp949,latin1
기본 인코딩이 아닌 파일을 읽고 해당 인코딩으로 다시 저장가능하게 한다.
Bom이란 동일한 인코딩 이더라도 컴퓨터 octet시스템에 따라 인코딩된 데이터가 byte에 옮겨지는 순서가 틀릴수 있는데

(그러나 동일한 인코딩) 이때 이를 추적하여 보정하게 해준다.이후 리눅스상 고유의 인코딩방식을 읽어 들인다.
위 순서대로 적어주면 웬만한건 다 읽어 들인다.ucs-bom(유니코드 인코딩들)과 latin1은 각각 맨처음과 맨마지막에 위치해야 한다.
정확히는 ucs-bom은 Unicode 인코딩앞에 2 bytes(cp949등) 인코딩들은 1 bytes(latin1등) 인코딩들 앞에 위치해야 합니다.

cf..utf8은 big edian 만 존재하므로 ucs-bom에서 걸러지지 않습니다.- 즉 ucs2le , ucs2be 같은 식이 없습니다.

기존 파일이 읽혀질 때에는 이 옵션값에 존재하는 인코딩만 불러올수 있으며 버퍼에 올라올때 fenc값으로 종전 인코딩 값이 부여된다.
(만약 fencs 값이 없으면 encoding 값이 마지막으로 사용된다.fenc는 아니다.)
따라서 설정파일내 이미 존재하는 fenc값 내용과 달리 이때 부여된 fenc 값에 따라 기존 인코딩대로 이후 저장하는 경우 사용된다.

그러나 새로 작성하는 모든 파일이나 기존 파일이 읽혀진후 명령 모드(Ex모드)에서 fenc 나 ++enc 값을 새로이 달리주면 새인코딩값으로 저장된다. 따라서 fencs(읽기 옵션이지만 fenc 를 통해 쓰기 옵션에 영향을 미침)는 fenc(쓰기 옵션)와 다르다.

set fenc=cp949
이값을 설정파일이나 명령라인에서 지정하면 기본인코딩(encoding)값에 우선하며 기존의 읽혀진 파일이나 새로 작성한 파일을 특정 인코딩으로 저장하고자 하는 경우에 사용된다(쓰기에만 적용)-- vim은 euc-kr명칭보다 cp949를 사용하는게 안정적이다(본인주)


위의 전역설정 옵션들과 달리 개별 파일별로 다음과 같이 할수도 있다. 즉 위옵션들은 한번 설정하면 미래에 발생하는 모든

파일에 적용된다.

e[dit][!] ++ff=unix newfile1
w[rite][!] ++enc=latin1 newfile1
한꺼번에 옵션값을 다 주어도 된다.
예: w ++enc=cp949 ++ff=dos newfile2

파일저장 인코딩시에는 encoding보다 fencoding(=fenc)가 우선하면 ++enc가 가장 우선한다.물론 ++enc는 파일 읽기에도 가장 우선한다. 그리고 가장 정확하다고 하는 인코딩 변환(파일 읽기등시 발생하는)은 ++enc 이다. vim은 내부적으로 iconv()를 사용하며 이들 옵션값으로 이인코딩 이름들을 사용할수 있다 -- man iconv

(= carrage return(<CR>),"Ctrl-V + M" 타이핑)이 존재하는 dos fileformat은 set fileformats 를 통하거나 ++ff 옵션을 사용하여 불러들이면 볼수있다.. ( fileformat 은 아니다) 왜냐하면 unix 파일포맷은 <NL> 이기 때문이다.

fileformat or ++ff 분류 (end of line, <EOL>)

dos "<CR><NL>" or <NL>
unix <NL>
mac <CR>

set fileformats(=ffs) 와 set fileformat(ff)의 관계는 fencs 와 fenc의 관계
와 동일하다고 보면된다.

따라서 기본적인 환경 이라면 디음과 같이 설정한다.

set encoding=utf-8
set fencs=ucs-bom,utf-8,cp949,latin1
set ff=unix
set ffs=unix,dos,mac

fileformat unix는 dos 에서도 읽혀지고 이렇게 설정하면 <CR>이 생성되지 않으므로 좀더 효율적이라 할수 있다.
추가적으로 개별적인 파일들은 edit,write의 ++opt(++enc,++ff등)옵션을 쓰는게 좋다.
기타 유닉스에서 작성하여 MS 윈도우에서 사용할 파일이라면 utf8환경 하인경우 set bomb 값을 주어 인코딩시 big edian,little edian 을 명시하여 좀더 compatibility 하게 문서를 저장할수 있다..


기타 자세한 사항은 help ++opt등






set ignorecase
"매칭(탐색)등의 경우 대소문자를 구분하지 않는다.

set smartcase
"ignorecase 설정후 이옵션값을 주면 대문자 입력시 대문자로 탐색한다.



set ve=all
"virtualedit 용으로 커셔가 비텍스트 영역에 위치할수 있도록 합니다.



if has("autocmd")
au BufReadPost * if line("'\"") > 0 && line("'\"") <= line("$")
\| exe "normal g'\"" | endif
endif
"창간이동이나 버퍼간 이동후 복귀시 커셔가 종전 line 위치를 유지 하도록 한다
(잘 알려진 Tip)




set nocompatible
"(=nocp,vi와 다른 vim기능을 사용하는 것으로 설정파일 최상위에 위치해야 한다
"vim 기본설정은 전역설정의 경우 cp로 설정파일을 읽는데 배포판마다 다릅니다)



if !exists("syntax_on") "명령라인에서 let을 내려보라..
syntax on
filetype on
filetype plugin on
" filetype indent on
endif
"사실 syntax는 무거운 옵션이라 두번읽지 않도록 한다(본인주)




set browsedir=/
"bro e 명령어 수행시 시작 디렉터리를 지정한다.
"브라우징은 기타 E 나 e[dit] /usr 등으로도 가능하다..



set tabstop=4

"탭크기의 지정(디폴트는 공백라인 8개 입니다)



set expandtab
"탭을 누른경우 탭대신 같은폭의 공백문자 여러개를 입력하도록 한다.



set shortmess+=r
"[readonly] 메세지를 [RO]로 출력하도록 하여 화면공간을 절약한다.

set nomodeline
"현재 설정파일의 내용을 덮어쓰지(변경) 않도록 한다.

set splitbelow
"새로운 창은 현재창 아래에 열리도록 한다.



set mouse=ar
"마우스 사용을 대부분 모드에서 사용가능하게 한다.




set ttyfast
"터미널 반응을 빠르게 한다.

behave xterm
"잘알려진 xterm 터미널 방식을 사용하도록 한다.

"Gvim의 경우에는 전용 GUI 터미널(?)을 사용하므로 이는 일반 터미널에만

"적용될 겁니다..





아래는 예로 설명한것입니다.. 세션이란 현재창 상태 그대로를 저장하는 겁니다..

그리고 나중에 동일한 상태로 불러 냅니다..



set ssop=buffers,curdir,folds,help,tabpages,winsize,resize
"세션설정 옵션으로 제가 사용하는 방식입니다..비설정시 Default 값도 무방..

":mks(=mksession) 세션 파일이름(any) -- 명령라인에서 세션 저장하는 명령어..
"shell 에서 불러오기 gvim -S 세션 파일이름

"vim 에서 불러오기 :so[urce] 세션 파일이름


set noswf(반대는 Default인 swapfile(=swf))
"스왑파일을 사용하지 않도록한다.
"중요문서 작성시에는 write 하지 않은 버퍼내용을 시스템 다운시 복구하려면 set swf

"옵션을 미리주어 복구할수 있도록 합니다 (가급적 요구, 다소 자주 다운되는편..).

"이를 위한 본인 맵..



nnoremap <silent> <C-S> :setlocal swf<CR>:echo "Swap-file on"<CR>




set dir=/var/swp
"스왑파일이 저장되는 위치지정(/tmp 디렉터리는 Rebooting시 완전히 사라지므로
" 기피한다.)

set buftype=""
"Default 값(공백)이지만 중요한 옵션이므로 다시 한번 설정을 확인합니다.


set hidden

"동일창에서 다른파일 블러낼때 종전 변경된 버퍼내용(파일에 쓰기전)이 계속 유지되도록 합니다.






아래설정 내용은 버퍼내용을 해당 파일에쓰기전에 기존 파일을 일시 백업해둔후

버퍼내용을 파일에 쓰기한후 이상이 없으면 이제 백업 파일을 삭제합니다..




set writebackup
set backupdir=/var/bak
set backupcopy=yes "attrs when copying
set bsk=/tmp/*




cf.set backup 으로 설정하면 진짜 백업이 됩니다..
디폴트는 writebackup 입니다.



set hls
"탐색(search)시 해당 매칭 단어를 컬러펄하게 출력.


nnoremap <silent> B :nohls<CR>
"위 탐색후에 표시된 컬러를 해제(B는 중요성이 떨어지는 명령어로 이를 대체
"사용)




set paste
"복사시 계단현상 방지..
GUI 의 경우에는 이미 기존의 paste 기능이(즉 타이핑한 텍스트와 복사하는 텍스트를

구별해주는 기능) 추가되어 있으므로 givm 시작시 이값이 nopaste로 할당 됩니다.

이렇게 함으로써 paste옵션이 설정되었을 때의 선호되는 다른 옵션값들이 변경되는

단점을 줄입니다..

개별적으로 명령모드에서 set paste를 줄수도 있습니다..




set paste가 적용되면 vim(not gvim)의 경우 textwidth 옵션이 적용되지 않습니다.

더불어 Gvim의 경우 xterm으로 시작하다가 gui로 다시 시작하는데 이때 옵션값들이 디폴트로 재 설정되고 이때 set textwidth 값도 디폴트값 0으로 부여 되므로 필요한 경우 다음과 같이 하거나 아니면 위 한글관련 has("gui_running") 조건문 내의 set nopaste 파트처럼 설정합니다.



autocmd! GUIEnter * set textwidth=77



관련명령어로 우선 버려도 되는 문서(이점유의)에다 set textwidth=5 하신후 보통모드에서 gq 명령어를 내려보세여. 그러면 현재 텍스트폭에 맞게 글들이 정렬됩니다(영어단어는 공백라인기준으로 잘라 집니다). 보통 nnoremap Q gq 로 맵을 걸어 사용합니다.

"ggvGl" 하신후 gq 를 내리시면 전체파일내용에 대해 적용됩니다.

gg =현재파일 맨위로 이동, v= 비주얼모드 진입, G= 현재파일 맨아래로 이동, l=우측화살키

gq 명령어 수행...

textwidth 를 쓰지 않는경우 대신 wrapmargin(우측 화면 가장자리로부터 계산되는 수치)을 사용합니다.



set wrap - 긴문장의 경우 현재 화면칼럼에 맞게 출력.

"set nowrap - 긴문장의 경우 현재 화면칼럼을 넘어 존재

wrap 은 textwidth 와 달리 linebreak를 자동 삽입하지 않고 화면 출력자체에만 적용됩니다.

cf..set linebreak(newline은 당연 no 삽입) 는 wrap 옵션 사용시에 적용되며 화면에 출력되는 형식 라인을 구분해주는 문자를

특별히 지정해 줍니다.

즉 공백을 기준으로 라인을 구분하느냐 아니면 !, $, ^, &, * , tab 등 원하는 조건을 넣을수 있습니다.

다시말해 형식 라인 마지막 단어가 finiish 라면 다음 라인으로 이어져 단어가 잘리게 되는 경우 (Default) linebreak 설정시

이 단어 앞의 공백문자를 기준으로 finish 는 다음 형식라인으로 들여 써집니다(영어와 한글때문에 매칭이 잘안되는 옵션).



set scrolloff=2
"커셔위치 지정으로 scrolling 시 2로설정하면 상하단 2번째라인에 위치한다.




다음 내용은 약간 중요도 떨어지는 사항입니다(하지만 필요합니다)..



redir @*> -출력 리다이렉션 지정
redir END -출력리다이렉션 해제




:redir @*> | version| redir END 하시면 시작시 메세지 출력방향을 복사시 사용되는 클립보드 레지스트리 *로 지정하여

version명령으로 출력되는 내용은 이곳으로 보내지며 다음으로 출력방향을 해제한후 이후 보통모드나 입력모드에서 마우스

중간키나 Shift-Insert 키를 누르시면 해당 내용이 copy-paste 한것과 동일하게 나타 납니다..

맵으로 설정해둬도 좋습니다..(보통 많이 사용하는 ,으로 시작하는 맵이 좋겠지여)..



현재 나의 vim설정파일에 적용된 설정을 알아보려면 set option-name? 하시면 됩니다.



기타 vim 관련 명령어 해설.....



f , a -- 현재라인에서 a 문자로 바로 이동 이후 ;를 누르면 2번째 a 로 이동하고

, 를 누르면 이전 a 로 한단계 이동 합니다.

F 는 위와 반대 입니다.. 꺼구로 이동합니다.. t 명령어는 w 와 b 명령와 동일한 관계입니다.



보통모드 명령어


창다루기...


Ctrl_W Ctrl_W 창간 z커셔 이동

Crrl_W Ctrl_N 새창 만들기

Ctrl_W c 현재창 닫기

Ctrl_W Ctrl_R 창간 내용치환



후자의 Ctrl 은 생략하고 바로 해당 키 하나만 입력해도 됩니다. 명령모드보다 보통모드 명령어가 직관적이고 더 빠릅니다..



"a2yy -- 현재 라인2줄을 레지스트리 a 로 복사합니다..

"ap -- 위 내용을 현재 커셔위치 아래에 붙여 넣습니다.. 레지스트리는 a-z 까지 사용가능 합니다..

명령모드나 입력모드에서 레지스트리를 사용하고자 할때에는 " 발현은 Ctrl-R 로 하셔야 합니다..

"a 와 A 는 동일 레지스트리를 나타내지만 a를 사용하면 기존 내용을 덮어쓰고 A 의 경우에는 내용이 덧붙여집니다.


명령모드 명령어



:tabnew 새 탭열기

:tab[edit] 새 탭으로 파일열기

:tabc[lose] 현재 탭 닫기..

:tabn[ext] 다음 탭으로 이동

:tabp 이전 탭으로 이동



현재버퍼명보기 files,ls,args 등

현재파일명 보기 f[ile]

버퍼불러오기 buffer 3 등

버퍼지우기 bd[elete] 1 2 3 등

현재창닫기 c[lose][!]

q[uit][!] 등도 당연 가능합니다..

본인이 초보시절에는 잘 몰랐습니다...



기타 jumps 명령어



파일을 드래그 앤 드랍으로 빔창에 넣어도 파일이 열립니다.





기타내용은 시간 남으면 조금씩 올립니다.



다음은 너무 잘 알려진거라 안올리려다 올립니다.



" function to cleanup a text -> mapped to F5
fun CleanText()
let curline = line(".")
let curcol = col(".")
exe ":retab"
exe ":%s/\v^M$//ge"
exe ":%s/\v^M/ /ge"
exe ":%s/\v \\+$//e"
call cursor(curline, curcol)
endfun
nnoremap <F5> :call CleanText()<CR>

retab 은 현재 텍스트내 탭간격들을 나의환결설정 tabstop 값으로 재 작성하는 경우이고 첫번째 ^M(반드시 Ctrl+V + M 타이핑을

해야) 는 윈도우 것이고 후자는 맥에서 작성한 파일에 주로 발생하는 경우 같고(대체시 첫번째(null 값 이미존재)와 달리 null값이

추가로 삽입 된다고 하네여-newline) 마지막은 모르겠네여..

리눅스측에서는 보통 이를 carriage return 을 잘못 표시한 경우라고 하는데 어느게 맞는지 모르겠네여..

%s#A#B# 와 %s/A/B/ 은 동일한 겁니다. 전자는 보통문자 backslash 검색시 backslash 하나만 넣어주고

후자인 경우에는 2개를 넣어 줍니다(그 차이뿐.즉 전자는 외관상 backslash 처리 때문에 존재하는걸루 추정)

이건 웹(외국)에서 따온건데 원저자가 누군지 모르겠네여..






copyright@2007 All Rights Reserved (무단전제 및 복사금지)










 





You could use [editor on GitHub](https://github.com/ryw1/pjj1/edit/gh-pages/index.md) to maintain and preview the content for your website in Markdown files.

Whenever you commit to this repository, GitHub Pages will run [Jekyll](https://jekyllrb.com/) to rebuild the pages in your site, from the content in your Markdown files.

### Markdown

Markdown is a lightweight and easy-to-use syntax for styling your writing. It includes conventions for

```markdown
Syntax highlighted code block

# Header 1
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```

For more details see [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/ryw1/pjj1/settings/pages). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://docs.github.com/categories/github-pages-basics/) or [contact support](https://support.github.com/contact) and we’ll help you sort it out.
