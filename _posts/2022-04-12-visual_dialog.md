---
layout : single
title : "visual dialog"
---

# Abstract 

We introduce the task of Visual Dialog, which requires an AI agent to hold a meaningful dialog with humans in natural, conversational language about visual content. Specifically, given an image, a dialog history, and a question about the image, the agent has to ground the question in image, infer context from history, and answer the question accurately. Visual Dialog is disentangled enough from a specific downstream task so as to serve as a general test of machine intelligence, while being grounded in vision enough to allow objective evaluation of individual responses and benchmark progress. We develop a novel two-person chat data-collection protocol to curate a large-scale Visual Dialog dataset (VisDial). VisDial v0.9 has been released and contains 1 dialog with 10 question-answer pairs on ∼120k images from COCO, with a total of ∼1.2M dialog questionanswer pairs.

Visual Dialog의 작업을 소개합니다. 이것은 AI 에이전트가 시각적 콘텐츠에 대해 자연스럽고 대화적인 언어로 인간과 의미 있는 대화를 해야 합니다. 구체적으로, 이미지, 대화기록 및 이미지에 대한 질문이 주어지면 에이전트는 이미지에 질문의 근거를 두고 기록에서 맥락을 추론하고 질문에 정확하게 대답해야 합니다. Visual Dialog는 기계 지능의 일반적인 테스트 역할을 하기 위해 특정 다운스트림 작업과 충분히 얽혀있으면서 개별 응답 및 벤치마크 진행 상황을 객관적평가할 수 있을 만큼 충분히 비전에 기반을 둡니다. 우리는 대규모 Visual Dialog 데이터 세트를 선별하기 위해 새로운 2인 채팅 데이터 수집 프로토콜을 개발합니다. visDial v0.9가 출시되었으며 COCO의 ~120k 이미지에 대한 10개의 질문-답변 쌍이 있는 1개의 대화 상자와 총 ~120만 개의 대화 질문 대답 쌍이 포함되어 있습니다.

We introduce a family of neural encoder-decoder models for Visual Dialog with 3 encoders – Late Fusion, Hierarchical Recurrent Encoder and Memory Network– and 2 decoders (generative and discriminative), which outperform a number of sophisticated baselines. We propose a retrieval-based evaluation protocol for Visual Dialog where the AI agent is asked to sort a set of candidate answers and evaluated on metrics such as mean-reciprocal-rank of human response. We quantify gap between machine and human performance on the Visual Dialog task via human studies. Putting it all together, we demonstrate the first ‘visual chatbot’! Our dataset, code, trained models and visual chatbot are available on https://visualdialog.org.

3개의 인코더가 있는 Visual Dialog용 신경 인코더-디코더 모델 제품군을 소개합니다. Late Fusion, 계층적 순환 인코더 및 메모리 네트워크, 그리고 2개의 디코더(생성 및 판별)는 정교한 기준선을 능가합니다.우리는 검색을 제안합니다. 우리는 AI 에이전트가 일련의 후보 답변을 정렬하고 인간 응답의 평균 역수와 같은 메트릭에 대해 평가하는 프로토콜을 제안합니다. 
우리는 인간 연구를 통해 Visual Dialog 작업에서 기계와 인간의 성능 사이의 격차를 정량화합니다. 이를 종합하여 최초의 '비주얼 챗봇'을 시연합니다! 데이터세트, 코드, 훈련된 모델 및 시각적 챗봇은 https://visualdialog.org에서 사용할 수 있습니다.

# Introduction
Weare witnessing unprecedented advances in computer vision (CV) and artificial intelligence (AI)– from ‘low-level’ AI tasks such as image classification [20], scene recognition [63], object detection [34]– to ‘high-level’ AI tasks such as learning to play Atari video games [42] and Go [55], answering reading comprehension questions by understanding short stories [21, 65], and even answering questions about images [6,39,49,71] and videos [57,58]!

What lies next for AI? We believe that the next generation of visual intelligence systems will need to posses the ability to hold a meaningful dialog with humans in natural language about visual content. 

우리는 차세대 시각 지능 시스템이 시각 콘텐츠에 대해 자연 언어로 인간과 의미 있는 대화를 나눌 수 있는 능력이 필요하다고 믿습니다.

Applications include: 
• Aiding visually impaired users in understanding their surroundings [7] or social media content [66] (AI: ‘John just uploaded a picture from his vacation in Hawaii’, Human: ‘Great, is he at the beach?’, AI: ‘No, on a mountain’). 
• Aiding analysts in making decisions based on large quantities of surveillance data (Human: ‘Did anyone enter this room last week?’, AI: ‘Yes, 27 instances logged on camera’, Human: ‘Were any of them carrying a black bag?’),
• Interacting with an AI assistant (Human: ‘Alexa– can you see the baby in the baby monitor?’, AI: ‘Yes, I can’, Human: ‘Is he sleeping or playing?’). 
• Robotics applications (e.g. search and rescue missions) where the operator may be ‘situationally blind’ and operating via language [40] (Human: ‘Is there smoke in any room around you?’, AI: ‘Yes, in one room’, Human: ‘Go there and look for people’).

Despite rapid progress at the intersection of vision and language– in particular, in image captioning and visual question answering (VQA)– it is clear that we are far from this grand goal of an AI agent that can ‘see’ and ‘communicate’. In captioning, the human-machine interaction consists of the machine simply talking at the human (‘Two people are in a wheelchair and one is holding a racket’), with no dialog or input from the human. While VQAtakesasignificant step towards human-machine interaction, it still represents only a single round of a dialog– unlike in human conversations, there is no scope for follow-up questions, no memory in the system of previous questions asked by the user nor consistency with respect to previous answers provided by the system (Q: ‘How many people on wheelchairs?’, A: ‘Two’; Q: ‘How many wheelchairs?’, A: ‘One’). 

특히 이미지 캡션과 VQA(시각적 질문 답변) 분야에서 시각과 언어의 교차점에서 급속한 발전에도 불구하고 '보고', '소통'할 수 있는 AI 에이전트의 이 원대한 목표와는 거리가 멀다는 것이 분명합니다. 캡션에서 인간-기계 상호 작용은 인간의 대화나 입력 없이 단순히 인간에게 말하는 기계로 구성됩니다('두 사람은 휠체어에 있고 한 사람은 라켓을 잡고 있습니다').
VQA는 인간-기계 상호 작용을 향한 중요한 단계를 취하지만 여전히 대화의 한 라운드만 나타냅니다. 인간 대화와 달리 후속 질문의 범위가 없으며 시스템에 사용자가 묻는 이전 질문에 대한 메모리나 일관성이 없습니다. 시스템에서 제공한 이전 답변과 관련하여(Q: '휠체어를 탄 사람은 몇 명입니까?', A: '둘', Q: '휠체어가 몇 개입니까?', A: '하나')

