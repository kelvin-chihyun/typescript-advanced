# Chapter 4: Latest TypeScript (최신 TypeScript)

TypeScript의 최신 기능들을 학습하는 챕터입니다. `satisfies` 연산자와 새로운 데코레이터 시스템 등 TypeScript 5.0+ 버전에서 도입된 최신 기능들을 익힙니다.

## 📚 학습 목표

- `satisfies` 연산자의 개념과 활용법 이해
- 새로운 데코레이터 시스템의 기초와 고급 패턴 학습
- 최신 TypeScript 기능을 활용한 타입 안전성 강화
- 실전에서 최신 기능들을 효과적으로 활용하는 방법 습득

## 🎯 핵심 개념

### satisfies 연산자

**핵심 개념:**
- TypeScript 4.9에서 도입된 새로운 연산자
- 타입 검사를 강화하면서도 타입 추론을 유지하는 기능
- `as`와 `:` 타입 어노테이션의 장점을 결합

**주요 기법:**
```typescript
// 기본 사용법
const a = 1;                    // number (타입 추론)
const b: number = 1;            // number (타입 어노테이션)
const c = 1 as number;          // number (타입 단언)
const d = 1 satisfies number;   // number (타입 검사 + 추론)

// 강력한 타입 검사
type GHIssueURLParams = {
    title: string;
    body: string;
};

const params = new URLSearchParams({
    title: "New Issue",
    body: "Lorem ipsum.",
} satisfies GHIssueURLParams); // 타입 검사하면서 리터럴 타입 보존

// API 요청에서 활용
type Post = {
    title: string;
    content: string;
};

fetch("/api/posts", {
    method: "POST",
    body: JSON.stringify({
        title: "New Post",
        content: "Hello World",
    } satisfies Post), // 타입 안전성 확보
});

// 유니온 타입과 함께 사용
let id = "123" satisfies string | number;
if (typeof numericId !== "undefined") {
    id = numericId; // 타입 호환성 검사
}
```

**학습 포인트:**
- `satisfies` vs `as` vs `:` 타입 어노테이션의 차이점
- 강력한 타입 검사와 타입 추론의 조합
- API 요청에서의 타입 안전성 확보
- 유니온 타입과의 호환성

### 튜플 타입과 satisfies

**핵심 개념:**
- 배열을 튜플로 처리하면서 타입 안전성 확보
- 인덱스 접근의 정확한 타입 추론

**주요 기법:**
```typescript
// 튜플 타입 정의
type MoreThanOneMember = [any, ...any[]];

const array = [1, 2, 3];
//    ^? number[]
const maybeExists = array[3];
//    ^? number | undefined

const tuple = [1, 2, 3] satisfies MoreThanOneMember;
//    ^? [number, number, number]
const doesNotExist = tuple[3];
//    ^? undefined (정확한 타입 추론)

// 네비게이션 엘리먼트와 함께 사용
type NavElement = {
    title: string;
    url?: string;
    children?: readonly NavElement[];
};

const nav = [
    {
        title: "Home",
        url: "/",
    },
    {
        title: "About",
        children: [
            {
                title: "Team",
                url: "/about/team",
            },
        ],
    },
] as const satisfies readonly NavElement[];

nav[0]?.children; // 정확한 타입 추론
```

**학습 포인트:**
- 튜플 타입의 정확한 인덱스 접근
- `as const`와 `satisfies`의 조합
- 복잡한 객체 구조에서의 타입 안전성

### 객체 키 타입 안전성

**핵심 개념:**
- 객체의 키에 대한 타입 안전한 접근
- 정확한 키 타입 추론

**주요 기법:**
```typescript
const routes = {
    "/": {},
    "/users": {},
    "/admin/users": {},
} satisfies Record<string, {}>;

routes["/admin/users"]; // 정확한 키 타입 추론

// API 엔드포인트 타입 안전성
const apiEndpoints = {
    "GET /users": { response: User[] },
    "POST /users": { body: CreateUserRequest, response: User },
    "PUT /users/:id": { body: UpdateUserRequest, response: User },
} satisfies Record<string, { body?: any; response: any }>;

// 타입 안전한 라우터
type RouteKey = keyof typeof apiEndpoints;
const callAPI = <K extends RouteKey>(route: K, data?: typeof apiEndpoints[K]["body"]) => {
    // API 호출 로직
};
```

**학습 포인트:**
- 객체 키의 정확한 타입 추론
- API 엔드포인트 타입 안전성
- 타입 안전한 라우터 구현

### 새로운 데코레이터 시스템

