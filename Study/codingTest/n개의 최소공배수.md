``` py
def solution(arr):
    arr_tmp = arr[:]
    # 리스트 값이 모두 같을때(최소 공배수일때) 빠져나옴
    while len(set(arr)) != 1:
        # 최소 값만 가져와 곱해준다
        a = min(arr)
        b = arr.index(a)
        # 자기 자신의 값을 더해 곱하는 것처럼 해줬다.
        a += arr_tmp[b]
        arr[b] = a
    return arr[0]

from fractions import gcd

def nlcm(num): 
    answer = num[0]
    for n in num:
        # 최대 공약수 / n * answer = 최소 공배수가 된다.(수학적 공식)
        # 근데 이게 여러수에도 적용된다. 이건 몰랐다. 
        # 아무 수 2개의 최소 공배수를 구하고 그 공배수로 다른 수와 최소공배수를 구하는 식으로
        # 왜 이렇게 되는지 잘 모르겠다.....
        answer = n * answer / gcd(n, answer)

    return answer


print(solution([2,6,8,14]))
print(solution([6015240, 1507968, 4530816]))
```
# 
- [n개의 최소공배수](https://school.programmers.co.kr/learn/courses/30/lessons/12953)