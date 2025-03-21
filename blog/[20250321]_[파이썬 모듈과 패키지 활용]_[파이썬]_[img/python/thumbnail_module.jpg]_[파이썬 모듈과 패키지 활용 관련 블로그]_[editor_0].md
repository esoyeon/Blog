# 파이썬 모듈과 패키지 활용

파이썬에서 **모듈(Module)**과 **패키지(Package)**는 코드를 논리적으로 분리하고 재사용성을 높이기 위한 핵심 개념입니다. 이번 글에서는 학생 관리 시스템을 예제로 삼아, 모듈과 패키지의 기본 개념, 파일별 역할, 그리고 실제 구현 방법을 단계별로 살펴보겠습니다.

---

## 1. 모듈과 패키지의 기본 개념

### 모듈(Module)이란?

- **모듈**은 함수, 변수, 클래스 등 파이썬 코드를 담고 있는 **`.py`** 파일입니다.
- 한 번 작성된 모듈은 여러 프로그램에서 재사용할 수 있으므로, 코드의 중복을 줄이고 관리하기 쉽습니다.

예를 들어, 아래와 같이 간단한 인사말 함수를 작성한 파일 `mymodule.py`가 있다고 가정합니다.

```python
# mymodule.py
def greet(name):
    return f"안녕하세요, {name}님!"
```

### 모듈 가져오기

모듈을 가져오는 방법에는 여러 가지가 있습니다.

- **기본 임포트:**  
  모듈 전체를 불러와 `모듈명.함수명`으로 사용합니다.
  
  ```python
  import mymodule
  print(mymodule.greet("홍길동"))
  ```

- **특정 함수만 가져오기:**  
  모듈 내 특정 함수나 변수를 바로 사용할 수 있도록 불러옵니다.
  
  ```python
  from mymodule import greet
  print(greet("홍길동"))
  ```

- **별칭(alias) 사용하기:**  
  모듈명을 간단하게 줄여서 사용합니다.
  
  ```python
  import mymodule as mm
  print(mm.greet("홍길동"))
  ```

또한, 모듈에는 **`__name__`**이라는 내장 속성이 있습니다.  
- 모듈이 **직접 실행**될 경우 `__name__`은 `"__main__"`이 되고,  
- 임포트될 때는 해당 모듈의 이름이 됩니다.  
이를 통해 모듈을 테스트 코드와 실제 사용 코드로 분리할 수 있습니다.

---

## 2. 패키지란?

패키지는 여러 모듈을 한 디렉터리로 묶어 관리할 수 있는 구조입니다.  
- 패키지 디렉터리 내에는 반드시 **`__init__.py`** 파일이 있어야 하며, 이 파일을 통해 패키지 초기화 및 외부에 노출할 모듈을 지정할 수 있습니다.

예시 디렉터리 구조:
```
mypackage/
├── __init__.py
├── math_operations.py
└── string_operations.py
```

`__init__.py` 파일 내에서 다음과 같이 모듈의 함수들을 임포트하면, 패키지 외부에서 간편하게 사용할 수 있습니다.

```python
# mypackage/__init__.py
from .math_operations import add
from .string_operations import to_uppercase
```

---

## 3. 모듈 임포트 시 위치에 따른 사례

실제 프로젝트를 구성할 때, 모듈의 위치에 따라 임포트 방식이 달라집니다.

### (1) 하위 폴더(서브 디렉터리) 모듈 임포트

예시:
```
project/
├── main.py
└── utils/
    ├── __init__.py
    └── helper.py
```

- **임포트 방법:**  
  `main.py`에서 `utils/helper.py`에 있는 함수를 사용하려면 다음과 같이 작성합니다.

  ```python
  from utils.helper import some_function
  some_function()
  ```

### (2) 상위 폴더(부모 디렉터리) 모듈 임포트

예시:
```
project/
├── common.py
└── subdir/
    └── main.py
```

