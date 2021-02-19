## Q-learning on nonedeterministic world
해당 포스트는 sung Kim 교수님의 모두를 위한 RL 강좌를 참고하여 작성되었으며, 하단에 링크를 기재하였습니다.

Agent의 action이 확률적으로 다음 state를 결정하는 environment를 의미합니다.
즉, 모든 state는 Stochastic하게 정해진다는 것입니다.

이는 frozen lake게임에서 예제를 볼 수 있는데, 우측으로 이동한다는 명령을 하더라도 state가 우측으로 이동하지 않을 수도 있다는 것입니다.

앞서 배운 Q-Learning에 따르면, $\hat{Q}(s, a) = r + \gamma max_{a’} \hat{Q}(s’, a’)$ 으로 배웠습니다. 

그러나, 확률론적으로 state가 결정되는 nondetermimistic 환경에서는 해당 방법이 잘 통하지 않습니다.
따라서, Q-Learning을 사용하되 조금은 다른 방식으로 접근할 필요가 있습니다. 

핵심적인 아이디어는 Q를 조금만 믿는 다는 것입니다. 조금은 복잡하게 느껴질 수 있지만, 이를  수식적으로 나타내면 아래와 같습니다. 

$$Q(s, a) = (1-\alpha)Q(s, a) + \alpha [r + \gamma max_{a’} Q(s’, a’)]$$

여기서 $\alpha$는 learning rate로 이전 Q를 현재상태에 얼마나 반영하는 비율을 의미합니다.

## Q-Network
우리가 이전까지는 Q-Table을 사용한 강화학습을 진행하였습니다. 그러나, 이것 역시 실제 pixel 단위에서 한계가 존재합니다.

예를 들면, 80 x 80 pixel 에서 2개의 color를 나타낸다면, 하나의 픽셀당 2개의 경우의 수를 가지므로 $2^6400$ 크기의 배열이 필요하게 됩니다.

따라서, 이를 해결하기 위해 Q-Network를 도입하였고 핵심 아이디어는 다음과 같습니다.

state와 action을 input으로 넣고, Q-value를 얻었던 기존 방법과는 다르게, state만을 input으로 넣고 action에 따라 가능한 Q-value를 얻는 것입니다.

여기서도 역시 최소제곱법을 통한 방법으로 진행하게 되는데, Network를 통과한 state를 $\hat{s_{t}, a_{t} | \theta)$ 로 표현하고, optimial Q 즉 참 값을 이용하여 목적함수를 수식으로 나타내면 아래와 같습니다.

$$min_{\theta} \sum_{0}^T  [\hat{s_{t}, a_{t} | \theta) - (r_t + \gamma max_{a’} \hat{Q} (s_{t+1}, a’ | \theta))]^2$$

## DQN
그럼에도 불구하고 Q-Network도 다시 한 번 한계를 경험합니다. 그 이유는 Network에서의 $\hat{Q}$가 converge 하지 못하기 때문입니다.

우선 sample간의 correlation이 크다는 것과, target이 Non-stationary하다는 것이 원인입니다.

따라서, 구글의 딥마인드 팀은 이를 개선하기 위해서 간단한 몇 가지의 방법을 도입하여 DQN을 만들어 냈습니다.

핵심 아이디어는 아래의 3가지 입니다.

1. Go deep
2. experience replay(sample간의 correlation 해결)
3. seperate target network(non-stationary target)

experiece replay의 경우에는 샘플들을 buffer에 저장한 후에 사용하는 방법으로 샘플간의 상관관계를 해소할 수 있습니다.

seperate target network의 경우에는 target이 함께 변하는 것을 우려하여 별도의 네트워크에서 학습하는 것을 의미합니다. 그리고 중간중간 일정 시간 이후에 두 네트워크를 동기화하는 방식입니다.
