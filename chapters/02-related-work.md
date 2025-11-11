# 제 2장 관련 연구

본 장에서는 본 연구와 밀접하게 관련된 세 가지 핵심 연구 축을 검토한다.
첫째, 건설현장 안전관리를 위한 영상 기반 객체 인식 기술 동향을 살펴보고,
둘째, 롱테일 분포를 갖는 시각인식(Long-tailed visual recognition) 문제 및 대응 전략을 정리하며,
셋째, 제한된 라벨링 샘플 환경에서의 퓨샷(Few-shot) 및 제로샷(Zero-shot) 학습 연구동향을 탐구한다.
이를 통해 본 연구가 기존 연구 대비 어떤 위치에 있으며 어떤 공백을 메우는지 명확히 한다.

## 2.1 건설현장 안전 모니터링을 위한 영상기반 인식

건설현장은 상시 동적이고 위험 요소가 다양한 작업 환경이다. 작업자, 중장비, 자재, 개방 가장자리 등이 복합적으로 존재하며, 이러한 환경에서의 안전관리에는 기존의 수작업 점검 방식만으로는 한계가 있다. 이에 최근에는 영상 기반 자동 모니터링 기술이 건설현장 안전관리 분야에서 각광받고 있다. 예컨대, Delhi et al.(2020)은 깊이 학습 기반 CNN을 활용하여 안전모 및 안전조끼 착용 여부를 실시간으로 탐지하는 프레임워크를 제안하였으며, 다양한 현장 조건에서도 준실시간 처리 가능함을 보였다. ([Frontiers][1])
또한 Liu et al.(2024)은 객체 탐지·분할·포즈 추정·트래킹을 통합한 다작업(Multi-Task) 접근법을 제안하여 중장비 및 작업자 동작까지 복합적으로 모니터링하는 시스템을 제시하였다. ([MDPI][2])
이와 같이 건설현장 특화 객체 인식 연구는 증가 추세에 있지만, 다음과 같은 제한점이 존재한다:

* 대부분의 연구가 안전모, 안전조끼, 안전화 등 **빈번히 등장하는 PPE(Head/Torso 클래스)**에 집중하고 있다.
* 희소 장비(예: 안전고리, 특수 추락방지 시스템, 밀폐공간 환기장치)나 새롭게 도입된 장비 유형은 충분히 다루어지지 않았다.
* 데이터 수집 및 라벨링이 용이한 장비 중심으로 이루어졌으며, **클래스 불균형(long-tail) 및 희소 클래스 인식**에 대한 본격적 연구는 드물다.
* 실제 현장 적용을 위한 **비용·라벨링·실시간성** 등에 대한 실증 연구가 부족하다.
* 산업현장 특유의 조명 변화, 배경 복잡성, 장비간 혼재, 카메라 시점 변화 등 현실적 제약이 충분히 반영되지 않은 경우가 많다. (Vukicevic et al., 2024) ([SpringerLink][3])

따라서 본 연구에서는 건설현장에 특화된 **희소 안전장비 클래스 인식** 및 **라벨링 비용 절감** 측면을 중점으로 한다는 점에서 기존 연구와 차별된다.

## 2.2 롱테일 인식(Long-tailed Visual Recognition)

### 2.2.1 문제 정의 및 현황

현실 세계의 영상 데이터는 일부 클래스에 매우 많은 샘플이 집중되고, 반면 다수의 클래스는 희소한 샘플만을 갖는 **롱테일 분포**를 보인다. 예컨대, 건설현장 이미지에서도 안전모·작업자·굴삭기 등이 자주 등장하는 반면, 특정 안전고리나 특수 장비는 매우 적게 나타나며 이는 학습 과정에서 문제를 야기한다. Zhang et al.(2024)은 이러한 롱테일 분포가 모델 학습에 미치는 영향을 정량적으로 분석하였다. ([ScienceDirect][4])
객체 탐지 분야에서는 단순 분류를 넘어 **위치 탐지(localization)**까지 수행해야 하기에, tail 클래스에 대한 일반화(generalization)가 특히 어렵다. Li et al.(2020)은 LVIS 데이터셋 기반으로 탐지 모델에서 tail 클래스 성능 저하 원인을 분석하고, Balanced Group Softmax(BAGS)를 제안하였다. ([Illinois Experts][5])

