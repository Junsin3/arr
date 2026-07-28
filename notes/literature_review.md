# Literature Review Notes

이 문서는 원고에 바로 넣기 전, 웹 GUI 학습 레시피와 본 연구의
차별점을 1차 논문 기준으로 추적하는 작업 노트다.

## 핵심 비교축

1. perception/grounding을 agent 학습보다 먼저 수행하는가?
2. 화면 수준 description을 사용하는가?
3. description의 정보량 또는 길이를 통제해 비교하는가?
4. PT 직후와 downstream stage 이후를 모두 평가하는가?
5. 최종 trajectory/agent 성능까지 전이를 측정하는가?

## 논문별 정리

### Dense and Aligned Captions (DAC) (Doveh et al., 2023)

- 논문: https://arxiv.org/abs/2305.19595
- 일반 이미지--텍스트 사전학습에서 caption의 두 속성, 즉
  image--text alignment와 화면 세부사항을 얼마나 빠짐없이 언급하는지
  나타내는 density가 compositional reasoning을 제한할 수 있다고
  분석한다.
- CC3M caption을 더 dense하고 image-aligned하게 보정해 CLIP을
  미세조정했을 때 compositional reasoning 성능이 base model보다
  최대 약 27%, 가장 강한 baseline보다 최대 약 20% 향상되었다고
  보고한다.
- 우리 논문에 쓸 수 있는 근거:
  - caption은 단지 image--text pair의 부가 문장이 아니라, 어떤
    시각 속성·관계·상태가 표현에 남는지를 결정하는 supervision이다.
  - Short/Long을 단순 token-length 비교가 아니라
    ``description density/granularity'' 비교로 정의할 이론적 근거다.
- 우리 연구와의 차이와 주의:
  - DAC는 자연 이미지에 대한 contrastive VL 학습과 compositional
    reasoning을 다룬다. 웹 GUI나 autoregressive MLLM, grounding,
    trajectory 전이는 평가하지 않는다.
  - 따라서 Long description이 GUI agent에도 유리하다는 직접
    증거가 아니라 가설의 출발점으로 인용해야 한다.

### WebSRC (Chen et al., 2021)

- 논문: https://aclanthology.org/2021.emnlp-main.343/
- 6.4K webpages에서 HTML, screenshot, metadata와 함께 400K QA
  pairs를 구축한다. 질문에 답하려면 텍스트 의미뿐 아니라 웹페이지
  구조도 이해해야 한다고 정의한다.
- 우리 논문에 쓸 수 있는 근거:
  - 웹 이해를 OCR 또는 텍스트 추출만으로 환원할 수 없고, 구조와
    시각적 배치를 함께 평가해야 한다는 고전적 근거.
  - understanding 평가군에 WebSRC를 두는 이유를 설명할 때 적합하다.
- 주의:
  - agent 학습이나 caption granularity의 근거로 쓰기보다는
    webpage understanding benchmark의 계보와 평가 범위를 설명하는
    인용으로 제한한다.

### SeeClick (Cheng et al., 2024)

- 논문: https://arxiv.org/abs/2401.10935
- Qwen-VL에 GUI grounding continual pre-training을 약 10K step
  수행한 뒤 MiniWob, AITW, Mind2Web에 각각 적응한다.
- PT 과제는 referring expression에서 point 또는 bounding box를
  예측하는 형태다. 화면 전체 description의 상세도를 다루지 않는다.
- 여러 PT checkpoint에서 ScreenSpot 향상과 downstream agent 성능
  향상이 함께 나타난다고 보고한다.
- 우리 논문에 쓸 수 있는 근거:
  - GUI grounding을 agent 학습 이전의 기반 능력으로 보는 관점.
  - PT의 중간 checkpoint와 downstream 성능을 함께 보는 설계의
    선행 근거.
- 우리 연구와의 차이:
  - SeeClick은 grounding PT의 유무/진행 정도를 분석한다.
  - 우리는 grounding 양을 동일하게 고정하고 화면 description의
    상세도만 바꾼다.

### UGround (Gou et al., 2025)

- 논문: https://arxiv.org/abs/2410.05243
- 1.3M screenshots와 10M GUI elements 규모의 web 중심 synthetic
  grounding data로 universal GUI grounder를 학습한다.
- grounding, offline agent, online agent 평가를 연결하여 강한
  grounding이 downstream agent에 기여할 수 있음을 보인다.
- 우리 논문에 쓸 수 있는 근거:
  - web에서 합성한 grounding supervision이 mobile/desktop으로
    일반화될 수 있다는 근거.
  - 동일한 1.2M grounding mixture를 Short/Long 양쪽에 넣는 설계의
    배경.
- 주의:
  - UGround 자체는 description granularity 실험이 아니다.

