# B+ Tree Analysis & Improvement

# B+트리 코드 분석

![캡처.PNG](B+%20Tree%20Analysis%20&%20Improvement%200740ad98aff74195bbdd956fa4d776b7/%25EC%25BA%25A1%25EC%25B2%2598.png)

선언된 구조체의 구조이다.

각 page는 4096B이고 leaf record = 8B+120B = 128B이므로 31개의 record,

internal record = 8B+8B = 16B이므로 248개의 record를 한 page당 저장할 수 있다.

page는 첫 128B에는 해당 page의 정보들이 저장되어 있고 나머지는 record의 union으로 되어있어 internal record 248개 또는 leaf record 31개를 저장할 수 있다.

header page는 free page offset, root page offset, page수를 담고있다.

free page는 버퍼같은 것으로, 파일 크기를 늘리지 않고도 새로운 node page를 만들 수 있게 해준다.

![캡처.PNG](B+%20Tree%20Analysis%20&%20Improvement%200740ad98aff74195bbdd956fa4d776b7/%25EC%25BA%25A1%25EC%25B2%2598%201.png)

header page와 root node page는 자주 쓰이므로 전역변수로 저장하고 있다.

load_header 함수는 헤더정보를 불러와 전역변수 hp에 저장하거나 헤더를 읽기/수정하는데 쓰인다.

load_page 함수는 root를 포함한 node page 정보를 불러온다.

![캡처.PNG](B+%20Tree%20Analysis%20&%20Improvement%200740ad98aff74195bbdd956fa4d776b7/%25EC%25BA%25A1%25EC%25B2%2598%202.png)

open_table() 은 데이터를 처음 불러올때 쓰인다.

파일이 없었을경우 기본적인 header 정보를 기입한 파일을 만들고 닫는다.

파일을 열어서 header page 정보와 root page 정보를 전역으로 저장한다.

reset()은 해당 page정보를 모두 0으로 초기화한다.

![캡처.PNG](B+%20Tree%20Analysis%20&%20Improvement%200740ad98aff74195bbdd956fa4d776b7/%25EC%25BA%25A1%25EC%25B2%2598%203.png)

freetouse()는 데이터를 쓰기 위해 free page를 used page로 전환해준다.

usetofree()는 delete되어 값이 없어진 node를 free page로 전환해준다.

![캡처.PNG](B+%20Tree%20Analysis%20&%20Improvement%200740ad98aff74195bbdd956fa4d776b7/%25EC%25BA%25A1%25EC%25B2%2598%204.png)

new_page()는 남는 free page가 있을 경우 freetouse()를 이용해 used page로 가져와서 사용준비시키고, 남는 free page가 없을 경우 파일 뒤쪽에 새로운 used page를 만들어서 사용한다.

![캡처.PNG](B+%20Tree%20Analysis%20&%20Improvement%200740ad98aff74195bbdd956fa4d776b7/%25EC%25BA%25A1%25EC%25B2%2598%205.png)

find_leaf()는 argument로 받은 key값을 포함하는 range를 갖는 leaf node page를 return한다.

page가 leaf node page에 도달할 때까지 B+트리의 search 알고리즘을 통해 key값과 비교하여 leaf로 들어간다.

tree 자료구조가 비어있으면 null을 리턴한다.

![캡처.PNG](B+%20Tree%20Analysis%20&%20Improvement%200740ad98aff74195bbdd956fa4d776b7/%25EC%25BA%25A1%25EC%25B2%2598%206.png)

db_find는 key값을 찾아 있으면 value를 리턴, 없으면 null을 리턴한다.

우선 리턴을 위해 value를 저장할 배열을 만들어 주고,

방금 전 find_leaf()를 통해 도달한 leaf 노드에서 key값을 for문으로 찾는다.

key값이 나오지 않으면 for문이 끝까지 돌 것이므로 null 반환, 그외의 경우 해당 record를 탐색한다.

