# YACL (Yet Another CBS Language) Spec

---
## 데이터 조작 구문

### `{{ @VARIABLE }}`
  > @VARIABLE: `EXPRESSION`  
  > 반환 타입: `DYNAMIC`

변수의 값을 반환합니다.
```
안녕하세요, {{ $USER }}님! // 안녕하세요, 유저님!
```

### `{{ SET @VARIABLE @EXPRESSION }}`
  > @VARIABLE: `VARIABLE`  
  > @EXPRESSION: `EXPRESSION`  
  > 반환 타입: `VOID`

변수에 값을 할당합니다. 만약 변수가 존재하는 경우, 기존 변수의 값을 대체합니다.
새로운 값은 기존 변수의 값과 타입이 다를 수 없습니다.
```
{{ SET SCORE 88 }}
당신의 점수: {{ $SCORE }}점 // 당신의 점수: 88점
```

### `{{ EXISTS @VARIABLE }}`
  > @VARIABLE: `VARIABLE`  
  > 반환 타입: `BOOL`

변수의 존재 여부를 반환합니다. 존재하는 경우 `TRUE`, 존재하지 않는 경우 
`FALSE`를 반환합니다.
```
{{ SET EGG 88 }}
{{ EXISTS SPAM }} // FALSE
```

### `{{ DEL @VARIABLE }}`
  > @VARIABLE: `EXPRESSION`  
  > 반환 타입: `VOID`

변수를 삭제합니다. 변수가 존재하지 않더라도 패닉이 발생하지 않습니다.
```
{{ SET SPAM 88 }}
{{ DEL SPAM }}
{{ EXISTS SPAM }} // FALSE
```

---
## 산술 및 논리 연산 구문

### `{{ CALC @EXPRESSION }}`
  > @EXPRESSION: `EXPRESSION`  
  > 반환 타입: `NUM`

표현식을 아래 표에 따라 연산합니다. 
- `BOOL`의 경우 `TRUE`이면 `1`, `FALSE`이면 `0`으로 간주됩니다. 
- `NUM` 또는 `BOOL` 이외의 다른 타입이 입력되면 패닉이 발생합니다.
- 연산에 실패한 경우(Syntax Error, Overflow 등) 패닉이 발생합니다.

|  연산  | 기호 |  예시  | 결과 |
|--------|------|--------|------|
| 덧셈   | `+`  | 11 + 5 | 16   |
| 뺄셈   | `-`  | 11 - 5 | 6    |
| 곱셈   | `*`  | 11 * 5 | 55   |
| 나눗셈 | `/`  | 11 / 5 | 2.2  |
| 몫     | `//` | 9 // 2 | 4    |
| 나머지 | `%`  | 10 % 4 | 2    |
| 제곱   | `^`  | 10 ^ 3 | 1000 |
| 반수   | `-`  | -12    | -12  |
```
{{ SET A 11 }}
{{ SET B 2 }}

{{ CALC $A + 3 }}        // 14
{{ CALC ($A + 3) * $B }} // 28
```

### `{{ CEIL @VALUE [@DIGIT = 0] }}`
  > @VALUE: `NUM`  
  > @DIGIT: `NUM`  
  > 반환 타입: `NUM`

`@VALUE`를 `@DIGIT`번째 소수점까지 올림합니다. 
- `@VALUE` 또는 `@DIGIT`이 `NUM` 타입이 아닌 경우 패닉이 발생합니다.
- `@DIGIT`이 `@VALUE`보다 정밀한 경우 이 명령어는 효력이 없습니다. 
- `@DIGIT`이 음수인 경우 `@VALUE`를 정수 자리까지 올림합니다.
- `@DIGIT`이 정수가 아닌 경우 소수점은 무시됩니다.
```
{{ CEIL 123.123 }}    // 124
{{ CEIL 123.123 1 }}  // 123.2
{{ CEIL 123.123 -1 }} // 130
```

### `{{ FLOOR @VALUE [@DIGIT = 0] }}`
  > @VALUE: `NUM`  
  > @DIGIT: `NUM`  
  > 반환 타입: `NUM`

`@VALUE`를 `@DIGIT`번째 소수점까지 내림합니다. 
- `@VALUE` 또는 `@DIGIT`이 `NUM` 타입이 아닌 경우 패닉이 발생합니다.
- `@DIGIT`이 `@VALUE`보다 정밀한 경우 이 명령어는 효력이 없습니다. 
- `@DIGIT`이 음수인 경우 `@VALUE`를 정수 자리까지 내림합니다.
- `@DIGIT`이 정수가 아닌 경우 소수점은 무시됩니다.
```
{{ FLOOR 123.123 }}    // 123
{{ FLOOR 123.123 1 }}  // 123.1
{{ FLOOR 123.123 -1 }} // 120
```

### `{{ ROUND @VALUE [@DIGIT = 0] }}`
  > @VALUE: `NUM`  
  > @DIGIT: `NUM`  
  > 반환 타입: `NUM`

