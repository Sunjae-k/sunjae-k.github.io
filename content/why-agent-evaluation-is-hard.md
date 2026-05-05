---
title: "AI Agent 평가는 왜 어려운가"
date: 2026-05-05
updated: 2026-05-05
draft: false
tags:
  - ai-agent
  - evaluation
  - llm-as-judge
  - observability
description: "정답률 하나로는 설명되지 않는 agent evaluation의 기준과 실패 유형"
---

# AI Agent 평가는 왜 어려운가

LLM 애플리케이션을 만들다 보면 결국 평가 문제가 온다. 처음에는 답변이 그럴듯한지만 보면 된다. 하지만 agent가 도구를 쓰고, 여러 단계를 거치고, 사용자의 상태를 바꾸기 시작하면 “좋은 답변인가?”만으로는 부족하다.

AI Agent 평가는 어려운 이유가 있다. agent의 결과는 문장 하나가 아니라 **과정과 행동의 묶음**이기 때문이다.

## 정답률만으로는 부족하다

전통적인 분류 모델이라면 정답과 오답을 비교하기 쉽다. 하지만 agent는 여러 층에서 실패할 수 있다.

- 올바른 도구를 선택했는가
- 필요한 정보를 충분히 검색했는가
- 근거와 답변이 일치하는가
- 사용자에게 물어야 할 때 물었는가
- 민감한 행동 전에 승인을 받았는가
- 실패했을 때 안전하게 멈췄는가

최종 답변이 맞아 보여도 과정이 위험할 수 있고, 과정은 좋았지만 마지막 설명이 부족할 수 있다. 그래서 agent 평가는 outcome과 trace를 같이 봐야 한다.

## LLM-as-Judge는 시작점이지 종착점이 아니다

LLM-as-Judge는 유용하다. 사람이 매번 모든 답변을 평가할 수 없기 때문에, LLM이 기준에 따라 답변 품질을 채점하게 하면 빠른 반복이 가능하다.

하지만 judge도 모델이다. 기준이 흐리면 흔들리고, 긴 답변에 관대해지고, 근거 없는 자신감에 속을 수 있다. 그래서 judge prompt에는 평가 기준이 명확해야 하고, 가능하면 reference answer나 rubric이 있어야 한다.

좋은 judge는 “좋음/나쁨”을 말하는 모델이 아니라, 어떤 기준을 통과했는지 실패했는지를 분리해 보는 검사기다.

## Trace-based evaluation이 필요하다

Agent는 내부에서 여러 행동을 한다. 따라서 최종 답변만 보면 원인을 놓친다.

Trace-based evaluation은 tool call, retrieval result, intermediate state, latency, error, retry 같은 실행 흔적을 본다. 이 방식은 특히 다음 문제를 잡는 데 중요하다.

- 필요 없는 tool call이 많다.
- retrieval은 맞았는데 final answer가 왜곡됐다.
- 같은 실패 경로를 반복한다.
- 비용이 높은 모델을 불필요하게 호출한다.
- 사용자 승인 없이 위험한 행동으로 넘어간다.

Agent를 운영한다는 것은 trace를 읽는다는 뜻에 가깝다.

## 데이터셋은 production log에서 자란다

처음부터 완벽한 평가 데이터셋을 만들기는 어렵다. 대신 운영 중 나온 실패 사례를 모아야 한다.

사용자가 불만을 표시한 질문, agent가 오래 걸린 질문, tool error가 난 세션, 사람이 수동으로 고친 답변은 모두 평가 데이터가 된다. 이 사례들을 regression set으로 만들면 agent를 수정할 때 같은 실패를 반복하지 않을 수 있다.

평가 데이터셋은 한 번 만드는 문서가 아니라 계속 자라는 운영 자산이다.

## 평가 도구는 목적별로 다르게 쓴다

OpenEvals, LangSmith, RAGAS, DeepEval 같은 도구는 서로 대체재라기보다 보는 층이 다르다.

- RAGAS류는 retrieval, faithfulness, answer relevance를 보기 좋다.
- DeepEval류는 테스트 케이스와 LLM judge 기반 검사를 구성하기 좋다.
- LangSmith류는 trace, dataset, experiment 비교와 운영 관찰에 강하다.
- OpenEvals류는 evaluator를 코드와 가까운 곳에서 구성하기 좋다.

중요한 것은 도구 이름이 아니라 평가 질문이다. “무엇을 실패로 볼 것인가?”가 먼저 정해져야 한다.

## 결론: Agent 평가는 제품 운영의 일부다

AI Agent 평가는 출시 전 체크리스트가 아니다. 운영 중 계속 돌아가는 품질 회로다.

좋은 agent evaluation은 세 가지를 같이 본다.

1. 최종 답변이 맞는가
2. 실행 과정이 안전하고 효율적인가
3. 같은 실패를 다시 막을 수 있는가

정답률 하나로는 agent를 설명할 수 없다. Agent는 답변하는 모델이 아니라 행동하는 시스템이기 때문이다.