![캡처.PNG](B+%20Tree%20Analysis%20&%20Improvement%200740ad98aff74195bbdd956fa4d776b7/%25EC%25BA%25A1%25EC%25B2%2598%207.png)

cut()은 ceiling(n/2)를 리턴하는 split 진행 또는 node의 최소 key 개수를 판단할때 쓰는 함수이다.

start_new_file()은 empty tree에 데이터를 넣으려고 할 때 root node를 만들어서 해당 record를 집어넣는다. root가 변경되었으므로 header page의 정보도 바꿔준다.

![캡처.PNG](B+%20Tree%20Analysis%20&%20Improvement%200740ad98aff74195bbdd956fa4d776b7/%25EC%25BA%25A1%25EC%25B2%2598%208.png)

db_insert()는 key + value record를 node에 집어넣는다.

우선 find()를 통해 중복된 key()값을 찾으면 에러를 보내고,

그렇지 않으면 insert할 node를 찾아 내려간다.

![캡처.PNG](B+%20Tree%20Analysis%20&%20Improvement%200740ad98aff74195bbdd956fa4d776b7/%25EC%25BA%25A1%25EC%25B2%2598%209.png)

insert할 공간이 leaf node에 있다면 이 함수가 실행된다.

sorting되어 있는 key값을 처음부터 비교해 나가고, 자기 key보다 큰 key를 갖는 위치에 멈춰서 record를 끼워넣는다. 자기 key보다 큰 key가 없으면 제일 오른쪽에 추가한다.

![캡처.PNG](B+%20Tree%20Analysis%20&%20Improvement%200740ad98aff74195bbdd956fa4d776b7/%25EC%25BA%25A1%25EC%25B2%2598%2010.png)

![캡처.PNG](B+%20Tree%20Analysis%20&%20Improvement%200740ad98aff74195bbdd956fa4d776b7/%25EC%25BA%25A1%25EC%25B2%2598%2011.png)

insert할 공간이 없다면 이 함수가 실행된다.

split의 경우 node의 수가 1개 늘어나므로 new_page()를 실행한다.

node의 공간이 부족하므로 max_size + 1 인 record를 temp 배열을 만들어 저장한다.

그 후 0 to ceiling(n/2)-1, ceiling(n/2) to end인 배열로 쪼개어 각각 기존의 insert했던 node는 왼쪽에, 새로운 node는 오른쪽에 붙여넣는다. 이 node는 leaf node이므로 old node - new node로 연결되도록 해준다.

마지막으로 두 node를 구분할 키(new lode의 첫번째 키)를 부모노드로 올려 insert해준다.

![캡처.PNG](B+%20Tree%20Analysis%20&%20Improvement%200740ad98aff74195bbdd956fa4d776b7/%25EC%25BA%25A1%25EC%25B2%2598%2012.png)

이 함수에서 부모노드에 key값을 추가해준다.

bumo는 key를 추가해줄 부모노드, left_index를 key를 추가해줄 위치, newp는 이전에 만들었던 새로운 노드이다.

![캡처.PNG](B+%20Tree%20Analysis%20&%20Improvement%200740ad98aff74195bbdd956fa4d776b7/%25EC%25BA%25A1%25EC%25B2%2598%2013.png)

이전의 함수에서 나오는 함수로, left_index값을 for문으로 찾아준다.

![캡처.PNG](B+%20Tree%20Analysis%20&%20Improvement%200740ad98aff74195bbdd956fa4d776b7/%25EC%25BA%25A1%25EC%25B2%2598%2014.png)

insert_into_parent()에서 split된 노드를 구분할 key를 추가해줄 부모노드가 존재하지 않을때(root 노드가 split 되었었을때) 실행되는 함수이다.

root 노드를 추가하여 해당 root 노드에 유일한 key값을 추가해준다. 그리고 split된 노드의 부모정보를 갱신해준다. 또한 root노드가 변경되었으니 header정보와 전역변수를 변경해준다.

