# Practice070521
Code for Graphics
import math
import vk
from time import sleep
from tqdm import tqdm
import matplotlib.pyplot as plt
def function(x, plusone, minusone):  # функция отвечающая за переход состояний, х-рассматриваемый в данный момент человек
    if x == -1:
        if random.random() < G * math.exp(((plusone - minusone) / (plusone + minusone)) * K):
            return 1
        return -1
    if x == 1:
        if random.random() < G * math.exp(((minusone - plusone) / (plusone + minusone)) * K):
            return -1
        return 1
if __name__ == '__main__':
    G = 0.1
    K = 0.5
    changeK = 100
    idmassive = [409400433, 99375576, 451943369, 488931311, 151847116, 477618616, 147631703, 232232431, 180425389, 291475605, 194483865, 504810803, 272567057, 226266495, 293169541, 597710505, 444254524, 451217211, 303788950, 332243407, 277417927 ]
    session = vk.AuthSession(7784717, '+79811541416', '2002aleksandr')
    api = vk.API(session)
    n = len(idmassive)  # количество рассматриваемых людей
    matrix = [[0] * n for _ in range(n)]  # матрица смежности (связь между людьми обозначается еденицой) В нее нужно внести связь между  людьми (ученики ФТШ) вк_апи
    for i in tqdm(range(0, n, 1)):
        iddopmassiv = api.friends.get(user_id=idmassive[i], v='5.126')['items']
        sleep(1)
        for j in range(0, len(iddopmassiv), 1):
            for k in range(0, n, 1):
                if iddopmassiv[j] == idmassive[k]:
                    matrix[i][k] = 1
    SRKstate = [] #массив состояний системы в зависимости от значения К
    for p in tqdm(range(0, changeK, 1)):
        SRstate = [] #массив средних состяний системы людей в зависимости от количества шагов
        people = [] # одномерный массив сосстояний (заполнен -1 или 1), заполняется рандомно
        for i in range(0, n, 1):
            people.append(random.randint(0, 1) * 2 - 1)
        numbersteps = 10000 #количество шагов
        for r in range(numbersteps):
            for i in range(0, n, 1): #идем по матрице смежности и заполняем массив состояний людей (people[]) -1 или 1 в зависимости от результата функции
                plusone = 0
                minusone = 0
                for j in range(0, n, 1):
                    if matrix[i][j] == 1:
                        if people[j] == -1:
                            minusone += 1
                        if people[j] == 1:
                            plusone += 1
                if plusone + minusone != 0:
                    people[i] = function(people[i], plusone, minusone)
            sumone = 0 #количество едениц на данном шаге в массиве состояний
            summinusone = 0 #количество минус едениц на данном шаге в массиве состояний
            for i in range(0, n, 1):
                if people[i] == 1:
                   sumone += 1
                if people[i] == -1:
                   summinusone += 1
            SRstate.append(abs((sumone - summinusone)) / n)
        sum = 0
        for i in range(0, len(SRstate), 1):
            sum += SRstate[i]
        SRKstate.append(sum / numbersteps)
        K += 0.02
    x = [] #координата по оси абцисс (состоние системы на каждом шаге в зависимости от К)
    ii = 0.48
    for i in tqdm(range(0, changeK, 1)):
        ii += 0.02
        x.append(ii)
    plt.plot(x, SRKstate)  # построение графика зависимости среднего состояния системы людей от количества шагов
    plt.show()