**핵심 개념:**
- TypeScript 5.0에서 도입된 새로운 데코레이터 시스템
- Stage 3 TC39 제안을 기반으로 한 표준 데코레이터
- 기존 experimental decorators와의 차이점

**주요 기법:**

#### 1. 클래스 데코레이터
```typescript
@withCreatedAt
@printDecoratorData
export class Manager {
    task = "Simple task"
    project = "Simple project"

    constructor() {
        console.log("Manager constructor")
    }
}

function withCreatedAt<T extends {new(...args: any[]): {}}>(
    baseClass: T, 
    context: ClassDecoratorContext
) {
    return class extends baseClass {
        createdAt = new Date();
        constructor(...args: any[]) {
            super(args);
        }
    }
}

function printDecoratorData(
    constructor: Function, 
    context: ClassDecoratorContext
) {
    console.log("constructor: ", constructor);
    console.log("context: ", context);

    context.addInitializer(() => {
        console.log("Initialized class: ", context.name);
    })
}
```

#### 2. 필드 데코레이터
```typescript
export class Manager {
    @withTasks
    tasks: string[] = []

    @withCompletedTasks("Done")
    completedTasks: string[] = []

    constructor() {
    }
}

function withTasks<V extends string[]>(
    target: undefined, 
    context: ClassFieldDecoratorContext<unknown, V>
) {
    return function (args: V) {
        args.push("Simple task");
        return args;
    }
}

function withCompletedTasks(task: string) {
    return function <V extends string[]>(
        target: undefined, 
        context: ClassFieldDecoratorContext<unknown, V>
    ) {
        return function (args: V) {
            args.push(task);
            return args;
        }
    }
}
```

#### 3. 메서드 데코레이터
```typescript
export class Manager {
    budget = 0
    
    constructor() {
    }

    @withBudget(100)
    writeTest() {
        console.log("writeTest");
    }
}

function withBudget(budget: number) {
    return function<T extends {budget: number}>(
        target: Function, 
        context: ClassMethodDecoratorContext
    ) {
        return function (this: T, ...args: any[]) {
            this.budget += budget;
            return target;
        }
    }
}
```

#### 4. 접근자 데코레이터 (Observable 패턴)
```typescript
interface Observer {
    update: (value: string) => void;
}

type ClassAccessorDecorator = (
    value: ClassAccessorDecoratorTarget<unknown, unknown>,
    context: ClassAccessorDecoratorContext
) => ClassAccessorDecoratorResult<unknown, any>;

const observable: ClassAccessorDecorator = (accessor, context) => {
    return {
        set(this, newValue: any) {
            // @ts-ignore
            this.observers.forEach(o => o.update(newValue))
            return accessor.set.call(this, newValue);
        }
    }
}

@Observers([new SimpleObserver()])
class Manager {
    @observable
    accessor age = 2

    constructor() {
    }
}

function Observers(_observers: Observer[]) {
    return function _<T extends {new(...args: any[]): {}}>(
        baseClass: T, 
        context: ClassDecoratorContext
    ) {
        return class extends baseClass {
            observers = _observers;
            constructor(...args: any[]) {
                super(args);
            }
        }
    }
}
```

**학습 포인트:**
- 새로운 데코레이터 시스템의 문법
- 클래스, 필드, 메서드, 접근자 데코레이터
- 데코레이터 팩토리 패턴
- Observable 패턴 구현
- 기존 experimental decorators와의 차이점

## 🔧 실전 활용 패턴

### 1. API 클라이언트와 satisfies
```typescript
type ApiConfig = {
    baseURL: string;
    timeout: number;
    headers: Record<string, string>;
};

const apiConfig = {
    baseURL: "https://api.example.com",
    timeout: 5000,
    headers: {
        "Content-Type": "application/json",
        "Authorization": "Bearer token",
    },
} satisfies ApiConfig;

class ApiClient {
    constructor(private config: ApiConfig) {}
    
    async request<T>(endpoint: string): Promise<T> {
        // API 요청 로직
    }
}
```

### 2. 폼 스키마와 satisfies
```typescript
type FormSchema = {
    name: { type: "text"; required: true };
    email: { type: "email"; required: true };
    age: { type: "number"; min: 18; max: 100 };
};

const formSchema = {
    name: { type: "text", required: true },
    email: { type: "email", required: true },
    age: { type: "number", min: 18, max: 100 },
} satisfies FormSchema;

const validateForm = (data: any) => {
    // 폼 검증 로직
};
```