### 2.2.2 주요 대응 전략

롱테일 인식 문제에 대응하기 위해 제안된 전략은 대체로 다음과 같다:

* **데이터 수준(data-level) 전략**: 희소 클래스 샘플을 복제하거나 증강하고, 다수 클래스의 샘플을 줄이는 리샘플링/언더샘플링 기법이 있다.
* **모델/손실 수준(model-level) 전략**: 클래스 가중치 재조정(class re-weighting), 손실 함수 조정(cost-sensitive loss) 등이 있으며, 대표적으로 Focal Loss, Equalization Loss(EQL)가 있다. ([arXiv][6])
* **표현 수준(representation-level) 전략**: 사전학습된 임베딩이나 콘트라스트 학습(contrastive learning)을 통해 희소 클래스의 표현력을 강화하는 방법이 최근 주목받고 있다. 예컨대, prototype 기반 메트릭 학습을 롱테일에 접목한 연구들이 제시되고 있다.

### 2.2.3 한계 및 본 연구의 위치

그러나 위 전략들에도 다음과 같은 한계가 존재한다:

* 대부분의 연구가 **클래스당 수십~수백 개**의 샘플이 존재하는 상황을 가정하고 있으나, 클래스당 단수~10개 수준의 희소 샘플에 대해서는 대응이 어렵다.
* 탐지(task) 문제에서는 위치 정보까지 학습해야 하므로 일반 분류(task)보다 난이도가 높고, 탐지 특유의 영역 제안(region proposal)·후처리(post-processing) 어려움이 있다.
* 특히 건설현장과 같은 동적이고 장비 종류가 수시로 추가되는 환경에서는 **새로운 클래스의 등장**, **라벨링이 어려운 클래스**, **현장 조건의 변화** 등이 존재하며, 이러한 실용적 제약이 기존 연구에서는 충분히 반영되지 않았다.

따라서 본 연구는 건설현장 안전장비 인식이라는 산업용 롱테일 문제에 대해 **클래스당 라벨링 샘플이 극히 적거나 없는 희소 클래스**를 대상으로 제로샷 및 퓨샷 학습을 적용함으로써, 기존 롱테일 인식 연구의 한계를 보완하고자 한다.

## 2.3 퓨샷 학습(Few-shot Learning) 및 제로샷 학습(Zero-shot Learning)

### 2.3.1 퓨샷 학습 개념 및 동향

퓨샷 학습은 새로운 클래스에 대해 매우 적은 수(K개)의 라벨링된 샘플만으로 인식 성능을 확보하도록 설계된 학습 패러다임이다. Wu & Xiao(2024)은 해당 기술이 라벨링 비용이 제한된 실제 산업현장 적용에 중요한 가능성을 지닌다고 언급하였다. ([arXiv][6])
대표적인 모델로는 메트릭 학습(metric-learning) 기반의 **Prototypical Networks**(Snell et al., 2017)가 있으며, 이는 클래스당 K개의 서포트(support)와 여러 개의 쿼리(query)를 통해 임베딩 공간에서 프로토타입(prototype)을 구성하고, 쿼리를 분류한다.
최근에는 객체 탐지 환경에서도 퓨샷 학습(Few-Shot Object Detection, FSOD)이 활발히 연구되고 있다. ([ACM Digital Library][7])

산업용 건설현장에서는 Irregular Openings(불규칙 개방부) 탐지를 위해 MAML 기반 메타 학습을 적용한 연구가 있으며, 5-shot 조건에서 90% 이상의 정확도를 달성한 바 있다. ([MDPI][8])

### 2.3.2 제로샷 학습 개념 및 동향

