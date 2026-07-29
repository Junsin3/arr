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
- 최종 mixture는 trajectory 80%, perception 20%다. Perception은
  grounding 15%와 screenshot QA 5%로 구성되며, trajectory에는
  synthetic single-agent 35%, synthetic multi-agent 18%, human
  trajectory 18%, human skill 5% 등이 포함된다.
- grounding에는 7M+ synthetic QA와 1.1M PixMoPoints examples가
  포함된다. Screenshot QA는 395개 사이트의 2,237,252 pairs이며
  OCR 54%, affordance 26%, summarization 20%로 구성된다.
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
- Scale ablation에서는 전체 데이터의 10%만으로도 최종 성능의
  약 85--90%에 도달한다. Human data의 이득은 benchmark마다
  일관되지 않았고, volume, task distribution, action set,
  thought style, annotation noise가 원인 후보로 제시된다.
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
- Qwen2.5-VL-3B/7B와 81K GUI reasoning data를 사용해
  ScreenSpot-v2 출력을 30-token bin으로 분석한다. Base와 CoT-SFT
  모두 response length와 grounding accuracy가 음의 관계를 보였고,
  250 tokens를 넘는 CoT에서 저하가 특히 컸다.
- 동일 데이터로 full reasoning--action, action-only,
  grounding-only SFT를 비교한다. Grounding-only는 소폭 개선되고
  action-only는 소폭 저하되지만 long-CoT SFT는 큰 폭으로 저하되어,
  SFT 자체보다 긴 reasoning sequence가 주된 간섭원임을 보인다.
- Reasoning--action과 direct-action samples를 섞고 action 및
  coordinate token의 loss를 높이는 action-aware SFT를 제안한다.
  7B reasoning mode의 grounding accuracy는 standard SFT 79.0,
  mixed SFT 81.4, ASFT 83.4로 증가한다.
- 후속 RL까지 적용하면 7B에서 더 긴 Reason output(176.1 tokens)이
  No-Reason output(124.4 tokens)보다 높은 grounding accuracy
  (89.3 vs. 88.5)를 보인다. 따라서 길이의 악영향은 고정 법칙이
  아니라 supervision과 optimization에 의존한다.
- 우리 논문에 쓸 수 있는 근거:
  - 더 긴 언어 출력이 grounding에 자동으로 유리하지 않으며,
    언어와 좌표 token 간 학습 균형이 중요하다는 최신 반례.
  - Long description이 understanding과 grounding에 서로 다른
    영향을 줄 수 있으므로 두 평가를 분리해야 한다는 논리.
- 주의:
  - CoT reasoning과 webpage description은 같은 개입이 아니므로,
    직접 증거가 아니라 동기와 해석상의 관련 연구로 제한해야 한다.
  - 이를 ``Long description이 grounding을 저해한다''는 직접
    증거로 바꾸어 쓰면 안 된다.

### KnowAda (Yanuka et al., 2025)

- 논문: https://aclanthology.org/2025.naacl-long.527/
- Small/medium VLM이 pretrained capability보다 복잡한 dense
  caption을 학습하면 세부사항을 정확히 포착하지 못하고
  hallucination이 증가할 수 있음을 분석한다.
- Caption을 proposition-level visual questions로 분해해 base
  model이 알지 못하는 부분을 찾고, 해당 부분만 model capability에
  맞게 단순화하는 Knowledge-Adapted fine-tuning을 제안한다.
- 단순한 caption complexity 축소나 일반 data curation만으로는
  descriptiveness--hallucination trade-off가 해결되지 않았다고
  보고한다.
- 우리 논문에 쓸 수 있는 근거:
  - Long supervision은 정보량뿐 아니라 factuality와 learner
    capability에 의존하므로 합성 품질과 hallucination filtering을
    함께 보고해야 한다.
  - 좌표와 텍스트 사실 검증을 Short/Long에 동일 적용하는 이유다.
- 주의:
  - Dense caption generation 연구이며 GUI agent transfer의
    직접 증거는 아니다.

### When More Words Say Less (Kapur et al., 2026)

- 논문: https://aclanthology.org/2026.acl-short.34/
- Description length와 specificity가 서로 다른 개념임을 지적한다.
  같은 길이에서 정보량을 변화시킨 데이터로 사람은 길이와 무관하게
  더 specific한 설명을 선호함을 보인다.
- 우리 연구에 주는 설계상 함의:
  - 독립 변수를 단순 length보다 description granularity/coverage로
    정의해야 한다.
  - 평균 token 수와 함께 언급 element, relation, state, function
    coverage를 보고해야 한다.
  - token 증가 자체와 유용한 GUI fact 증가를 동일시하면 안 된다.
- 주의:
  - Description 평가 연구이며 representation learning의 직접
    증거는 아니다.

### ShareGPT4V (Chen et al., 2024)

- 논문: https://arxiv.org/abs/2311.12793
- GPT-4V로 만든 100K 고품질 상세 caption과, 이 데이터로 학습한
  captioner가 확장한 1.2M image--text pair를 구축한다.
