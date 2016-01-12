---
layout: post
title:  "make git page with jekyll"
date:   2016-01-12 10:05:16 +0900
categories: jekyll
---

#### Jekyll로 github-pages 만들기
1. Jekyll 설치
 *	Ruby 설치 필요.
2. Jekyll new [github username].github.com or [github username].github.io
	* 주소는 중요합니다.
3. github repository [github username].github.com 이름으로 생성
4. git 연동
```
git init
git remote add origin 저장소URL
git add .
git commit -m "first commit"
git push origin master
```
자세한 사항은 [여기 link](http://nolboo.github.io/blog/2013/10/15/free-blog-with-github-jekyll) 를 참고하세요.