`@VALUE`를 `@DIGIT`번째 소수점까지 반올림합니다. 
- `@VALUE` 또는 `@DIGIT`이 `NUM` 타입이 아닌 경우 패닉이 발생합니다.
- `@DIGIT`이 `@VALUE`보다 정밀한 경우 이 명령어는 효력이 없습니다. 
- `@DIGIT`이 음수인 경우 `@VALUE`를 정수 자리까지 반올림합니다.
- `@DIGIT`이 정수가 아닌 경우 소수점은 무시됩니다.
```
{{ ROUND 147.147 }}    // 147
{{ ROUND 147.147 2 }}  // 147.15
{{ ROUND 147.147 -1 }} // 150
```

### `{{ ABS @VALUE }}`
  > @VALUE: `NUM`  
  > 반환 타입: `NUM`

`@VALUE`의 절댓값을 반환합니다.
- `@VALUE`가 `NUM`타입이 아닌 경우 패닉이 발생합니다.
```
{{ ABS 123.123 }}  // 123.123
{{ ABS -123.123 }} // 123.123
```

### `{{ MIN @A @B @C ... }}`
  > @A, B, C...: `NUM`  
  > 반환 타입: `NUM`

`@A, @B, @C...` 중 가장 작은 값을 반환합니다.
- `@A, @B, @C...`가 `NUM` 타입이 아닌 경우 패닉이 발생합니다.
```
{{ MIN 1 3 5 }} // 1
```

### `{{ MAX @A @B @C ... }}`
  > @A, B, C...: `NUM`  
  > 반환 타입: `NUM`

`@A, @B, @C...` 중 가장 큰 값을 반환합니다.
- `@A, @B, @C...`가 `NUM` 타입이 아닌 경우 패닉이 발생합니다.
```
{{ MAX 1 3 5 }} // 5
```

### `{{ SUM @A @B @C ... }}`
  > @A, B, C...: `NUM`  
  > 반환 타입: `NUM`

`@A, @B, @C...`의 합을 반환합니다.
- `@A, @B, @C...`가 `NUM` 타입이 아닌 경우 패닉이 발생합니다.
```
{{ SUM 1 3 5 }} // 9
```

### `{{ AVG @A @B @C ... }}`
  > @A, B, C...: `NUM`  
  > 반환 타입: `NUM`

`@A, @B, @C...`의 평균값을 반환합니다.
- `@A, @B, @C...`가 `NUM` 타입이 아닌 경우 패닉이 발생합니다.
```
{{ AVG 1 3 5 }} // 3
```

### `{{ ALL @A @B @C ... }}`
  > @A, @B, @C...: `BOOL`  
  > 반환 타입: `BOOL`

`@A, @B, @C...`의 모든 요소가 참이면 `TRUE`, 그렇지 않으면 `FALSE`를 반환합니다.
- `NUM`의 경우, 0이면 `FALSE`, 0이 아니면 `TRUE`로 간주됩니다.
- `@A, @B, @C...`가 `BOOL` 또는 `NUM` 타입이 아닌 경우 패닉이 발생합니다.
```
{{ ALL TRUE TRUE 1 }}   // TRUE
{{ ALL TRUE TRUE 0 }}   // FALSE
{{ ALL TRUE FALSE -1 }} // FALSE
```

### `{{ ANY @A @B @C ... }}`
  > @A, @B, @C...: `BOOL`  
  > 반환 타입: `BOOL`

`@A, @B, @C...` 요소 중 어느 하나라도 참이면 `TRUE`, 모든 요소가 거짓이면 
`FALSE`를 반환합니다.
- `NUM`의 경우, 0이면 `FALSE`, 0이 아니면 `TRUE`로 간주됩니다.
- `@A, @B, @C...`가 `BOOL` 또는 `NUM` 타입이 아닌 경우 패닉이 발생합니다.
```
{{ ANY TRUE TRUE 1 }}    // TRUE
{{ ANY FALSE FALSE 0 }}  // FALSE
{{ ANY FALSE FALSE -1 }} // TRUE
```

---
## 제어 구문

### `{{ IF @CONDITION }} ... {{ ELSEIF|ELSE|END }}`
  > @CONDITION: `EXPRESSION`  
  > 반환 타입: `DYNAMIC`

`@CONDITION`이 참인 경우 블록 내부를 실행합니다.
```
{{ SET LANG 1 }}

{{ IF LANG = 1 }}
    Hello, World!
{{ END }} // Hello, World!
```

### `{{ ELSEIF @CONDITION }} ... {{ ELSEIF|ELSE|END }}`
  > @CONDITION: `EXPRESSION`  
  > 반환 타입: `DYNAMIC`

첫 번째 `IF` 조건이 거짓일 때 추가 `@CONDITION`을 조사하고 참인 경우 
블록 내부를 실행합니다. 여러 개를 연결할 수 있습니다.
- `IF` 구문이 선행되지 않으면 패닉이 발생합니다.
```
{{ SET LANG 3 }}

{{ IF LANG = 1 }}
    Hello, World!
{{ ELSEIF LANG = 2 }}
    ¡Hola, Mundo!
{{ ELSEIF LANG = 3 }}
    Hallo, Welt!
{{ END }} // Hallo, Welt!
```