- 같은 수의 기존 SFT caption을 상세 caption으로 대체하는 비교에서
  LLaVA-7B, LLaVA-1.5-13B, Qwen-VL-Chat-7B의 MME가 각각
  222.8, 22.0, 22.3점 증가하고 MMBench도 각각 2.7, 1.3, 1.5점
  증가했다고 보고한다.
- pre-training과 SFT 모두에 상세 caption을 사용하며, caption의
  상세도ㆍ정확도ㆍ데이터 규모가 modality alignment에 영향을 준다는
  실험적 근거를 제공한다.
- 우리 논문에 쓸 수 있는 근거:
  - LLaVA 계열에서도 상세 caption이 초기 정렬과 후속 SFT에 유용할
    수 있다는 직접적인 범용 MLLM 선례다.
  - 단순히 긴 문장을 생성하는 것이 아니라 실제 이미지를 본
    teacher로 정확하고 세밀한 caption을 만드는 것이 중요하다는
    데이터 생성 설계의 근거다.
- 차이와 주의:
  - 동일 이미지의 Short/Long을 완전히 통제하지 않고, caption 품질과
    생성 방식도 함께 달라진다.
  - 웹 GUI, 좌표 grounding, trajectory transfer는 평가하지 않는다.

### LoTLIP (Wu et al., 2024)

- 논문: https://proceedings.neurips.cc/paper_files/paper/2024/hash/77828623211df05497ce3658300dafd9-Abstract-Conference.html
- 100M 이미지를 평균 약 136-token의 long text로 다시 caption하고,
  short-text와 long-text 이해의 trade-off를 분석한다.
- 통제 실험에서 long caption의 sub-caption 수가 증가할수록
  long-text retrieval은 향상되지만, MSCOCO short-text retrieval과
  ImageNet classification은 하락한다.
- 이를 완화하기 위해 여러 텍스트 요약 지점을 두는 corner token을
  제안하며, long-text 이해를 높이면서 short-text 성능을 회복한다.
- 우리 논문에 쓸 수 있는 근거:
  - 상세 supervision의 이득이 모든 평가축에 단조롭게 나타나지
    않으므로 웹 이해, grounding, 일반화를 분리해 보고해야 한다.
  - Long 조건이 좋아진 benchmark만 제시하지 않고 Short가 유리할 수
    있는 능력도 함께 평가해야 한다.
- 주의:
  - contrastive language--image pre-training 연구이며
    autoregressive GUI agent에 대한 직접 증거는 아니다.

### Long Story Short (Salazar et al., 2026)

- 논문: https://aclanthology.org/2026.findings-acl.2131/
- 동일 CLIP 구조와 학습 objective를 유지한 통제 모델을 포함해
  compositional training과 long-caption understanding의 양방향
  전이를 분석한다.
- long-caption data 자체의 효과를 분리하기 위해 ShareGPT4V로
  표준 CLIP을 full fine-tuning한 LSS를 구성한다. 또한 ShareGPT4V,
  DOCCI, sDCI, Localized Narratives를 같은 설정으로 비교한다.
- 핵심 관측:
  - long caption만으로 compositional generalization이 보장되지
    않는다.
  - ShareGPT4V와 사람이 작성하고 시각적으로 grounding된 DOCCI가
    더 강하게 일반화한다.
  - 1,000단어 이상의 합성 설명을 결합한 sDCI는 복잡성은 높지만
    coherence와 visual grounding이 약할 수 있고, 작은 이미지
    다양성과 함께 학습 후반에 과적합 양상을 보인다.
  - 제한된 LoRA update보다 full fine-tuning이 의도한 표현 변화를
    학습하는 데 중요할 수 있다.
- 우리 논문에 쓸 수 있는 근거:
  - 독립변수를 단순 길이가 아니라 grounded fact coverage와
    description quality로 정의해야 한다는 가장 최신의 직접 근거다.
  - Stage 1의 사실ㆍ좌표 검증과 Stage 2의 Short/Long 변환을 분리한
    이유를 뒷받침한다.
  - Long의 이득이 없다면 “긴 설명은 무용하다”보다 teacher 품질,
    grounding, 언어 분포, 학습 용량을 함께 점검해야 한다.
- 주의:
  - 자연 이미지 contrastive VLM 결과이므로 웹 GUI 및 agent
    trajectory로의 전이는 본 논문이 직접 검증해야 한다.

### GUI-G1 (Zhou et al., 2025)

- 논문: https://papers.nips.cc/paper_files/paper/2025/hash/89dcbea9f19960edd7765068adb13b1d-Abstract-Conference.html
- GUI grounding RL에서 input template, output reward, policy update를
  각각 분석한다. 긴 reasoning chain이 grounding 성능을 낮출 수
  있음을 관찰하고 direct answer를 유도하는 Fast Thinking template을
  제안한다.
