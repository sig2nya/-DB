1. 개요
```
1.1) 다섯 명의 철학자가 하나의 원탁에 앉아 식사를 한다. 각 철학자 사이에는 포크가 하나씩 있으며, 포크의 갯수와 철학자 인원 수는 같다.
1.2) 철학자는 취할 수 있는 행동은 식사와 생각하기 2가지 종류만 가능하다.
1.3) 철학자는 식사를 할 때, 반드시 양 옆에 있는 포크 2개를 사용해야 한다. 따라서, 한 첧학자가 식사를 하기 위해서는 왼쪽 / 오른쪽에 있는 철학자가 식사를 하지 않고 생각하고 있어야만 한다.
1.4) 철학자가 식사를 마친 후, 다른 철학자가 사용 가능하도록 왼쪽 / 오른쪽 포크를 내려 놓아야 한다.
```
<img width="233" alt="image" src="https://github.com/sig2nya/ETC/assets/70207093/fdf4252e-3fe3-4a01-8b4e-10c535435f02"/>

2. 문제점 - DeadLock
```
2.1) 만약, 다섯명의 철학자가 식사를 동시에 시도하려고 왼쪽 포크를 집어든다고 하자.
2.2) 이런 상황에서, 모든 철학자의 오른쪽 포크는 이미 점유된 상태이다.
2.3) 즉, 모든 철학자는 오른쪽 포크를 얻을 때까지 대기하는 문제가 생긴다.
```

3. 해결 방법
```
3.1) MUTEX : Mutual Exclusion. 한 철학자가 포크를 점유하고 있는 경우(왼쪽 오른쪽 상관 없이), 다른 철학자가 포크를 점유하지 못 하도록 한다.
3.2) MUTEX LOCK : 이처럼, 특정 스레드가 자원을 점유하였을 때, 다른 스레드가 자원을 사용하지 못하도록 하는 것이 MUTEX LOCK이다. (스레드가 자원을 사용하기 위한 자물쇠)
```


4. 구현
```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <pthread.h>

#define true 1
#define NUM_PHILS 5

void init();
int leftOf(int i);
int rightOf(int i);
void *philosopher(void *param);
void think(int id);
void eat(int id);
void pickup(int id);
void putdown(int id);
void test(int i);

enum {
    THINKING,
    HUNGRY,
    EATING
} state[NUM_PHILS];

pthread_mutex_t mutex_lock;
pthread_cond_t cond_vars[NUM_PHILS];

int main() {
    int       i;
    pthread_t tid;
    init();

    for (i = 0; i < NUM_PHILS; i++) {
        pthread_create(&tid, NULL, philosopher, (void *) &i);
    }

    for (i = 0; i < NUM_PHILS; i++) {
        pthread_join(tid, NULL);
    }

    return 0;
}

void init() {
    int i;
    for (i = 0; i < NUM_PHILS; i++) {
        state[i] = THINKING;
        pthread_cond_init(&cond_vars[i], NULL);
    }

    pthread_mutex_init(&mutex_lock, NULL);
    srand(time(0));
}

int leftOf(int i) {
    return (i + NUM_PHILS - 1) % NUM_PHILS;
}

int rightOf(int i) {
    return (i + 1) % NUM_PHILS;
}

void *philosopher(void *param) {
    int id = *((int *) param);

    while (true) {
        think(id);
        pickup(id);
        eat(id);
        putdown(id);
    }
}

void think(int id) {
    printf("%d : Now, I'm thinking...\n", id);
    usleep((1 + rand() % 50) * 10000);
}

void pickup(int id) {
    pthread_mutex_lock(&mutex_lock);

    state[id] = HUNGRY;
    test(id);

    while(state[id] != EATING) {
        pthread_cond_wait(&cond_vars[id], &mutex_lock);
    }

    pthread_mutex_unlock(&mutex_lock);
}

void eat(int id) {
    printf("%d : Now, I'm eating...\n", id);
    usleep((1 + rand() % 50) * 10000);
}

void putdown(int id) {
    pthread_mutex_lock(&mutex_lock);

    state[id] = THINKING;
    test(leftOf(id));
    test(rightOf(id));

    pthread_mutex_unlock(&mutex_lock);
}

void test(int i) {
    if(state[i] == HUNGRY &&
       state[leftOf(i)] != EATING &&
       state[rightOf(i)] != EATING)
    {
        state[i] = EATING;
        pthread_cond_signal(&cond_vars[i]);
    }
}
```
