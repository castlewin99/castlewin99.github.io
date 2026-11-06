---
title: "MacBook Neo에서 기술 블로그 개발 환경 준비하기"
date: 2026-11-06 00:00:00 +0900
categories: [Development, macOS]
tags: [macos, vscode, git, homebrew, ruby, jekyll, chirpy]
description: "맥북에 VS Code, Git, Homebrew, Ruby를 준비하고 기존 Jekyll 블로그를 로컬에서 실행한 기록입니다."
---

## 시작하게 된 이유

기술 학습 내용을 기록할 블로그를 맥에서 작성하고 미리보기 할 수 있도록 개발 환경을 구성했습니다. Jekyll과 Chirpy 테마를 사용하는 블로그를 기준으로 설명합니다.

맥에서 개발 환경을 구성하는 것이 처음이라, 편집기 설치부터 블로그 로컬 미리보기까지 진행한 내용을 기록했습니다. 이 글의 완료 범위는 **로컬 실행 확인**입니다. 새 글의 GitHub 업로드와 배포는 다음 단계입니다.

## 확인한 환경과 버전

| 항목 | 확인 결과 |
|---|---|
| 장비 | MacBook Neo |
| macOS | 26.3.2 (25D2140) |
| 아키텍처 | arm64 |
| 편집기 | Visual Studio Code, 한국어 UI |
| Git | 2.50.1 (Apple Git-155) |
| Ruby | 3.3.13 |
| Bundler | 4.0.23 |
| Jekyll | 4.4.1 |
| 테마 | jekyll-theme-chirpy 7.6.0 |

VS Code와 Homebrew의 정확한 버전은 이 기록에서 확인하지 않았습니다. 아래 버전은 설치 당시 결과이며, 같은 명령을 나중에 실행하면 설치 버전이 달라질 수 있습니다.

## 1. VS Code 설치