- box 크기를 이용한 reward hacking과 쉬운 example 편향도 분석하며,
  17K 공개 sample로 학습한 3B 모델이 ScreenSpot 90.3%,
  ScreenSpot-Pro 37.1%를 보고한다.
- 우리 논문에 쓸 수 있는 근거:
  - 생성 언어량과 정밀 좌표 예측 사이의 간섭은 SFT뿐 아니라 RL
    단계에서도 관찰된다.
  - description의 효과를 grounding score와 분리하고 Action 및
    Trajectory 이후까지 추적해야 한다.
- 주의:
  - reasoning output 길이와 PT description 상세도는 다른 변수다.
    Long PT가 grounding을 저해한다는 직접 근거로 사용하면 안 된다.

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

### AgentTrek (Xu et al., 2025)

- 논문: https://arxiv.org/abs/2412.09605
- ICLR 2025 Spotlight.
- 인터넷의 tutorial 문서를 수집ㆍ분류한 뒤 고수준 목표와 단계별
  지침으로 바꾸고, VLM agent가 실제 웹에서 이를 replay하도록 하여
  trajectory를 합성한다. 별도 VLM evaluator가 task instruction
  준수와 핵심 단계 성공 여부를 trajectory와 step 수준에서 검증한다.
- 최종 데이터는 10,398 trajectories, 평균 12.1 steps, 127개
  websites 규모다. 관측은 screenshot, HTML, accessibility tree를
  포함하며 reasoning과 action도 함께 기록한다.
- 순수 시각 agent에서는 Playwright action을 표준 pyautogui-style
  action으로 변환하여 website 구현과 분리된 행동 공간을 사용한다.
- 우리 논문에 쓸 수 있는 근거:
  - trajectory는 단순 action label 집합이 아니라 목표, 시간 순서의
    화면 관측, reasoning, grounded action이 결합된 supervision이다.
  - 동일한 trajectory sample과 action normalization을 모든 PT
    조건에 사용해야 description 효과를 분리할 수 있다.
  - 실제 환경에서 replay한 합성 trajectory도 evaluator filtering이
    필요하므로 trajectory 수만으로 품질을 대표할 수 없다.
- 차이와 주의:
  - trajectory 합성법의 효과를 다루며 PT description granularity는
    조작하지 않는다.
  - 우리 Action 2K 단계의 직접 선례는 아니다. 이 단계는 일곱 개
    action의 출력 계약을 맞추는 실험 구현으로 설명해야 한다.

### ScaleTrack (Huang et al., 2025)

- 논문: https://arxiv.org/abs/2505.00416
- GUI agent 학습을 grounding과 planning으로 나누고, element
  referring, context awareness, functional description으로 만든
  grounding sample을 하나의 template으로 통합한다.
- 일반적인 next-action prediction에 더해 현재 화면에 도달한 과거
  action을 역으로 예측하는 back-tracking objective를 추가한다.
  논문은 이를 GUI 상태 변화의 규칙을 학습하기 위한 supervision으로
  해석한다.
- 우리 논문에 쓸 수 있는 근거:
  - 화면 설명의 affordance 및 transition 정보가 trajectory에서
    필요한 상태 변화 표현과 연결될 수 있다는 mechanism 가설.
  - `Long w/o transition/affordance` ablation이 planning transfer를
    진단하는 조작이라는 근거.
- 주의:
  - Short/Long caption을 비교하지 않으며, 공개된 결과만으로
    transition description 하나의 기여를 분리할 수는 없다.

### OpAgent (Guo et al., 2026)

- 논문: https://arxiv.org/abs/2602.13559
- 초기 SFT를 Planning, Acting, Grounding이라는 기능별 primitive로
  분류한 hierarchical multi-task mixture로 구성한 뒤 실제 웹에서
  online agentic RL을 수행한다.
- offline trajectory는 실제 웹의 stochastic transition과 feedback을
  충분히 반영하지 못해 distribution shift가 생길 수 있다고
  문제를 제기한다.
- 우리 논문에 쓸 수 있는 근거:
  - IT, Action, Trajectory를 구분해 기록하는 것은 최종 성능을
    하나의 post-training으로 묶는 것보다 각 기능의 간섭과 전달을
    해석하기 쉽다.
  - static Multimodal-Mind2Web 결과는 offline planning transfer를
    보여주지만 실제 웹 성공률 전체를 대변하지 않는다는 한계 서술.
- 주의:
  - online RL과 modular inference가 핵심이며, 본 연구는 동일한
    online interaction evaluation을 수행하지 않는다.

### MPR-GUI (Chen et al., 2025)

- 논문: https://arxiv.org/abs/2512.00756
- 동일한 평가 설정을 영어, 중국어, 프랑스어, 러시아어, 일본어,
  티그리냐어, 아랍어의 일곱 언어로 확장한 fine-grained GUI
  perception/reasoning benchmark다. 한국어는 포함하지 않는다.
- GPT-4o와 사람 검수를 결합해 multilingual VQA를 구축하며, widget
  function과 element spatial relation을 세부 능력으로 분리한다.