### 3. 라우터와 satisfies
```typescript
type RouteConfig = {
    path: string;
    component: React.ComponentType;
    auth: boolean;
};

const routes = {
    home: { path: "/", component: HomePage, auth: false },
    profile: { path: "/profile", component: ProfilePage, auth: true },
    admin: { path: "/admin", component: AdminPage, auth: true },
} satisfies Record<string, RouteConfig>;

const createRouter = (routes: Record<string, RouteConfig>) => {
    // 라우터 생성 로직
};
```

### 4. 데코레이터와 의존성 주입
```typescript
@Injectable()
class UserService {
    @Inject('DATABASE_URL')
    private databaseUrl: string;
    
    @Log()
    async getUsers(): Promise<User[]> {
        // 사용자 조회 로직
    }
}

function Injectable() {
    return function <T extends {new(...args: any[]): {}}>(
        target: T, 
        context: ClassDecoratorContext
    ) {
        // 의존성 주입 로직
    }
}

function Inject(token: string) {
    return function (
        target: undefined, 
        context: ClassFieldDecoratorContext
    ) {
        // 필드 주입 로직
    }
}

function Log() {
    return function (
        target: Function, 
        context: ClassMethodDecoratorContext
    ) {
        return function (this: any, ...args: any[]) {
            console.log(`Calling ${context.name} with args:`, args);
            const result = target.call(this, ...args);
            console.log(`Result:`, result);
            return result;
        }
    }
}
```

## 📝 학습 체크리스트

- [ ] `satisfies` 연산자의 기본 개념 이해
- [ ] `satisfies` vs `as` vs `:` 타입 어노테이션의 차이점 파악
- [ ] 강력한 타입 검사와 타입 추론의 조합 활용
- [ ] 튜플 타입과 `satisfies` 조합
- [ ] 객체 키 타입 안전성 확보
- [ ] 새로운 데코레이터 시스템의 문법 이해
- [ ] 클래스, 필드, 메서드, 접근자 데코레이터 구현
- [ ] 데코레이터 팩토리 패턴 활용
- [ ] Observable 패턴과 데코레이터 조합
- [ ] 실전에서 최신 기능들을 효과적으로 활용

## 🎯 고급 패턴

### 1. satisfies와 제네릭 조합
```typescript
const createTypedConfig = <T extends Record<string, any>>(config: T) => {
    return config satisfies T;
};

const apiConfig = createTypedConfig({
    baseURL: "https://api.example.com",
    timeout: 5000,
});
```

### 2. 데코레이터와 메타데이터
```typescript
function Metadata(metadata: Record<string, any>) {
    return function <T extends {new(...args: any[]): {}}>(
        target: T, 
        context: ClassDecoratorContext
    ) {
        Reflect.defineMetadata('custom:metadata', metadata, target);
        return target;
    }
}

@Metadata({ version: '1.0.0', author: 'John Doe' })
class MyClass {
    // 클래스 구현
}
```

### 3. 타입 안전한 이벤트 시스템
```typescript
type EventMap = {
    'user:created': { userId: string; name: string };
    'user:deleted': { userId: string };
};

const eventEmitter = {
    emit: <K extends keyof EventMap>(event: K, data: EventMap[K]) => {
        // 이벤트 발생 로직
    }
} satisfies {
    emit: <K extends keyof EventMap>(event: K, data: EventMap[K]) => void;
};
```

## 🎯 다음 단계

이 챕터에서 학습한 최신 TypeScript 기능들은 현대적인 TypeScript 개발의 핵심입니다:
- **실전 프로젝트**: 최신 기능들을 활용한 대규모 프로젝트 설계
- **라이브러리 개발**: 최신 기능을 활용한 타입 안전한 라이브러리 개발
- **팀 협업**: 최신 기능을 활용한 팀 개발 표준 수립

각 기능을 개별적으로 이해한 후, 실전 프로젝트에서 조합적으로 활용하는 연습을 권장합니다. 이러한 최신 기능들은 개발자 경험을 크게 향상시키고 타입 안전성을 극대화하는 데 매우 유용합니다.

## 🔧 설정 및 환경

### TypeScript 설정
```json
{
  "compilerOptions": {
    "target": "ES2022",
    "experimentalDecorators": false, // 새로운 데코레이터 사용 시
    "strict": true,
    "noImplicitAny": true
  }
}
```

### 데코레이터 사용 시 주의사항
- TypeScript 5.0+ 버전 필요
- `experimentalDecorators: false` 설정
- ES2022 이상의 target 설정
- Node.js 16+ 또는 적절한 런타임 환경