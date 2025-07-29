# Chapter 3: Advanced Patterns (고급 패턴)

TypeScript의 고급 패턴과 실전 활용법을 학습하는 챕터입니다. 브랜드 타입, 타입 술어, 단언 함수, 전역 스코프 확장 등 실무에서 유용한 고급 기법들을 익힙니다.

## 📚 학습 목표

- 브랜드 타입을 통한 타입 안전성 강화
- 타입 술어와 단언 함수로 런타임 타입 검증
- 전역 스코프와 외부 라이브러리 타입 확장
- 클래스와 고급 타입 패턴 조합
- 정체성 함수와 const 어노테이션 활용
- 실전 도전 과제를 통한 복합 패턴 적용

## 🎯 핵심 개념

### 01-branded-types: 브랜드 타입

**핵심 개념:**
- 동일한 기본 타입이지만 의미적으로 다른 타입들을 구분하는 패턴
- 타입 안전성을 강화하면서 런타임 오버헤드 없이 타입 검증

**주요 기법:**
```typescript
// 브랜드 타입 정의
type Password = Brand<string, "Password">;
type Email = Brand<string, "Email">;
type UserId = Brand<string, "UserId">;

// 브랜드 타입 객체
type UserObject = Brand<{
    id: string;
    name: string;
}, "User">;

// 폼 검증과 브랜드 타입
const validateValues = (values: { email: string; password: string }) => {
    if (!values.email.includes("@")) {
        throw new Error("Email invalid");
    }
    if (values.password.length < 8) {
        throw new Error("Password not long enough");
    }
    
    return {
        email: values.email as Email,
        password: values.password as Password,
    };
};

// 엔티티 페칭과 브랜드 타입
const fetchUser = async (id: UserId): Promise<UserObject> => {
    // API 호출 로직
};
```

**학습 포인트:**
- 브랜드 타입의 기본 개념과 구현
- 폼 검증에서 브랜드 타입 활용
- 엔티티 페칭에서 타입 안전성 확보
- 재사용 가능한 유효 브랜드 패턴
- 인덱스 시그니처와 브랜드 타입

### 02-globals: 전역 스코프 확장

**핵심 개념:**
- 전역 스코프에 함수와 변수를 추가하여 타입 안전성 확보
- Window 객체와 Process 환경 변수 확장

**주요 기법:**
```typescript
// 전역 함수 추가
declare global {
    function myFunc(): boolean;
    var myVar: number;
}

globalThis.myFunc = () => true;
globalThis.myVar = 1;

// Window 객체 확장
declare global {
    interface Window {
        add: (a: number, b: number) => number;
        subtract: (a: number, b: number) => number;
    }
}

// Process 환경 변수 확장
declare global {
    namespace NodeJS {
        interface ProcessEnv {
            DATABASE_URL: string;
            API_KEY: string;
        }
    }
}

// 이벤트 디스패처
type EventMap = {
    'user:created': { userId: string; name: string };
    'user:deleted': { userId: string };
};

const eventDispatcher = {
    emit<T extends keyof EventMap>(event: T, data: EventMap[T]): void {
        // 이벤트 발생 로직
    }
};
```

**학습 포인트:**
- 전역 스코프에 함수와 변수 추가
- Window 객체 타입 확장
- Process 환경 변수 타입 정의
- 타입 안전한 이벤트 디스패처 구현

### 03-type-predicates-assertion-functions: 타입 술어와 단언 함수

**핵심 개념:**
- 런타임에서 타입을 검증하고 타입 가드를 제공하는 기법
- TypeScript의 타입 시스템과 런타임 검증을 연결

**주요 기법:**
```typescript
// 타입 술어 (Type Predicates)
const isString = (value: unknown): value is string => {
    return typeof value === "string";
};

const isAdmin = (user: User): user is AdminUser => {
    return user.role === "admin";
};

// 필터와 타입 술어 조합
const filteredValues = values.filter((value): value is string => 
    Boolean(value)
);

// 단언 함수 (Assertion Functions)
function assertUserIsAdmin(user: NormalUser | AdminUser): asserts user is AdminUser {
    if (user.role !== "admin") {
        throw new Error("Not an admin user");
    }
}

function assertIsString(value: unknown): asserts value is string {
    if (typeof value !== "string") {
        throw new Error("Value is not a string");
    }
}

// 제네릭과 타입 술어
const isArrayOf = <T>(predicate: (item: unknown) => item is T) => 
    (value: unknown): value is T[] => 
        Array.isArray(value) && value.every(predicate);

// 브랜드 타입과 타입 술어
const isValidEmail = (email: string): email is Email => {
    return email.includes("@") && email.includes(".");
};
```

