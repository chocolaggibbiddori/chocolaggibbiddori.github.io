---
layout: single
title: "스파르타 코딩클럽 (3주차)"
tag: sparta
---

# 학습일지

## 개요

&nbsp;&nbsp; 3주차에서는 ECS를 이용해 클러스터를 구축하고, CD까지 완성하였다.

## 새롭게 알게된 점

- ECS 사용해보기
- CD 구성하기

## 어려웠던 점

&nbsp;&nbsp; ECS 서비스 배포가 안되길래 과정을 천천히 살펴봤더니, 나는 맥북(M1)에서 이미지를 만들고 올렸기 때문에 Linux/ARM64 아키텍처로 컨테이너를 띄워야 했는데
Linux/X86_64 아키텍처를 선택해서 배포에 문제가 있었다. 이를 알아내고 Linux/ARM64 태스크를 다시 만든 후 실행하였더니 정상 동작 하였다.

&nbsp;&nbsp; 컨테이너는 잘 띄워지지만 로드밸런서가 계속 동작하지 않아 애먹었는데, 헬스 체크 매핑이 제대로 수행되지 않아 생긴 문제였다.
Python 애플리케이션은 잘 모르지만 `/` 경로에 대해 헬스 체크가 되는 것 같았다.
하지만 내가 만든 Java 애플리케이션으로 실습을 진행하니 계속 오류가 나는 것이었다.
`/health` 경로를 통해 헬스 체크를 하도록 설정함으로서 문제를 해결하였다.
