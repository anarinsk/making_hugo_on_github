# making_hugo_on_github
깃허브 위에 휴고 올리는 법 self-tutorial

# Purpose
Hugo를 github 위에 올리는 법에 관한 자기 튜토리얼이다.

# Step by Step

## github 계정 만들기 (in github.com)


생략

## github repo 생성 (in github.com)

새로운 리포를 생성한다. 이름은 YOUR_NAME.github.io
README.md 파일을 포함해서 생성하는 것이 좋다.
해당 폴더의 터미널에서 `git clone YOUR_GITHUB_REPO_NAME`을 쳐서 복사해온다.

## Hugo 및 관련 홈페이지 파일 생성 (in RStudio)

### Preliminary

**RStudio에서 작업 폴더를 반드시 해당 `github/YOUR_NAME.github.io`로 바꿔서 진행해야 한다.**

### 기본적인 설치
```{R}
devtools::install_github("rstudio/blogdown")
library(blogdown)
```

```{R}
install_hugo()
```

휴고 설치는 또 해줄 필요는 없다.

### Hugo page 생성

```{R}
library(blogdown)
new_site(Force = TRUE)
```

원래 가이드에는 `Force = TRUE` 옵션은 없다. 해당 폴더에 파일이 있으면 에러를 띄운다. 이 메시지를 적어주면 에러를 무시하고 설치가 시작된다.  

### Theme 설치

```{R}
install_theme("kishaningithub/hugo-creative-portfolio-theme", theme_example = TRUE, update_config = TRUE)
```

테마 이름은 `github_NAME/theme_NAME` 스타일로 되어 있다.
테마 검색은 [Hugo theme](http://themes.gohugo.io)를 참고

## Setup & Deploy

### master / sources branch 생성 (in gutub.com)

깃허브 페이지 상에서 branch를 생성한다.

  * 새로운 브렌치 이름은 sources, 그리고 settings에서 default branch를 sources로 바꿔준다.
  * 브랜치를 생성한 이후 github desktop에서 sync를 눌러주거나 혹은 terminal에서 'git fetch' 입력한다.

### setup 실행
  * setup.sh 파일을 실행하여 sources / master 브랜치를 초기화한다.
  * 해당 파일을 github local에 넣고 스크립트를 terminal에서 실행한다.  
  * bash 스크립트 안에 게정 이름과 소스 이름을 바꿔주는 것을 잊지 말도록

```bash
#!/usr/bin/env bash

# This script does the required work to set up your personal GitHub Pages
# repository for deployment using Hugo. Run this script only once -- when the
# setup has been done, run the `deploy.sh` script to deploy changes and update
# your website. See
# https://hjdskes.github.io/blog/deploying-hugo-on-personal-github-pages/index.html
# for more information.

# GitHub username
USERNAME=YOUR_GITHUB_ACCOUNT
# Name of the branch containing the Hugo source files.
SOURCE=SOURCE_NAME

msg() {
    printf "\033[1;32m :: %s\n\033[0m" "$1"
}

msg "Adding a README.md file to \'$SOURCE\' branch"
touch README.md

msg "Deleting the \`master\` branch"
git branch -D master
git push origin --delete master

msg "Creating an empty, orphaned \`master\` branch"
git checkout --orphan master
git rm --cached $(git ls-files)

msg "Grabbing one file from the \`$SOURCE\` branch so that a commit can be made"
git checkout "$SOURCE" README.md
git commit -m "Initial commit on master branch"
git push origin master

msg "Returning to the \`$SOURCE\` branch"
git checkout -f "$SOURCE"

msg "Removing the \`public\` folder to make room for the \`master\` subtree"
rm -rf public
git add -u
git commit -m "Remove stale public folder"

msg "Adding the new \`master\` branch as a subtree"
git subtree add --prefix=public \
    git@github.com:$USERNAME/$USERNAME.github.io.git master --squash

msg "Pulling down the just committed file to help avoid merge conflicts"
git subtree pull --prefix=public \
    git@github.com:$USERNAME/$USERNAME.github.io.git master
```

이후에 github commit 및 동기화를 실시한다.

```git
git status
git add .
git commit -m "my messege"
git push
```

### deploy

블로그 포스트를 추가하거나 콘텐츠의 변화가 있을 경우 디플로이를 해주는 과정이 필요하다. terminal에서 `bash deploy.sh`

```bash
#!/usr/bin/env bash

# This script allows you to easily and quickly generate and deploy your website
# using Hugo to your personal GitHub Pages repository. This script requires a
# certain configuration, run the `setup.sh` script to configure this. See
# https://hjdskes.github.io/blog/deploying-hugo-on-personal-github-pages/index.html
# for more information.

# Set the English locale for the `date` command.
export LC_TIME=en_US.UTF-8

# GitHub username.
USERNAME=YOUR_GITHUB_ACCOUNT
# Name of the branch containing the Hugo source files.
SOURCE=SOURCE_NAME
# The commit message.
MESSAGE="Site rebuild $(date)"

msg() {
    printf "\033[1;32m :: %s\n\033[0m" "$1"
}

msg "Pulling down the \`master\` branch into \`public\` to help avoid merge conflicts"
git subtree pull --prefix=public \
    git@github.com:$USERNAME/$USERNAME.github.io.git origin master -m "Merge origin master"

msg "Building the website"
hugo

msg "Pushing the updated \`public\` folder to the \`$SOURCE\` branch"
git add public
git commit -m "$MESSAGE"
git push origin "$SOURCE"

msg "Pushing the updated \`public\` folder to the \`master\` branch"
git subtree push --prefix=public \
    git@github.com:$USERNAME/$USERNAME.github.io.git master
```
