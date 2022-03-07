---
title: Dataloader란?
author: hyunobae
categories: [Pytorch]
tags: [Pytorch]
published: true
use_math: true
---

Pytorch로 딥러닝을 학습하면 빼놓을 수 없는 dataloader이다. 보통 딥러닝 시작을 keras와 sklearn의 train_test_split()으로 사용하기 때문에 dataloader를 마주하면 당황하기 마련이다. 나 또한 dataloader가 처음에는 난해했지만 오히려 사용하면서 편하다고 느끼기도 했다. 오늘은 특히 기본으로 제공되지 않는 데이터를 커스텀하여 사용할 수 있는 CustomDataloader에 대해서 작성하도록 하겠다. 지금부터 dataloader에 대해서 알아보자.

## Dataloader()?
- 우선 torch의 dataloader는 말 그대로 훈련과 검증, 테스트시 사용할 데이터를 불러와주는 모듈이다. Dataloader class는 아래와 같이 구성되어 있다.

``` python
DataLoader(dataset, batch_size=1, shuffle=False, sampler=None,
           batch_sampler=None, num_workers=0, collate_fn=None,
           pin_memory=False, drop_last=False, timeout=0,
           worker_init_fn=None, *, prefetch_factor=2,
           persistent_workers=False)
```

CustomDataloader는 보통 map-style dataset으로 구성하며, iteration마다 indices와 key를 반환해준다. 이때, 무조건 만들어줘야하는 메소드는 \__getitem\__()과 \__len\__()이다. 아래 코드를 예시로 들며 자세히 설명하도록 하겠다.

CustomDataloader의 기본 틀은 다음과 같다. 아래 코드는 본인이 super-resolution 관련 연구 중 실제로 사용한 코드 중 **일부**이다. 

``` python
class CustomDataloader(Dataset):
    def __init__(self, dataset_dir, crop_size, upscale):
        super(CustomDataloader).__init__()
        self.dirlist = os.listdir(dataset_dir)
        self.image = []

        for dir in self.dirlist:
            for imglist in dir:
                self.image.append(~ + imglist)
        self.hr_trans = transform(crop_size)
        self.lr_trans = trsnsform(upscale)


    def __getitem__(self, index):
        hr_img = self.hr_trans(self.image[index])
        lr_img = self.lr_trans(hr_img)
        return hr_img, lr_img


    def __len__(self):
        return len(self.image)
```

### Dataloader Class
Dataloader를 만들 땐 Dataset을 상속받아야하는데 from torch.utils.data.dataset으로부터 상속받는다. 

### \__init\__()
init은 이름 그대로 초기화 작업을 수행한다. 작업에서 필요한 경로 설정이나 getitem에서 사용할 변수 초기화 등을 주로 한다. 
<br>

### \__getitem\__()
getitem은 dataloader의 객체에서 인덱스로 접근할 때 자동으로 호출된다. 따라서, 이 부분은 데이터 전처리과정을 거쳐서 return으로 *훈련이나 추론에 사용될 데이터를 반환하는 부분이다.* 

### \__len\__()
사용되는 데이터의 갯수를 반환하는 부분이다. 아주 간단한 예를 들자면, train 과정에서 사용되는 데이터의 갯수가 15000장이라면 **return 15000** 을 작성해주는 부분이다. 

---

Torch를 사용하면서 굉장히 난해한 부분 중 하나였는데 알고보면 생각보다 어렵지 않은 부분이었다. 오늘도 프레임워크 개발자들에게 감사함을 표하며 마무리...