- 일곱 baseline의 평균 fine-grained accuracy는 영어 75.3%,
  비영어 67.7%로 차이가 난다. hidden-state intervention인 GUI-XLI는
  비영어 성능을 평균 6.5% 개선한다고 보고한다.
- 우리 논문에 쓸 수 있는 근거:
  - 영어 GUI 성능을 한국어 능력의 대리값으로 둘 수 없으며, 언어별
    paired evaluation이 필요하다.
  - 번역 subset에서도 perception과 reasoning/grounding을 분리해
    보고해야 한다.
- 주의:
  - mobile GUI 중심이고 한국어가 없으므로 우리의 한국어 평가를
    표준 benchmark라고 부를 수 없다.

### X-WebAgentBench (Peng et al., 2025)

- 논문: https://aclanthology.org/2025.findings-acl.988/
- WebShop 기반 interactive environment를 14개 언어로 확장하고,
  언어별 200개씩 총 2,800 instruction과 약 590K multilingual
  products를 제공한다. 한국어는 포함하지 않는다.
- local-language input, 외부 영어 번역, self-translation,
  cross-lingual prompting을 비교한다. 8B 이하 모델에서는 외부
  영어 번역이 효과적인 반면 self-alignment는 제한적이라고 보고한다.
- 비영어 환경에서 불필요한 action 수와 token cost가 언어별로
  달라지고, 긴 interaction에서 성능 저하가 더 일찍 나타난다.
- 우리 논문에 쓸 수 있는 근거:
  - 한국어 model 평가는 답변 정확도뿐 아니라 action 수 또는
    trajectory 길이도 함께 보면 cross-lingual failure를 더 잘
    진단할 수 있다.
  - 영어 원본과 한국어 번역본을 paired하게 유지해야 언어 효과와
    task 난이도를 분리할 수 있다.
- 주의:
  - screenshot 기반 GUI grounding이 아니라 text-centric WebShop
    agent benchmark다.

### K-BrowseComp (Lee et al., 2026)

- 논문: https://arxiv.org/abs/2606.02404
- 한국 문화ㆍ웹 맥락에 grounded된 400개 browsing problem을
  구축한다. 그중 300개 verified split은 한국어 원어민이 직접
  작성하고 검증하며, 100개 synthetic diagnostic split은 별도로
  보고한다.
- frontier model도 verified split에서 30.00--45.67%에 그쳐
  한국어 agentic evaluation의 난이도와 영어 benchmark 성능의
  제한된 전이를 보여준다.
- 우리 논문에 쓸 수 있는 근거:
  - 한국어 agent benchmark가 부족하다는 문제 제기의 직접 근거.
  - 사람이 검증한 주 split과 합성 diagnostic split을 분리해
    보고하는 관행은 우리의 translated subset을 표준 영어 결과와
    분리하는 설계에 참고할 수 있다.
- 주의:
  - visual GUI understanding/grounding이 아니라 정보 검색형 browsing
    benchmark이므로 직접 평가 대체재가 아니다.

### ScreenSpot-Pro (Li et al., 2025)

- 논문: https://arxiv.org/abs/2504.07981
- 23개 전문 application, 5개 산업군, 3개 OS에서 수집한 1,581개
  고해상도 full-screen grounding instruction으로 구성된다.
- ScreenSpot-v2에서도 target box의 상대 크기가 작아질수록 여러
  모델의 정확도가 일관되게 하락한다. ScreenSpot-Pro의 planner-free
  최고 baseline은 18.9%에 그친다.
- 검색 영역을 좁히는 ScreenSeekeR는 추가 학습 없이 OS-Atlas-7B를
  18.9%에서 48.1%로 높인다. crop ablation에서는 OS-Atlas와
  UGround의 최적 crop size가 달라, 입력 해상도를 높이는 것이 항상
  유리하지 않음을 보인다.
- 우리 논문에 쓸 수 있는 근거:
  - grounding 결과는 PT뿐 아니라 pixel budget, target-to-screen
    ratio, crop/search strategy에 민감하다.
  - Short/Long 비교에서 동일 resize와 좌표 복원을 적용하고,
    가능하면 target-size bin별 성능을 함께 보고해야 한다.
- 주의:
  - 전문 desktop application 중심이며 search method의 이득을
    representation 개선으로 해석하면 안 된다.

### UI-E2I-Synth / UI-I2E-Bench (Liu et al., 2025)

- 논문: https://aclanthology.org/2025.findings-acl.809/
- 기존 grounding 평가의 공백으로 비현실적인 element-to-screen
  ratio, element type 불균형, explicit instruction 편중을 지적한다.
- GPT-4o로 explicit/implicit instruction을 포함하는 대규모 grounding
  data를 합성하고, 이 특성을 구분하는 UI-I2E-Bench를 제안한다.
