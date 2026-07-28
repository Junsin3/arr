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
- GUI screenshot의 context-aware understanding과 precise captioning을
  perception 학습의 일부로 둔다.
- 우리 연구에 가장 가까운 지점:
  - captioning을 agent 이전의 perception 능력으로 사용한다.
- 남은 확인사항:
  - caption target의 평균 길이, 화면당 정보량, short/long ablation
    여부를 본문/부록에서 추가 확인해야 한다.

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

## 다음 확인 순서

1. UI-TARS 부록에서 perception/caption 데이터 형식과 길이 확인.
2. MolmoWebMix의 screenshot QA/caption/grounding 비율 및 ablation 확인.
3. MultiUI의 web caption task template과 description 길이 확인.
4. GUI-Libra에서 CoT SFT가 grounding을 저해한 정확한 실험 조건 확인.