![캡처.PNG](B+%20Tree%20Analysis%20&%20Improvement%200740ad98aff74195bbdd956fa4d776b7/%25EC%25BA%25A1%25EC%25B2%2598%2015.png)

key를 insert해줄 부모노드에 공간이 남을때 실행된다.

for문을 이용해 index 사이에 key값을 끼워주고 split된 두 node로 가는 포인터를 연결해준다.

![캡처.PNG](B+%20Tree%20Analysis%20&%20Improvement%200740ad98aff74195bbdd956fa4d776b7/%25EC%25BA%25A1%25EC%25B2%2598%2016.png)

![캡처.PNG](B+%20Tree%20Analysis%20&%20Improvement%200740ad98aff74195bbdd956fa4d776b7/%25EC%25BA%25A1%25EC%25B2%2598%2017.png)

key를 insert해줄 부모노드에 공간이 없을때 실행된다.

기본적인 흐름은 자식노드를 split해줄때와 같으며 split된 new node의 자식으로 내려가는 포인터를 모두 재설정해주어야 한다는 차이점이 있다. 이 때 new node의 key값의 개수 +1만큼 File RW가 일어나게 된다.

![캡처.PNG](B+%20Tree%20Analysis%20&%20Improvement%200740ad98aff74195bbdd956fa4d776b7/%25EC%25BA%25A1%25EC%25B2%2598%2018.png)

key를 찾아 해당 record를 delete하는 함수이다.

tree가 비어 있을 경우 -1을 반환한다.

db_find를 사용하여 해당 key값이 있는지 확인하고 없으면 -1을 반환한다.

key값이 있다면 해당 leaf node로 들어가 delete작업을 시작한다.

![캡처.PNG](B+%20Tree%20Analysis%20&%20Improvement%200740ad98aff74195bbdd956fa4d776b7/%25EC%25BA%25A1%25EC%25B2%2598%2019.png)

![캡처.PNG](B+%20Tree%20Analysis%20&%20Improvement%200740ad98aff74195bbdd956fa4d776b7/%25EC%25BA%25A1%25EC%25B2%2598%2020.png)

본격적인 delete 작업이 시작되는 함수이다.

remove_entry_from_page()를 호출하여 해당 key값을 가진 record를 지운다.

이 때 지운 record를 root에서 지웠다면, root에 key값이 없을 수도 있으므로 adjust_root()를 통해 확인해준다.

지운 후에 internal node의 경우, leaf node의 경우 underflow를 체크한다. underflow되지 않았을 경우 삭제를 마친다.

underflow되었을 경우 redistribute 또는 merge를 진행해야 한다. 우선 delete node의 부모노드, 이웃노드(왼쪽노드, delete node가 해당 subtree의 가장 왼쪽에 있으면 오른쪽 노드)를 이웃으로 정한다. 또한 자신과 이웃을 가르는 부모의 key를 prime으로 구해 놓는다.

이웃의 key 개수 + 자신의 key 개수가 max를 넘으면 redistribute, 아니면 merge를 실행한다.

![캡처.PNG](B+%20Tree%20Analysis%20&%20Improvement%200740ad98aff74195bbdd956fa4d776b7/%25EC%25BA%25A1%25EC%25B2%2598%2021.png)

![캡처.PNG](B+%20Tree%20Analysis%20&%20Improvement%200740ad98aff74195bbdd956fa4d776b7/%25EC%25BA%25A1%25EC%25B2%2598%2022.png)

이웃노드와 redistribute하는 함수이다.

이웃노드가 오른쪽일 경우 (without loss of generality) node가 leaf일 경우 이웃노드의 0번 record를 가져온다. 그 후 이웃노드의 record를 하나씩 앞당긴다. 그리고 부모노드의 이웃과 구분하는 key를 이웃노드의 새로운 0번 key값으로 바꾼다. node가 internal일 경우 해당되는 child로의 포인터도 수정해준다. 그 후 key 개수 필드를 수정해준다.