**학습 포인트:**
- 타입 술어의 기본 문법과 활용
- 단언 함수로 타입 가드 제공
- 필터 함수와 타입 술어 조합
- 제네릭과 타입 술어 결합
- 브랜드 타입과 타입 술어 활용

### 04-classes: 클래스와 고급 타입

**핵심 개념:**
- 클래스에서 고급 타입 패턴을 활용하여 타입 안전성 확보
- 제네릭 클래스와 타입 술어, 단언 함수 조합

**주요 기법:**
```typescript
// 타입 안전한 Map 클래스
class TypeSafeStringMap<TMap extends Record<string, string> = {}> {
    private map: TMap;
    
    constructor() {
        this.map = {} as TMap;
    }
    
    get<K extends keyof TMap>(key: K): TMap[K] {
        return this.map[key];
    }
    
    set<K extends string>(key: K, value: string): TypeSafeStringMap<TMap & Record<K, string>> {
        (this.map as any)[key] = value;
        return this;
    }
}

// 클래스와 타입 술어
class UserManager {
    private users: User[] = [];
    
    addUser(user: User): void {
        this.users.push(user);
    }
    
    getAdminUsers(): AdminUser[] {
        return this.users.filter((user): user is AdminUser => 
            user.role === "admin"
        );
    }
}

// 클래스와 단언 함수
class DataValidator {
    static assertValidUser(user: unknown): asserts user is User {
        if (!user || typeof user !== "object") {
            throw new Error("Invalid user");
        }
        if (!("id" in user) || !("name" in user)) {
            throw new Error("User missing required fields");
        }
    }
}
```

**학습 포인트:**
- 제네릭 클래스로 타입 안전한 컨테이너 구현
- 클래스 메서드에서 타입 술어 활용
- 클래스에서 단언 함수 구현
- 타입 안전한 Map과 Set 구현

### 05-external-libraries: 외부 라이브러리 타입

**핵심 개념:**
- 외부 라이브러리의 타입을 확장하거나 오버라이드하는 기법
- 타입 선언 파일(.d.ts) 활용

**주요 기법:**
```typescript
// Lodash groupBy 타입 개선
const groupByAge = <T extends { age: number }>(array: T[]) => {
    const grouped = _.groupBy(array, "age");
    return grouped;
};

// 외부 라이브러리 타입 오버라이드
declare module "external-lib" {
    interface Config {
        apiKey: string;
        endpoint: string;
    }
    
    function fetchData<T>(config: Config): Promise<T>;
}

// 타입 선언 파일
declare global {
    interface Window {
        externalLib: {
            version: string;
            init(config: any): void;
        };
    }
}
```

**학습 포인트:**
- 외부 라이브러리 타입 확장
- 모듈 선언과 타입 오버라이드
- 타입 선언 파일 작성
- Lodash와 같은 라이브러리 타입 개선

### 06-identity-functions: 정체성 함수

**핵심 개념:**
- 타입 추론을 개선하고 const 어노테이션을 제공하는 함수
- 타입 안전성을 유지하면서 타입 추론 최적화

**주요 기법:**
```typescript
// 기본 정체성 함수
const asConst = <T>(t: T) => t;

// 제약 조건이 있는 정체성 함수
const asConstWithConstraint = <T extends Record<string, any>>(t: T) => t;

// 객체에 제네릭 없는 정체성 함수
const createObject = <T extends Record<string, any>>(obj: T) => obj;

// 역방향 매핑 타입
type ReverseMapped<T> = {
    [K in keyof T as T[K]]: K;
};

// 정체성 함수 활용
const fruits = asConst([
    { name: "apple", price: 1 },
    { name: "banana", price: 2 }
]); // readonly 튜플 타입으로 추론

const config = asConstWithConstraint({
    apiKey: "secret",
    endpoint: "https://api.example.com"
}); // 리터럴 타입 보존
```

**학습 포인트:**
- 정체성 함수의 기본 개념
- const 어노테이션과 타입 추론
- 제약 조건이 있는 정체성 함수
- 역방향 매핑 타입 구현
- 객체 제네릭 패턴

### 07-challenges: 실전 도전 과제

**핵심 개념:**
- 복합적인 고급 패턴을 활용한 실전 문제 해결
- 여러 기법을 조합한 복잡한 타입 시스템 구현

