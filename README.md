# TypeScript Advanced Course

[본 게시물은 파트너스 활동의 일환으로 소정의 수수료를 받을 수 있습니다.]

이 저장소는 [실전 연습으로 익히는 고급 타입스크립트 기술](https://inf.run/C9TFT) 의 소스코드로, TypeScript의 고급 기능들을 체계적으로 학습하기 위한 실습 코드를 포함하고 있습니다. 
각 챕터는 특정 주제에 집중하여 TypeScript의 강력한 타입 시스템을 마스터할 수 있도록 구성되어 있습니다.

## 📚 학습 내용

### Chapter 1: Type Transformations (타입 변환)

TypeScript의 타입 변환과 조작 기법들을 학습합니다.

#### 00-basic
- **Type-Value Space**: 타입 공간과 값 공간의 차이점 이해
- **typeof 연산자**: 런타임과 컴파일타임에서의 typeof 활용

#### 01-function
- **Function Return Types**: `ReturnType<T>` 유틸리티 타입
- **Function Parameter Types**: `Parameters<T>` 유틸리티 타입
- **Promise Return Types**: 비동기 함수의 반환 타입 추론

#### 02-union
- **Discriminated Unions**: 태그된 유니온 타입 패턴
- **Extract**: 유니온 타입에서 특정 타입 추출
- **Exclude**: 유니온 타입에서 특정 타입 제외

#### 03-indexing
- **Index Access Types**: 객체 타입의 인덱스 접근
- **Discriminated Index Access**: 태그된 유니온의 인덱스 접근
- **as const**: 리터럴 타입 보존
- **Object Values**: 객체 값들의 유니온 타입 추출
- **Array Indexing**: 배열 타입의 인덱스 접근

#### 04-template-literals
- **String Pattern Matching**: 문자열 패턴 매칭
- **Extract String Patterns**: 문자열에서 패턴 추출
- **Union Template Literals**: 유니온 타입과 템플릿 리터럴
- **Object Key Template Literals**: 객체 키에 템플릿 리터럴 적용
- **Uppercase/Lowercase**: 문자열 대소문자 변환

#### 05-key-remapping
- **Union to Object**: 유니온 타입을 객체로 변환
- **Keyof**: 객체의 키 타입 추출
- **Object to Tuple**: 객체를 튜플로 변환
- **Object to Template Literals**: 객체를 템플릿 리터럴로 변환

### Chapter 2: Generics (제네릭)

TypeScript 제네릭의 기초부터 고급 패턴까지 학습합니다.

#### 01-generic-basic
- **Basic Generics**: 제네릭 함수와 타입의 기본 개념
- **Generic Constraints**: 제네릭 제약 조건
- **Generics with Objects**: 객체와 제네릭
- **Generic Classes**: 제네릭 클래스
- **Type Constraints**: 타입 제약 조건
- **Maybe Type**: 옵셔널 타입 구현
- **Non-empty Array**: 비어있지 않은 배열 타입
- **Reduce Function**: 제네릭 reduce 함수
- **Fetch Function**: 제네릭 fetch 함수

#### 02-conditional-type
- **Basic Conditional Types**: 조건부 타입의 기본
- **Returning Never**: never 타입 반환
- **Key Remapping**: 키 재매핑
- **Infer Keyword**: infer 키워드 활용
- **Infer with Generics**: 제네릭과 infer
- **Template Literals with Infer**: 템플릿 리터럴과 infer
- **Async Function Result**: 비동기 함수 결과 추론
- **Infer in Union Types**: 유니온 타입에서의 infer

#### 03-type-arguments
- **Generics at Different Levels**: 다양한 레벨의 제네릭
- **Object Keys**: 객체 키 타입
- **Result Type**: 결과 타입 추론
- **String Number Union**: 문자열-숫자 유니온
- **Array Member Generics**: 배열 멤버 제네릭

#### 04-generics-advanced
- **Advanced Generic Patterns**: 고급 제네릭 패턴
- **Generic Currying**: 제네릭 커링
- **Generic Interfaces with Functions**: 함수가 있는 제네릭 인터페이스
- **Missing Generics**: 누락된 제네릭 식별

#### 05-function-overload
- **Function Overloads**: 함수 오버로드
- **Overloads vs Conditional Types**: 오버로드 vs 조건부 타입
- **Overload Implementation**: 오버로드 구현
- **Generics in Overloads**: 오버로드의 제네릭
- **Data Hook**: 데이터 훅 패턴
- **Subtype Instantiation**: 서브타입 인스턴스화

#### challenge
- **Infinite Scroll**: 무한 스크롤 구현
- **Dynamic Function Arguments**: 동적 함수 인자
- **Pick Utility**: Pick 유틸리티 타입
- **Form Validator**: 폼 검증기
- **Data Fetcher with Warning**: 경고가 있는 데이터 페처
- **Compose Function**: 함수 합성
- **Internationalization**: 국제화

### Chapter 3: Advanced Patterns (고급 패턴)

TypeScript의 고급 패턴과 실전 활용법을 학습합니다.

#### 01-branded-types
- **Branded Types**: 브랜드 타입 패턴
- **Form Validation**: 폼 검증에 브랜드 타입 적용
- **Entity Fetching**: 엔티티 페칭
- **Reusable Valid Brand**: 재사용 가능한 유효 브랜드
- **Index Signatures**: 인덱스 시그니처

#### 02-globals
- **Global Scope Functions**: 전역 스코프 함수 추가
- **Window Object**: Window 객체 확장
- **Process Environment**: Process 환경 변수
- **Event Dispatcher**: 이벤트 디스패처

#### 03-type-predicates-assertion-functions
- **Type Predicates with Filter**: 필터와 타입 술어
- **Assertion Functions**: 단언 함수
- **TypeScript's Worst Error**: TypeScript의 최악의 에러
- **Type Predicates with Generics**: 제네릭과 타입 술어
- **Brands and Type Predicates**: 브랜드와 타입 술어
- **Brands and Assertion Functions**: 브랜드와 단언 함수

#### 04-classes
- **Type Predicates and Classes**: 클래스와 타입 술어
- **Assertion Functions and Classes**: 클래스와 단언 함수
- **Type Safe Map**: 타입 안전한 Map

#### 05-external-libraries
- **Lodash GroupBy**: Lodash의 groupBy
- **Override External Lib Types**: 외부 라이브러리 타입 오버라이드

#### 06-identity-functions
- **Const Annotations**: const 어노테이션
- **Constraints with Const Annotations**: const 어노테이션과 제약 조건
- **No Generics on Objects**: 객체에 제네릭 없이
- **Reverse Mapped Types**: 역방향 매핑 타입

#### 07-challenges
- **Merge Dynamic Object with Global**: 동적 객체와 전역 병합
- **Narrow with Arrays**: 배열로 타입 좁히기
- **Zod with Express**: Zod와 Express
- **Dynamic Reducer**: 동적 리듀서
- **Custom JSX Element**: 커스텀 JSX 엘리먼트

### Chapter 4: Latest TypeScript (최신 TypeScript)

TypeScript의 최신 기능들을 학습합니다.

#### satisfies 연산자
- **Strong Type Checking**: 강력한 타입 검사
- **Tuple Types**: 튜플 타입과 satisfies
- **Navigation Elements**: 네비게이션 엘리먼트

#### decorator
- **Class Decorators**: 클래스 데코레이터
- **Field Decorators**: 필드 데코레이터
- **Method Decorators**: 메서드 데코레이터
- **Observable Pattern**: 옵저버블 패턴

## 🛠️ 사용된 도구

- **TypeScript**: 최신 TypeScript 기능 활용
- **Vitest**: 테스트 프레임워크
- **Helper Types**: 커스텀 헬퍼 타입들 (`Equal`, `Expect`, `Brand` 등)

## 📁 프로젝트 구조

```
src/
├── chapter1-type-tranformation/    # 타입 변환 기법
├── chapter2-generic/               # 제네릭 심화
├── chapter3-advanced-pattern/      # 고급 패턴
├── chapter4-latest-typescript/     # 최신 TypeScript 기능
└── helper.ts                       # 공통 헬퍼 타입들
```

## 🚀 시작하기

1. 저장소를 클론합니다
2. 의존성을 설치합니다: `npm install`
3. 각 챕터의 예제 파일들을 순서대로 학습합니다
4. 문제 파일(`*.problem.ts`)을 먼저 풀어보고, 해답(`*.solution.ts`)과 비교해보세요

## 📝 학습 팁

- 각 챕터는 기초부터 고급까지 순차적으로 구성되어 있습니다
- 문제 파일을 먼저 풀어보고 해답을 확인하는 것이 효과적입니다
- TypeScript 컴파일러의 에러 메시지를 주의 깊게 읽어보세요
- 실습 코드를 직접 수정하고 실행해보면서 학습하세요