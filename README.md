# making_hugo_on_github
깃허브 위에 휴고 올리는 법 self-tutorial

# Purpose
Hugo를 github 위에 올리는 법에 관한 자기 튜토리얼이다.

# Step by Step

## github 계정 만들기 (in github.com)


생략

## github repo 새성 (in github.com)

새로운 리포를 생성한다. 이름은 YOUR_NAME.github.io

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

### Hugo papge 생성

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
테마 검색은 [Hogo theme](http://themes.gohugo.io)를 참고
