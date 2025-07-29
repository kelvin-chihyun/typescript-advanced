# Chapter 2: Generics (제네릭)

TypeScript 제네릭의 기초부터 고급 패턴까지 체계적으로 학습하는 챕터입니다. 타입 안전성을 유지하면서 재사용 가능한 코드를 작성하는 방법을 익힙니다.

## 📚 학습 목표

- 제네릭의 기본 개념과 활용법 이해
- 제네릭 제약 조건과 타입 제약 마스터
- 조건부 타입과 infer 키워드 활용
- 다양한 레벨의 제네릭 패턴 학습
- 고급 제네릭 패턴과 함수 오버로드 이해
- 실전 문제를 통한 제네릭 활용 능력 향상

## 🎯 핵심 개념

### 01-generic-basic: 제네릭 기초

**핵심 개념:**
- 제네릭을 통한 타입 안전한 재사용 가능한 코드 작성
- 제네릭 제약 조건으로 타입 범위 제한

**주요 기법:**
```typescript
// 기본 제네릭 함수
const returnWhatIPassIn = <T>(t: T) => t;

// 제네릭 제약 조건
const onlyStrings = <T extends string>(t: T) => t;

// 제네릭 클래스
class Container<T> {
    constructor(private value: T) {}
    getValue(): T { return this.value; }
}

// Maybe 타입 (null/undefined 제외)
type Maybe<T> = T extends null | undefined ? never : T;

// 비어있지 않은 배열
type NonEmptyArray<T> = [T, ...T[]];
```

**학습 포인트:**
- 제네릭 함수의 타입 추론
- `extends` 키워드로 제약 조건 설정
- 제네릭 클래스와 인터페이스
- 유틸리티 타입 구현 (Maybe, NonEmptyArray)
- 제네릭 reduce 함수와 fetch 함수

### 02-conditional-type: 조건부 타입

**핵심 개념:**
- 조건에 따라 다른 타입을 반환하는 조건부 타입
- `infer` 키워드로 타입 정보 추출

**주요 기법:**
```typescript
// 기본 조건부 타입
type YouSayGoodbyeAndISayHello<T> = 
    T extends "hello" ? "goodbye" : "hello";

// infer로 타입 추출
type GetDataValue<T> = T extends { data: infer U } ? U : never;

// 키 재매핑과 조건부 타입
type EventHandlers<T> = {
    [K in keyof T as K extends `on${infer Event}` 
        ? Event 
        : never]: T[K];
};

// 템플릿 리터럴과 infer
type ExtractFirst<T> = T extends `${infer First}${string}` ? First : never;

// 유니온 타입에서 infer
type ExtractUnion<T> = T extends (infer U)[] ? U : never;
```

**학습 포인트:**
- 삼항 연산자를 이용한 조건부 타입
- `infer` 키워드의 다양한 활용
- 키 재매핑과 조건부 타입 조합
- 템플릿 리터럴에서 패턴 추출
- 유니온 타입에서 타입 추출

### 03-type-arguments: 타입 인자

**핵심 개념:**
- 다양한 레벨에서 제네릭을 활용하는 패턴
- 복잡한 객체 구조에서 타입 안전성 확보

**주요 기법:**
```typescript
// 중첩된 제네릭 함수
const getHomePageFeatureFlags = <
    TConfig extends { rawConfig: { featureFlags: { homePage: any } } },
    TOverride extends (flags: any) => any
>(
    config: TConfig,
    override: TOverride
) => {
    return override(config.rawConfig.featureFlags.homePage);
};

// 객체 키 타입 추출
type ObjectKeys<T> = keyof T;

// 결과 타입 추론
type ResultType<T> = T extends (...args: any[]) => infer R ? R : never;

// 문자열-숫자 유니온 처리
type StringOrNumber<T> = T extends string | number ? T : never;
```

**학습 포인트:**
- 복잡한 제네릭 함수 시그니처
- 중첩된 객체 구조의 타입 안전성
- 함수 결과 타입의 정확한 추론
- 다양한 타입 인자 패턴

### 04-generics-advanced: 고급 제네릭

**핵심 개념:**
- 제네릭의 고급 패턴과 실전 활용법
- 복잡한 타입 변환과 매핑

**주요 기법:**
```typescript
// 제네릭 커링
type Curry<T extends (...args: any[]) => any> = 
    T extends (first: infer First, ...rest: infer Rest) => infer Return
        ? Rest extends []
            ? T
            : (first: First) => Curry<(...args: Rest) => Return>
        : never;

// 제네릭 인터페이스와 함수
interface DataFetcher<TData, TParams> {
    fetch(params: TParams): Promise<TData>;
    transform?(data: TData): TData;
}

// 누락된 제네릭 식별
type MissingGenerics<T> = T extends any[] 
    ? T extends (infer U)[] 
        ? U extends any 
            ? U 
            : never 
        : never 
    : never;
```

