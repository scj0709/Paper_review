Towards Real-World Blind Face Restoration with Generative Facial Prior
=========================
+ CVPR 2021년 논문이다. 
+ Blind face restoration을 다루는 논문이다.
+ 기존의 방법들이 여러 geometric한 facial prior를 사용해 restoration하지만 실제로 저하된 이미지에서 그러한 prior를 얻는 것은 쉽지않다. 또한 guide 이미지나 facial component dictionaries를 사용해 restoration하기도 하지만 guide 이미지는 접근이 어렵고, dictionaries는 얼굴의 세부적인 다양성을 제한한다는 문제점을 지적한다. GAN을 사용해서 restoration하는 방법들은 real-world의 이미지를 잘표현하고 textures들을 잘표현했 저하된 이미지에 신뢰도가 없는 이미지를 생성하는 문제점을 지적한다.
+ 본 논문의 contribution은 아래와 같다.
  1. generative prior을 사용하여 facial textures와 vivid color 정보를 공동으로 활용한다.
  2. GFP-GAN framework를 제안하는데, architecture와 losses를 잘 설계했고, CS-SFT Layer를 통해 textures와 저하된 이미지의 신뢰도의 balance를 잘 잡아준다.
  3. 여러 실험을 통해 우수한 성능을 증명한다.


Proposed Method
--------------------
![image](https://user-images.githubusercontent.com/40060713/125026006-b4672a00-e0be-11eb-91bf-63e50573a0b5.png)

우선 위의 그림이 전체적인 GFP-GAN의 framework이다. 전체적인 restoration 과정을 요약하면 우선 저하된 이미지 x를 degradation removal network에 넣고 이때 생기는 F_latent는 pretrained된 GAN prior (Style GAN의 방법) 에 인풋으로 들어간다. 각 scale별 F_spatial이 GAN prior의 featuremap 들과 Channel-Split SFT를 통해서 GAN prior의 texture와 저하도니 이미지의 정보가 조화를 이루게 된다. 마지막으로 결과 이미지에 Adversarial Loss, Facial Component Loss, Identity Preserving Loss를 걸어준다. F_spatial에는 Pyramid Restoration Loss를 걸어준다.