### `{{ ELSE }} ... {{ END }}`
  > 반환 타입: `DYNAMIC`

선행된 `IF` 및 `ELSEIF` 조건이 모두 거짓일 때 블록 내부를 실행합니다.
- `IF` 또는 `ELSEIF` 구문이 선행되지 않으면 패닉이 발생합니다.
```
{{ SET LANG 4 }}

{{ IF LANG = 1 }}
    Hello, World!
{{ ELSEIF LANG = 2 }}
    ¡Hola, Mundo!
{{ ELSEIF LANG = 3 }}
    Hallo, Welt!
{{ ELSE }}
    세상아, 안녕!
{{ END }} // 세상아, 안녕!
```

### `{{ GOTO @LABEL }}`
  > @LABEL: `EXPRESSION`  
  > 반환 타입: `VOID`

### `{{ LABEL @NAME }}`
  > @NAME: `EXPRESSION`  
  > 반환 타입: `VOID`

---
## 문자열 조작 구문

### `{{ CFORMAT @FORMAT @A @B @C ... }}`
  > @FORMAT: `TEXT`
  > @A, @B, @C...: `DYNAMIC`
  > 반환 타입: `TEXT`

`@FORMAT` 형식 텍스트에 순서대로 `@A, @B, @C...`를 적용해 문자열을 생성합니다.
`%s`, `%d`, `%.2f` 등 형식을 지원합니다. `%`를 출력하려면 `%%`를 사용하세요.
- 포맷 지정자와 인자 개수 및 타입이 맞지 않으면 패닉이 발생합니다.
```
{{ CFORMAT "%s(%d) - %.2f" "item" 3 1.2345 }} // item(3) - 1.23
{{ CFORMAT "%.1f%% done: %d/%d" 61.67 3 5 }}  // 61.6% done: 3/5
```

### `{{ STARTSWITH @PREFIX @SOURCE }}`
  > @PREFIX: `TEXT`
  > @SOURCE: `TEXT`
  > 반환 타입: `BOOL`

`@SOURCE`가 `@PREFIX`로 시작하면 `TRUE`, 그렇지 않으면 `FALSE`를 반환합니다.
```
{{ STARTSWITH "pre" "prefix" }} // TRUE
{{ STARTSWITH "Pre" "prefix" }} // FALSE
{{ STARTSWITH "" "anything" }}  // TRUE
```

### `{{ ENDSWITH @SUFFIX @SOURCE }}`
  > @SUFFIX: `TEXT`
  > @SOURCE: `TEXT`
  > 반환 타입: `BOOL`

`@SOURCE`가 `@SUFFIX`로 끝나면 `TRUE`, 그렇지 않으면 `FALSE`를 반환합니다.
```
{{ ENDSWITH ".pdf" "test.pdf" }} // TRUE
{{ ENDSWITH ".PDF" "test.pdf" }} // FALSE
{{ ENDSWITH "" "anything" }}     // TRUE
```

### `{{ UPPER @VALUE }}`
  > @VALUE: `TEXT`
  > 반환 타입: `TEXT`

`@VALUE`를 대문자로 변환합니다.
```
{{ UPPER "hello, WORLD!" }} -> "HELLO, WORLD!"
```

### `{{ LOWER @VALUE }}`
  > @VALUE: `TEXT`
  > 반환 타입: `TEXT`

`@VALUE`를 소문자로 변환합니다.
```
{{ LOWER "hello, WORLD!" }} -> "hello, world!"
```

### `{{ CAPITALIZE @VALUE }}`
  > @VALUE: `TEXT`
  > 반환 타입: `TEXT`

단어의 첫 글자를 대문자로, 나머지는 소문자로 변환합니다.
```
{{ CAPITALIZE "hello WORLD" }} // "Hello World"
{{ CAPITALIZE "123abc def" }}      // "123abc Def"
```

### `{{ TRIM @VALUE }}`
  > @VALUE: `TEXT`
  > 반환 타입: `TEXT`

`@VALUE`의 앞뒤 공백(스페이스, 탭, 개행 등)을 제거합니다.
```
{{ TRIM "  hello \n" }} // "hello"
```

### `{{ SPLIT @SEPARATOR @VALUE [@LIMIT = 0] }}`
  > @SEPARATOR: `TEXT`
  > @VALUE: `TEXT`
  > @LIMIT: `NUM`
  > 반환 타입: `LIST`

`@VALUE`를 `@SEPARATOR`로 `@LIMIT`번 분리하여 리스트로 반환합니다.
```
{{ SPLIT "," "a,b,,c" }}      // ("a", "b", "", "c")
{{ SPLIT "," "a,b,,c" 2 }}    // ("a", "b", ",c")
{{ SPLIT " | " "x | y | z" }} // ("x", "y", "z")
```

---
## 리스트 구문

### `{{ LIST @A @B @C ... }}`
  > @A, @B, @C...: `DYNAMIC`  
  > 반환 타입: `LIST`

