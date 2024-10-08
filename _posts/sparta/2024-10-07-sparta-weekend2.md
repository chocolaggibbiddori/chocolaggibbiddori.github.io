---
layout: single
title: "스파르타 코딩클럽 (2주차)"
tag: sparta
---

# 학습일지

## 개요

&nbsp;&nbsp; 2주차에서는 gitlab을 이용한 CI 파이프라인 구축해보기를 학습했다.
gitlab을 처음 사용해 보기도 했고, github의 git action 보다는 조금 더 쉬웠던 것 같다.
aws cli를 사용해서 ecr에 이미지를 push 하는 것도 실습해 보았다.

## 새롭게 알게된 점

- gitlab 사용해보기
- gitlab private repository에 ssh로 연결하기
- AWS ECR 사용해보기
- gitlab CI 파이프라인 구축해보기
- Linux make를 이용해 build, push 작업 해보기

## 어려웠던 점

&nbsp;&nbsp; `make push` 명령이 제대로 동작하지 않아 이것저것 알아봤었는데,
알고보니 `Makefile`이 잘못된거였다... 파일을 올바르게 수정하고 나니 정상적으로 동작하는 것을 확인했다.

&nbsp;&nbsp; gitlab에 push를 하면 자동으로 파이프라인이 동작해야 할 것 같은데, 일일이 `gitlab-runner run` 명령을 입력해주어야 동작하는게 쉽지 않았다.
자동화 할 수 있는 방법을 찾아봐야겠다.