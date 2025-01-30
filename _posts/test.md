## swagger-typescript-api란?

<b>swagger-typescript-api</b>는 Swagger 문서를 기반으로 TypeScript API 클라이언트를 자동 생성해주는 도구입니다. 이를 통해 개발자는 API 문서를 바탕으로 TypeScript 코드를 자동으로 생성하고, 타입 안전성을 보장받을 수 있습니다. 주요 기능으로는 **자동 코드 생성, 타입 안전성, 일관성 유지**등이 있으며, 이러한 기능들을 통해 개발 생산성을 높이고 오류를 줄일 수 있습니다.

<br />

또한, swagger-typescript-api는 **반복적인 작업**을 줄여줍니다. 예를 들어, API가 변경될 때마다 수동으로 코드를 수정하는 <u>번거로움</u>을 없애줍니다. Swagger 문서를 업데이트하고 swagger-typescript-api를 실행하면, 최신 API 클라이언트 코드를 자동으로 생성할 수 있어서 유지보수 작업을 간소화하고 일관성을 유지할 수 있습니다.

<br />

위 라이브러리를 통해서 옵션에 대해 최대한 정리했습니다. 라이브러리를 함께 살펴봅니다.

<br />
<br />

## 설치

해당 라이브러리르 설치하도록 합니다.

```js
npm i -dev swagger-typescript-api
// or
yarn add --dev swagger-typescript-api
```

package.json에서 아래처럼 작성을 해줍니다.

```javascript
// package.json
{
  "scripts": {
    "types": "swagger-typescript-api -p https://petstore.swagger.io/v2/swagger.json -o src/@types"
  }
}
```

`yarn types` 또는 `npm run types` 실행해보면 아래와 같이 폴더와 파일이 생선되는 것을 확인할 수 있습니다.

<div style="text-align: center">
<img src="https://velog.velcdn.com/images/devhyuk/post/5d2c4080-52b4-4d9e-b1ce-50d957525387/image.png" width="50%" alt="이미지" />
</div>

간단하게 설치와 사용이 끝났습니다.

이후 옵션과 같은 경우 아래에서 이어서 진행하도록 하겠습니다.

<br/>
<br/>
<br/>

## 옵션 설명

swagger에서 제공해주는 <a href="https://petstore.swagger.io" target="_blank">샘플 서버</a>를 통해서 진행하도록 하겠습니다.

<br />
<br />

### -p, --path `<string>`