### OS-Atlas (Wu et al., 2024)

- 논문: https://arxiv.org/abs/2410.23218
- Windows, Linux, macOS, Android, web을 포괄하는 13M+ GUI element
  grounding corpus를 합성한다.
- grounding 및 OOD agent 성능을 foundation action model의 핵심
  목표로 둔다.
- 우리 논문에 쓸 수 있는 근거:
  - cross-platform GUI agent가 대규모 grounding corpus를 기반
    단계로 사용한다는 학습 관행.
- 우리 연구와의 차이:
  - 데이터 규모와 플랫폼 확장이 중심이며, 동일 화면에 대한
    description의 선택적/포괄적 언어화를 비교하지 않는다.

### Aguvis (Xu et al., 2024)

- 논문: https://arxiv.org/abs/2412.04454
- Stage 1은 general GUI grounding, Stage 2는 planning and reasoning
  training으로 구성된다.
- Base, grounding-only(Aguvis-G), full model을 비교하여 각 stage의
  영향을 평가한다.
- reasoning/inner-monologue를 제거하면 ScreenSpot과
  AndroidControl이 함께 하락한다고 보고한다.
- 우리 논문에 쓸 수 있는 근거:
  - grounding→planning/reasoning의 단계적 학습 구조.
  - 중간 checkpoint를 남겨 효과가 어느 stage에서 생기는지
    분석해야 한다는 직접적인 선행 사례.
- 우리 연구와의 차이:
  - Aguvis의 ablation은 grounding stage와 reasoning stage의
    존재를 비교한다.
  - 우리는 downstream stage를 동일하게 고정하고 PT description
    granularity가 이후까지 남는지를 추적한다.

### ShowUI (Lin et al., 2024)

- 논문: https://arxiv.org/abs/2411.17465
- grounding과 navigation data를 interleaved
  vision-language-action stream으로 통합한다.
- 256K 규모의 선별된 instruction-following data와 data-type
  imbalance를 줄이는 resampling을 강조한다.
- 우리 논문에 쓸 수 있는 근거:
  - 총 데이터 규모뿐 아니라 과제 혼합과 sampling이 GUI agent
    성능의 중요한 교란변수라는 점.
- 우리 연구와의 연결:
  - Short/Long 조건에 같은 grounding data, downstream sample ID,
    순서를 사용하는 이유를 설명할 때 활용 가능하다.

### UI-TARS (Qin et al., 2025)

- 논문: https://arxiv.org/abs/2501.12326
- enhanced perception, unified action modeling, deliberate reasoning,
  iterative trajectory refinement를 결합한다.
- perception data는 element description, dense captioning, state
  transition captioning, QA, set-of-mark의 다섯 과제로 구성된다.
- element description은 type, appearance, relative position,
  function을 포함한다. Dense caption은 모든 element description과
  embedded-image caption을 결합하여 전체 layout, spatial relation,
  hierarchy, interaction을 포괄하는 상세 설명으로 합성한다.
- 데이터 구축은 개별 element에서 전체 interface로 올라가는
  bottom-up 방식이며, 논문은 이를 component precision과 holistic
  understanding을 함께 확보하기 위한 설계로 설명한다.
- 학습은 세 phase다. Continual PT에서는 reflection을 제외한
  perception, grounding, action trace를 모두 함께 사용하고,
  annealing에서는 각 유형의 고품질 subset을 다시 혼합한다. 이후
  reflection data로 DPO한다. 전체 규모는 약 50B tokens다.
- 우리 연구에 가장 가까운 지점:
  - GUI-specific dense description이 element 나열을 넘어 관계와
    기능을 학습시키는 perception supervision이라는 직접적 선례다.
- 확인 결과 및 공백:
  - 본문과 부록은 dense caption 예시를 제공하지만 평균 target
    length, 화면당 언급 element 수, Short/Long 기준을 보고하지 않는다.
  - perception data만 제거하거나 caption 상세도만 바꾼 ablation은
    없고, 공개 ablation은 주로 System-1/2 reasoning에 집중한다.
  - 모든 supervision이 continual PT와 annealing mixture에 얽혀
    있어 dense caption 자체의 즉시 효과와 agent-stage 지속성을
    분리할 수 없다.

### MultiUI / Harnessing Webpage UIs (Liu et al., 2024)

- 논문: https://arxiv.org/abs/2410.13824
- ICLR 2025 발표본: https://openreview.net/pdf?id=IIsTO4P3Ag
- 1M websites에서 7.3M web-UI instructions를 구축한다. 접근성
  트리를 text LLM에 입력해 visual understanding/reasoning, OCR,
  grounding의 세 범주와 아홉 과제를 합성한다.
