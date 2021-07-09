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

  + 우선 위의 그림이 전체적인 GFP-GAN의 framework이다. Degradation Removal module과 pretrained face GAN으로 이루어져 있다. 두개의 모듈은 latent code mapping과 Channel-Split Spatial Feature Transform  (CS-SFT) layers를 통해 연결된다. Degradation removal module은 저하된 입력의 저하를 풀어주고, clean한 F_latent를 W space로 mapping한다. W space는 style gan 논문을 읽어보자. 그리고 multi-resolution spatial features F_spatial을 생성한다. 이렇게 저하된 이미지의 coarsely 찾아진 깨끗한 F_latent를 GAN prior F_prior로 얻는다. 이렇게 얻어진 F_spatial과 F_prior를 CS-SFT를 통해 real하고 신뢰있는 결과를 얻을 수 있다. 결과 이미지에 adversarial loss, facial component loss, identity preserving loss를 걸어주고, F_spatial에 pyramid restoration loss를 걸어준다.

  - Degradation Removal Moudule   
    Pyramid networks를 통해서 다중 해상도의 로스를 통해 깨끗한 영상을 얻을 수 있다고한다. 여기서 refer한 "Deep Laplacial Pyramid Networks for Fast and Accurate Super-Resolution"논문은 안읽어 봐서 읽어봐야 겠다. 하튼 U-net구조를 이용하고 encoder는 W space로 mapping decoder는 clean한 feature를 뽑는데 이때 위의 pyramid network에서 사용한 방법이 사용된다.
 
  - Generative Facial Prior and Latent Code Mapping   
   개인적으로 이 부분이 가장 놀라웠다. Indomain GAN 논문을 읽고 어떻게 GAN inversion을 사용할 수 있을지란 생각만 했었는데, 이 논문에서는 아예 Indomain GAN과 같은 W mapping을 네트워크 학습안에 집어넣었다. 즉 위의 U-net 구조의 encoder부분이 저하된 이미지를 GAN prior의 W space로 mapping해준다. 이때 최대한 깨끗한 이미지를 표현한 F_latent를 뽑도록 encoder가 학습될 것이다.

  - Channel-Split Spatial Feature Transform   
    이 부분도 본 논문의 핵심내용 중 하나이다. 기존에 사용되던 Spatial Feature Transform에 channel split을 적용한다. 기존의 Spatial Feature Transform을 F_spatial과 F_prior에 단순하게 적용할 경우 아무리 앞에서 Degradation removal을 했다고 하여도 저하가 심하면 blurry 한 feature이기 때문에 결과가 blurry해진다고 한다. 이를 해결하기 위해서 F_prior를 두개의 channel로 나눈 뒤 한채널은 identity mapping을 하고 나머지 한채널을 F_spatial과 Spatial Feature Transform을 진행한다. 이 결과로 fidelity와 texture의 faithfulness의 balance를 잡았다고 한다.
  
  - Model Objectives   
    이 논문에는 여러 loss들이 사용되었다. 우선 reconstrrction loss로 L1 loss와 perceptual loss로 vgg loss가 사용되었다. 결과이미지의 real함을 위해서 adversarial loss도 사용되 었고, 눈과 입에 Facial component loss가 사용되었다. 이때 discirinator loss 뿐만 아니라 GRAM matrix를 통해 style loss를 걸어준다. 마지막으로 결과 이미지가 저하된 이미지와의 일치를 주기 위해서 ArcFace model 을 통한 loss를 걸어준다. 
