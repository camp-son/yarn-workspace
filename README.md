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
