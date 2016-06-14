## Jekyll & git-page 연동해 만든 블로그 레파지토리 입니다.

## Jekyll & git-page 연동법

### git repository 생성
* 페이지로 만들 레파지토리를 생성해야합니다. 이때 레파지토리 이름은 **[github계정].github.io** **[github계정].github.com** 라는 이름으로 만들어야 합니다. ex) sooyoung32.github.io 
* 생성 후 이 레파지토리를 내 컴퓨터에 clone 합니다.
그럼 git page에서 할 준비는 끝!!

### jeykyll
깃 페이지에도 여러 테마가 있지만 딱히 마음에 드는것이 없었습니다. 따라서 좀 더 다양한 테마가 있는 지킬을 이용해 github와 연동해 블로그를 관리하고 싶었습니다.

#### jeykyll 설치
지킬을 이용하기위해 설치를 해야합니다. 그리고 지킬을 설치하기위해선 ruby를 설치해야합니다.

##### ruby 설치
루비는 다양한 버전이 존재합니다. 따라서 루비 버전 관리인 rvm 을 설치하고 관리하는것이 훨씬 편리합니다.. (저는 여기서 삽질을 좀...휴) 지킬을 사용하기위해선 ruby 2.x 버전을 사용해야합니다. 저는 우분투를 사용하고 있는데, apt-get install ruby 하면 1.9.x 버전이 다운로드 되니 이 점 주의하세요. 

##### rvm 설치 
rvm 설치를 위해서는 https://rvm.io/rvm/install 와 블로그 [멘붕없이 rvm과 루비 설치](http://bigmatch.i-um.net/2013/12/%EB%A9%98%EB%B6%95%EC%97%86%EC%9D%B4-rvm%EA%B3%BC-%EB%A3%A8%EB%B9%84-%EC%84%A4%EC%B9%98%ED%95%98%EA%B8%B0/))

저는 이렇게 받았습니다.(~~던것 같습니다~~)
```
$ gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3
$ \curl -sSL https://get.rvm.io | bash
$ \curl -sSL https://get.rvm.io | bash -s stable --without-gems="rvm rubygems-bundler"
```

##### ruby 설치
```
$ rvm install 2.0.0
```

##### jekyll 설치
드디어 지킬 설치!!! 참고 : https://jekyllrb.com/docs/installation/

```
$ gem install jekyll
```

### 지킬 테마 선택

* 지킬 설치를 마치셨다면 [이곳](http://jekyllthemes.org/) 마음에 드는 테마를 설치합니다.

### git-repository 에 clone

* 마음에 드는 테마를 처음에 만든 git repository (github계정.github.io) 에 clone 합니다.

* 아마 웬만한 테마에 Gemfile을 가지고 있을거기 때문에 이에 필요한것을 설치하기위해
```
$ gem install bundler // 번들러 설치
$ bundle install
```
* 하면 테마를 사용하기위한 라이브러리가 설치됩니다.

### 실행
드디어!! 지킬을 실행합니다.

```
$ jekyll serve
```

그리고 http://localhost:4000으로 들어가면 실행되시는것을 알수 있습니다.


### 마무리
* 다운 받으신 테마 README를 보며 사용법을 숙지 및 _config.yml 설정파일을 바꿔준신후 해당 레파지토리에 푸시합니다.
* 그리고 [github계정].github.io 로 접속하시면 블로그에 접속가능합니다^^!!