![캡처.PNG](B+%20Tree%20Analysis%20&%20Improvement%200740ad98aff74195bbdd956fa4d776b7/%25EC%25BA%25A1%25EC%25B2%2598%2023.png)

![캡처.PNG](B+%20Tree%20Analysis%20&%20Improvement%200740ad98aff74195bbdd956fa4d776b7/%25EC%25BA%25A1%25EC%25B2%2598%2024.png)

이웃노드와 merge하는 함수이다.

자신의 정보를 이웃노드로 떠넘길 node인 wbc, 정보를 받을 nbor, 부모노드를 정의한다.

leaf의 경우 wbc의 값을 모두 nbor로 옮기고, internal의 경우 child와의 관계도 수정해준다. 이 때 사라지는 node의 기존 key개수 + 1개만큼 File RW가 생긴다.

![캡처.PNG](B+%20Tree%20Analysis%20&%20Improvement%200740ad98aff74195bbdd956fa4d776b7/%25EC%25BA%25A1%25EC%25B2%2598%2025.png)

root 노드에서 delete가 일어났을 때, root 노드의 key개수가 0이 됐는지 확인한다. 맞으면 해당 root 노드를 지우고 root의 left child를 새로운 root로 설정하고 header 정보를 수정한다. child가 없으면 tree를 비운다.

![캡처.PNG](B+%20Tree%20Analysis%20&%20Improvement%200740ad98aff74195bbdd956fa4d776b7/%25EC%25BA%25A1%25EC%25B2%2598%2026.png)

node에서 key값을 찾아서 지우는 함수이다. root node의 값을 지웠을경우 전역변수를 수정해준다.

# B+트리 개선 방안

아래는 다음 B+트리를 개선할 방안이다.

1. 이 함수들중 insert와 delete가 처음 실행될 때 명령의 유효성 체크(중복체크, 존재여부체크)를 위해 find를 한번, 실제 작업 한번 총 2번의 탐색이 이루어진다. 이를 하나로 묶으면 h*(File RW time)만큼의 시간을 줄일 수 있다.

![캡처.PNG](B+%20Tree%20Analysis%20&%20Improvement%200740ad98aff74195bbdd956fa4d776b7/%25EC%25BA%25A1%25EC%25B2%2598%2027.png)

기존의 insert_db의 첫부분의 중복체크(delete의 존재여부 체크도 동일)를 아래 코드로 대체함으로써 db_find를 제거할 수 있다. 이것으로 insert시 height만큼의 탐색횟수를 2번에서 1번으로 줄인다.

![캡처.PNG](B+%20Tree%20Analysis%20&%20Improvement%200740ad98aff74195bbdd956fa4d776b7/%25EC%25BA%25A1%25EC%25B2%2598%2028.png)

1. insert를 할 때 노드에 값을 계속 넣어주고 값이 너무 커지면 split을 한다. split을 하면 최소 key개수를 갖는 노드 2개로 분할된다. 따라서 한 노드쪽에만 계속 값이 들어가면 최소 근처의 key개수를 가지는 노드의 수가 과도하게 많아질 수 있다. 또한 split 한번 진행시 생성되는 new node의 키 개수+1만큼의 파일 입출력이 발생한다. 이 두 문제를 해결하기 위해 insert에도 이웃 노드와 redistribute할 수 있는 기회를 추가한다.

![캡처.PNG](B+%20Tree%20Analysis%20&%20Improvement%200740ad98aff74195bbdd956fa4d776b7/%25EC%25BA%25A1%25EC%25B2%2598%2029.png)

![캡처.PNG](B+%20Tree%20Analysis%20&%20Improvement%200740ad98aff74195bbdd956fa4d776b7/%25EC%25BA%25A1%25EC%25B2%2598%2030.png)