[공식 다운로드 페이지](https://code.visualstudio.com/Download)에서 Mac용 Apple silicon 버전을 내려받고, 압축을 푼 앱을 응용 프로그램 폴더로 옮겼습니다.

실행 후 확장 프로그램에서 Microsoft의 Korean Language Pack을 설치했습니다. VS Code가 한국어 화면으로 실행되는 것을 확인했습니다.

## 2. macOS 환경과 Git 확인

터미널에서 다음 명령을 실행했습니다.

```bash
sw_vers
uname -m
git --version
```

macOS와 아키텍처는 확인됐지만 Git 실행 시 다음 안내가 나왔습니다.

```text
xcode-select: note: No developer tools were found, requesting install.
```

개발자 도구가 없는 상태라 아래 명령으로 설치를 요청했습니다.

```bash
xcode-select --install
```

이 명령의 `install requested` 메시지는 설치 완료가 아니라 요청을 뜻합니다. 별도 설치 창에서 설치를 진행하고 완료될 때까지 기다려야 했습니다.

완료 후 다시 확인했습니다.

```bash
git --version
```

```text
git version 2.50.1 (Apple Git-155)
```

## 3. 블로그 프로젝트 열기

Jekyll·Chirpy 기반 블로그 프로젝트가 준비되어 있다는 전제입니다. 아직 프로젝트가 없다면 [Chirpy Starter](https://github.com/cotes2020/chirpy-starter)의 안내에 따라 저장소를 먼저 준비합니다.

기존 저장소를 내려받는 명령 형식은 다음과 같습니다. `<계정명>`과 `<저장소명>`은 실제 값으로 바꿉니다. 이 부분은 독자를 위한 일반 예시입니다.

```bash
git clone --recurse-submodules https://github.com/<계정명>/<저장소명>.git
cd <저장소명>
git status
```

VS Code에서 내려받은 프로젝트 폴더를 열고 `_posts`, `_config.yml`, `Gemfile` 등을 확인합니다. 이후 명령은 특별한 설명이 없다면 `Gemfile`이 있는 프로젝트 루트에서 실행합니다.

## 4. 기본 Ruby와 Bundler 확인

VS Code의 터미널에서 아래 명령을 실행했습니다.

```bash
pwd
ruby -v
bundle -v
brew --version
```

초기 상태는 다음과 같았습니다.

| 항목 | 초기 결과 |
|---|---|
| Ruby | 2.6.10 |
| Bundler | 1.17.2 |
| Homebrew | command not found |

블로그의 GitHub Actions 설정은 Ruby 3.3을 사용하고 있었습니다. macOS 기본 Ruby를 변경하는 대신, Homebrew로 Ruby 3.3을 별도 설치하기로 했습니다.

## 5. Homebrew 설치와 PATH 설정

[Homebrew 공식 페이지](https://brew.sh/)의 설치 명령을 사용했습니다.

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

비밀번호 요청 시 맥 로그인 비밀번호를 입력하고, 안내에 따라 설치했습니다. 터미널에서는 비밀번호를 입력해도 문자가 표시되지 않습니다.

설치 완료 후 `Next steps`에 나온 경로 설정을 적용했습니다. 아래의 영구 설정 명령은 중복해서 실행할 필요가 없습니다.

```bash
echo >> ~/.zprofile
echo 'eval "$(/opt/homebrew/bin/brew shellenv zsh)"' >> ~/.zprofile
eval "$(/opt/homebrew/bin/brew shellenv zsh)"
```

`echo`로 설정한 줄은 이후 로그인 셸에 적용되고, 마지막 `eval` 명령은 현재 터미널에 즉시 적용됩니다.

## 6. Ruby 3.3 설치와 기본 Ruby 선택 문제 해결

```bash
brew install ruby@3.3
```

설치 과정에서 버전을 확인했을 때는 여전히 기본 Ruby 2.6.10과 Bundler 1.17.2가 출력됐습니다. Ruby가 설치돼 있어도 터미널이 어떤 경로의 실행 파일을 선택하는지는 별개입니다.

설치 여부를 확인하고 현재 터미널의 PATH 앞에 새 Ruby 경로를 추가했습니다.

```bash
/opt/homebrew/bin/brew list --versions ruby@3.3
export PATH="/opt/homebrew/opt/ruby@3.3/bin:$PATH"
rehash
```

다시 확인했습니다.

```bash
which ruby
ruby -v
bundle -v
```

```text
/opt/homebrew/opt/ruby@3.3/bin/ruby
ruby 3.3.13 (2026-10-23 revision af48f08f21) [arm64-darwin25]
4.0.23
```

이후 로그인 셸에서도 같은 Ruby를 선택하도록 다음 줄을 추가했습니다.

```bash
echo 'export PATH="/opt/homebrew/opt/ruby@3.3/bin:$PATH"' >> ~/.zprofile
```

`~/.zprofile`은 zsh 로그인 셸이 읽는 설정 파일입니다. 새 VS Code 터미널에서 다른 Ruby가 선택된다면 `which ruby`로 다시 확인하고, 현재 터미널에 위의 `export PATH` 명령을 적용할 수 있습니다.

## 7. 의존성 설치

`Gemfile`이 있는 블로그 폴더로 이동했습니다.

```bash
bundle install
```

설치 로그에서 다음을 확인했습니다.

```text
Installing jekyll 4.4.1
Installing jekyll-theme-chirpy 7.6.0
Bundle complete! 5 Gemfile dependencies, 61 gems now installed.
```

Windows 플랫폼 이름에 대한 `DEPRECATED` 경고와 후원 안내가 함께 출력됐지만, 이번 설치는 정상 완료됐습니다.

## 8. 로컬 미리보기 실행

```bash
bundle exec jekyll serve --livereload
```

브라우저에서 아래 주소를 열어 블로그가 표시되는 것을 확인했습니다.

```text
http://127.0.0.1:4000
```

이 주소는 로컬 미리보기용이며, 공개 사이트에 배포한 것이 아닙니다. 서버가 실행되는 동안 터미널을 유지하고, 종료할 때는 `Control + C`를 사용합니다.

## 마무리와 다음 단계

맥에서 블로그를 관리할 도구를 준비하고, 기존 Jekyll 블로그를 로컬에서 실행했습니다. 특히 Ruby 설치와 실행 경로 선택을 구분해서 확인하는 과정을 배웠습니다.

다음 단계에서는 새 Markdown 글을 `_posts`에 추가하고, 로컬 미리보기에서 확인한 뒤 GitHub에 올려 배포 결과를 확인할 예정입니다.

## 참고 자료

- [Visual Studio Code 다운로드](https://code.visualstudio.com/Download)
- [Homebrew](https://brew.sh/)
- [Homebrew Ruby 3.3](https://formulae.brew.sh/formula/ruby@3.3)
- [Chirpy 테마](https://github.com/cotes2020/jekyll-theme-chirpy)
- [Chirpy Starter](https://github.com/cotes2020/chirpy-starter)
