# CI
https://www.redhat.com/ko/topics/devops/what-is-ci-cd

![Untitled](./img/CI%20그림.png)

## 의미

CI/CD의 "CI"는 개발자를 위한 자동화 프로세스인 지속적인 통합(Continuous Integration)을 의미합니다. 지속적인 통합이 제대로 구현되면 애플리케이션 코드의 새로운 변경 사항이 정기적으로 빌드 및 테스트를 거쳐 공유 리포지토리에 병합됩니다. 따라서 여러 명의 개발자가 동시에 애플리케이션 개발과 관련된 코드 작업을 할 경우 서로 충돌하는 문제를 이 방법으로 해결할 수 있습니다.

라고 나와있다 

## 지금까지의 우리

지금까지의 나는 CD만 해결하고 CI/CD 구축을 했다고 생각하고 있었다.

하지만 우리팀은 현재 코드를 병합할 때 빌드와 테스트를 거치치않고 병합이 되고 있었다.

이는 테스트가 안된 즉 검증이 안된 코드가 prod 서버에 올라갈 수 있다는걸 암시하고 있다

## 문제

그래서 우리는 코드를 병합하기 전에 빌드와 테스트를 진행 할 수 있도록 변경 하였다

PR을 보내게 됬을 때 빌드와 테스트를 한 후 병합을 한다.

라는 계획을 세웠지만 우리 팀 내에서 포크 모델을 사용하고 있었다….. 

![Untitled](./img/fork%20pull%20request.png)

포크 에서 업스트림으로 pr을 할때 wolkrlows가 실행되게 해야하는데 우리 회사에서 막았는지….. 비활성화가 되어있다.

## pull_request_target

https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows#pull_request_target

> This event runs in the context of the base of the pull request, rather than in the context of the merge commit, as the `pull_request` event does. This prevents execution of unsafe code from the head of the pull request that could alter your repository or steal any secrets you use in your workflow. This event allows your workflow to do things like label or comment on pull requests from forks. Avoid using this event if you need to build or run code from the pull request.
> 

정리하면 **`pull_request` 이벤트가 PR로 올라온 커밋에서 실행되는 반면 `pull_request_target`은 풀 리퀘스트에 의해서 실행되는 이벤트이지만 풀 리퀘스트가 머지 대상(target)으로 지정한 base를 기준으로 실행된다.** 

쉽게 말해서 pr을 했을때 체크아웃이 변경된 코드가 있는 브랜치로 되지않고 base에 있는 기존 브랜치로 체크아웃이 되어 새로운 코드를 테스트를 하지 못한다.

## 현재의 우리

그래서 우리는 

```yaml
- name: Checkout code
  uses: actions/checkout@v3
  with:
    ref: ${{ github.event.pull_request.head.sha }}
```

이런식으로 체크아웃 하는 위치를 정해주어 사용했다.

이렇게 하면 보안에 아주 취약하다고 하나 private repo이기때문에 회사사람들끼리만 사용해 상관없다고 생각한다.~~(사실 설정 열어주면 해결되는문제)~~

```yaml
name: Java CI

on:
  pull_request_target:
    types: [ opened, reopened, synchronize ]
    paths:
      - 'application/api-admin-v2/**'
      - 'domainv2/**'

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: checkout
        uses: actions/checkout@v2
        with:
          ref: ${{ github.event.pull_request.head.ref }}
          repository: ${{ github.event.pull_request.head.repo.full_name }}
          token: ${{ secrets.JAMIN_GIT_ACCESS_TOKEN }}

      - name: Set up JDK 17
        uses: actions/setup-java@v2
        with:
          java-version: '17'
          distribution: 'zulu'

      - name: Cache Gradle dependencies
        uses: actions/cache@v3
        with:
          path: ~/.gradle/caches
          # .gradle 파일을 해시한 후 키로 만든다.
          # runner.os: 운영체제가 들어간다.
          key: ${{ runner.os }}-gradle-${{ hashFiles('**/*.gradle', '**/*.properties', '**/*.yml') }}
          restore-keys: |
            ${{ runner.os }}-gradle-

      - name: Build with Gradle
        run: ./gradlew :application:api-admin-v2:build -x test

  application-test:
    needs: build
    runs-on: ubuntu-latest
    steps:
      - name: checkout
        uses: actions/checkout@v2
        with:
          ref: ${{ github.event.pull_request.head.ref }}
          repository: ${{ github.event.pull_request.head.repo.full_name }}
          token: ${{ secrets.JAMIN_GIT_ACCESS_TOKEN }}

      - name: Setup MySQL
        uses: ./.github/actions/mysql-setup
        with:
          mysql-version: '8.0'
          database-name: 'test'
          mysql-root-password: 'test1!'

      - name: Set up JDK 17
        uses: actions/setup-java@v2
        with:
          java-version: '17'
          distribution: 'zulu'

      # Set timezone to KST
      - name: Set timezone
        run: |
          sudo ln -sf /usr/share/zoneinfo/Asia/Seoul /etc/localtime
          echo "Asia/Seoul" | sudo tee /etc/timezone
          sudo dpkg-reconfigure --frontend noninteractive tzdata

      - name: Test with Gradle
        run: ./gradlew :application:api-admin-v2:test

  domain-test:
    needs: build
    runs-on: ubuntu-latest
    steps:
      - name: checkout
        uses: actions/checkout@v2
        with:
          ref: ${{ github.event.pull_request.head.ref }}
          repository: ${{ github.event.pull_request.head.repo.full_name }}
          token: ${{ secrets.JAMIN_GIT_ACCESS_TOKEN }}

      - name: Setup MySQL
        uses: ./.github/actions/mysql-setup
        with:
          mysql-version: '8.0'
          database-name: 'test'
          mysql-root-password: 'test1!'

      - name: Set up JDK 17
        uses: actions/setup-java@v2
        with:
          java-version: '17'
          distribution: 'zulu'

      # Set timezone to KST
      - name: Set timezone
        run: |
          sudo ln -sf /usr/share/zoneinfo/Asia/Seoul /etc/localtime
          echo "Asia/Seoul" | sudo tee /etc/timezone
          sudo dpkg-reconfigure --frontend noninteractive tzdata

      - name: Test with Gradle
        run: ./gradlew :domainv2:test
```

테스트를 하기 위해 mysql이 필요해 gicaction에서 mysql을 띄워주고 테스트를 진행한다.

![Untitled](./img/ci%20성공.png)

먼저 빌드가 되고 테스트가 병렬적으로 실행된다.