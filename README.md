# Mondeique 기술 블로그 by using Tale Theme

> mondeique 개발자들의 생생한 개발 일지를 담은 블로그입니다.(https://mondeique.github.io)

## Installation

<https://github.com/mondeique/mondeique.github.io> 에 push 권한이 있다면:

1. git fetch or pull or clone
2. [Jekyll] 설치

```console
$ git clone git@github.com:kakao/kakao.github.io.git
$ cd kakao.github.io
$ bundle install
```

<https://github.com/mondeique/mondeique.github.io> 에 push 권한이 없다면:

1. <https://github.com/mondeique/mondeique.github.io> 에서 `Fork` 버튼 클릭하고,
2. 포크 저장소 계정 선택
3. git fetch or pull or clone
4. 포크 설정 [Configuring a remote for a fork](https://help.github.com/articles/configuring-a-remote-for-a-fork/)
5. 포크 동기화 [Syncing a fork](https://help.github.com/articles/syncing-a-fork/)
6. [Jekyll] 설치

```console
$ git clone git@github.com:YOUR_GITHUB_ACCOUNT/mondeique.github.io.git
$ cd mondeique.github.io
$ git remote add upstream git@github.com:mondeique/mondeique.github.io.git
$ git fetch upstream
$ git checkout main
$ git merge upstream/main
$ bundle install
```

### 새로운 글 쓰기  

1. `_posts` 디렉토리에 `yyyy-mm-dd-title.md` 파일에 작성
 - title : 작성 글 title 
 - yyyy,mm,dd: 발행 년,월,일.
2. 파일 상단에 [front matter] 작성
 - layout: post # 레이아웃(필수). `page` 레이아웃을 사용하면 목록에 보이지 않는 글을 쓸 수 있음.
 - title: '제목' # 제목(필수)
 - author: `lastname.firstname` # 필자(필수). 회사 영어이름(예: eren) 사용
 - tags: `[tag1,tag2,tag3,...]` # 태그 목록(선택). 왠만하면 특수문자없이 영소문자,숫자,-(하이픈),.(점)...만 사용.
3. 포스트를 마크다운으로 작성
  - [gfm] 문법, [kramdown] 파서, [rouge] 문법강조기 사용
4. 확인 
```
$ bundle exec jekyll serve
$ open https://localhost:4000
```

### 배포(발행)

<https://github.com/mondeique/mondeique.github.io> 에 push 권한이 있다면:

```
$ git commit -m '...'
$ git push origin main
````

<https://github.com/mondeique/mondeique.github.io> 에 push 권한이 없다면:

1. Fork 동기화 [Syncing a fork](https://help.github.com/articles/syncing-a-fork/)
2. Pull Request 보내기 [Creating a pull request](https://help.github.com/articles/creating-a-pull-request/)

### Enabling Comments
Comments are disabled by default. To enable them, look for the following line in `_config.yml` and change `jekyll-tale` to your site's Disqus id.

```yml
disqus: jekyll-tale
```

Next, add `comments: true` to the YAML front matter of the posts which you would like to enable comments for.

[Jekyll]: https://jekyllrb.com
[front matter]: https://jekyllrb.com/docs/frontmatter/
[gfm]: https://guides.github.com/features/mastering-markdown/
[kramdown]: http://kramdown.gettalong.org
[rouge]: http://rouge.jneen.net

## License
See [LICENSE](https://github.com/mondeique/mondeique.github.io/blob/master/LICENSE)
