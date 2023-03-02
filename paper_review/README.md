<H3>
    CycleMix: A Holistic Strategy for Medical Image Segmentation from Scribble Supervision
</h3>

<hr>

<h4>
    CycleMix의 특징
</h4>

의료 이미지는 특히 라벨링 하는데에 시간과 비용이 많이 든다.

이 논문에서 집중하는 Scribble 이란 annotate가 덜된 형태이다. scribble은 쉽게 얻을 수 있지만 

Supervision에는 제한된다.



이러한 문제를 해결하기위해 mix agmentation과 cycle consistency를 결합한 CycleMix라는 새로

운 프레임워크를 제안했다.



지도학습의 agmentation을 위해 CycleMix는 label의 scribble 증가를 수행하는 mix agmentation

과 감소를 수행하는 random occlusion(랜덤 폐색) 전략을 채택했다.



regluarzation을 위해서는 object training을 일관성 없는 segmentation에 페널티를 주는 

consistency loss를 사용한다. consistency loss는 segmentation에서 상당한 성능향상을 보여준다.

다음은 CycleMix에 핵심 내용인 mix agmentation과 cycle consistency에 대한 사진이다.

<figure1 a 사진첨부>



WSL(weak supervision learning)은 희미하거나 nosiy한 labels을 활용하도록 제안된다.

그중에서도 그림 (a)의 이미지가 보여주듯이 scribble은 가장 편리한 형태의 wsl label중 하나이며

의료영상 분할에 큰 잠재력을 가지고있다. 그러나 지도학습에선 여전히 물체의 모양을 학습하는 것

이 힘들며, 이는 경계를 명확하게 segmentation하지 못한다.



scribble learning은 두 단계가 있다.

첫번째는 scribble annotation을 확장하는 단계이다.

하지만 nosiy한 경우는 오히려 segmentation 성능을 저하시킬 수 있다.

두번째는 이전에 볼 수 없던 data를 만드는 것으로 잘 알려진 mixup이라는 전략이다.

그러나 mixup은 classification을 위해 제안되었고 segmentation에는 성능이 훨씬 나빠질 수 있다.



위에서 언급한 문제를 해결하고자 이 논문은 scribble에서 segmentation을 배우기 위해 CycleMix를 제안한다.











