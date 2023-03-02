
<H3>
    CycleMix: A Holistic Strategy for Medical Image Segmentation from Scribble Supervision
</h3>

<hr>


의료 이미지는 특히 라벨링 하는데에 시간과 비용이 많이 든다.

이 논문에서 집중하는 Scribble 이란 annotate가 덜된 형태이다. scribble 데이터는 쉽게 얻을 수 있지만 Supervision에는 제한된다.

이러한 문제를 해결하기위해 증강 기법인 mix augmentation과 cycle consistency loss를 결합한 

CycleMix라는 새로운 프레임워크를 제안했다.

지도학습의 augmentation을 위해 CycleMix는 label의 scribble 증가를 수행하는 mix augmentation과 

감소를 수행하는 random occlusion(랜덤 폐색) 전략을 채택했다.

regluarzation을 위해서는 object training을 일관성 없는 segmentation에 페널티를 주는 

consistency loss를 사용한다. consistency loss는 segmentation에서 상당한 성능향상을 보여준다.

다음은 CycleMix에 핵심 내용인 mix augmentation과 cycle consistency에 대한 사진이다.

<h4>
    CycleMix 기본 구조
</h4>
<img src="https://github.com/2Swon/DeepLearing/blob/main/paper_review/CycleMIx/images/figure1_a.png" width=80%>
<img src="https://github.com/2Swon/DeepLearing/blob/main/paper_review/CycleMIx/images/figure1_b.png" width=80%>


WSL(weak supervision learning)은 희미하거나 nosiy한 labels을 활용하도록 제안된다.

그중에서도 그림 (a)의 이미지가 보여주듯이 scribble은 가장 편리한 형태의 wsl label중 하나이며

의료영상 분할에 큰 잠재력을 가지고있다. 그러나 지도학습에선 여전히 물체의 모양을 학습하는 것 이 힘들며, 

이는 경계를 명확하게 segmentation하지 못한다.

그림 (b)에는 consistency loss의 활용을 보여준다.

global loss 는 원본 이미지와 mix 이미지에 일관성 없는 부분에 불이익을 주는 반면

local loss 는 예측과 원본 이미지의 유사도를 이용한다.
<h4>
    CycleMix 전체 구조
</h4>
<img src="https://github.com/2Swon/DeepLearing/blob/main/paper_review/CycleMIx/images/figure3.png" width=70%>

이 그림은 CycleMix 모델의 구조이다. mix augmentation은 Puzzle Mix를 사용했다.

Puzzle Mix는 지역 통계를 보존하면서 각 데이터의 두드러진 정보를 최대화하는 방법이다.
<h4>
    Puzzle Mix 과정
</h4>
<img src="https://github.com/2Swon/DeepLearing/blob/main/paper_review/CycleMIx/images/22.gif" width=50%>

사용방법
1. data의 saliency map을 계산한다.
2. down-sampled 후에 saliency map을 계산한다.
3. saliency를 기반으로 mix된 data의 각 위치에 비율에 맞는 label을 할당해준다.
4. 각 입력에 대해 transport를 수행하여 최대화 한다.



<h4>
    CycleMix 성능 지표
</h4>
<img src="https://github.com/2Swon/DeepLearing/blob/main/paper_review/CycleMIx/images/table3.png" width=60%>

위의 사진을 보면 Scribble만 있는 데이터셋임에도 불구하고 성능이 가장 좋다.

<img src="https://github.com/2Swon/DeepLearing/blob/main/paper_review/CycleMIx/images/table4.png">

unmix sample, mix sample, random occlusion, local consistency loss, global consistency loss을 모두 사용했을때 AVG가 제일 높은 것을 알 수 있다. 