요소들을 이용해 새로운 리스트를 생성합니다.
```
{{ SET NUMBERS {{ LIST 1 2 3 4 5 }} }}
{{ SET NAMES {{ LIST "Alice" "Bob" "Charlie" }} }}
{{ SET VARIABLES {{ LIST TRUE, 2, "3" }} }}
{{ SET EMPTY {{ LIST }} }}
```

### `$LIST[ @INDEX ]`
  > @INDEX: `NUM`
  > 반환 타입: `DYNAMIC`

리스트의 특정 위치에 있는 요소에 접근합니다. `@INDEX`는 0부터 시작하며, 
음수는 뒤에서부터 접근합니다.
- `@INDEX`가 `NUM` 타입이 아닌 경우 패닉이 발생합니다.
- `@INDEX`가 리스트의 범위를 벗어나는 경우 패닉이 발생합니다.
```
{{ SET FRUITS {{ LIST "apple" "banana" "cherry" }} }}
{{ $FRUITS[0] }}   // apple
{{ $FRUITS[1] }}   // banana
{{ $FRUITS[-1] }}  // cherry (마지막 요소)
{{ $FRUITS[-2] }}  // banana (뒤에서 두 번째)
```

### `$~LIST`
  > 반환 타입: `EXPRESSION`

리스트의 모든 요소를 언패킹하여 개별 인자로 전달합니다.
- 리스트가 비어있는 경우 아무것도 전달하지 않습니다.
```
{{ SET NUMBERS {{ LIST 1 2 3 }} }}
{{ MAX $~NUMBERS }}  // MAX 1 2 3과 동일, 결과: 3
{{ SUM $~NUMBERS }}  // SUM 1 2 3과 동일, 결과: 6
```

### `{{ LIST::SIZE @LIST }}`
  > @LIST: `LIST`  
  > 반환 타입: `NUM`

리스트의 요소 개수를 반환합니다.
```
{{ SET COLORS {{ LIST "red" "green" "blue" }} }}
{{ LIST::SIZE $COLORS }}  // 3
{{ LIST::SIZE {{ LIST }} }}  // 0
```

### `{{ LIST::INSERT @LIST @ELEMENT [@INDEX = -1] }}`
  > @LIST: `LIST`  
  > @ELEMENT: `DYNAMIC`  
  > @INDEX: `NUM`  
  > 반환 타입: `VOID`

리스트의 지정된 위치에 요소를 삽입합니다. 기본값은 맨 끝(-1)입니다.
```
{{ SET NUMBERS {{ LIST 1 3 5 }} }}
{{ LIST::INSERT $NUMBERS 2 1 }}    // [1, 2, 3, 5]
{{ LIST::INSERT $NUMBERS 7 }}      // [1, 2, 3, 5, 7] (맨 끝에 추가)
{{ LIST::INSERT $NUMBERS 0 0 }}    // [0, 1, 2, 3, 5, 7] (맨 앞에 추가)
```

### `{{ LIST::REMOVE @LIST [@INDEX = -1] }}`
  > @LIST: `LIST`  
  > @INDEX: `NUM`  
  > 반환 타입: `VOID`

리스트의 지정된 인덱스에 있는 요소를 제거합니다. 기본값은 마지막 요소(-1)입니다.
- `@INDEX`가 리스트의 범위를 벗어나는 경우 패닉이 발생합니다.
- 빈 리스트에서 제거를 시도하는 경우 패닉이 발생합니다.
```
{{ SET ITEMS {{ LIST "a" "b" "c" "d" }} }}
{{ LIST::REMOVE $ITEMS 1 }}   // ["a", "c", "d"] (인덱스 1 제거)
{{ LIST::REMOVE $ITEMS }}     // ["a", "c"] (마지막 요소 제거)
```

### `{{ LIST::POP @LIST [@INDEX = -1] }}`
  > @LIST: `LIST`  
  > @INDEX: `NUM`  
  > 반환 타입: `DYNAMIC`

리스트에서 지정된 인덱스의 요소를 제거하고 해당 값을 반환합니다.
- `@INDEX`가 리스트의 범위를 벗어나는 경우 패닉이 발생합니다.
- 빈 리스트에서 POP을 시도하는 경우 패닉이 발생합니다.
```
{{ SET STACK {{ LIST 1 2 3 4 }} }}
{{ LIST::POP $STACK }}      // 4 반환, STACK은 [1, 2, 3]
{{ LIST::POP $STACK 0 }}    // 1 반환, STACK은 [2, 3]
```

### `{{ LIST::EXTEND @LIST @OTHER }}`
  > @LIST: `LIST`
  > @OTHER: `LIST`
  > 반환 타입: `VOID`

첫 번째 리스트에 두 번째 리스트의 모든 요소를 추가합니다.
```
{{ SET LIST1 {{ LIST 1 2 3 }} }}
{{ SET LIST2 {{ LIST 4 5 6 }} }}
{{ LIST::EXTEND $LIST1 $LIST2 }}
// LIST1은 이제 [1, 2, 3, 4, 5, 6]
```

