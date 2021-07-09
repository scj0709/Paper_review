Towards Real-World Blind Face Restoration with Generative Facial Prior
=========================
+CVPR 2021년 논문이다. 
+Blind face restoration을 다루는 논문이고, 기존의 방법들이 generative prior와 GAN등을 사용해서 restoration하는데 저하된 이미지에 신뢰도가 없는 이미지를 생성하는 문제점을 지적한다.
+ 본 논문의 contribution은 아래와 같다.
1. generative prior을 사용하여 facial textures와 vivid color 정보를 공동으로 활용한다.
2. GFP-GAN framework를 제안하는데, architecture와 losses를 잘 설계했고, CS-SFT Layer를 통해 textures와 저하된 이미지의 신뢰도의 balance를 잘 잡아준다.
3. 여러 실험을 통해 우수한 성능을 증명한다.