- 전체 webpage caption은 desktop 150K, mobile 100K로 총 250K다.
  Llama-3-70B-Instruct가 accessibility tree를 coherent and detailed
  description으로 합성하며, 공개 prompt는 모든 visual/textual
  component를 comprehensively 설명하도록 요구한다.
- caption 예시는 navigation, heading, subheading, icon, 요소의
  상대 위치, 전체 layout을 여러 문장으로 기술한다. 다만 논문은
  평균 token 수나 coverage 통계를 보고하지 않는다.
- GUI knowledge learning(Stage 1)은 MultiUI의 95%를 사용하고,
  visual instruction tuning(Stage 2)은 일반 LLaVA data와 나머지
  5% MultiUI를 혼합한다.
- task-type ablation에서 Caption samples+LLaVA data는
  VisualWebBench와 DocVQA를 크게 개선한다. 모든 task type을 함께
  넣었을 때 네 평가 범주에서 가장 균형 잡힌 결과를 보인다.
- 우리 논문에 쓸 수 있는 근거:
  - 구조화된 웹 metadata를 상세한 screenshot description으로
    변환하면 web understanding뿐 아니라 OCR/document task로도
    전이될 수 있다는 가장 직접적인 선행 증거.
  - GUI knowledge stage 뒤에 general instruction stage를 두는
    two-stage recipe와 stage-wise 평가의 비교 대상.
- 우리 연구와의 차이:
  - Caption 유무/과제 유형은 ablate하지만, 동일 screenshot에 대한
    Short와 Long target을 통제 비교하지 않는다.
  - Caption-only condition도 일반 LLaVA data를 함께 사용하므로
    webpage caption granularity의 독립 효과를 보여주지는 않는다.
  - 최종 Mind2Web 평가는 있으나 PT 직후부터 trajectory SFT
    이후까지 동일 조건을 종단 추적하지 않는다.

### MolmoWeb (Gupta et al., 2026)

- 논문: https://arxiv.org/abs/2604.08516
- MolmoWebMix는 100K+ synthetic trajectories, 30K+ human
  demonstrations, atomic skill trajectory, referring-expression
  grounding, screenshot QA를 결합한다.
- grounding specialist를 제외한 최종 agent는 perception과 모든
  trajectory 유형을 별도 stage가 아니라 하나의 SFT mixture에서
  함께 학습한다. 데이터 유형별 mixing ratio를 hyperparameter로
  보고 ablation을 통해 선택한다.
- 출발 checkpoint 자체도 image captioning pre-training과
  single-image QA fine-tuning을 거친 Molmo2이다. 따라서 최종
  MolmoWeb 결과만으로 captioning, perception mixture, trajectory
  각각의 기여를 분리하기 어렵다.
- 같은 task instruction을 사용한 비교에서 AxTree agent가 만든
  synthetic trajectory가 human trajectory보다 성능이 높았으며,
  논문은 인간의 탐색적 detour와 행동 변동성을 원인 후보로 든다.
- 우리 논문에 쓸 수 있는 근거:
  - perception과 trajectory를 함께 갖춘 최신 open web-agent
    recipe.
  - 데이터 양뿐 아니라 mixture와 trajectory style이 결과를
    바꿀 수 있으므로 조건 간 downstream sample/order를 고정해야
    한다는 근거.
- 우리 연구와의 차이:
  - mixture 비율과 trajectory source의 효과가 중심이며
    description granularity를 독립 변수로 두지 않는다.
  - 우리 연구는 PT, IT, Trajectory를 분리하고 중간 checkpoint를
    남겨 초기 supervision의 지속성을 직접 추적한다.

### GUI-Libra (Yang et al., 2026)

- 논문: https://arxiv.org/abs/2602.22190
- 일반적인 CoT SFT가 grounding을 해칠 수 있음을 관찰하고,
  reasoning/action 혼합과 action-token reweighting을 제안한다.
- 우리 논문에 쓸 수 있는 근거:
  - 더 긴 또는 더 많은 언어 supervision이 grounding에 자동으로
    유리하지 않다는 최신 반례.
  - Long description이 understanding과 grounding에 서로 다른
    영향을 줄 수 있으므로 두 평가를 분리해야 한다는 논리.
- 주의:
  - CoT reasoning과 webpage description은 같은 개입이 아니므로,
    직접 증거가 아니라 동기와 해석상의 관련 연구로 제한해야 한다.

### VisualWebBench (Liu et al., 2024)

- 논문: https://arxiv.org/abs/2404.05955
- 139개 실제 website, 87개 sub-domain에서 수집한 1.5K
  human-curated instances와 일곱 과제로 OCR, understanding,
  grounding을 세분해 평가한다.
- 분석 결과 기존 MLLM은 text-rich environment의 grounding과
  low-resolution input에서 특히 약하다고 보고한다.
