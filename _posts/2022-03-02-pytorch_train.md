---
title: zero_grad(), step(), 그리고 backward()에 대한 고찰
author: hyunobae
categories: [Pytorch]
tags: [Pytorch]
published: true
use_math: true
---

Pytorch로 연구 관련 코드를 사용하고 있지만 그냥 당연시하는 코드들이 있다. 바로 ***zero_grad()*** 와 ***step()***, 그리고 ***backward()*** 이다. 오늘은 각 메서드가 어떤 일을 하는지 정리하여 앞으로 헷갈리는 일이 없도록 하는 것이 좋겠다.<br><br>


## Pytorch에서 gradient update 과정
앞서 제시한 메소드를 알기 전에 먼저 pytorch에서 gradient update가 일어나는 과정을 이해할 필요가 있다. 

보통 torch 기반 딥러닝 모델은 아래와 같은 과정으로 gradient update가 일어난다.

``` python
optimizer.zero_grad()
loss.backward()
optimizer.step() 
```

여기서 **optimizer**는 *gradient를 update하는 방법*을 의미한다. 예를 들면, 어떤 작업을 수행할 때, 작업에 따라 가장 효율적인 방법이 다를 수 있다. 이때 어떤 작업에 사용 할 **효율적인 방법**에 해당하는 것이 optimizer이다. 그렇다면 *딥러닝에서 optimizer는 어떤 역할을 하는가?* 처음 제시한 대로 정답과 예측 값 사이의 loss를 줄이는 과정에서 사용하는 최적화 방법이다. (optimizer 말 그대로 최적화해주는 것 이라고 이해해도 괜찮을 것 같다.)<br>
ex) Adam, AdaGrad, RMSProp 등

이제 하나하나 정리해보도록 하자.<br><br>


## backward()
[backward()](https://pytorch.org/docs/stable/generated/torch.Tensor.backward.html?highlight=backward#torch.Tensor.backward)는 gradient update에 있어서 가장 중요한 부분이라 할 수 있다. 이름에서 유추할 수 있듯이, 우리가 흔히 아는 굉장히 복잡한 *back-propagation 과정*이 이 backward()를 호출함으로써 계산된다. 이는 ***loss와의 손실***을 따지기 때문에 위 예제코드와 같이 loss.backward()를 사용하면 된다.<br><br>


## zero_grad()
[Pytorch documentation](https://pytorch.org/docs/stable/generated/torch.optim.Optimizer.zero_grad.html)에서는 아래와 같이 정리해주었다. 
>Optimizer.zero_grad(set_to_none=False)
>
이는 **gradient를** 가지는 optimizer의 torch.Tensor를 **0으로 설정하는 것**이다. <br>
이 작업이 굉장히 중요하다. 기껏 해당 iteration에서 loss를 잘 구했는데 이 전 iteration의 batch의 loss와 결과가 섞여버린다면 결국 모델의 학습은 제대로 되지 않을 것이다. ***zero_grad()는 optimizer가 이번 단계의 gradient만 추적할 수 있도록 이 전 단계의 gradient를 0으로 만드는 과정이다.*** 따라서, optimizer.zero_grad()를 호출하면 되겠다. <br>


## step()
마지막으로 step()은 상대적으로 간단하다. 위 과정에서 ***계산된 gradient를 갱신에 사용하겠다는 의미이다.*** gradient 갱신의 주체는 optimizer이기 때문에 optimizer.step()을 사용하면 되겠다.

---
이렇게 pytorch를 사용하여 훈련하는 경우 항상 사용되는 부분을 정리해보았다. 다음에는 항상 헷갈리는 dataloader에 대해 정리해봐야겠다.