### `{{ LIST::CONCAT @LIST1 @LIST2 }}`
  > @LIST1: `LIST`
  > @LIST2: `LIST`
  > 반환 타입: `LIST`

두 리스트를 연결한 새로운 리스트를 반환합니다. 원본 리스트는 변경되지 않습니다.
```
{{ SET A {{ LIST 1 2 3 }} }}
{{ SET B {{ LIST 4 5 6 }} }}
{{ SET C {{ LIST::CONCAT $A $B }} }}  // [1, 2, 3, 4, 5, 6]
// A와 B는 그대로 유지됨
```

### `{{ LIST::CONTAINS @LIST @ELEMENT }}`
  > @LIST: `LIST`
  > @ELEMENT: `DYNAMIC`
  > 반환 타입: `BOOL` 

리스트에 특정 요소가 포함되어 있는지 확인합니다.
```
{{ SET FRUITS {{ LIST "apple" "banana" "cherry" }} }}
{{ LIST::CONTAINS $FRUITS "banana" }}  // TRUE
{{ LIST::CONTAINS $FRUITS "orange" }}  // FALSE
```

### `{{ LIST::CLEAR @LIST }}`
  > @LIST: `LIST`
  > 반환 타입: `VOID` 

리스트의 모든 요소를 제거합니다.
```
{{ SET ITEMS {{ LIST 1 2 3 4 5 }} }}
{{ LIST::CLEAR $ITEMS }}
{{ LIST::SIZE $ITEMS }}  // 0
```

### `{{ LIST::REVERSE @LIST }}`
  > @LIST: `LIST`
  > 반환 타입: `VOID` 

리스트의 요소 순서를 뒤집습니다. 원본 리스트를 수정합니다.
```
{{ SET NUMBERS {{ LIST 1 2 3 4 5 }} }}
{{ LIST::REVERSE $NUMBERS }}
// NUMBERS는 이제 [5, 4, 3, 2, 1]
```

### `{{ LIST::REVERSED @LIST }}`
  > @LIST: `LIST`
  > 반환 타입: `LIST`

요소 순서가 뒤집힌 새로운 리스트를 반환합니다. 원본 리스트는 변경되지 않습니다.
```
{{ SET ORIGINAL {{ LIST 1 2 3 4 5 }} }}
{{ SET REVERSED {{ LIST::REVERSED $ORIGINAL }} }}
// REVERSED는 [5, 4, 3, 2, 1], ORIGINAL은 [1, 2, 3, 4, 5]
```

### `{{ LIST::SORT @LIST [@REVERSE = FALSE] }}`
  > @LIST: `LIST`
  > @REVERSE: `BOOL`
  > 반환 타입: `VOID`

리스트를 정렬합니다. `@REVERSE`가 TRUE이면 내림차순으로 정렬합니다.
```
{{ SET NUMBERS {{ LIST 3 1 4 1 5 9 }} }}
{{ LIST::SORT $NUMBERS }}          // [1, 1, 3, 4, 5, 9]
{{ LIST::SORT $NUMBERS TRUE }}     // [9, 5, 4, 3, 1, 1]
```

### `{{ LIST::SORTED @LIST [@REVERSE = FALSE] }}`
  > @LIST: `LIST`
  > @REVERSE: `BOOL`
  > 반환 타입: `LIST`

정렬된 새로운 리스트를 반환합니다. 원본 리스트는 변경되지 않습니다.
```
{{ SET ORIGINAL {{ LIST 3 1 4 1 5 }} }}
{{ SET SORTED {{ LIST::SORTED $ORIGINAL }} }}
// SORTED는 [1, 1, 3, 4, 5], ORIGINAL은 [3, 1, 4, 1, 5]
```

### `{{ LIST::WHERE @LIST @ELEMENT [@START = 0] [@END = -1] }}`
  > @LIST: `LIST`
  > @ELEMENT: `DYNAMIC`
  > @START: `NUM`
  > @END: `NUM`
  > 반환 타입: `LIST` 

지정된 범위에서 특정 요소가 있는 모든 인덱스를 반환합니다.
```
{{ SET ITEMS {{ LIST "a" "b" "a" "c" "a" }} }}
{{ LIST::WHERE $ITEMS "a" }}      // [0, 2, 4]
{{ LIST::WHERE $ITEMS "a" 1 3 }}  // [2] (인덱스 1~3 범위에서)
```

### `{{ LIST::COUNT @LIST @ELEMENT [@START = 0] [@END = -1] }}`
  > @LIST: `LIST`
  > @ELEMENT: `DYNAMIC`
  > @START: `NUM`
  > @END: `NUM`
  > 반환 타입: `NUM` 

지정된 범위에서 특정 요소의 개수를 세어 반환합니다.
```
{{ SET LETTERS {{ LIST "a" "b" "a" "c" "a" "b" }} }}
{{ LIST::COUNT $LETTERS "a" }}      // 3
{{ LIST::COUNT $LETTERS "b" 0 3 }}  // 1 (인덱스 0~3 범위에서)
```

