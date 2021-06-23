---
id: 0
title: "Preprocessor"
subtitle: "전처리기"
date: "2020.01.19"
tags: "System"
---

# Preprocessor

## How the Preprocessor Works

preprocessor(전처리기)는 컴파일 과정 이전에 소스파일을 수정하는 프로그램이다. preprocessing directive는 `#` 문자로 시작하는 커맨드를 의미하는데, 전처리기는 이 문장들을 편집하는 역할을 한다.

preprocessing directive는 크게 3가지 목록으로 나뉜다.

- **매크로 정의** : `#define`은 macro를 정의한다. `#undef`는 그 매크로를 삭제한다.
- **파일 들여오기** : `#include`는 전처리기가 특정한 파일을 가져와 소스파일에 붙이게 한다.
- **조건적 컴파일** : `#if`, `#ifdef`, `#ifndef`등의 디렉티브들은 조건에 따라 텍스트를 프로그램 안으로 들여오거나 밖으로 내보내는 역할을 한다.

## Macro Definition

### Simple Macros

```c
#define (identifier) (replacement_list)
```

`#define`을 통해 정의된 매크로는 전처리기에 의해 소스파일 내에서 나타나는 모든 identifier들을 replacement_list로 대체한다. 이는 다음과 같은 특징들을 가진다.

- 프로그램의 가독성을 높일 수 있다.
- 프로그램을 수정하기 쉽게 한다.
- 오타로 인한 에러를 방지할 수 있다.
- 자신만의 특별한 매크로를 통해 C 문법과 살짝 다르게 프로그램을 작성할 수 있다.
- 타입을 다른 이름으로 작성할 수 있다.

### Parameterized Macros

```c
#define (identifier(x1, x2, ..., xn)) (replacement_list)
```

매개화된 매크로는 함수의 정의와 닮았다. 매크로에 들어온 파라미터들을 이용해 replacement_list를 작성할 수 있다.

이 매크로는 변수 타입에 제한을 받지 않는다. 그래서 더욱 간편하게 코드를 작성할 수는 있지만 동시에 타입 체크가 안되기 때문에 불안정하다. 또한, 포인터 변수를 매크로 인자로 받을 수는 없다.

매크로 인자를 replacement_list에 넣을 때는 **반드시 인자를 괄호로 묶어야 한다.** 매크로는 단순히 전처리기가 소스파일의 identifier를 치환하는 것이기 때문에 연산 순서가 예상과 달라질 수 있다.

#### `#` Operator, `##` Operator

매개화된 매크로는 두 가지 특별한 연산자를 가진다. `#` 연산자는 매크로 인자를 스트링 리터럴로 바꿔준다.

```c
#define PRINT_INT(n) printf(#n " = %d\n", n)
int i = 10;
int j =  2;
PRINT_INT(i/j);
```

예를 들어, 다음 프로그램을 컴파일 하면 전처리기가 `PRINT_INT`를 매크로를 따라 치환한다. 그럼 `#n`은 인자로 넘어온 `i/j`를 그대로 스트링 리터럴로 바꾸어 그 자리에 치환한다. 즉, `printf("i/j = %d\n", n)`과 같이 변하는 것이다. 서로 떨어진 두 스트링 리터럴은 자동적으로 컴파일러가 하나로 붙인다.

`##`연산자는 두 토큰을 붙여 하나의 토큰으로 만든다.

```c
#define MK_ID(n) i##n
int MK_ID(1), MK_ID(2); /* <==> int i1, i2; */
```

`i`와 `n`두 토큰을 붙여 하나로 만들었다. 만약 둘 사이에 `##` 연산자가 없다면 어떻게 될까?

#### Predefined Macros

C는 미리 정의된 매크로를 몇 가지 지원한다. 이들은 컴파일 과정이나 컴파일러에 대한 정보를 지원해주는 역할을 한다.

## Conditional Compilation

### `#if` and `#endif` Directives

```c
#if (const_exp)
(program_segment)
#endif
```