- 우리 논문에 쓸 수 있는 근거:
  - 하나의 aggregate score만으로는 description이 OCR,
    understanding, grounding 중 어디에 기여하는지 구분하기
    어렵다는 평가 설계의 근거.
  - Long description이 understanding을 높이더라도 grounding까지
    자동으로 높인다고 가정하지 않고 하위 능력을 분리할 이유다.

### Mind2Web (Deng et al., 2023)

- 논문: https://arxiv.org/abs/2306.06070
- 137개 real-world website와 31개 domain에서 2K+ open-ended
  tasks와 crowdsourced action sequences를 수집한다.
- raw HTML은 너무 길기 때문에 small LM으로 candidate element를
  먼저 filtering하면 LLM의 효과와 효율이 개선된다고 보고한다.
- 우리 논문에 쓸 수 있는 근거:
  - 웹 agent 성능에는 화면 이해뿐 아니라 action history,
    candidate selection, task/domain generalization이 함께
    작용한다는 점.
  - 따라서 PT 효과를 최종 agent score만 보고 귀속하지 않고,
    PT→IT→Trajectory checkpoint를 분리해야 한다는 논리.
- 주의:
  - screenshot-only perception이나 description granularity
    연구가 아니며, downstream web-agent evaluation의 출발점으로
    인용하는 것이 적절하다.

## 현재까지의 핵심 공백

검토한 주요 레시피들은 다음 중 하나 이상을 보여준다.

- grounding pre-training이 downstream GUI agent에 기여한다;
- perception, grounding, reasoning, trajectory를 단계적으로 학습한다;
- 데이터 혼합과 sampling이 최종 성능에 중요하다.

그러나 동일한 웹 스크린샷과 동일한 grounding mixture를 유지한 채,
화면 전체 description을 선택적 Short supervision과 포괄적 Long
supervision으로 나누고, 그 차이를 PT→IT→Trajectory 전 과정에서
추적한 연구는 현재까지 확인하지 못했다.

## 원고에 넣기 좋은 주장 후보

1. "Grounding pre-training has repeatedly been treated as a foundation
   for downstream GUI agents."  
   후보 인용: SeeClick; UGround; Aguvis; OS-Atlas.

2. "Existing recipes entangle perception supervision with data scale,
   task mixture, and post-training stages."  
   후보 인용: UI-TARS; ShowUI; MolmoWeb; Aguvis.

3. "More language supervision need not monotonically improve grounding."  
   후보 인용: GUI-Libra. 단, CoT와 description의 차이를 명시할 것.

4. "Stage-wise checkpoints are necessary to distinguish an immediate
   representation gain from one that survives agent post-training."  
   후보 인용: Aguvis의 stage ablation; SeeClick의 checkpoint 분석.

5. "Caption density can determine which attributes and relations are
   retained in vision--language representations."  
   후보 인용: DAC. 자연 이미지 연구라는 범위를 함께 명시할 것.

6. "Recent web-UI recipes already use comprehensive page descriptions,
   but do not isolate their granularity from task mixture and later
   training."  
   후보 인용: MultiUI; UI-TARS.

## 원고 위치별 인용 후보

| 원고 위치 | 안전하게 지지되는 주장 | 후보 인용 |
|---|---|---|
| Introduction | 웹페이지 이해는 텍스트 의미와 구조·배치를 함께 요구한다. | WebSRC; VisualWebBench |
| Introduction | caption density가 시각 속성과 관계의 학습에 영향을 줄 수 있다. | DAC |
| Related Work | 최신 GUI 모델은 perception/grounding을 agent 능력의 기반으로 학습한다. | SeeClick; Aguvis; UI-TARS; UGround |
| Related Work | 상세 webpage description은 실제 최신 recipe에 사용된다. | MultiUI; UI-TARS |
| Method | downstream data와 순서를 고정해야 초기 supervision의 효과를 분리할 수 있다. | ShowUI; MolmoWeb; UI-TARS |
| Evaluation | understanding, grounding, agent execution은 분리해 측정해야 한다. | VisualWebBench; SeeClick; Mind2Web |
| Discussion | 더 풍부한 언어 supervision의 효과는 능력별로 단조롭지 않을 수 있다. | GUI-Libra; MultiUI task ablation |

## 다음 확인 순서

1. MolmoWebMix의 screenshot QA/caption/grounding 비율 및 ablation 확인.
2. GUI-Libra에서 CoT SFT가 grounding을 저해한 정확한 실험 조건 확인.
3. Dense-caption 계열(ShareGPT4V, ALLaVA, UltraCaption)이 caption
   length/coverage를 실제로 통제한 방식과 downstream 결과 확인.
4. 각 논문의 BibTeX key를 `references.bib`와 대조하고, 원고 문장에
   넣을 인용만 선별한다.
