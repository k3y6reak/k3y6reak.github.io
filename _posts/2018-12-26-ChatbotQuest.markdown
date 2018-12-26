---
layout: post
title:  "ChatBot Quest"
subtitle: "SSAFY에서 진행한 Slack 챗봇 프로젝트 입니다."
date:   2018-12-26 19:43:00 +0900
categories: ['misc']
comments: true
---

# Python Error Solution Bot

광주_3_박사홍 / https://github.com/k3y6reak/PESB

## I. 스펙(Speicification)
구현된 어플리케이션의 주요 기능.

### (1) 단순 호출
단순 호출시에 명령어 사용을 위한 메시지를 출력.

### (2) command 명령
command 명령 호출시 존재하는 명령어 목록을 출력.

### (3) Find 명령
Find: Errorname 을 호출시 해당 에러에 대한 원인, 해결방안, 공식문서의 내용, 스택오버플로우의 채택 답변 크롤링.

## II. 회고(Retrospective)
어플리케이션 구현 과정에서의 어려움과 문제점

### (1) 명령어 입력
사용자가 Find: Errorname을 입력할 때 `Find:` 와 `Errorname` 사이의 공백을 신경쓰지 않을 경우.
또는 영문자 입력시 대소문자를 신경쓰지 않을 경우.

### (2) 스택오버플로우 크롤링
교육동안 배운 방식으로 스택오버플로우의 내용을 크롤링한 내용이 제대로 출력되지 않음.
다른 방법을 찾아서 해결함.

## III. 보완 계획(Feedback)
현재 미완성이지만 추가로 구현할 기능 및 기존 문제점 보완 계획.

### (1) 에러 목록 추가하기.
현재 구현된 에러 목록은 일부분만 추가되었기 때문에, 많은 에러 목록을 추가하는 것.