`#if`뒤에 오는 토큰의 값에 의해 전처리기가 `(program_segment)`를 제거할지 가만히 놔둘지 결정한다. 참일 경우 그대로 놔두고, 거짓일 경우 이 block을 통째로 없앤다. if 뒤에오는 표현식이 정의되지 않은 매크로일 경우, 그 값을 0으로 치환한다.

#### `defined` Operator

`defined` 연산자는 전처리기에 의해 수행된다. 매크로가 이미 정의 되었는지, 혹은 그렇지 않은지를 검사한다.

```c
#if defined(DEBUG) /* or #if defined DEBUG */
...
#endif
```

#### `#ifdef` and `#ifndef` Directives

```c
#ifdef (identifier)
#if defined (identifier)
```

위의 두 디렉티브는 똑같은 의미이다. 즉, `#ifdef`는 `#if`와 `#defined`를 섞은 표현인 것이다.

`#ifndef`는 `#ifdef`의 부정이다. 앞에 `n`이 붙은 것이다. `#if !definced`와 같다.

#### `#elif` and `#else` Directives

```c
#if expr1
(Lines to be included if expr1 is nonzero)
#elif expr2
(Lined to be included else if expr2 is nonzero)
#else
(Lines to be included if both expr1 and expr2 are zero)
#endif
```

일반적인 `if`, `else if`, `else`와 문법이 동일하게 쓰인다. 마지막에 `#endif`를 적어야 함을 유의하자.

#### Uses of Conditional Compilations

그럼 이러한 조건적 디렉티브를 왜 쓰이는가? 주로 쓰이는 이유는 조건적 컴파일, 즉 디버깅을 편하게 하기 위해서지만 그 밖에도 다른 응용 방법이 있다.

- 여러 머신이나 OS에 portable한 프로그램을 쓸 때
- 컴파일러 종류별로 서로 다른 프로그램을 작성할 때
- default macro를 작성할 때. 매크로의 정의가 이미 있는지 없는지를 통해 매크로를 정의할지 말지도 조건으로 나눌 수 있다.
- comment를 포함한 코드를 일시적으로 감출 때

## Miscellaneous Directives

위에서 분류한 3가지 디렉티브 외에도 추가적인 것들이 몇 가지 더 있다. `#error`, `#line`, `#pragma`에 대해 간략하게 살펴보자.

### `#error` Directive

```c
#error message
```

전처리기가 이 디렉티브를 마주쳤을 때 전처리기는 `message`를 출력하며 error를 낸다. 에러 메세지의 양식은 컴파일러마다 다를 수 있지만 보통 아래같은 형식이다.

```c
Error directive: message
(or)
#error message
```

조건 디렉티브랑 활용하면 에러를 처리할 때 유용할 것 같다.

### `#line` Directive

이미 정의된 매크로인 `__LINE__`과 `__FILE__`을 재정의한다.

```c
#line n "file"
```

`file`은 생략 가능하며 `n`은 새롭게 재정의할 line number이다. line값은 `__LINE__`에, file명은 `__FILE__`에 재정의된다.

프로그램에 에러가 있을 경우 컴파일을 시도했을 때 에러가 뜬 line number가 나온다. 여기서 출력되는 line number는 전체 소스파일을 기준으로 위치를 계산하기 때문에 에러의 위치를 찾기 힘들 수 있다. 이 때 `#line`을 이용하면 이 디렉티브를 기준으로 line이 재정의되기 때문에 에러 위치를 더 찾기 쉬워진다.

### `#pragma` Directive

`#pragma`는 컴파일러에게 특별한 행동을 요청한다. 보통 비정상적으로 큰 프로그램이나 혹은 특정 컴파일러의 capability를 이용할 때 쓰인다.

```c
#pragma tokens
```

`#pragma`뒤에는 지시 사항을 전달하는 토큰 문자열이 오는데 이 토큰은 컴파일러 종류마다 다르다. 컴파일러는 토큰의 내용을 이해할 수 없을때는 명령을 단순히 무시하고 넘어간다.
