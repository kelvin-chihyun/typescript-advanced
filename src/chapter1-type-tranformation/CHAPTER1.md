# Chapter 1: Type Transformations (타입 변환)

TypeScript의 타입 변환과 조작 기법들을 체계적으로 학습하는 챕터입니다. 타입 시스템의 핵심 개념부터 고급 패턴까지 단계별로 익힐 수 있습니다.

## 📚 학습 목표

- TypeScript의 타입 공간과 값 공간 이해
- 함수 타입에서 정보 추출하기
- 유니온 타입 조작 기법 마스터
- 인덱스 접근을 통한 타입 추출
- 템플릿 리터럴 타입 활용
- 키 재매핑을 통한 타입 변환

## 🎯 핵심 개념

### 00-basic: 타입-값 공간의 이해

**핵심 개념:**
- **타입 공간 (Type Space)**: 컴파일 타임에만 존재하는 타입 정보
- **값 공간 (Value Space)**: 런타임에 실제로 존재하는 값들

**주요 기법:**
```typescript
// 값에서 타입 추출
const v = typeof p;        // 런타임: "object"
type t = typeof p;         // 컴파일타임: Person

// 인덱스 접근
const first = p['first'];  // 값 접근
type First = Person['first']; // 타입 접근
```

**학습 포인트:**
- `typeof`의 이중 역할 (런타임 vs 컴파일타임)
- 객체 속성의 값 접근과 타입 접근의 차이
- 타입 시스템의 기본 원리 이해

### 01-function: 함수 타입에서 정보 추출

**핵심 개념:**
- 함수의 반환 타입과 매개변수 타입을 추출하는 유틸리티 타입들

**주요 기법:**
```typescript
// ReturnType<T>: 함수의 반환 타입 추출
type MyFuncReturn = ReturnType<typeof myFunc>; // string

// Parameters<T>: 함수의 매개변수 타입 추출
type MyFuncParams = Parameters<typeof myFunc>; // []

// Promise 함수의 반환 타입 추출
type AsyncReturn = Awaited<ReturnType<typeof asyncFunc>>;
```

**학습 포인트:**
- `ReturnType<T>` 유틸리티 타입 활용
- `Parameters<T>` 유틸리티 타입 활용
- 비동기 함수의 반환 타입 처리
- 함수 타입에서 정보를 추출하는 패턴

### 02-union: 유니온 타입 조작

**핵심 개념:**
- 유니온 타입에서 특정 타입을 추출하거나 제외하는 기법

**주요 기법:**
```typescript
// Discriminated Union (태그된 유니온)
type Shape = Square | Rectangle;

// Extract<T, U>: 유니온에서 특정 타입 추출
type ClickEvent = Extract<Event, { type: "click" }>;

// Exclude<T, U>: 유니온에서 특정 타입 제외
type NonClickEvents = Exclude<Event, { type: "click" }>;
```

**학습 포인트:**
- Discriminated Union 패턴의 이해
- `Extract` 유틸리티 타입으로 타입 필터링
- `Exclude` 유틸리티 타입으로 타입 제외
- 유니온 타입의 체계적 조작

### 03-indexing: 인덱스 접근 타입

**핵심 개념:**
- 객체나 배열의 인덱스를 통해 타입 정보를 추출하는 기법

**주요 기법:**
```typescript
// 객체 인덱스 접근
type FooType = typeof fakeDataDefaults['foo']; // string

// 배열 인덱스 접근
type FirstElement = ArrayType[0];

// as const와 함께 사용
const Color = { Red: "red", Green: "green" } as const;
type Red = typeof Color['Red']; // "red" (리터럴 타입)
```

**학습 포인트:**
- 객체 속성의 타입 추출
- 배열 요소의 타입 추출
- `as const`의 리터럴 타입 보존 효과
- enum vs as const 객체의 차이점
- Discriminated Union의 인덱스 접근

### 04-template-literals: 템플릿 리터럴 타입

**핵심 개념:**
- 문자열 패턴을 타입으로 정의하고 조작하는 기법

**주요 기법:**
```typescript
// 기본 문자열 패턴
type Path = `/${string}`;

// 문자열 패턴 추출
type ExtractPattern<T> = T extends `${infer Start}${infer Rest}` ? Start : never;

// 유니온과 템플릿 리터럴
type Routes = "/" | "/about" | "/admin";
type ApiRoutes = `${Routes}/api`;

// 객체 키 템플릿 리터럴
type EventHandlers = {
    [K in `on${Capitalize<string>}`]: () => void;
};
```

**학습 포인트:**
- 문자열 패턴 매칭
- `infer` 키워드로 패턴 추출
- 유니온 타입과 템플릿 리터럴 조합
- 객체 키에 템플릿 리터럴 적용
- 대소문자 변환 유틸리티 타입

### 05-key-remapping: 키 재매핑

**핵심 개념:**
- 객체의 키를 변환하여 새로운 타입을 만드는 고급 기법

**주요 기법:**
```typescript
// 유니온을 객체로 변환
type RoutesObject = {
    [K in Route]: K;
};

// keyof 활용
type ObjectKeys<T> = keyof T;

// 객체를 튜플로 변환
type ObjectToTuple<T> = {
    [K in keyof T]: [K, T[K]];
}[keyof T];

// 객체를 템플릿 리터럴로 변환
type ObjectToTemplate<T> = {
    [K in keyof T]: `${string & K}:${string & T[K]}`;
};
```

**학습 포인트:**
- `in` 키워드로 키 순회
- `keyof` 연산자의 활용
- 유니온 타입을 객체로 변환
- 객체를 다른 형태로 변환
- Discriminated Union의 키 재매핑

## 🔧 실전 활용 패턴

### 1. API 응답 타입 추론
```typescript
type ApiResponse<T> = {
    data: T;
    status: number;
    message: string;
};

type UserResponse = ApiResponse<User>;
type UserData = UserResponse['data']; // User 타입 추출
```

### 2. 이벤트 핸들러 타입 생성
```typescript
type Events = 'click' | 'focus' | 'blur';
type EventHandlers = {
    [K in `on${Capitalize<Events>}`]: (event: Event) => void;
};
```

### 3. 라우트 타입 안전성
```typescript
type Routes = '/' | '/about' | '/admin';
type ValidPath = `/${string}`;

function navigate(path: Routes) {
    // 타입 안전한 라우팅
}
```

## 📝 학습 체크리스트

- [ ] 타입 공간과 값 공간의 차이점 이해
- [ ] `typeof`의 이중 역할 파악
- [ ] `ReturnType<T>`, `Parameters<T>` 활용
- [ ] Discriminated Union 패턴 이해
- [ ] `Extract<T, U>`, `Exclude<T, U>` 활용
- [ ] 인덱스 접근 타입으로 속성 타입 추출
- [ ] `as const`의 리터럴 타입 보존 효과
- [ ] 템플릿 리터럴 타입으로 문자열 패턴 정의
- [ ] `infer` 키워드로 패턴 추출
- [ ] 키 재매핑으로 객체 타입 변환
- [ ] `keyof`와 `in` 키워드 조합 활용

## 🎯 다음 단계

이 챕터에서 학습한 타입 변환 기법들은 다음 챕터들의 기초가 됩니다:
- **Chapter 2**: 제네릭과 조건부 타입에서 활용
- **Chapter 3**: 고급 패턴에서 복합적 활용
- **Chapter 4**: 최신 TypeScript 기능과 결합

각 기법을 개별적으로 이해한 후, 실전 문제들을 통해 조합적으로 활용하는 연습을 권장합니다.