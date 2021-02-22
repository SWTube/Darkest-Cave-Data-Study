# Darkest-Cave-Data-Study
Darkest-Cave-Data-Study
This repository was made just for studying.

<HR>

# GDC 영상 시청 요약

## 목차 
- [1주차] 
 [Building An Intelligent Game Testing System in Netease MMORPG Games](#Building-An-Intelligent-Game-Testing-System-in-Netease-MMORPG-Games )
- [2주차]
  [Applying Reinforcement Learning to Develop Game AI in NetEase Games](#applying-reinforcement-learning-to-develop-game-ai-in-netease-games) 


<hr>

# Building An Intelligent Game Testing System in Netease MMORPG Games 

# 0. 개요

- [1. 서론](#1-서론)    
  * [1.1 게임 테스팅](#11-게임-테스팅)    
  * [1.2 테스팅을 하는 이유는?](#12-테스팅을-하는-이유는)    
  * [1.3 테스팅의 발전](#13-테스팅의-발전)
- [2. Regression Testing  of Quests](#2-regression-testing--of-quests)    
  * [2.1 Quest 에서 회귀 시험을 실행하기 어려운 이유](#21-quest-에서-회귀-시험을-실행하기-어려운-이유)    
  * [2.3 과거 자동화된 회귀 시험 프레임워크의 절차](#23-과거-자동화된-회귀-시험-프레임워크의-절차)        
    * [기존 방법의 문제점](#기존-방법의-문제점)    
  * [2.4 퀘스트 회귀 시험을 어떻게 향상시킬 수 있을까?](#24-퀘스트-회귀-시험을-어떻게-향상시킬-수-있을까)       
    * [고안](#고안)        
    - [states 와 actions](#states-와-actions)        
    - [회귀 시험의 3가지 과정](#회귀-시험의-3가지-과정)    
    - [How to choose action while searching](#how-to-choose-action-while-searching)    
    - [Workflow of our searching procedures](#workflow-of-our-searching-procedures)    
    - [새로운 quest 에 대한 탐색 속도를 높이는 방법](#새로운-quest-에-대한-탐색-속도를-높이는-방법)        
    - [과정](#과정)        
    - [결과](#결과)
- [3. Class Balance Testing](#3-class-balance-testing)        
  * [3.1 설명](#31-설명)        
  * [3.2 경험에 의존한 방법들](#32-경험에-의존한-방법들)    
  * [3.3 클래스 밸런스를 테스트하기 위해 고안된 프레임워크 사용.](#33-클래스-밸런스를-테스트하기-위해-고안된-프레임워크-사용)    
  * [3.4 예시](#34-예시)
 
 
#### 영상 내용
중국게임 “Justice” 에서 채택한 두 유형의 테스팅에  대해 다룬다. 
- Regression Testing of Quests 
- class Balance Testing

 
![image](https://user-images.githubusercontent.com/68385605/108742620-38d76700-757b-11eb-8cfe-3342ae027d33.png)

#### 짧은 요약 

#### Regression Testing of Quests 
- 더 많은 mmo rpg 게임에 적용된다.
- 가장 빠른 방법이 최선의 방법은 아닐 수 있다.
- 비 결정적 게임에서 더 탄탄하다

#### Class balance Testing
- RL traning 의 비용을 감소시켜준다.
- PVP 팀 밸런싱 테스팅으로 확장가능하다. 

# 1. 서론

## 1.1 게임 테스팅 
* 게임을  테스팅하는것은 개발에 있어 중요한 부분이다. 
* 실제로 여러 기업들에서 발생되는 사고들을 보면 테스팅의 부족으로 발생되는 경우가 대다수이다. 이를 통해 테스팅의 중요성을 알 수 있다.  

## 1.2 테스팅을 하는 이유는? 
* 게임 퀄리티의 보장하기 위해
* 운영 사고를 피하기 위해
* 사용자 경험(player experience) 를 향상시키기 위해

## 1.3 테스팅의 발전

AI 와 머신러닝 기술 향상은 급속도로 성장하고 있다.
아래와 같은 기술들의 발전은 더 많은 인공지능 게임 테스팅을 가능하도록 해준다. 
- Evalutionary Algorithm 
- Reinforcement Learning
- Deep Neural Network 

![image](https://user-images.githubusercontent.com/68385605/108742511-1cd3c580-757b-11eb-9d26-b1020fd86d75.png)

# 2. Regression Testing  of Quests

Quests design 은 사용자가 게임스토리에 몰입하는 것과 게임세계관에 친숙해지는것을 돕는다는 점에서 MMORPG 게임의 주춧돌이라고 할 수 있다. 각각의 스토리는 몇 가지 task  Step 들로 구성되어 있다. 
Step 은   퀘스트의 목적, 몬스터의 처치 등과 같은  독립적인 구성단위를 의미한다.
이러한 Quests design 에서 회귀시험을 하는것이 매우 중요하지만, 퀘스트에 대해 회귀시험을 하는것은 시간이 많이 걸리고 시험하는 인원을 확보하기가 어렵다.


## 2.1 Quest 에서 회귀 시험을 실행하기 어려운 이유

-  메인 스토리, 사이드 스토리, 등장 스토리등 각각 스토리에서 quest 수가 너무 많음  
- 시험을 실행하는데 시간이 많이 듬
-  잦은 게임 반복
- 서로 다른 퀘스트임에도 각각에 영향을 미침
  *  ex) 게임 디자이너가 미션 A에 대한 게임맵을 수정했는데, 수정된 맵이 미션 B 의 게임플레이에 사용되고 있다면  가이드 NPC가 미션 B 의 맵에서 사라져, 퀘스트를 수행할 수 없는 상황이 발생할 수 있음


-  자동화된 테스트 스크립트를 이용한다고 하더라도, 작성하고 유지하는것이 어려움
   * ex) 여러가지 상황에 따른 테스트스크립트를 빈번히 바뀌는 게임 상황에서 유지보수하는것이 힘듬



## 2.3 과거 자동화된 회귀 시험 프레임워크의 절차

 
![image](https://user-images.githubusercontent.com/68385605/108743102-cf0b8d00-757b-11eb-8700-8018f7d2010b.png)


### 기존 방법의 문제점 
- 1000 개 이상의 테스팅 스크립트를 작성해야 함.
- 많이 작성하지만 실제로 커버되는건 몇가지 경우에 불과함
- 새로운 quest 가 발생하면 테스트 스크립트도 또 만들어야함
- 모든 스크립트를 작성을 완료하려면  끊임없이 일해야함
- 퀘스트가 변경되면 스크립트또한 변경해야함


## 2.4 퀘스트 회귀 시험을 어떻게 향상시킬 수 있을까?

### 고안 
과거 테스팅은 기본적으로 `code-based` 모델이다. 만약 스스로 퀘스트를 끝낼 수 있도록 인공지능 테스터를 만든다면 위와같은 문제의 해결이 가능할 것이다.

또한 모델을 재정의할 필요가 있다. 사람이 보는 관점에서. 즉, 무언가를 보고 무언가 행동을 하는 모델을 만들어야 한다.

### states 와 actions 
- states 는 현재 게임 정보의 추상화(abstraction) 인데 서로다른 퀘스트의 진행을 통해 구별되어야 한다.


![image](https://user-images.githubusercontent.com/68385605/108743289-fd896800-757b-11eb-8c5d-349796a0cca6.png)

- actions 는 게임플레이어가 게임에서 하는 행동을 의미한다.

![image](https://user-images.githubusercontent.com/68385605/108743474-26116200-757c-11eb-8a4d-65b48ecacba1.png)


### 회귀 시험의 3가지 과정 

- search/learning
- planning/exploiting
- speeding up search

 
![image](https://user-images.githubusercontent.com/68385605/108743613-4e995c00-757c-11eb-84b9-750212bb95ff.png)


- 문제가  Markov Decision Process(MDP) 에서 모델링되어서 quest 의 탐색 그래프를 구성할 수 있다.  이를통해 quest 들을 달성하기 위한 최적 행동 순서를 찾는다.
- 퀘스트가 달성되기 전에 퀘스트 그래프를 탐색하고 만든다.  
- 최적 순서 경로를 찾는다.  
A → Action1 → B → Action2 → C …

![image](https://user-images.githubusercontent.com/68385605/108743684-61139580-757c-11eb-913a-04afe9d8a2f1.png)

## How to choose action while searching

- 방법 
  - Monte Carlo Tree Search(MCTS) with UCB strategy 를 이용한다. 

![image](https://user-images.githubusercontent.com/68385605/108743778-82748180-757c-11eb-9e23-914aa6e8f726.png)



- 특징 
  - 착취탐사를 이용한다. (exploitation exploration trade off)
  - 두 노드간 무한 반복을 회피할 수 있다. 

  
![image](https://user-images.githubusercontent.com/68385605/108743905-ad5ed580-757c-11eb-9b43-90184ddd4e55.png)

## Workflow of our searching procedures  

- 게임의 모든 정보를 받고 핵심 정보를 추출한 다음 action 스페이스 생성한다.
- 아래 과정을 계속 반복한해 quests 를 위한 최적의 경로를 찾는다. 


![image](https://user-images.githubusercontent.com/68385605/108743968-bf407880-757c-11eb-9502-c7872040a1e3.png)


##  새로운 quest 에 대한 탐색 속도를 높이는 방법

실제 사용자는 가능한 행동 경우의 수를 모두 수행하지 않는다. 게임 옆 tips 나 설명을 읽고 퀘스트를 하러 가기 때문에 퀘스트를 달성하는 경우의 수는 사실상 몇 안된다.

이러한 점으로 미루어 보았을 때 tips and description (사용자에게 주는 설명및 힌트란) 을 이용해 테스트의 빌딩속도를 높일 수 있다.  

### 과정 

- 1.NPC 가 주는 힌트를 확인한다. 
ex) leave for army camp with qi shaoshang

- 2.1 힌트의 엔티티(핵심 정보)를 추출한다. 이때 엔티티 추출에는 자연어처리(NLP) 를 활용할 수 있다. 
ex) army canp , qi shaoshang  
- 2.2 행동 분류를 수행한다.

- 3.엔티티 정보를 검색한다.
- 4.후보 행동들을 생성한다.

### 결과 

이런 과정을 통해 속도를 3배가량 증가시킬 수 있다. 

# 3. Class Balance Testing

### 3.1 설명 
각기 다른 게임 클래스들이 각 게임의 특성과 스킬 시스템에 기반에 디자인되었다. 클래스 밸런스(class balance) 는 어렵지만 MMORPG 에서 매우 중요하다. 

### 3.2 경험에 의존한 방법들 
- 게임디자이너와 함께 게임을 런칭하기 전에 비용이 많이드는 플레이테스트들에 의존한다.
-  런칭한다음에 플레이어 피드백에 따라 조정한다.

## 3.3 클래스 밸런스를 테스트하기 위해 고안된 프레임워크 사용. 

 
![image](https://user-images.githubusercontent.com/68385605/108744240-10506c80-757d-11eb-96b4-f648e9db11d6.png)

![image](https://user-images.githubusercontent.com/68385605/108744262-16dee400-757d-11eb-871e-4cb7c7cef1a3.png)

## 3.4 예시 

예를들어 Justice Online 게임에서 새로운 Long Yin 클래스를 만들고, 밸런싱 테스트를 통해 새로운 클래스가 기존에 있는 클래스과 견줄 수 있는지 비교할 수 있다. 

![image](https://user-images.githubusercontent.com/68385605/108744313-23633c80-757d-11eb-94ab-595683d21ddb.png)

 
![image](https://user-images.githubusercontent.com/68385605/108744367-3118c200-757d-11eb-86ee-5926757699a8.png)




# applying-reinforcement-learning-to-develop-game-ai-in-netease-games 

시청 예정
 