db_insert() 안에 추가한 insert redistribution이 가능한지 체크하는 부분이다. insert_into_parent()에도 변수명만 parent 에서 grandp로 바꾼 동일한 기능을 하는 코드를 추가하였다.

우선 insert한 node가 root일 경우 이웃이 없으므로 split한다.

그 다음 왼쪽 이웃과 오른쪽 이웃을 모두 탐색하는데, 해당 노드가 가장 왼쪽일 경우 왼쪽이웃을 NULL로, 가장 오른쪽일 경우 오른쪽이웃을 NULL로 설정한다.

왼쪽 노드 또는 오른쪽 노드에 공간이 있을 경우 해당 노드와 redistribution을 수행한다.

![캡처.PNG](B+%20Tree%20Analysis%20&%20Improvement%200740ad98aff74195bbdd956fa4d776b7/%25EC%25BA%25A1%25EC%25B2%2598%2031.png)

![캡처.PNG](B+%20Tree%20Analysis%20&%20Improvement%200740ad98aff74195bbdd956fa4d776b7/%25EC%25BA%25A1%25EC%25B2%2598%2032.png)

leaf 노드의 redistribution을 수행하는 함수이다.

재분배할 노드가 왼쪽 이웃일 경우, 자신의 0번째 record를 왼쪽으로 옮기고 새 record자리까지 나머지 record도 한칸씩 당긴다. 그 후 부모의 자신으로 향하는 key값을 새로운 0번으로 바꿔준다.

재분배할 노드가 오른쪽 이웃일 경우, 오른쪽 record를 밀어 0번 record 자리에 공간을 만들고, 새로운 record와 자신의 오른쪽끝 record중 key값이 더 큰 것을 오른쪽 0번으로 옮긴다. 오른쪽끝 record를 옮겼을 경우 새로운 record를 적절히 끼워넣는다. 그 후 부모의 오른쪽 이웃으로 향하는 key값을 새로운 0번으로 바꿔준다.

![캡처.PNG](B+%20Tree%20Analysis%20&%20Improvement%200740ad98aff74195bbdd956fa4d776b7/%25EC%25BA%25A1%25EC%25B2%2598%2033.png)

![캡처.PNG](B+%20Tree%20Analysis%20&%20Improvement%200740ad98aff74195bbdd956fa4d776b7/%25EC%25BA%25A1%25EC%25B2%2598%2034.png)

internal 노드의 redistribution을 수행하는 코드이다.

왼쪽 이웃과 redistribute할 경우, 자신의 next_offset을 왼쪽으로 민다. 그 후 자식의 부모값을 이웃으로 조정한다. 자신을 가리키는 부모 record의 key값을 왼쪽 이웃 마지막값으로 옮긴다. 그러면 왼쪽 이웃에 새로운 record가 만들어진다. 또 자신의 0번째 key를 부모의 자신으로 가는 key로, 0번째 pointer를 next_offset으로 옮기면 0번째 record가 비게 되는데, 나머지 record를 하나씩 당겨서 new record가 들어갈 자리를 만든다.

오른쪽 이웃과 redistribute할 경우, 우선 오른쪽 record를 한칸씩 오른쪽으로 민다. next_offset과 0번째 key값이 비게 되는데, key값은 부모의 해당 이웃으로 가던 key값에서 가져온다. 

만약 split된 자신의 자식노드가 끝에 있었다면(key > bumo의 마지막 레코드의 key값) 새 노드의 pointer는 이웃의 next_offset으로, 부모의 이웃으로 가는 key는 split 가르는 key값으로 한다. 그렇지 않을 경우 제일 끝 record의 pointer를 이웃의 next_offset으로, 부모의 이웃으로 가는 key는 자신의 제일 끝 key값으로 한다.