### `{{ LIST::JOIN @LIST @SEPARATOR }}`
  > @LIST: `LIST`
  > @SEPARATOR: `TEXT`
  > 반환 타입: `TEXT`

리스트의 모든 요소를 구분자로 연결한 문자열을 반환합니다.
```
{{ SET WORDS {{ LIST "Hello" "beautiful" "world" }} }}
{{ LIST::JOIN $WORDS " " }}     // "Hello beautiful world"
{{ LIST::JOIN $WORDS ", " }}    // "Hello, beautiful, world"

{{ SET NUMBERS {{ LIST 1 2 3 4 }} }}
{{ LIST::JOIN $NUMBERS "-" }}   // "1-2-3-4"
```

### `{{ LIST::CHOOSE @LIST }}`
  > @LIST: `LIST`
  > 반환 타입: `DYNAMIC`

리스트에서 임의의 요소를 하나 선택하여 반환합니다.
- 빈 리스트의 경우 패닉이 발생합니다.
```
{{ SET OPTIONS {{ LIST "rock" "paper" "scissors" }} }}
{{ LIST::CHOOSE $OPTIONS }}  // "paper" (임의)

{{ SET DICE {{ LIST 1 2 3 4 5 6 }} }}
{{ LIST::CHOOSE $DICE }}     // 4 (임의)
```

### `{{ LIST::COPY @LIST }}`
  > @LIST: `LIST`
  > 반환 타입: `LIST` 

리스트의 복사본을 생성합니다.
```
{{ SET ORIGINAL {{ LIST 1 2 3 }} }}
{{ SET COPY {{ LIST::COPY $ORIGINAL }} }}
{{ LIST::INSERT $COPY 4 }}
// COPY는 [1, 2, 3, 4], ORIGINAL은 [1, 2, 3]으로 유지
``` 

---
## 맵 구문

### `{{ MAP @K1:@V1 @K2:@V2 @K3:@V3 ... }}`
  > @K1, @K2, @K3...: `DYNAMIC`  
  > @V1, @V2, @V3...: `DYNAMIC`  
  > 반환 타입: `MAP`

키-값 쌍들을 이용해 새로운 맵을 생성합니다. 키와 값은 콜론(:)으로 구분합니다.
```
{{ SET USER {{ MAP "name":"Alice" "age":25 "city":"Seoul" }} }}
{{ SET SCORES {{ MAP "math":95 "science":88 "english":92 }} }}
{{ SET EMPTY {{ MAP }} }}
```

### `$MAP[ @KEY ]`
  > @KEY: `DYNAMIC`  
  > 반환 타입: `DYNAMIC`

맵에서 특정 키에 해당하는 값에 접근합니다.
- 키가 존재하지 않으면 패닉이 발생합니다.
```
{{ SET PERSON {{ MAP "name":"Bob" "age":30 "job":"developer" }} }}
{{ $PERSON["name"] }}  // Bob
{{ $PERSON["age"] }}   // 30
{{ $PERSON["job"] }}   // developer
```

### `{{ MAP::SIZE @MAP }}`
  > @MAP: `MAP`  
  > 반환 타입: `NUM`

맵의 키-값 쌍 개수를 반환합니다.
```
{{ SET CONFIG {{ MAP "debug":TRUE "port":8080 "host":"localhost" }} }}
{{ MAP::SIZE $CONFIG }}  // 3
{{ MAP::SIZE {{ MAP }} }}  // 0
```

### `{{ MAP::INSERT @MAP @KEY @VALUE }}`
  > @MAP: `MAP`  
  > @KEY: `DYNAMIC`  
  > @VALUE: `DYNAMIC`  
  > 반환 타입: `VOID`

맵에 새로운 키-값 쌍을 삽입하거나 기존 키의 값을 업데이트합니다. 
새로운 키가 추가되면 `TRUE`, 기존 키가 업데이트되면 `FALSE`를 반환합니다.
```
{{ SET INVENTORY {{ MAP "apples":10 "oranges":5 }} }}
{{ MAP::INSERT $INVENTORY "bananas" 15 }}   // TRUE (새로운 키)
{{ MAP::INSERT $INVENTORY "apples" 12 }}    // FALSE (기존 키 업데이트)
// INVENTORY는 이제 {"apples":12, "oranges":5, "bananas":15}
```

### `{{ MAP::REMOVE @MAP @KEY }}`
  > @MAP: `MAP`  
  > @KEY: `DYNAMIC`  
  > 반환 타입: `VOID`

맵에서 지정된 키와 해당 값을 제거합니다.
- 키가 존재하지 않아도 패닉이 발생하지 않습니다.
```
{{ SET SETTINGS {{ MAP "volume":50 "brightness":80 "contrast":60 }} }}
{{ MAP::REMOVE $SETTINGS "brightness" }}
// SETTINGS는 이제 {"volume":50, "contrast":60}
```

