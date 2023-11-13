# Commit-Message-Conventions

# Commit Message Conventions
[AngularJs 커밋 컨벤션](https://gist.github.com/stephenparish/9941e89d80e2bc58a153)에서 채택된 규칙이다.

# Goals 목표
- 스크립트로 CHANGELOG.md를 생성
- git bisect를 사용해 커밋 무시 (포매팅과 같은 중요하지 않은 커밋)
- 기록을 탐색할 때 더 나은 정보 제공

# Generating CHANGELOG.md CHANGELOG마크다운 문서 생성하기
우리는 changelog에는 3가지 섹션을 사용한다: 새로운 기능(new features), 버그 수정(bug fixes), 큰 변항 사항(breaking change)
이 목록은 release할 때 스크립트에 의해 생성될 수 있다. 관련 커밋들에 대한 링크와 함께
물론 실제 release전에 changelog를 편집할 수 있다. skeleton(기본적인 구조)을 생성한다.

마지막 릴리즈 이후 모든 주제들의 목록 (커밋 메시지의 첫 줄들)
```
git log <last tag> HEAD --pretty=format:%s
```

이번 릴리즈에서 새로운 기능들
```
git log <last release> HEAD --grep feature
```

## Recognizing unimportant commits 중요하지 않은 커밋 인식하기
포매팅 변경 사항들 (스페이스 추가/제거, 빈 줄, 들여쓰기), 빠진 세미콜론, 주석 등
어떤 변경 사항을 찾고 있을 때, 이러한 커밋들은 무시할 수 있다. - 로직에 영향을 주지 않는 커밋

bisect를 할 때, 이러한 것들을 무시할 수 있다.

```
git bisect skip $(git rev-list --grep irrelevant <good place> HEAD)
```



## Provide more information when browsing the history 기록을 탐색할 때 더 많은 정보 제공

이렇게 하면 "컨텐스트" 정보가 추가된다. 이 메시지들을 봐라 (지난 몇개의 angular의 commit에서 발췌)

- Fix small typo in docs widget (tutorial instructions)
- Fix test for scenario.Application - should remove old iframe
- docs - various doc fixes
- docs - stripping extra new lines
- Replaced double line break with single when text is fetched from Google
- Added support for properties in documentation

이 메시지들은 변경 사항이 어디에 있는지 명시하려고 한다. 그러나 어떤 컨벤션도 공유하고 있지 않다.

- fix comment stripping
- fixing broken links
- Bit of refactoring
- Check whether links do exist and throw exception
- Fix sitemap include (to work on case sensitive linux)

안에 무엇이 있는지 짐작할 수 있나요? 이 메시지에는 구체적인 장소가 누락되어 있다. 아마도 문서, 문서 파서, 컴파일러, 시나리오 실행기 등과 같은 것일 수 있다.

어떤 파일이 변경되었는지 확인하면 이 정보를 찾을 수 있지만, 그것은 느리다. 그리고 git 기록을 보면 우리 모두가 위치를 지정하려고 시도하지만 규칙을 놓치는 것을 볼 수 있다.

# Format of the commit message 커밋 메시지의 형식
```
<type>(<scope>): <subject>
<BLANK LINE>
<body>
<BLANK LINE>
<footer>
```
어떤 커밋 메시지의 줄도 100자(영어 기준)를 넘길 수 없다!
이를 통해 github나 다양한 git tools에서도 메시지를 더 쉽게 읽을 수 있다.

## Subject line 제목
제목 줄에는 변경 사항에 대한 간결한 설명을 포함한다.

### Allowed type 허용되는 타입
feat (feature)
fix (bug fix)
docs (documentation)
style (formatting, missing semi colons, ...)
rafactor
test (when adding missing tests)
chore (maintain)

### Allowed scope 허용되는 범위
범위는 커밋 변경 위치를 지정하는 모든 것이 될 수 있다.
```
e.g. $location, $browser, $compile, $rootScope, ngHref, ngClick, ngView, etc...
```

### subject text 주제
명령형, 현재 시제 사용: `change`를 사용, `changed`혹은 `changes`사용 금지
첫 글자를 대문자로 쓰지 않는다.
끝에 점 (.) 을 사용하지 않는다.

## Message body 메시지 본문
명령형, 현재 시제 사용: `change`를 사용, `changed`혹은 `changes`사용 금지
변경 동기, 이전 코드의 행동과 대조되는 점을 포함해라

## Message footer 메시지 바닥글

### Breaking changes 중요한 변경 사항
모든 중요한 변경 사항은 바닥글에 변경 사항 설명, 정당성 및 마이그레이션 참고 사항과 함께 언급해야한다.

```
BREAKING CHANGE: isolate scope bindings definition has changed and
    the inject option for the directive controller injection was removed.
    
    To migrate the code follow the example below:
    
    Before:
    
    scope: {
      myAttr: 'attribute',
      myBind: 'bind',
      myExpression: 'expression',
      myEval: 'evaluate',
      myAccessor: 'accessor'
    }
    
    After:
    
    scope: {
      myAttr: '@',
      myBind: '@',
      myExpression: '&',
      // myEval - usually not useful, but in cases where the expression is assignable, you can use '='
      myAccessor: '=' // in directive's template change myAccessor() to myAccessor
    }
    
    The removed `inject` wasn't generaly useful for directives so there should be no code using it.
```

### Referencing issues 이슈 참조
종료된 버그는 다음과 같이 바닥글(footer) 별도의 줄에 `Closes`키워드를 앞에 붙여 함께 나열해야한다.

```
Closes #234
```

여러 이슈의 경우
```
Closes #123, #245, #992
```

## Examples
```
docs(guide): Google 문서 도구에서 수정된 문서 업데이트

몇 가지 오타가 수정되었습니다:
- 들여쓰기
- 배치 로그배치 로그 -> 배치 로그
- 주기적 검사 시작
- 중괄호 누락
```

```
style($location): 누락된 세미콜론 몇 개 추가
```

```
fix($compile): IE9에 대한 몇 가지 단위 테스트

이전 IE는 html을 대문자로 직렬화하지만 IE9는 그렇지 않습니다.
대소문자를 구분하지 않는 것이 더 좋겠지만, 불행히도 재스민은
대소문자를 구분하지 않습니다.

Closes #392
foo.bar api를 중단하고 대신 foo.baz를 사용해야 합니다.
```
