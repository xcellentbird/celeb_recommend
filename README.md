# celeb_recommend

이상형 추천 프로젝트에 데이터 수집 및 이상형 추천 알고리즘 제작 역할로 참여했습니다.
본 코드는 얼굴 Feature를 이용하여 사용자 선택에 따른 이상형 Feature 도출하고 이상형 연예인을 추천해주는 알고리즘 테스트 코드입니다.

---

최근 3주동안 지인들을 만날 때마다 팀에서 만든 서비스 테스트를 부탁드려봤습니다. 재미는 물론, 어느 정도 정확도도 나오는 걸 확인할 수 있었어요. 대략 이상형에 가까운 연예인 7명을 추천하면, 그 중 2~5명은 이상형에 해당한다고 응답해주셨습니다.

![](https://images.velog.io/images/xcellentbird/post/eee02a3f-b6c6-4f08-b9ad-81dc67bb9372/image.png)


---
# 1. FaceNet 모델을 이용한 얼굴 Feature 생성

다른 조원분이 AutoEncoder를 대신해서 쓸 수 있는 모델을 찾아와 주셨습니다. FaceNet은 얼굴 Feature에 Metric Learning을 이용하여, 동일한 일물이라면 distance가 작도록 학습시킨 모델입니다. ([FaceNet 논문](https://arxiv.org/abs/1503.03832), [논문 리뷰](https://deep-learning-study.tistory.com/681)) 일단 저는 이 모델을 써볼 겸, 얻어낸 Feature가 어떤 Feature인지 구분해낼 수 있을지 실험해보기로 했습니다.
![](https://images.velog.io/images/xcellentbird/post/e46b58ad-1650-4623-b7da-2a57edc60b5a/image.png)

MTCNN 모델을 이용하여 얼굴을 찾아 crop하고, FaceNet을 이용하여 128개의 Feature를 얻어냈습니다. 그 다음, 각 Feature마다 가장 작은 값을 갖는 이미지 6장, 가장 큰 값을 갖는 6장을 출력해보았습니다.
 
 ---
 # 2. 인지심리학: '끌리는 얼굴은 무엇이 다른가?' 
 ![](https://images.velog.io/images/xcellentbird/post/550b74ae-ed75-4eba-8567-7ae97ad29f10/image.png)
 일단, 이상형 Feature를 얻기 전에, 이상형이란 무엇인지, 어떤 것들과 연관이 있을지 연구해볼 필요가 있었습니다.
 
 1. 눈을 통해 얼굴이라 인지할 수 있는 시각 정보를 얻고, 해당 정보를 가지고 있던 기억과 연결시키는 원리로 이상형이 만들어지게 됩니다. 이 내용은 어떠한 아이디어를 주기보다, '얼굴 Feature에 이상형 Feature가 담겨있다'는 가설을 입증해주는 자료가 되었습니다. FaceNet을 이용하여 얼굴 Feature를 얻었으니, 이제 이 Feature 중에서 어떤 Feature가 이상형과 관계있는지 학습시키기만 하면 되는 것이라 생각됩니다. 즉, 이상형 Feature를 알아낸다는 것은, 어떤 Feature가 기억과 연관되어 있는지 알아내는 작업이 될 것 같습니다.
 
 2. 사람은 평균적인 얼굴을 매력적이라 생각한다고 합니다. 사람의 얼굴을 모두 모아 합성했을 때, 더 매력적인 얼굴이 나옵니다. 그리고 매력적이라 느끼는 얼굴끼리 합성하면 더 더욱 매력적인 얼굴이 나온다고 합니다. - 사용자가 선택한 얼굴 Feature를 모두 합성하면 이상형에 가장 가까운 얼굴이 만들어지지 않을까 생각했습니다. 그리고 그 얼굴의 Feature는 사용자의 이상형에 가까운 Feature일 것입니다. 그리고 굳이 GAN을 이용하여 합성할 필요없이, 선택한 얼굴 Feature를 모두 더해 평균을 내면 그게 이상형에 가장 가까운 Feature가 되지 않을까 생각했습니다.

---
# 3. 선택한 얼굴 Feature로 이상형 Feature 만들기

 1. 일단, 아직 Cluster Sampling 기능이 만들어지지 않았으니, 사용자에게 무작위의 이미지가 선택지로 주어지게끔 만들었습니다. 그리고 아래 사진과 같이 N개의 선택지가 있는 문제를 M번 보여주기로 하였습니다. (N, M은 사용자 설정 상수) 
![](https://images.velog.io/images/xcellentbird/post/6cf80e81-4580-44ed-92d7-a097139494f3/image.png)
 2. 사용자가 선택한 이미지에 해당한 Feature들의 평균을 구합니다. 그 다음, 그 Feature와 가까운(euclidean distance 사용) Feature를 가진 얼굴 이미지들을 출력해냅니다.
![](https://images.velog.io/images/xcellentbird/post/7700ad15-18c2-41d2-9108-9e4bf8514955/image.png)