### `{{ MAP::POP @MAP @KEY }}`
  > @MAP: `MAP`  
  > @KEY: `DYNAMIC`  
  > 반환 타입: `DYNAMIC`

맵에서 지정된 키를 제거하고 해당 값을 반환합니다.
- 키가 존재하지 않으면 패닉이 발생합니다.
```
{{ SET CACHE {{ MAP "user1":"Alice" "user2":"Bob" "user3":"Charlie" }} }}
{{ MAP::POP $CACHE "user2" }}  // "Bob" 반환
// CACHE는 이제 {"user1":"Alice", "user3":"Charlie"}
```

### `{{ MAP::EXTEND @MAP @OTHER }}`
  > @MAP: `MAP`  
  > @OTHER: `MAP`  
  > 반환 타입: `VOID`

`@MAP`에 `@OTHER`의 모든 키-값 쌍을 추가합니다. 중복되는 키는 `@OTHER`의 
값으로 대체합니다.
```
{{ SET MAP1 {{ MAP "a":1 "b":2 }} }}
{{ SET MAP2 {{ MAP "b":3 "c":4 }} }}
{{ MAP::EXTEND $MAP1 $MAP2 }} 

{{ $MAP1 }} // {"a":1, "b":3, "c":4}
```

### `{{ MAP::CONCAT @MAP1 @MAP2 }}`
  > @MAP1: `MAP`  
  > @MAP2: `MAP`  
  > 반환 타입: `MAP`

두 맵을 병합한 새로운 맵을 반환합니다. 중복되는 키는 두 번째 맵의 값을 사용합니다. 원본 맵들은 변경되지 않습니다.
```
{{ SET A {{ MAP "x":10 "y":20 }} }}
{{ SET B {{ MAP "y":30 "z":40 }} }}
{{ SET C {{ MAP::CONCAT $A $B }} }}  // {"x":10, "y":30, "z":40}
// A와 B는 그대로 유지됨
```

### `{{ MAP::HAS @MAP @KEY }}`
  > @MAP: `MAP`  
  > @KEY: `DYNAMIC`  
  > 반환 타입: `BOOL`

맵에 특정 키가 존재하는지 확인합니다.
```
{{ SET DATA {{ MAP "username":"admin" "password":"secret" "role":"admin" }} }}
{{ MAP::HAS $DATA "username" }}  // TRUE
{{ MAP::HAS $DATA "email" }}     // FALSE
```

### `{{ MAP::CONTAINS @MAP @VALUE }}`
  > @MAP: `MAP`  
  > @VALUE: `DYNAMIC`  
  > 반환 타입: `BOOL`

맵에 특정 값이 포함되어 있는지 확인합니다.
```
{{ SET COLORS {{ MAP "red":"#FF0000" "green":"#00FF00" "blue":"#0000FF" }} }}
{{ MAP::CONTAINS $COLORS "#FF0000" }}  // TRUE
{{ MAP::CONTAINS $COLORS "#FFFF00" }}  // FALSE
```

### `{{ MAP::CLEAR @MAP }}`
  > @MAP: `MAP`  
  > 반환 타입: `VOID`

맵의 모든 키-값 쌍을 제거합니다.
```
{{ SET SESSION {{ MAP "token":"abc123" "expires":3600 "user":"john" }} }}
{{ MAP::CLEAR $SESSION }}
{{ MAP::SIZE $SESSION }}  // 0
```

### `{{ MAP::KEYS @MAP }}`
  > @MAP: `MAP`  
  > 반환 타입: `LIST`

맵의 모든 키를 리스트로 반환합니다.
```
{{ SET GRADES {{ MAP "Alice":95 "Bob":87 "Charlie":92 }} }}
{{ MAP::KEYS $GRADES }}  // ["Alice", "Bob", "Charlie"]
```

### `{{ MAP::VALUES @MAP }}`
  > @MAP: `MAP`  
  > 반환 타입: `LIST`

맵의 모든 값을 리스트로 반환합니다.
```
{{ SET PRICES {{ MAP "apple":1200 "banana":800 "cherry":2500 }} }}
{{ MAP::VALUES $PRICES }}  // [1200, 800, 2500]
```

### `{{ MAP::COPY @MAP }}`
  > @MAP: `MAP`  
  > 반환 타입: `MAP`

맵의 복사본을 생성합니다.
```
{{ SET ORIGINAL {{ MAP "name":"Alice" "age":25 }} }}
{{ SET COPY {{ MAP::COPY $ORIGINAL }} }}
{{ MAP::INSERT $COPY "city" "Seoul" }}
// COPY는 {"name":"Alice", "age":25, "city":"Seoul"}
// ORIGINAL은 {"name":"Alice", "age":25}로 유지
```

---
## 유틸리티 구문

### `{{ ARK }} ... {{ END }}`
  > 반환 타입: `VOID`

블록 내부를 실행하고 발생한 모든 출력값을 무시합니다.
```
{{ ARK }}
  Hello, World!
{{ END }} // (nothing)
```