- 우리 논문에 쓸 수 있는 근거:
  - Long description의 기능ㆍaffordance 보존 가설은 단순 label
    matching보다 implicit instruction에서 더 직접적으로 검증된다.
  - aggregate accuracy만 보고하면 target size와 instruction
    specificity에 따른 차이를 놓칠 수 있다.
- 분석 권고:
  - 가능하면 explicit/implicit 결과를 분리한다. 실행이 어렵다면
    기존 평가를 target box area와 text/icon으로 stratify한다.

### UI-Vision (Nayak et al., 2025)

- 논문: https://proceedings.mlr.press/v267/nayak25a.html
- 83개 desktop application의 사람 demonstration에 bounding box,
  UI label, click/drag/keyboard trajectory를 함께 제공한다.
- element grounding, layout grounding, action prediction의
  fine-to-coarse 세 단계로 평가한다.
- 우리 논문에 쓸 수 있는 근거:
  - description PT의 효과를 single-element localization, 전체
    layout 이해, next-action prediction으로 분리하는 선례다.
- 주의:
  - desktop benchmark이며 main web claim의 직접 증거는 아니다.

### AutoGUI (Li et al., 2025)

- 논문: https://aclanthology.org/2025.acl-long.510/
- UI element를 상호작용하기 전과 후의 화면 상태 변화를 LLM이
  비교하여 element functionality description을 합성한다.
- LLM-aided rejection과 verification으로 invalid annotation을
  자동 제거하고 AutoGUI-704K를 구축한다. 사람 평가에서 훈련된
  human annotator와 유사한 annotation correctness를 보고한다.
- 동일한 25K 규모에서 full functionality annotation은 FuncPred
  21.1, MoTIF 22.5, ScreenSpot 16.4를 기록하며 element HTML
  (5.3/11.7/5.7)과 condensed functionality
  (3.8/19.8/4.8)보다 모두 높다. 이는 규모를 맞춘 annotation
  type 비교이므로 기능 상세도의 직접 근거다.
- 다만 target은 개별 element functionality이며 화면 전체의
  Short/Long caption을 비교하지 않는다. 따라서 우리 가설 전체의
  선행 답이 아니라 `w/o function/affordance` ablation의 근거다.
- 우리 논문에 쓸 수 있는 근거:
  - element의 예상 결과와 affordance는 정적 screenshot에서 임의로
    상상하기보다 구조화된 근거 또는 state transition에 결부해야 한다.
  - 합성 annotation의 제약을 prompt에만 맡기지 않고 rejection 및
    verifier로 강제하는 설계의 직접 선례다.
- 우리 연구와의 차이:
  - AutoGUI는 실제 전후 state를 이용하지만, 우리 Stage 1의 예상
    결과는 accessibility tree와 현재 화면을 바탕으로 teacher가
    생성한다. 따라서 `expected result`를 관찰된 사실과 구분하고,
    가능하면 별도의 transition ablation을 두어야 한다.
  - 우선순위는 (1) `Long w/o transition/outcome`, (2) `Long w/o
    function/affordance`, (3) `Long w/o element detail`이다. 첫 둘은
    각각 동적 예측과 정적 기능 의미를 분리한다.

### GUI-HalluBench (Zhang et al., 2026)

- 논문: https://openaccess.thecvf.com/content/CVPR2026/papers/Zhang_Exposing_and_Evaluating_Hallucinations_for_GUI_Grounding_CVPR_2026_paper.pdf
- GUI grounding hallucination을 시각적으로 유사한 distractor를
  고르는 confusion hallucination과, 존재하지 않는 element 및
  좌표를 만들어내는 fabricated hallucination으로 구분한다.
- parsing subset과 hallucination subset을 분리한 GUI-HalluBench를
  만들고, parsing deficiency가 두 hallucination 유형 모두와
  연결됨을 분석한다.
- 우리 논문에 쓸 수 있는 근거:
  - 좌표가 형식적으로 0--999 범위에 있다는 것만으로 factual
    grounding이 보장되지 않는다. 좌표가 실제 Stage 1 element
    집합에 속하는지도 검증해야 한다.
  - Short 생성에서 제거한 약 4.7K fabricated coordinates와 최종
    0건이라는 통계의 의미를 설명하는 직접 근거다.
- 주의:
  - 모델 평가용 benchmark 결과이며 합성 caption 품질을 직접
    평가하는 연구는 아니다.

### OSWorld-G / Jedi (Xie et al., 2025)

- 논문: https://proceedings.neurips.cc/paper_files/paper/2025/hash/22c868099177ee278eb7baccec649f35-Abstract-Datasets_and_Benchmarks_Track.html
- short referring expression만으로는 software commonsense, layout,
  fine-grained manipulation을 포괄하기 어렵다고 보고, task를
  multi-perspective로 분해해 4M Jedi grounding examples를 합성한다.
- OSWorld-G는 text matching, element recognition, layout
  understanding, precise manipulation을 나누어 평가한다.
- Jedi로 grounding을 강화했을 때 OSWorld agent 성능이 23%에서
  51%로 증가했다고 보고한다.
