GLEAN: Generative Latent Bank for Large-Factor Image Super-Resolution
================
+ Kelvin C.K. Chan, Xintao Wang, Xiangyu Xu, Jinwei Gu, Chen Chenge Loy
+ CVPR 2021년 논문
+ Super-Resolution (SR) 을 다루는 논문이다. 
+ 이전에 리뷰한 GFP-GAN과 마찬가지로 미리 학습된 GAN을 prior로 사용해서 restoration하는 방식이다.
+ GAN을 이용한 기존의 방식은 크게 SR generator를 처음부터 학습시키는 방법과 GAN inversion을 사용하는 방식이 있다. 전자의 경우 자연이미지의 특성과 ground-truth의 일치성까지 generator가 모두해야 하므로 natural image의 manifold를 담아내는데 한계가 있다. 후자의 경우 low-dimensional latent codes가 이미지를 복원하는 가이드로 충분하지 않아 ground-truth에 충실하지 못하다. 또한 optimization하는데 많은 시간이 걸린다. GFP-GAN과 기존의 방법을 거의 비슷한 이유로 지적하고 있다.
+ 이에 본 논문에서는 잘 학습된 GAN을 사용해서 SR을 진행한다. pre-trained GAN을 latent bank로 언급하고 있다. GFP-GAN은 encoder-decoder-bank 순의 구조라면 여기서는 encoder-bank-decoder 순의 구조이다. 이러한 구조의 순서는 딱히 중요한 부분이 아니고 같다고 봐도 무방하지만 두 논문사이에 미묘한 차이가 있다. decoder의 차이이다. 

Proposed Method
--------------------
![image](https://user-images.githubusercontent.com/40060713/125558837-29c75b09-3ea6-450c-8366-4f4e46e9fd8f.png)

  + 우선 위의 그림을 보면 낮은 resolution의 이미지를 encoder로 부터 pre-trained GAN의 W space로 매핑해 GAN을 통해 feature를 뽑고 이 feature를 decoder로 전달해준다. 이때 decoder의 첫 입력으로는 encoder의 첫번째 feature가 들어간다. 

  - **Encoder**
    pre-trained GAN에 들어갈 latent vectors를 생성해주기위한 encoder이다. RRDB-Net의 방법으로 우선 f_0 feature를 뽑고, 그뒤 resolution을 줄여가며 features를 뽑아준다. 마지막 출력으로는 C가 나온다. 이 C는 generative latent bank의 latent vectors로 사용된다.
    
  - **Generative Latent Bank**
    pre-trained GAN으로 style GAN이 사용되었다. 이전 encoder에서의 features와 latent vector C가 입력으로 사용된다. Style GAN은 이미지 generation tasks에 사용되므로 직접적으로 이 framework에 사용될 수 없어서 세가지 수정사항을 통해 적용했다.
    1.Style GAN에 서는 하나의 벡터가 latent vector로 사용되고 각 block에 같은 벡터들이 들어간다 하지만 본 논문에서는 각 블록 별로 다른 latent vectors가 들어간다. 즉 위의 C는 여러개의 벡터가 된다. 
    2.encoder로 부터 생성된 feature들을 style block에 추가해준다. 정확한 부분은 코드가 나와 봐야 알겠지만 style block으로 feature fusion을 한다는 것은 style GAN에서의 noise 추가부분을 encoder의 features를 넣어주는 것 같다. 
    3.이러한 generator로 바로 이미지를 생성하는 것이 아니라 decoder를 통해 latent bank와 encoder의 feature를 보다 잘 fuse 시켜준다.

  - **Decoder**
    encoder의 feature와 latent bank의 features의 progressive 한 fusion을 위해서 추가적인 decoder를 달아준다. decoder의 첫인풋으로 encoder의 첫 번째 feature인 f_0가 들어가고 그 이후에 각 decoder의 block에 latent bank의 feature들을 넣어준다. 어떤 feature들이 합쳐지는 지는 위의 그림을 보면 쉽게 알 수 있다. 이때는 아마 concat한뒤 convolution하는 듯 하다. 
   
  - **Training**
    기존의 방법들과 비슷하게 l2 loss와 perceptual loss를 사용하고, adversarial loss도 걸어준다. perceptual loss로는 VGG face가 사용된다. 학습할때는 latent bank의 weights는 고정하고 학습시킨다. 
    
   
Experiments
------------- 