redistribution의 예시는 입출력의 편의를 위해 internal_max = leaf_max = 3으로 설정하였다. p를 입력하면 root부터 dfs로 노드들을 탐색한다.

![캡처.PNG](B+%20Tree%20Analysis%20&%20Improvement%200740ad98aff74195bbdd956fa4d776b7/%25EC%25BA%25A1%25EC%25B2%2598%2035.png)

![캡처.PNG](B+%20Tree%20Analysis%20&%20Improvement%200740ad98aff74195bbdd956fa4d776b7/%25EC%25BA%25A1%25EC%25B2%2598%2036.png)

현재 1~5까지의 노드를 넣은 상태이다.

![캡처.PNG](B+%20Tree%20Analysis%20&%20Improvement%200740ad98aff74195bbdd956fa4d776b7/%25EC%25BA%25A1%25EC%25B2%2598%2037.png)

![캡처.PNG](B+%20Tree%20Analysis%20&%20Improvement%200740ad98aff74195bbdd956fa4d776b7/%25EC%25BA%25A1%25EC%25B2%2598%2038.png)

6을 입력하면 오른쪽 노드에 insert되어 split이 일어나야 하지만 왼쪽 이웃에 공간이 있으므로 leaf redistribution이 일어난다.

![캡처.PNG](B+%20Tree%20Analysis%20&%20Improvement%200740ad98aff74195bbdd956fa4d776b7/%25EC%25BA%25A1%25EC%25B2%2598%2039.png)

![캡처.PNG](B+%20Tree%20Analysis%20&%20Improvement%200740ad98aff74195bbdd956fa4d776b7/%25EC%25BA%25A1%25EC%25B2%2598%2040.png)

21까지 node를 입력한 모습이다.

![캡처.PNG](B+%20Tree%20Analysis%20&%20Improvement%200740ad98aff74195bbdd956fa4d776b7/%25EC%25BA%25A1%25EC%25B2%2598%2041.png)

![캡처.PNG](B+%20Tree%20Analysis%20&%20Improvement%200740ad98aff74195bbdd956fa4d776b7/%25EC%25BA%25A1%25EC%25B2%2598%2042.png)

22를 입력하면 오른쪽 13 16 19 노드에 새로운 key 21이 추가되어야 하므로 redistribution이 일어나는 것을 볼 수 있다.

![캡처.PNG](B+%20Tree%20Analysis%20&%20Improvement%200740ad98aff74195bbdd956fa4d776b7/%25EC%25BA%25A1%25EC%25B2%2598%2043.png)

![캡처.PNG](B+%20Tree%20Analysis%20&%20Improvement%200740ad98aff74195bbdd956fa4d776b7/%25EC%25BA%25A1%25EC%25B2%2598%2044.png)

![캡처.PNG](B+%20Tree%20Analysis%20&%20Improvement%200740ad98aff74195bbdd956fa4d776b7/%25EC%25BA%25A1%25EC%25B2%2598%2045.png)

![캡처.PNG](B+%20Tree%20Analysis%20&%20Improvement%200740ad98aff74195bbdd956fa4d776b7/%25EC%25BA%25A1%25EC%25B2%2598%2046.png)

오른쪽의 leaf node redistribution이다. next_offset의 노드에서 분배되므로 prime_key가 -1로 나타난다.

![캡처.PNG](B+%20Tree%20Analysis%20&%20Improvement%200740ad98aff74195bbdd956fa4d776b7/%25EC%25BA%25A1%25EC%25B2%2598%2047.png)

![캡처.PNG](B+%20Tree%20Analysis%20&%20Improvement%200740ad98aff74195bbdd956fa4d776b7/%25EC%25BA%25A1%25EC%25B2%2598%2048.png)

![캡처.PNG](B+%20Tree%20Analysis%20&%20Improvement%200740ad98aff74195bbdd956fa4d776b7/%25EC%25BA%25A1%25EC%25B2%2598%2049.png)