- 우리 논문에 쓸 수 있는 근거:
  - 기능ㆍlayoutㆍ조작 정보를 분해하여 합성하는 것이 단일한
    referring expression 확장보다 진단적이다.
  - Long description 요소 ablation을 element, layout, affordance,
    transition 축으로 설계할 근거다.

## 현재까지의 핵심 공백

검토한 주요 레시피들은 다음 중 하나 이상을 보여준다.

- grounding pre-training이 downstream GUI agent에 기여한다;
- perception, grounding, reasoning, trajectory를 단계적으로 학습한다;
- 데이터 혼합과 sampling이 최종 성능에 중요하다.

그러나 동일한 웹 스크린샷과 동일한 grounding mixture를 유지한 채,
화면 전체 description을 선택적 Short supervision과 포괄적 Long
supervision으로 나누고, 그 차이를 PT→IT→Action→Trajectory 전 과정에서
추적한 연구는 현재까지 확인하지 못했다.

## 현재 분석 설계 권고

우선순위는 다음과 같다.

1. **학습 ablation을 주된 원인 분석으로 둔다.** Long description에서
   element-level detail 또는 click 이후 예상 상태를 제거한 조건은
   실제 supervision을 조작하므로 성능 변화에 대한 인과적 근거를
   제공한다. 가능하면 `Long-full`, `Long w/o element details`,
   `Long w/o transition/affordance`를 동일 sample과 optimizer로
   비교한다.
2. **Embedding distance는 보조 mechanism probe로 둔다.** Short/Long
   checkpoint가 같은 화면과 hard-negative 화면을 얼마나 구분하는지
   retrieval 또는 matched--mismatched margin으로 측정할 수 있다.
   단순 평균 cosine distance는 표현의 유용성이나 원인을 직접
   증명하지 못하므로 단독 분석으로 두지 않는다.
3. **한국어 subset 평가는 별도의 multilingual generalization
   결과로 둔다.** 한국어 학습 모델을 보고하려면 필요하지만,
   비공식 번역 subset은 표준 benchmark가 아니므로 번역 절차,
   bilingual 검수, 원본 난이도 보존, 영어 원본과의 paired comparison을
   명시하고 main claim보다 appendix/secondary table에 둔다.

Compute가 제한되면 `Long w/o transition/affordance` 한 조건을 먼저
권한다. Click 이후 상태 예측은 Short와 Long의 의미적 차별점이면서
trajectory transfer와 가장 직접적으로 연결되기 때문이다. 다만
element grounding이 주로 개선된 결과라면 `w/o element details`가
더 진단적이므로, 최종 main table의 개선 축에 맞춰 하나를 선택한다.

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

7. "Long-caption gains depend on visual grounding and data quality rather
   than length alone, and can trade off against short-text capabilities."
   후보 인용: Long Story Short; LoTLIP; ShareGPT4V.

8. "Agent trajectories couple sequential observations with grounded
   actions, so their source, filtering, and action normalization are part
   of the training recipe rather than incidental preprocessing."
   후보 인용: AgentTrek; MolmoWeb; UI-TARS.

9. "Grounding, acting, and planning are related but distinguishable
   post-training functions."
   후보 인용: Aguvis; ScaleTrack; OpAgent; GUI-Libra.

10. "English GUI performance does not reliably represent non-English
    perception and agent behavior."
    후보 인용: MPR-GUI; X-WebAgentBench.

11. "A translated Korean subset should be reported as a paired diagnostic,
    not presented as a new standard benchmark."
    근거: MPR-GUI와 X-WebAgentBench에는 한국어가 없고,
    K-BrowseComp는 native Korean benchmark지만 visual GUI 과제가
    아니다. 방법론적 선택이므로 번역ㆍ검수 절차도 함께 명시할 것.

12. "GUI grounding accuracy is strongly conditioned on target size,
    screenshot resolution, and whether the instruction names the target
    explicitly."
    후보 인용: ScreenSpot-Pro; UI-E2I-Synth.

13. "Synthetic GUI annotations require rejection or verification because
    models can fabricate plausible coordinates for nonexistent elements."
    후보 인용: AutoGUI; GUI-HalluBench.

14. "Functional, layout, and manipulation supervision should be tracked
    separately rather than collapsed into a single grounding label."
    후보 인용: OSWorld-G/Jedi; UI-Vision; MultiUI.

## 원고 위치별 인용 후보