### `{{ RANGE @START @END [@STEP = 1] }}`
  > @START: `NUM`  
  > @END: `NUM`  
  > @STEP: `NUM`  
  > 반환 타입: `LIST`

`@START`에서 `@END`까지 `@STEP` 간격으로 수열을 생성하여 리스트로 반환합니다.
- `@START`, `@END`, `@STEP`이 `NUM` 타입이 아닌 경우 패닉이 발생합니다.
- `@STEP`이 0인 경우 패닉이 발생합니다.
```
{{ RANGE 1 5 }}    // [1, 2, 3, 4]
{{ RANGE 0 10 2 }} // [0, 2, 4, 6, 8]
{{ RANGE 5 1 -1 }} // [5, 4, 3, 2]
```

### `{{ UNIXTIME }}`
  > 반환 타입: `NUM`

유닉스 시간을 반환합니다.
```
{{ UNIXTIME }} // 1755000000
```

### `{{ DATETIME @FORMAT [@SOURCE = -1] [@TIMEZONE = "LO"] }}`
  > @FORMAT: `TEXT`
  > @SOURCE: `NUM`
  > @TIMEZONE: `TEXT`
  > 반환 타입: `TEXT`

유닉스 시간(`@SOURCE`)을 C89 표준에 기반한 `@FORMAT`에 따라 변환합니다.
`@TIMEZONE`의 값은 `UTC`, `LO` 또는 IANA Timezone 형식을 따릅니다.
- `@SOURCE`가 `NUM`타입이 아닌 경우 패닉이 발생합니다.
- `@SOURCE`가 음수인 경우 현재 유닉스 시간을 사용합니다.
- `@TIMEZONE`의 값이 `UTC`인 경우 협정 세계시를, `LO`인 경우 지역시를 따릅니다. 
```
{{ DATETIME "%Y-%m-%d %H:%M:%S" 86399 }}              // 1970-01-01 23:59:59
{{ DATETIME "%Y-%m-%d %H:%M:%S" 86399 "LO" }}         // 1970-01-02 08:59:59
{{ DATETIME "%Y-%m-%d %H:%M:%S" 86399 "Asia/Seoul" }} // 1970-01-02 08:59:59
```

### `{{ RAND @MIN @MAX }}`
  > @MIN: `NUM`  
  > @MAX: `NUM`  
  > 반환 타입: `NUM`

`@MIN`과 `@MAX` 사이의 임의의 실수(Real number)를 반환합니다.
- `@MIN` 또는 `@MAX`가 `NUM` 타입이 아닌 경우 패닉이 발생합니다.
```
{{ RAND 0 1 }}    // 0.23934778070964846
{{ RAND -10 10 }} // -8.52502725806918
```

### `{{ RANDINT @MIN @MAX }}`
  > @MIN: `NUM`  
  > @MAX: `NUM`  
  > 반환 타입: `NUM`

  > @MIN: `NUM`  
  > @MAX: `NUM`  
  > 반환 타입: `NUM`

`@MIN`과 `@MAX` 사이의 임의의 정수를 반환합니다.
- `@MIN` 또는 `@MAX`가 `NUM` 타입이 아닌 경우 패닉이 발생합니다.
- `@MIN` 또는 `@MAX`가 정수가 아닌 경우 소수점은 무시됩니다.
```
{{ RANDINT 0 1000 }}  // 791
{{ RANDINT -10 10 }} // -3
```

---
## 디버깅용 구문

### `{{ // @COMMENT }}`
  > @COMMENT: `EXPRESSION`
  > 반환 타입: `VOID`

한 줄 주석입니다. 어떠한 영향도 끼칠 수 없습니다.

### `{{ /* }} ... {{ END }}`
  > 반환 타입: `VOID`

블록 주석입니다. 어떠한 영향도 끼칠 수 없습니다.

### `{{ PANIC @MESSAGE }}`
  > @MESSAGE: `TEXT`  
  > 반환 타입: `VOID`

패닉을 임의로 발생시킵니다.
- 디버깅 목적으로만 사용하세요.
```
{{ PANIC "Panic!" }} // PANIC: Panic!
```

### `{{ SUPPRESS }} ... {{ END }}`
  > 반환 타입: `VOID`

블록 내부에 발생한 패닉을 무시합니다. 패닉이 발생한 경우 
블록 내부를 탈출하고 더 이상 실행되지 않습니다.
- SUPPRESS 블록 안에서만 패닉을 막을 수 있습니다.
```
{{ SUPPRESS }}
    {{ PANIC "Panic!" }}
    is running?
{{ END }} // (nothing)
```

### `{{ DEBUG }} ... {{ END }}`
  > 반환 타입: `VOID`

디버그 메세지를 출력합니다. 디버그 메세지는 출력에 포함되지 않습니다.
- 디버그 모드가 사용 가능한 경우에만 출력됩니다.
```
{{ DEBUG }} Hello, Debugger! {{ END }} // [00:00:00][DEBUG] Hello, Debugger!
{{ DEBUG }} {{ CALC 1 + 1 }} {{ END }} // [00:00:00][DEBUG] <NUM>2
```