**주요 기법:**
```typescript
// 동적 객체와 전역 병합
const addAllOfThisToWindow = {
    add: (a: number, b: number) => a + b,
    subtract: (a: number, b: number) => a - b,
} as const;

declare global {
    interface Window {
        [K in keyof typeof addAllOfThisToWindow]: typeof addAllOfThisToWindow[K];
    }
}

// 배열로 타입 좁히기
const narrowWithArrays = <T extends readonly any[]>(
    array: T
): T extends readonly [infer First, ...infer Rest] 
    ? [First, ...narrowWithArrays<Rest>]
    : [] => {
    // 구현...
};

// Zod와 Express 통합
const createExpressApp = <TSchema extends z.ZodSchema>(
    schema: TSchema
) => {
    return {
        validate: (data: unknown): z.infer<TSchema> => {
            return schema.parse(data);
        }
    };
};

// 동적 리듀서
const createDynamicReducer = <
    TState,
    TAction extends { type: string; payload?: any }
>(
    initialState: TState,
    reducers: Record<string, (state: TState, payload: any) => TState>
) => {
    return (state: TState, action: TAction): TState => {
        const reducer = reducers[action.type];
        return reducer ? reducer(state, action.payload) : state;
    };
};
```

**학습 포인트:**
- 복합적인 타입 패턴 조합
- 동적 타입 생성과 전역 확장
- 외부 라이브러리와의 타입 통합
- 고급 리듀서 패턴 구현

## 🔧 실전 활용 패턴

### 1. API 클라이언트와 브랜드 타입
```typescript
type ApiResponse<T> = Brand<{
    data: T;
    status: number;
}, "ApiResponse">;

class ApiClient {
    async fetch<T>(url: string): Promise<ApiResponse<T>> {
        const response = await fetch(url);
        const data = await response.json();
        return { data, status: response.status } as ApiResponse<T>;
    }
}
```

### 2. 폼 검증과 타입 안전성
```typescript
type ValidatedForm<T> = {
    [K in keyof T]: Brand<T[K], `Validated_${string & K}`>;
};

const createFormValidator = <T extends Record<string, any>>(
    schema: ValidationSchema<T>
) => {
    return (data: T): ValidatedForm<T> => {
        // 검증 로직
        return data as ValidatedForm<T>;
    };
};
```

### 3. 이벤트 시스템과 타입 안전성
```typescript
type EventMap = {
    'user:created': { userId: string; name: string };
    'user:deleted': { userId: string };
};

class EventEmitter {
    emit<T extends keyof EventMap>(event: T, data: EventMap[T]): void {
        // 이벤트 발생 로직
    }
    
    on<T extends keyof EventMap>(event: T, handler: (data: EventMap[T]) => void): void {
        // 이벤트 리스너 등록
    }
}
```

## 📝 학습 체크리스트

- [ ] 브랜드 타입의 개념과 구현 방법 이해
- [ ] 폼 검증에서 브랜드 타입 활용
- [ ] 타입 술어로 런타임 타입 검증
- [ ] 단언 함수로 타입 가드 제공
- [ ] 전역 스코프에 타입 안전한 함수/변수 추가
- [ ] Window 객체와 Process 환경 변수 확장
- [ ] 제네릭 클래스에서 고급 타입 패턴 활용
- [ ] 외부 라이브러리 타입 확장/오버라이드
- [ ] 정체성 함수로 타입 추론 개선
- [ ] 복합적인 고급 패턴 조합
- [ ] 실전 문제에서 여러 기법 통합 활용

## 🎯 고급 패턴

### 1. 브랜드 타입과 타입 술어 조합
```typescript
type ValidEmail = Brand<string, "ValidEmail">;
type ValidPassword = Brand<string, "ValidPassword">;

const isValidEmail = (email: string): email is ValidEmail => {
    return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email);
};

const isValidPassword = (password: string): password is ValidPassword => {
    return password.length >= 8 && /[A-Z]/.test(password);
};
```

### 2. 제네릭과 단언 함수 조합
```typescript
function assertIsArray<T>(value: unknown): asserts value is T[] {
    if (!Array.isArray(value)) {
        throw new Error("Value is not an array");
    }
}

function assertIsObject<T extends Record<string, any>>(
    value: unknown
): asserts value is T {
    if (!value || typeof value !== "object") {
        throw new Error("Value is not an object");
    }
}
```

### 3. 동적 타입 생성과 전역 확장
```typescript
const createGlobalAPI = <T extends Record<string, any>>(api: T) => {
    Object.assign(globalThis, api);
    
    declare global {
        var [K in keyof T]: T[K];
    }
};
```

## 🎯 다음 단계

이 챕터에서 학습한 고급 패턴들은 실무에서 타입 안전성을 극대화하는 데 필수적입니다:
- **Chapter 4**: 최신 TypeScript 기능과 고급 패턴 결합
- **실전 프로젝트**: 복합적인 타입 시스템 설계

각 패턴을 개별적으로 이해한 후, 실전 도전 과제를 통해 복합적으로 활용하는 연습을 권장합니다. 이러한 고급 패턴들은 대규모 프로젝트에서 타입 안전성을 확보하고 개발자 경험을 향상시키는 데 매우 유용합니다. 