| 원고 위치 | 안전하게 지지되는 주장 | 후보 인용 |
|---|---|---|
| Introduction | 웹페이지 이해는 텍스트 의미와 구조·배치를 함께 요구한다. | WebSRC; VisualWebBench |
| Introduction | caption density가 시각 속성과 관계의 학습에 영향을 줄 수 있다. | DAC |
| Related Work | 최신 GUI 모델은 perception/grounding을 agent 능력의 기반으로 학습한다. | SeeClick; Aguvis; UI-TARS; UGround |
| Related Work | 상세 webpage description은 실제 최신 recipe에 사용된다. | MultiUI; UI-TARS |
| Method | downstream data와 순서를 고정해야 초기 supervision의 효과를 분리할 수 있다. | ShowUI; MolmoWeb; UI-TARS |
| Method | trajectory 조건에서는 action vocabulary와 sample/filtering을 조건 간 고정해야 한다. | AgentTrek; MolmoWeb |
| Method | teacher가 생성한 좌표와 기능 설명은 구조화 근거 및 verifier로 제한해야 한다. | AutoGUI; GUI-HalluBench; MultiUI |
| Evaluation | understanding, grounding, agent execution은 분리해 측정해야 한다. | VisualWebBench; SeeClick; Mind2Web |
| Evaluation | 한국어 subset은 영어 원본과 paired하게 평가하고 별도 diagnostic으로 보고한다. | MPR-GUI; X-WebAgentBench; K-BrowseComp |
| Evaluation | grounding 결과는 target size와 instruction type별로 분해해야 한다. | ScreenSpot-Pro; UI-E2I-Synth |
| Discussion | 더 풍부한 언어 supervision의 효과는 능력별로 단조롭지 않을 수 있다. | GUI-Libra; GUI-G1; LoTLIP; MultiUI task ablation |
| Discussion | Long의 효과는 길이뿐 아니라 grounding, teacher 품질, 데이터 다양성에 의존한다. | Long Story Short; ShareGPT4V; KnowAda |

## 다음 확인 순서

1. MolmoWebMix의 screenshot QA/caption/grounding 비율 및 ablation 확인.
2. GUI-Libra에서 CoT SFT가 grounding을 저해한 정확한 실험 조건 확인.
3. ALLaVA와 UltraCaption이 caption length/coverage를 실제로
   통제한 방식과 downstream 결과 확인.
4. ShareGPT4V, LoTLIP, Long Story Short의 실험을 본문 가설과
   Discussion에 최소한으로 연결.
5. 각 논문의 BibTeX key를 `references.bib`와 대조하고, 원고 문장에
   넣을 인용만 선별한다.
6. Action 2K의 source와 실제 출력 template을 받은 뒤, AgentTrek 및
   GUI-Libra와 비교 가능한 범위를 확정한다.
### On the Effects of Data Scale on UI Control Agents (Li et al., 2024)

- NeurIPS 2024 Datasets and Benchmarks 논문. AndroidControl은 833개 Android app의 14,548개 고유 task와 15,283개 demonstration을 포함한다.
- UI control data 증가는 in-domain 성능을 안정적으로 높이지만 OOD, 특히 high-level instruction의 향상은 더 느리다.
- 원고 적용: 약 2.6M Long bilingual recipe의 우위는 규모 효과와 언어/mixture 효과를 포함하므로 granularity의 인과 증거가 아니다. matched Short/Long 실험과 최종 recipe 비교를 분리해야 한다.

### Scaling GUI Agents with Visual State Transitions (Liu et al., 2026)

- 2026-07-27 공개된 최신 arXiv preprint(2607.24112)로, peer review 전이라는 점을 명시해야 한다.
- State Transition Pretraining은 전후 화면에서 action을 예측하는 inverse dynamics와 현재 화면/action에서 다음 상태를 예측하는 forward dynamics를 함께 최적화한다.
- 동일한 2K AgentNet Win&Mac trajectory 후속 학습 조건에서 AgentNetBench, AndroidControl, GUIOdyssey 향상 및 transition data 규모에 따른 scaling을 보고한다.
- 우리 연구와의 차이: 이 논문은 실제 visual state pair를 사용하지만 우리는 현재 화면/AX tree에서 합성한 텍스트 expected outcome을 사용한다. 따라서 직접적인 입증이 아니라 mechanism의 동기다.
- 분석 우선순위: `Long-full` 대 `Long w/o transition/affordance`를 최우선 causal ablation으로 둔다. 그 다음 `Long w/o element details`, embedding distance probe, 한국어 paired diagnostic 순이다.
### GUICourse (Chen et al., 2025)

- ACL 2025 long paper. GUIEnv-global은 C4에서 렌더링한 웹 화면 10M개이며, 각 target은 화면의 모든 기술 가능한 텍스트, grounding 정보, layout sequence를 포함하는 long text다.
- GUIEnv-local은 text2bbox/bbox2text 0.7M, GUIAct는 단일/다단계 action, GUIChat은 GUI 지식 QA를 담당한다. 즉 perception PT → GUI/action SFT의 multi-stage recipe다.
- GUIEnv-global 규모를 0에서 2.5M 이상으로 늘릴 때 OCR, grounding, web-single navigation이 함께 상승한다. action type EM은 위치와 무관하여 거의 변하지 않는다.
- 중요한 한계: long target 자체의 효과와 데이터 규모가 분리되지 않았고 Short target 대조군도 없다. “상세한 GUI PT가 쓰인다”는 선행 근거이지 “Long이 Short보다 낫다”는 증거는 아니다.
- 원고 적용: 우리 matched Short/Long 설계의 필요성과 stage별 OCR/grounding/agent 평가를 뒷받침한다.

