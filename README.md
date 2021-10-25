# yarn workspace 구성해보기

## Initialize yarn

```
yarn init
```

## package.json workspaces

`package.json` 파일에 `workspaces` 프로퍼티에 지정할 폴더들을 배열로 넣을 수 있습니다. 그리고 workspace는 게시할 목적이 아니여서 `private` 속성을 `true`로 지정해주어야 합니다.

```json
// package.json
{
    "workspaces": ["packages/*"],
    "private": true
}
```

## workspace 구성

1. workspace에 설정한 폴더를 생성하고 해당 폴더에 대한 내용을 구성합니다.

    ```
    (root)
    ├ packages
        ⌊ package-a
        ⌊ package-b
    ⌊ package.json
    ```

1. 각 패키지 폴더에서 [Initialize yarn](#initialize-yarn)과 같이 초기화를 합니다.

1. 패키지 별로 `index.js` 파일을 만들어 동작할 수 있도록 해줍니다.

1. `package-a` 패키지에 `package-b` 패키지를 의존성을 추가해줍니다.

    ```json
    // package-a package.json
    {
        "dependencies": {
            "package-b": "1.0.0"
        }
    }
    ```

1. `package-a` 패키지에서 실행 스크립트를 작성해줍니다.

    ```json
    // package-a package.json
    {
        "scripts": {
            "dev": "node index.js"
        }
    }
    ```

1. 루트 `package.json` 파일에 패키지의 실행 스크립트가 동작될 수 있도록 합니다.

    ```json
    {
        "scripts": {
            "dev": "yarn workspace package-a dev"
        }
    }
    ```

1. 패키지를 설치해주고, 실행 스크립트를 동작해봅니다.

    ```
    yarn

    yarn dev
    ```

## Same library, Different version

포함된 패키지들 중 같은 라이브러리를 사용하지만, 버전이 다른 경우 `node_modules` 는 어떻게 되는지 살펴봅시다.

1. `package-a` 패키지에는 `lodash@3.10.1`, `package-b` 패키지에는 `lodash@4.0.0`을 의존성을 추가해줍니다.
    ```json
    // package-a package.json
    {
        "dependencies": {
            "lodash": "3.10.1"
        }
    }
    ```
    ```json
    // package-b package.json
    "dependencies": {
        "lodash": "4.0.0"
    }
    ```
1. 패키지를 설치합니다.
    ```
    yarn
    ```

### Result

```
(root)
├ node_modules
    ├ package-a
    ├ package-b
    ⌊ lodash
├ packages
    ├ package-a
    ⌊ package-b
        ⌊ node_modules/lodash
⌊ package.json
```

위 폴더 구조처럼 하나의 버전은 루트의 `node_modules` 폴더에 설치가 되고 나머지 버전에 대해선 각 패키지의 `node_modules` 폴더에 설치되게 됩니다. 이로써 패키지의 버전별 사용이 가능하게 되었습니다. 

## Package alias

패키지가 많지 않은 경우엔 별도의 alias 없이 개별적으로 설치가 되어 폴더가 구성되어도 됩니다.
하지만, 다른 프로젝트에서 해당 패키지들을 설치할 때 하나의 폴더에서 관리를 하고 싶다면 어떻게 해야될까요?

대표적으로 [babel](https://github.com/babel/babel) 패키지를 설치할 때 `yarn add @babel/core` 형태로 설치를 하게 됩니다.

babel 저장소에서 살펴보면 `packages` 라는 폴더에 패키지들이 포함되어 있습니다. `babel-core`의 `packages.json` 파일을 들어가 보면 `name: @bable/core` 이라고 지정되어 있습니다.

여러 패키지를 관리하고 하나의 폴더에 설치되게 하려면 `name` 앞에 공통적으로 경로를 작성해주면 됩니다.

```json
// package-a pacakge.json
{
    "name": "@camp-son/package-a"
}
```

```json
// package-b pacakge.json
{
    "name": "@camp-son/package-b"
}
```

### Result

```
(root)
├ node_modules
    ⌊ @camp-son
        ⌊ package-a
        ⌊ package-b
├ packages
    ⌊ package-a
    ⌊ package-b
⌊ package.json
```