**학습 포인트:**
- 제네릭 커링 패턴
- 제네릭 인터페이스 설계
- 복잡한 타입 매핑
- 제네릭 누락 감지

### 05-function-overload: 함수 오버로드

**핵심 개념:**
- 함수 오버로드를 통한 정확한 타입 추론
- 제네릭과 오버로드의 조합

**주요 기법:**
```typescript
// 기본 함수 오버로드
function returnWhatIPassIn(t: 1): 1;
function returnWhatIPassIn(t: "hello"): "hello";
function returnWhatIPassIn(t: unknown) {
    return t;
}

// 제네릭 함수 오버로드
function processData<T extends string>(data: T): `processed_${T}`;
function processData<T extends number>(data: T): T;
function processData(data: any): any {
    if (typeof data === "string") {
        return `processed_${data}`;
    }
    return data;
}

// 데이터 훅 패턴
function useData<T>(fetcher: () => Promise<T>): {
    data: T | undefined;
    loading: boolean;
    error: Error | undefined;
};
```

**학습 포인트:**
- 함수 오버로드 vs 조건부 타입
- 제네릭 오버로드 구현
- 실전 패턴 (데이터 훅, 서브타입 인스턴스화)
- 오버로드 구현의 정확성

### challenge: 실전 도전 과제

**핵심 개념:**
- 복합적인 제네릭 패턴을 활용한 실전 문제 해결

**주요 기법:**
```typescript
// 무한 스크롤 구현
const makeInfiniteScroll = <T extends Record<string, any>, K extends keyof T>(
    params: {
        key: K;
        initialRows?: T[];
        fetchRows: () => Promise<T[]>;
    }
) => {
    // 구현...
};

// 동적 함수 인자
const createDynamicFunction = <T extends any[]>(
    ...args: T
) => {
    // 구현...
};

// 폼 검증기
const createFormValidator = <T extends Record<string, any>>(
    schema: ValidationSchema<T>
) => {
    // 구현...
};

// 함수 합성
const compose = <T extends any[], U, V>(
    f: (x: U) => V,
    g: (...args: T) => U
) => {
    // 구현...
};
```

**학습 포인트:**
- 복잡한 제네릭 제약 조건
- 동적 타입 생성
- 실전 애플리케이션 패턴
- 타입 안전한 API 설계

## 🔧 실전 활용 패턴

### 1. API 클라이언트 제네릭화
```typescript
class ApiClient<TData, TParams> {
    async fetch(params: TParams): Promise<TData> {
        // API 호출 로직
    }
    
    transform<R>(transformer: (data: TData) => R): ApiClient<R, TParams> {
        // 변환 로직
    }
}
```

### 2. 상태 관리 제네릭 패턴
```typescript
type StateMachine<TState extends string, TEvent extends string> = {
    state: TState;
    transition: (event: TEvent) => StateMachine<TState, TEvent>;
};
```

### 3. 유틸리티 함수 제네릭화
```typescript
const createSelector = <TState, TResult>(
    selector: (state: TState) => TResult
) => {
    return (state: TState) => selector(state);
};
```

## 📝 학습 체크리스트

- [ ] 제네릭 함수의 기본 문법 이해
- [ ] 제네릭 제약 조건 (`extends`) 활용
- [ ] 제네릭 클래스와 인터페이스 구현
- [ ] 조건부 타입 (`T extends U ? X : Y`) 활용
- [ ] `infer` 키워드로 타입 추출
- [ ] 복잡한 제네릭 함수 시그니처 작성
- [ ] 함수 오버로드 vs 조건부 타입 선택
- [ ] 제네릭 커링 패턴 이해
- [ ] 실전 문제에서 제네릭 패턴 적용
- [ ] 타입 안전한 API 설계

## 🎯 고급 패턴

### 1. 제네릭 제약 조건 체이닝
```typescript
type DeepPartial<T> = {
    [P in keyof T]?: T[P] extends object ? DeepPartial<T[P]> : T[P];
};
```

### 2. 조건부 타입과 매핑 타입 조합
```typescript
type ConditionalKeys<T, U> = {
    [K in keyof T]: T[K] extends U ? K : never;
}[keyof T];
```

### 3. 재귀적 제네릭 타입
```typescript
type RecursiveArray<T> = T | RecursiveArray<T>[];
```

## 🎯 다음 단계

이 챕터에서 학습한 제네릭 기법들은 다음 챕터들의 기초가 됩니다:
- **Chapter 3**: 고급 패턴에서 제네릭과 브랜드 타입, 타입 술어 조합
- **Chapter 4**: 최신 TypeScript 기능과 제네릭 결합

제네릭은 TypeScript의 핵심 기능으로, 실전에서 타입 안전성을 확보하면서 재사용 가능한 코드를 작성하는 데 필수적입니다. 각 패턴을 개별적으로 이해한 후, 복합적으로 활용하는 연습을 권장합니다.