### Do GUI Grounders Truly Understand UI Elements? (Jandial et al., 2026)

- Findings of EACL 2026. 동일 UI element를 시각 속성, 공간 관계 등 여러 유효한 instruction으로 지칭하는 GUI Grounding Sensitivity Benchmark를 제안한다.
- 12개 모델이 같은 target의 표현 변화에 상당히 민감하며, 기존 단일 instruction 정확도가 실제 grounding 능력을 충분히 나타내지 못한다고 보고한다.
- 생성 instruction은 frontier model도 hallucination하므로 별도 validation이 필요하다고 지적한다.
- 원고 적용: 한국어 paired diagnostic과 description granularity 평가에서 단일 문구 정확도만 보고하지 말고, 가능하면 동일 target paraphrase별 평균과 consistency를 함께 보고한다.
### InfiGUIAgent (Liu et al., 2026)

- EACL 2026 long paper. 2-stage SFT로 Stage 1은 GUI understanding, grounding, QA와 일반 능력을, Stage 2는 trajectory 기반 hierarchical reasoning 및 expectation--reflection을 학습한다.
- Stage 1은 총 1M samples, Stage 2는 45K synthesized samples다. Stage 2에서는 현재 관측과 action으로 다음 state description을 예측하는 auxiliary task도 포함한다.
- Full: ScreenSpot 76.8 / AndroidWorld SR 0.13. w/o Stage 2: 76.0 / 0.00, w/o Stage 1: 74.3 / 0.09, w/o structured reasoning: 76.6 / 0.09.
- 해석: Stage 1은 grounding, Stage 2는 multi-step success에 더 직접적으로 연결된다. 그러나 Stage 2에 30K `Stage 1-aligned` samples가 포함되어 완전한 factor isolation은 아니다.
- 우리 원고 적용: PT→IT→Action→Trajectory의 모든 checkpoint를 평가해야 하는 직접 근거다. Long의 expected post-click outcome 효과가 PT 직후보다 Trajectory 이후 커지는지 보는 interaction 분석을 정당화한다.
- causal claim 조건: `Long-full` 대 `Long w/o transition/affordance`가 있어야 textual expectation의 기여를 말할 수 있다. stage별 상관만으로 mechanism을 확정하지 않는다.
### AgentCPM-GUI (Zhang et al., 2025)

- EMNLP 2025 System Demonstrations. Stage I은 OCR/widget localization 중심 12M grounding-aware PT, Stage II는 중국어와 영어 trajectory SFT, Stage III는 GRPO 기반 RFT다.
- 중국어 실제 app 30여 개에서 55K trajectories/470K steps를 수집하고 AITW, AITZ, AMEX, AndroidControl, GUI-Odyssey 영어 데이터를 결합한다. SFT 전체는 일반 multimodal data 50%를 포함한 6.9M instances다.
- CAGUI는 중국어 Fun2Point, Text2Point, Bbox2Text 및 action prediction을 평가하고, 영어 AndroidControl/GUI-Odyssey/AITZ도 함께 보고한다.
- RFT ablation은 어려운 long-horizon benchmark에는 이득이 있지만 AndroidControl-High와 CAGUI에서는 SFT와 같거나 소폭 낮다. 후속 stage의 이득이 모든 분포에서 단조롭지 않다는 근거다.
- 중요한 한계: Chinese-only, English-only, bilingual mixture 대조가 없다. 따라서 cross-lingual 평가 성공은 multilingual recipe의 선례지만 언어 혼합의 인과 효과를 입증하지 않는다.
- 우리 원고 적용: Long bilingual 2.6M의 한국어 성능을 보고하되 영어 혼합의 효과로 단정하지 않는다. 언어별 matched PT 또는 동일 화면의 EN/KO paired evaluation이 필요하다.
### AutoGUI-v2 (Li et al., 2026)

- 2026-04 arXiv preprint(2604.24441). 6개 OS의 2,753 tasks로 region/element semantics, functional grounding, interaction outcome prediction을 평가한다.
- 정적 위치 일치와 최종 task success 사이에 빠져 있던 “이 요소가 무엇을 하고, interaction 뒤 어떤 상태가 되는가”를 별도 평가축으로 만든다.
- agent data로 fine-tuning된 open model은 functional grounding에 강하지만, frontier commercial model은 functionality captioning에서 우세하며 uncommon action의 transition logic은 모든 모델이 어렵다.
- 우리 원고 적용 후보: Long-full과 transition/functionality 제거 모델을 FuncPred/AutoGUI-v2 계열 task에서 비교하면 왜 agent 성능이 달라지는지 직접 진단할 수 있다.
- 주의: 아직 peer-reviewed publication이 확인되지 않은 최신 preprint다. main benchmark로 새로 넣기보다, 계산 여유가 있을 때 mechanism diagnostic 또는 appendix 분석 후보로 둔다.