swagger의 주소를 입력하는 부분입니다.
![](https://velog.velcdn.com/images/devhyuk/post/fbbf5af1-b668-43f4-8e8f-81a925097de0/image.png)

위 빨간색을 입력하면 됩니다.

```javascript
  -p https://petstore.swagger.io/v2/swagger.json
```

> [!TIP]
> 해당 주소로 접속 시 json 데이터로 이루어진 페이지로 이동하게 되는데 해당 페이지가 없는 경우에 `.json`이 아닌 `-json`으로 접속을 해보시길 바랍니다.

<br />

### -o, --output `<string>` <small>(default: ./)</small>

저장할 파일의 생성 경로를 입력하는 부분입니다.

```javascript
  -o src/@types
```

<div style="text-align: center">
<img src="https://velog.velcdn.com/images/devhyuk/post/5d2c4080-52b4-4d9e-b1ce-50d957525387/image.png" width="50%" alt="이미지" />
</div>

<br />

### -n, --name `<string>` <small>(default: "Api.ts")</small>

생성할 TypeScript API 파일의 이름을 지정합니다.

```
  -n Test
```

<div style="text-align: center">
<img src="https://velog.velcdn.com/images/devhyuk/post/e3549ac0-afb6-481f-ac35-4598216da3f9/image.png" width="50%" alt="이미지" />
</div>

<br />

### -d, --default-as-success <small>(default: false)</small>

`deafult` 응답 상태 코드를 성공 응답으로 사용합니다.

몇몇 Swagger 스키마는 기본적으로 `deafult` 응답 상태 코드를 성공 응답 타입으로 사용합니다.

```diff
-       this.request<void, any>({})
+      this.request<any, void>({})
```

this.request<<span style="color: skyblue">void</span>,<span style="color: hotpink">any</span>>는 성공 시 응답 데이터가 없고, 실패 시 오류 데이터가 있을 수 있는 경우에 사용합니다.

this.request<<span style="color: hotpink">void</span>,<span style="color: skyblue">any</span>>는 성공 시 응답 데이터가 있을 수 있고, 실패 시 오류 데이터가 없는 경우에 사용합니다.

<br />

### --default-response `<type>` <small>(default: "void")</small>

빈`void` 응답 스키마에 대한 기본 타입을 지정합니다.

```
--default-response any
```

```diff
-  this.request<any, void>({})
+ this.request<any, any>({})
```

<br />

### -r, --responses <small>(default: false)</small>

요청(response) 응답에 대한 추가 정보를 주석에 추가 생성하며, 잘못된 응답에 대한 타입도 추가합니다.

```diff
/**
   * No description
   *
   * @tags pet
   * @name UpdatePet
   * @summary Update an existing pet
   * @request PUT:/pet
   * @secure
+ * @response `400` `void` Invalid ID supplied
+ * @response `404` `void` Pet not found
+ * @response `405` `void` Validation exception
   */
```

<br />

### --no-client

기본적으로 API 클래스가 생성이 됩니다.

하지만 `--no-client`을 추가하게 된다면 해당 API를 생성 하지 않습니다.

```diff
- export class Api<SecurityDataType extends unknown> extends HttpClient<SecurityDataType> {
- pet = {    /**
-     * No description
-     *
-     * @tags pet
-     * @name AddPet
-     * @summary Add a new pet to the store
-     * @request POST:/pet
-     * @secure
-     */
-     addPet: (body: Pet, params: RequestParams = {}) =>
-      this.request<any, void>({
-        path: `/pet`,
-        method: "POST",
-        body: body,
-        secure: true,
-        type: ContentType.Json,
-        ...params,
-      })
- }
```

<br />

### --modular (default: false)

swagger의 엔드포인트를 기준으로 파일을 만들어줍니다.

<div style="text-align: center">
 <img src="https://velog.velcdn.com/images/devhyuk/post/29fbc27b-8665-49c7-ab43-f61f41b03c35/image.png" width="50%" alt="이미지" />
</div>

|     파일명     |                                           기능                                            |
| :------------: | :---------------------------------------------------------------------------------------: |
| data-contracts |                         공통적인 데이터 타입들이 있는 파일입니다.                         |
| http-client.ts |                    데이터 통신의 request등의 함수들이 있는 파일입니다.                    |
|   나머지.ts    | 각각의 `swagger`의 앤드포인트를 기준으로 각각의 태그들의 함수와 타입들이 있는 파일입니다. |

<br />
  
### --route-types
각각 API 라우트에 대한 타입 정의를 생성합니다.

```diff
+ export namespace AddPet {
+    export type RequestParams = {};
+    export type RequestQuery = {};
+    export type RequestBody = Pet;
+    export type RequestHeaders = {};
+    export type ResponseBody = any;
+  }

```

> [!TIP]
> 데이터와 타입을 분리하여 각각의 파일로 생성하고 싶다면??
>
> `--modular --route-types"`를 이용하면 됩니다.
>
> <div style="text-align: center">
> <img src="https://velog.velcdn.com/images/devhyuk/post/e5e49d19-57f9-4ed8-9a4a-0648a010d086/image.png" width="50%" alt="이미지" />
> </div>

<br />

> [!TIP]
> 타입만 각각의 파일로 생성하고 싶다면??
>
> `--no-client --modular --route-types`를 이용하면 됩니다.
>
> <div style="text-align: center">
> <img src="https://velog.velcdn.com/images/devhyuk/post/da275a72-704a-4e00-ba37-0ef80665c150/image.png" width="50%" alt="이미지" />
> </div>

<br />

### --extract-request-params <small>(default: false)</small>

요청 파라미터 `request params` 타입을 **interface**으로 추출합니다.

```diff
+       export interface FindPetsByStatusParams {
+           status: ("available" | "pending" | "sold")[];
+       }

+       findPetsByStatus: (query: FindPetsByStatusParams, params: RequestParams = {}) => {})
-        findPetsByStatus: (query: {status: ("available" | "pending" | "sold")[]},params: RequestParams = {},) =>{})
```

<br />

### --extract-request-body <small>(default: false)</small>

요청 파라미터 `request body` 타입을 **interface**으로 추출합니다.

```diff
+export interface UpdatePetWithFormPayload {
+ name?: string;
+ status?: string;
+ }

+ updatePetWithForm: (petId: number, data: UpdatePetWithFormPayload, params: RequestParams = {}) => {})
-  updatePetWithForm: (petId: number,data: {name?: string; status?: string;},params: RequestParams = {}) => {})
```

<br />

### --extract-response-body <small>(default: false)</small>

응답 본문`response body` 타입을 **type**으로 추출합니다.

```diff
+       export type FindPetsByStatusData = Pet[];
+       this.request<FindPetsByStatusData, void>({})
-        this.request<Pet[], void>({})
```

<br />

### --module-name-index <number> (default: 0)

라우트 분리에 사용할 경로 인덱스를 결정합니다.

```
 --module-name-index 1
```

```diff
-   pet = {
-       addPet: (body: Pet, params: RequestParams = {}) =>{})
- }
+     addPet = (body: Pet, params: RequestParams = {}) =>{})
```

<br />

### --module-name-first-tag <small>(default: false)</small>

첫 번째 태그를 기준으로 라우트를 분리합니다.

```diff
  📁 src
  └── 📁 @type
-    ├── 📄 auth.ts
+   └── 📄 인증.ts
```

```diff
-  export class Auth<SecurityDataType = unknown> extends HttpClient<SecurityDataType> {
+ export class 인증<SecurityDataType = unknown> extends HttpClient<SecurityDataType> {
  /**
    * No description
    *
    * @tags 인증
    * @name AuthControllerPostAuth
    * @summary 로그인
    * @request POST:/auth/login
    */
```

<br />

### --axios <small>(default: false)</small>

Fetch에서 Axios HTTP 클라이언트로 변경 생성합니다.

<br />

### --unwrap-response-data <small>(default: false)</small>

응답 데이터 항목을 풀어서 반환합니다.

```diff
  public request = async <T = any, E = any>({
-  }: FullRequestParams): Promise<HttpResponse<T, E>> => {
+ }: FullRequestParams): Promise<T> => {
-          return data
+         return data.data
	}
```

<br />    
          
### --disable-throw-on-error <small>(default: false)</small>
응답값 response.ok가 true가 아닐 때 에러를 던지지 않습니다.
```diff
  public request = async <T = any, E = any>({}: FullRequestParams): Promise<HttpResponse<T, E>> => {
-        if (!response.ok) throw data
          return data
 }
```

<br />

### --type-prefix `<string>` <small>(default: "")</small>

타입명의 앞쪽(접두사)에 이름을 추가합니다.

```js
  --type-prefix before
```

```diff
-  export interface Category {}
+ export interface BeforeCategory {}
```

  <br />
  
### --type-suffix `<string>`<small>(default: "")</small>
타입명의 뒷쪽(접미사)에 이름을 추가합니다.
```js
  --type-suffix after
```
```diff
-  export interface Category {}
+ export interface CategoryAfter {}
```

  <br />
  
### --api-class-name `<string>`<small>(default: "Api")</small>
API 클래스의 이름을 지정합니다.

```js
  --api-class-name Test
```

```diff
-   export class Api<SecurityDataType extends unknown> extends HttpClient<SecurityDataType> {
+  export class Test<SecurityDataType extends unknown> extends HttpClient<SecurityDataType> {
```

  <br />
  


### --another-array-type <small>(default: false)</small>

배열 타입을 Array<Type>으로 생성합니다. (기본적으로 Type[]로 생성됩니다.)

```diff
-  name: string[]
+  name: Array<string>
```

  <br />
  
###  --sort-types <small>(default: false)</small>
필드와 타입을 알파벳순으로 정렬합니다.
  ```diff
export interface Pet {
+ category?: Category
     id?: number
 - category?: Category
     name: string
     photoUrls: string[]
+ status?: 'available' | 'pending' | 'sold'
      tags?: Tag[]
-  status?: 'available' | 'pending' | 'sold'
}
  ```
  
> [!TIP]
> 알파벳순으로 정렬을 한다고 좋은 것은 아닙니다.
>
> <div style="text-align: center">
> <img src="https://velog.velcdn.com/images/devhyuk/post/847983ad-2703-4142-b652-dca758783eee/image.png" width="50%" alt="이미지" />
> </div>
>
>위와 같이 **swagger에서 보이는 순서대로** 정렬해주기때문에 <u>알파벳 순서로 정렬</u>을 하게 된다면 되려 가독성이 떨어질 수 있습니다. 
  
  
  <br />
  
  
  
###   --sort-routes <small>(default: false)</small>
API 라우트를 알파벳 순으로 정렬합니다.
  ```diff
-  updatePet: (body: Pet, params: RequestParams = {}) => this.request<any, void>({}),
      getPetById: (petId: number, params: RequestParams = {}) => this.request<Pet, void>({}),
+ updatePet: (body: Pet, params: RequestParams = {}) => this.request<any, void>({}),
  ```
  > [!TIP] 
> 알파벳순으로 정렬을 한다고 좋은 것은 아닙니다.
> 
> <div style="text-align: center">
> <img src="https://velog.velcdn.com/images/devhyuk/post/1162f42f-590f-4438-a1fb-6934ba0765bf/image.png" width="50%" alt="이미지" />
> </div>
>
> 위와 같이 **swagger에서 보이는 순서대로** 정렬해주기때문에 <u>알파벳 순서로 정렬</u>을 하게 된다면 되려 가독성이 떨어질 수 있습니다. 
  
  
  
  <br />
  
  
  ### --extract-enums <small>(default: false)</small>
인라인 인터페이스/타입 내부에서 모든 enum을 TypeScript enum 구성으로 추출합니다.
  
```diff
  /** Status */
+ export enum StatusEnum {
+   Good = 'good',
+   Bad = 'bad',
+ }
  
-   status: 'good' | 'bad'
+  status: StatusEnum
  ```