제로샷 학습은 학습 중 전혀 등장하지 않은 클래스(unseen class)를 인식하도록 설계된 방법이다. 이는 이미지와 클래스 설명(자연어 텍스트) 간의 의미론적 관계(semantic alignment)를 활용한다. 최근에는 이미지-텍스트 대조학습(Vision-Language Pre-training, VLP) 모델인 CLIP(Radford et al., 2021)을 비롯해 다양한 비전-언어 모델이 제로샷 학습의 핵심으로 자리 잡았다.
건설현장에서는 최근 “Recognizing temporary construction site objects using CLIP-based few-shot algorithm” 연구가 발표되었으며, 특히 임시 구조물 및 희소 장비 인식을 위해 CLIP을 활용한 접근이 소개되었다. ([ScienceDirect][9])

### 2.3.3 한계 및 본 연구의 위치

그러나 다음과 같은 제약이 존재한다:

* 대부분의 퓨샷/제로샷 연구는 자연 이미지 도메인에 집중되어 있으며, **건설현장 특유의 장비·환경·카메라 시점 변화** 등에 대한 적용은 아직 미흡하다.
* 건설현장 객체 탐지에서는 **위치 탐지(localization) + 분류(classification)** 모두 필요하나, 제로샷 학습 대부분은 분류(task) 위주로 연구되었다.
* 산업현장 적용에는 단지 성능 향상뿐 아니라 **라벨링 비용, 시스템 통합, 실시간성** 등이 중요한데, 이에 대한 분석은 부족하다.

본 연구는 이러한 공백을 바탕으로 건설현장 45개 클래스 데이터셋의 롱테일 분포를 대상으로 퓨샷 및 제로샷 학습을 적용하고, 라벨링 비용 절감 및 성능 비교 분석을 통해 실무 적용 가능성을 탐구한다.

## 2.4 요약 및 연구적 시사점

본 장에서는 건설현장 안전모니터링을 위한 영상기반 인식, 롱테일 인식, 퓨샷/제로샷 학습이라는 세 가지 연구 흐름을 고찰하였다. 주요 시사점은 다음과 같다:

* 건설현장 안전장비 인식 연구는 주로 빈도가 높은 장비(head class)에 집중되어 왔으며, 희소 장비나 새로운 장비 유형에 대한 인식 연구는 상대적으로 부족하다.
* 롱테일 분포 문제는 여전히 시각인식 분야의 중요 이슈이며, 특히 건설현장 객체 탐지 환경에서는 tail 클래스에 대한 일반화가 더욱 어렵다.
* 퓨샷/제로샷 학습은 라벨링 비용이 제약된 환경에서 유망한 방법이지만, 건설현장과 같은 산업용 도메인 적용은 아직 초기 단계이며, 비용-효율성과 실시간성 등 실무적 요소까지 고려된 연구는 드물다.
* 본 연구는 위 세 흐름이 만나는 지점—건설현장 안전장비 인식, 롱테일 분포, 라벨링이 제한된 학습—에서 기여할 수 있으며, 학문적·실무적 의미를 가질 것이다.

다음 장에서는 이러한 고찰을 바탕으로, 본 논문이 제안하는 **제로샷 + 퓨샷 통합 학습 프레임워크**의 구조 및 알고리즘을 상세히 서술한다.


## 참고문헌

