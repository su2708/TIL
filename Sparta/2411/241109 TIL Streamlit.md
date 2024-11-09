#TIL #스파르타코딩클럽 [[2411]]

## 1. Streamlit
### Streamlit
- 데이터 분석과 시각화를 함께 간편하게 수행할 수 있는 파이썬 오픈 소스 라이브러리
- python 코드 만으로 웹 애플리케이션을 개발할 수 있도록 함


### Streamlit의 장점
- 데이터 시각화, 프로토타이핑, 결과 공유 및 웹 기반 앱 개발에 최적화되어 있어 데이터 분석과 시각화를 더욱 효율적으로 수행 가능
- 학습한 모델의 결과물을 비교하거나, 단순한 데이터 분석 및 시각화가 필요할 때에도 Streamlit을 이용한 웹을 띄워서 서버 주소(+포트 번호)만 공유하면 결과물 공유 가능


### Streamlit의 위젯
- Button
	- 사용자가 클릭하여 어떤 동작을 수행하도록 하는 위젯
	- 이벤트 핸들러를 이용하여 버튼을 클릭할 때 특정 기능을 실행
```python
import streamlit as st
import pandas as pd
import numpy as np

def main():
    st.title('App4 - 아이리스 꽃의 종류')
    df = pd.read_csv('./data/iris.csv')

    # 버튼 만드는 방법
    if st.button('데이터프레임 보기'):
        st.dataframe(df)

    name = 'Mike'
    if st.button('대문자'):
        st.subheader( name.upper() )

    if st.button('소문자'):
        st.subheader( name.lower() )

    # 라디오 버튼 만드는 방법
    selected = st.radio( '정렬을 선택하세요' , ['오름차순 정렬','내림차순 정렬'] )
    if selected == '오름차순 정렬':
        st.dataframe( df.sort_values('petal_length') )
    elif selected == '내림차순 정렬':
        st.dataframe( df.sort_values('petal_length' , ascending=False))

if __name__ == '__main__':
    main()
```

- Checkbox, Selectbox
	- Checkbox는 사용자가 선택하거나 선택 해제할 수 있는 옵션을 제공하는 위젯
	- 여러 개의 옵션을 동시에 선택 가능
	- Selectbox는 사용자가 목록에서 하나의 옵션을 선택할 수 있는 드롭다운 형태의 위젯
```python
import streamlit as st
import pandas as pd
import numpy as np

def main():
    st.title('App4 - 아이리스 꽃의 종류')
    df = pd.read_csv('./data/iris.csv')

    #체크박스를 나타내는 방법
    if st.checkbox('데이터프레임 보이기'):
        st.dataframe(df)
    else :
        st.error('박스를 체크 해주십시오')
    
    #셀렉트박스 : 여러개 중에서 한개를 선택할 때
    language = ['Pyhton', 'Java','C','PHP','GO']
    
    my_choice = st.selectbox('좋아하는 언어를 선택하시오', language)
    st.text( '저는 {}언어를 좋아합니다.'.format(my_choice) )

    if my_choice == language[0] or my_choice == language[3] or  my_choice == language[4]:
        st.text('배우기 쉽습니다')
    elif my_choice == language[1] or my_choice == language[-3]:
        st.text('배우기 어렵습니다')

    #멀티셀렉트 : 여러개를 동시에 선택 가능
    selected_list = st.multiselect('여러개 선택 가능',df.columns)
    print(selected_list)
    
    if len(selected_list) != 0:
        st.dataframe(df[selected_list])
    else :
        st.text('')

if __name__ == '__main__':
    main()
```

- 슬라이더(Slider)
	- 사용자가 값을 선택하거나 조절할 수 있는 위젯
	- 숫자 범위 내에서 값을 조정 가능
```python
import streamlit as st
import pandas as pd
import numpy as np

def main():
    st.title('App4 - 아이리스 꽃의 종류')
    df = pd.read_csv('./data/iris.csv')
    
    # 슬라이더
    age = st.slider('나이' , 0 , 100)

    #st.text('제 나이는 {}살 입니다'.format(age))
    st.text('제 나이는 ' + str(age) + '세 입니다')
    st.slider('데이터',50,200,step=10)   ###step = 증가 단위
    st.slider('나이' , 0 , 100, value=28)    #value = 초기 셋팅 값
    st.slider('데이터',-0.5,2.7,step=0.1,value=1.0)

    with st.expander('상세내용보기') :
        st.text('상세 내용 입니다')

if __name__ == '__main__':
    main()
```