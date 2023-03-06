![](https://devocean.sk.com/resource/images/external/logo/logo_web.png)

## 목차
1. CPython
2. Cython
3. Jython
4. IronPython
5. 정리
6. 배운점

## 1. CPython
- CPython은 python.org에서 배포되고 있는 Python을 의미
- 여기서 C는 C/C++언어로 구현된 python을 의미
- 파이썬은 C언어로 작성된 컴파일러를 활용해서 라인단위로 코드를 인터프리팅 하여 실행한다.
- 때문에, CPython을 수정한 다음 컴파일을 하면 python의 문법이 바뀐다.다
- numpy도 빠른 속도를 위해서 cpython으로 작성된 소스코드가 있다.
- pypy
    - CPython의 컴파일러를 python으로 작성한 구현체이다
    - 구현체 자체가 다르기 때문에 pypy를 활용하기 위해서는 별도의 pypy로된 python 설치가 필요하다
    
## 2. Cython
- C와 유사한 문법으로 함수를 작성하고, 이를 CPython 패키지 형태로 만들어준다.
- pandas의 경우도 일부 소스코드는 Cython을 이용해서 최적화된 함수를 사용하는 것으로 알려져있다.

## 3. Jython
![](https://devocean.sk.com/editorImg/2023/2/12/b1512be8ef9718ebc26950c38189b0ff89e8079c95995a3e3d3f2dd8a08001ce)
- JVM을 활용하는 Python 구현체 중 하나로, python syntax로 작성된 코드를 JVM이 이해할 수 있는 바이트코드로 만들고, 이 바이트코드를 JVM이 실행하게 된다
- CPython 대비 속도의 차이점이 있을수는 있지만, 사용자 입장에서는 큰 차이점을 느끼지 못한다.
- Jython의 특징으로는 GIL을 사용하지 않는다는 장점과 python3 문법이 아직 지원안된다는 점이 있다.

## 4. IronPython
![](https://devocean.sk.com/editorImg/2023/2/12/02c0086025fde995449544973147a8b69fafaf2ffb9f2685f18de441844630b2)
- Jython이 JVM을 활용한 구현체 인 것처럼
- IronPython은 C#의 .NET 프레임워크를 사용한 Python 구현체이다.

## 5. 정리
- CPython : Python 그 자체
- pypy : Python Compiler를 Python으로 작성한 Python 구현체
- Cython : C언어 비슷한 문법으로 함수를 작성하고, CPython Library를 만들어주는 패키지
- Jython : Python을 구현할 때 "JVM"을 사용한 Python
- IronPython : Python을 구현할 때 ".NET"을 사용한 Python

## 6. 배운점
- Cpython은 들어는 봤지만 Jython, IronPython 등은 처음 알게되었다.
- "CPython", "Cython". "Jython", "IronPython" 이 어떻게 이루어져 있는지를 새롭게 알게되었다.

## 참고자료 출처
[https://devocean.sk.com/blog/techBoardDetail.do?ID=164537&boardType=techBlog&searchData=&page=&subIndex=%EC%B5%9C%EC%8B%A0+%EA%B8%B0%EC%88%A0+%EB%B8%94%EB%A1%9C%EA%B7%B8](https://devocean.sk.com/blog/techBoardDetail.do?ID=164537&boardType=techBlog&searchData=&page=&subIndex=%EC%B5%9C%EC%8B%A0+%EA%B8%B0%EC%88%A0+%EB%B8%94%EB%A1%9C%EA%B7%B8)