Delhi, V. S. K., Ansari, F., Shehab, E., & Alshawi, M. (2020). Detection of Personal Protective Equipment (PPE) compliance in construction sites. *Frontiers in Built Environment, 6*, 136. [https://doi.org/10.3389/fbuil.2020.00136](https://doi.org/10.3389/fbuil.2020.00136) ([Frontiers][1])
Liu, L., Guo, Z., Liu, Z., Zhang, Y., Cai, R., Hu, X., Yang, R., & Wang, G. (2024). Multi-Task Intelligent Monitoring of Construction Safety Based on Computer Vision. *Buildings, 14*(8), 2429. [https://doi.org/10.3390/buildings14082429](https://doi.org/10.3390/buildings14082429) ([MDPI][2])
Vukicevic, A. M., Petrovic, M., Milosevic, P., Peulic, A., Jovanovic, K., Novakovic, A., & et al. (2024). A systematic review of computer vision-based personal protective equipment compliance in industry practice: advancements, challenges and future directions. *Artificial Intelligence Review, 57*, 319. [https://doi.org/10.1007/s10462-024-10978-x](https://doi.org/10.1007/s10462-024-10978-x) ([SpringerLink][3])
Zhang, H., Li, Y., Xu, J., & Gao, Z. (2024). Long-tail distribution learning in visual recognition: A survey. *Pattern Recognition, 141*, 109101. [https://doi.org/10.1016/j.patcog.2024.109101](https://doi.org/10.1016/j.patcog.2024.109101) ([ScienceDirect][4])
Li, Y., Wang, T., Kang, B., Tang, S., Li, J., & Feng, J. (2020). Overcoming classifier imbalance for long-tail object detection with Balanced Group Softmax. *arXiv preprint arXiv:2006.10408.* ([Illinois Experts][5])
Snell, J., Swersky, K., & Zemel, R. (2017). Prototypical Networks for Few-Shot Learning. In *Advances in Neural Information Processing Systems (NeurIPS 2017)*, (pp. 4077–4087).
Wu, Z., & Xiao, Z. (2024). Few-Shot Learning based on Deep Learning: A Survey. *Mathematical Biosciences and Engineering, 21*(1), 679–711. [https://doi.org/10.3934/mbe.2024029](https://doi.org/10.3934/mbe.2024029) ([arXiv][6])
Wang, X., El-Gohary, N. (2025). Few-Shot Object Detection and Attribute Recognition from Construction Site Images for Improved Field Compliance. *Construction Safety Journal*. (Preprint) ([Illinois Experts][10])
Islam, M. S., Shaqib, S. M., Ramit, S., Akter, S., Sattar, A., & Noori, S. R. H. (2024). A Deep Learning Approach to Detect Complete Safety Equipment for Construction Workers Based on YOLOv7. *arXiv preprint arXiv:2406.07707.* ([arXiv][11])

---

[1]: https://www.frontiersin.org/journals/built-environment/articles/10.3389/fbuil.2020.00136/full?utm_source=chatgpt.com "Detection of Personal Protective Equipment (PPE ..."
[2]: https://www.mdpi.com/2075-5309/14/8/2429?utm_source=chatgpt.com "Multi-Task Intelligent Monitoring of Construction Safety ..."
[3]: https://link.springer.com/article/10.1007/s10462-024-10978-x?utm_source=chatgpt.com "A systematic review of computer vision-based personal ..."
[4]: https://www.sciencedirect.com/science/article/abs/pii/S0926580524002759?utm_source=chatgpt.com "Few-shot object detection and attribute recognition from construction ..."
[5]: https://experts.illinois.edu/en/publications/few-shot-object-detection-and-attribute-recognition-from-construc?utm_source=chatgpt.com "Few-shot object detection and attribute recognition from construction ..."
[6]: https://arxiv.org/html/2408.14249v1?utm_source=chatgpt.com "Beyond Few-shot Object Detection: A Detailed Survey - arXiv"
[7]: https://dl.acm.org/doi/10.1145/3519022?utm_source=chatgpt.com "Few-Shot Object Detection: A Survey - ACM Digital Library"
[8]: https://www.mdpi.com/2075-5309/15/11/1834?utm_source=chatgpt.com "Irregular Openings Identification at Construction Sites Based on Few ..."
[9]: https://www.sciencedirect.com/science/article/abs/pii/S0926580524002784?dgcid=rss_sd_all&utm_source=chatgpt.com "Recognizing temporary construction site objects using CLIP-based ..."
[10]: https://experts.illinois.edu/en/publications/few-shot-object-detection-and-attribute-recognition-from-construc/fingerprints/?utm_source=chatgpt.com "Few-shot object detection and attribute recognition from construction ..."
[11]: https://arxiv.org/abs/2406.07707?utm_source=chatgpt.com "A Deep Learning Approach to Detect Complete Safety Equipment For Construction Workers Based On YOLOv7"
