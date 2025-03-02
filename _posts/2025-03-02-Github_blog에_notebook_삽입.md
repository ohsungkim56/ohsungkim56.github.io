---
title: "Github blog에 notebook 삽입"
author: ohsungkim56

categories:
  [Blog]
tags:
  [Blog]

toc: true

date: 2025-03-02
# last_modified_at: 2025-03-02
---

Jekyll 블로그는 기본적으로 Markdown 형식의 파일을 사용하여 포스트를 작성하는데,
Jupyter Notebook(.ipynb) 파일을 직접 Markdown 형식으로 사용할 수 없으므로 변환 과정이 필요했다. 

관련 툴을 찾아본 결과, `nbconvert`라는 툴이 있었고, 이를 사용하여 Jupyter Notebook을 다른 형식으로 변환하였다.

`nbconvert`는 Notebook을 `HTML 파일`로 변환할 때, 그 안의 이미지 파일은 base64 인코딩되어 HTML 파일 내에 삽입해줘서, 이로 인해 **모든 Notebook내용이 하나의 파일로 출력**되어 HTML 단일 파일로 사용할 수 있었다.

`nbconvert`는 Notebook을 `Markdown 파일`으로 변환하는 옵션도 제공하지만... 이 경우, 변환 후 **md파일과 이미지 파일로 분리**되어 경로 및 파일 관리가 어려울 것으로 보여, 이 방법을 사용하지 않았다.


### 1. notebook 파일을 html으로 변환

```sh
pip install -y jupyter nbconvert # notebook을 html 파일로 변환하기 위해서 설치
jupyter nbconvert MyNotebook.ipynb --to html # 변환
```

### 2. 변환한 html 파일 업로드
```
{blog directory}/assets/html/{날짜}/{post이름}/{HTML 파일}
```
생성한 HTML 파일을 위 경로로 위치 시켰다. 경로 설정은 블로그마다 다를 수 있다. 

### 3. embed/notebook.html 생성 
```html
{% if include.full_content %}
  <script>
    function __iframe_loaded(t) {
          var iframeDocument = t.contentDocument || t.contentWindow.document;
          t.style.height = iframeDocument.documentElement.scrollHeight + 'px';
        }
  </script>
  <iframe
    loading="lazy"
    frameborder="0"
    src="{{ include.src }}"
    style="margin-bottom: 1rem;  width: 100%"
    onload="__iframe_loaded(this)"></iframe>
{% else %}
  <iframe
    loading="lazy"
    frameborder="0"
    src="{{ include.src }}"
    style="margin-bottom: 1rem; 
      width: {{ include.width | default : '100%'}}; 
      height: {{ include.height | default : '100%' }}; 
      aspect-ratio: {{ include.ratio | default : '4/3' }};"
  ></iframe>
{% endif %}
```
삽입하기 위한 template(?)을 생성하였다.

### 4. markdown에 html 파일 삽입
```liquid
{% include embed/notebook.html src="/assets/html/{날짜}/{post이름}/{HTML 파일}" %}
```

```liquid
# default size 설정 width="100%" height="100%" aspect-ratio="4/3"
{% include embed/notebook.html src="/assets/html/{날짜}/{post이름}/{HTML 파일}" width="100%" height="100%" aspect-ratio="4/3" %} 

```

### 5. 스크롤 없이 html 파일 전체 내용 삽입
```markdown
{% include embed/notebook.html src="/assets/html/{날짜}/{post이름}/{HTML 파일}" full_content="true" %}
```