![캡처.PNG](B+%20Tree%20Analysis%20&%20Improvement%200740ad98aff74195bbdd956fa4d776b7/%25EC%25BA%25A1%25EC%25B2%2598%2050.png)

오른쪽의 internal node redistribution이다.

1. node에 key가 underflow되었을때 바로 merge하지 않고 그 후 해당 node에 key가 insert될 상황을 대비하여 height에 영향을 주지 않는 범위 내에서 merge를 delay한다. 그러면 split과 merge의 수를 줄일 수 있다.

delay_merge를 구현하기 위한 여러 방법을 생각해 보았다. (구현은 실패..)

1. underflow를 판정하는 조건인 최소키값을 낮춘다. 이렇게하면 merge가 일어나는 횟수를 줄일수는 있지만 height가 높아지게 된다. 극단적으로 노드마다 key가 1개씩 있을 경우 binary tree와 같아진다.
2. merge를 해주어야 하는 이유는 height를 감소시켜 find 등의 연산속도를 올리기 위해서이다. 따라서 merge를 통해 height를 줄일 수 있는 상황의 경우 merge를 해 주는것이 좋다. height가 확실히 줄 것으로 예상되는 key의 값은 현재 height의 (ceil(n/2) - 1)승이 key의 개수보다 클때이다. 따라서 이 때까지 merge를 delay하였다가 한번에 merge하는 방법이 있다. 하지만 merge를 시작할 때 underflow상태의 노드를 확인하기 위해 모든 노드를 탐색해야 하므로 비효율적이고 저 조건이 height가 낮아지는 필요조건은 아니므로 height가 낮아질 수 있음에도 merge를 안하는 상황이 발생할 수 있다.

1. 각 노드의 key값이 부족하면 underflow flag를 1로 만든다. underflow flag가 1인 node는 부모 노드에서 존재하지 않는 노드로 취급한다. root의 underflow flag가 1이 될 경우 flag가 1인 노드만 dfs로 들어가 merge를 모두 수행한다. 이럴 경우 merge 수행 이후에도 height가 변하지 않을 가능성이 있지만 height가 줄어들 수 있는 상황에는 반드시 merge가 일어나게 된다.

![캡처.PNG](B+%20Tree%20Analysis%20&%20Improvement%200740ad98aff74195bbdd956fa4d776b7/%25EC%25BA%25A1%25EC%25B2%2598%2051.png)

![캡처.PNG](B+%20Tree%20Analysis%20&%20Improvement%200740ad98aff74195bbdd956fa4d776b7/%25EC%25BA%25A1%25EC%25B2%2598%2052.png)

![캡처.PNG](B+%20Tree%20Analysis%20&%20Improvement%200740ad98aff74195bbdd956fa4d776b7/%25EC%25BA%25A1%25EC%25B2%2598%2053.png)

![캡처.PNG](B+%20Tree%20Analysis%20&%20Improvement%200740ad98aff74195bbdd956fa4d776b7/%25EC%25BA%25A1%25EC%25B2%2598%2054.png)

![캡처.PNG](B+%20Tree%20Analysis%20&%20Improvement%200740ad98aff74195bbdd956fa4d776b7/%25EC%25BA%25A1%25EC%25B2%2598%2055.png)

![캡처.PNG](B+%20Tree%20Analysis%20&%20Improvement%200740ad98aff74195bbdd956fa4d776b7/%25EC%25BA%25A1%25EC%25B2%2598%2056.png)

![캡처.PNG](B+%20Tree%20Analysis%20&%20Improvement%200740ad98aff74195bbdd956fa4d776b7/%25EC%25BA%25A1%25EC%25B2%2598%2057.png)

![캡처.PNG](B+%20Tree%20Analysis%20&%20Improvement%200740ad98aff74195bbdd956fa4d776b7/%25EC%25BA%25A1%25EC%25B2%2598%2058.png)