- **임포트 방법:**  
  `subdir/main.py`에서 상위 폴더의 모듈을 사용하려면, 부모 디렉터리를 임시로 `sys.path`에 추가합니다.

  ```python
  # subdir/main.py
  import sys, os
  sys.path.append(os.path.abspath(".."))
  import common
  common.some_function()
  ```

> ※ 주의: 이 방법은 임시 해결책이며, 프로젝트 구조를 패키지화하여 올바르게 관리하는 것이 좋습니다. (확실하지 않음)

### (3) 자매(같은 레벨) 폴더 모듈 임포트

예시:
```
project/
├── common/
│   └── helper.py
└── app/
    └── main.py
```

- **임포트 방법:**  
  상위 폴더를 패키지로 인식하게 하고, 자매 디렉터리 간에 임포트할 수 있습니다.

  ```python
  # app/main.py
  from common.helper import some_function
  some_function()
  ```

> ※ 이 경우, 프로젝트 루트 디렉터리가 모듈 검색 경로에 포함되어 있어야 합니다.

---

## 4. 학생 관리 시스템 구현 예제

이번 예제에서는 **학생 관리 시스템**을 구현해봅니다. 이 시스템은 두 가지 주요 기능을 수행합니다.

- **학생 관리 기능:**  
  학생의 이름과 나이를 등록하고, 등록된 학생 목록을 조회합니다.
- **수업 관리 기능:**  
  수업의 제목과 강사 정보를 등록하고, 등록된 수업 목록을 조회합니다.

### 프로젝트 디렉터리 구조

```
school/
├── __init__.py
├── student_management.py
├── course_management.py
main.py
```

### 각 파일의 역할 및 구현

#### 1. `student_management.py`

- **목적:**  
  학생 등록 및 학생 목록 조회 기능을 구현합니다.

- **구현 예시:**

  ```python
  students = []

  def add_student(name, age):
      student = {"name": name, "age": age}
      students.append(student)
      print(f"학생 등록: {student}")

  def list_students():
      return students
  ```

#### 2. `course_management.py`

- **목적:**  
  수업 등록 및 수업 목록 조회 기능을 구현합니다.

- **구현 예시:**

  ```python
  courses = []

  def add_course(title, instructor):
      course = {"title": title, "instructor": instructor}
      courses.append(course)
      print(f"수업 등록: {course}")

  def list_courses():
      return courses
  ```

#### 3. `__init__.py`

- **목적:**  
  외부에서 학생 관리와 수업 관리 기능을 쉽게 임포트할 수 있도록 모듈들을 노출합니다.

- **구현 예시:**

  ```python
  from .student_management import add_student, list_students
  from .course_management import add_course, list_courses
  ```

#### 4. `main.py`

- **목적:**  
  전체 시스템의 진입점으로, 실제 학생과 수업을 등록하고 결과를 출력하는 역할을 합니다.

- **구현 예시:**

  ```python
  from school import add_student, list_students, add_course, list_courses
  
  # 학생 추가
  add_student("홍길동", 20)
  add_student("김영희", 22)
  
  # 수업 추가
  add_course("파이썬 프로그래밍", "홍길동")
  add_course("자료 구조", "김영희")
  
  # 정보 출력
  print("\n학생 목록:")
  print(list_students())
  
  print("\n수업 목록:")
  print(list_courses())
  ```

### 실행 결과

프로그램을 실행하면 다음과 같이 각 모듈에서 등록된 학생과 수업 정보를 출력하게 됩니다.

```
학생 등록: {'name': '홍길동', 'age': 20}
학생 등록: {'name': '김영희', 'age': 22}
수업 등록: {'title': '파이썬 프로그래밍', 'instructor': '홍길동'}
수업 등록: {'title': '자료 구조', 'instructor': '김영희'}

학생 목록:
[{'name': '홍길동', 'age': 20}, {'name': '김영희', 'age': 22}]

수업 목록:
[{'title': '파이썬 프로그래밍', 'instructor': '홍길동'}, {'title': '자료 구조', 'instructor': '김영희'}]
```

---

## 참고자료

- [Python 공식 튜토리얼 - 모듈](https://docs.python.org/3/tutorial/modules.html)
