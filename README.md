# Основы работы с Unity
Отчет по лабораторной работе #2 выполнил(а):
- Валиев Константин Дмитриевич 
- РИ300022
Отметка о выполнении заданий (заполняется студентом):

| Задание | Выполнение | Баллы |
| ------ | ------ | ------ |
| Задание 1 | * | 60 |
| Задание 2 | * | 20 |
| Задание 3 | * | 20 |

знак "*" - задание выполнено; знак "#" - задание не выполнено;

Работу проверили:
- к.т.н., доцент Денисов Д.В.
- к.э.н., доцент Панов М.А.
- ст. преп., Фадеев В.О.

[![N|Solid](https://cldup.com/dTxpPi9lDf.thumb.png)](https://nodesource.com/products/nsolid)

[![Build Status](https://travis-ci.org/joemccann/dillinger.svg?branch=master)](https://travis-ci.org/joemccann/dillinger)

Структура отчета

- Задание 1.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Задание 2.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Задание 3.
- Ссылка на реферат по теме.
- Выводы.
- ✨Magic ✨

## Цель работы
Создание интерактивного приложения и изучение принципов интеграции в него игровых сервисов.

## Задание 1
### По теме видео практических работ 1-5 повторить реализацию игры на Unity. Привести описание выполненных действий.

Ход работы:

1) Создать новый проект из шаблона 3D – Core;
2) Проверить, что настроена интеграция редактора Unity и Visual Studio Code
3) Установить необходимые ассет-паки из Asset Unity Store;
![Alt text](Images/1png?raw=true "Title")
![Alt text](Images/2.png?raw=true "Title")

4) Настроить Animated Controller для дракона на сцене
![Alt text](Images/3.png?raw=true "Title")


5) Создать объект Sphere и Plane на сцене, добавив текстуры;
6) Изменить проекцию камеры на Orthographic;
![Alt text](Images/4.png?raw=true "Title")

7) Создать скрипт "EnemyDragon" для управления драконом;
```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class EnemyDragon : MonoBehaviour
{
    public GameObject dragonEggPrefab;
    public float speed = 1;
    public float timeBetweenEggDrops = 1f;
    public float leftRightDistance = 10f;
    public float chanceDirection = 0.1f;

    void Start()
    {
        Invoke("DropEgg", 2f);
    }

    void DropEgg()
    {
        Vector3 myVector = new Vector3(0.05f, 5.0f, 0.0f);
        GameObject egg = Instantiate<GameObject>(dragonEggPrefab);
        egg.transform.position = transform.position + myVector;
        Invoke("DropEgg", timeBetweenEggDrops);
    }

    void Update()
    {
        Vector3 pos = transform.position;
        pos.x += speed * Time.deltaTime;
        transform.position = pos;

        if (pos.x < -leftRightDistance)
        {
            speed = Mathf.Abs(speed);
        }
        else if (pos.x > leftRightDistance)
        {
            speed = -Mathf.Abs(speed);
        }
    }

    private void FixedUpdate()
    {
        if (Random.value < chanceDirection)
        {
            speed *= -1;
        }
    }
}
```
DroppEgg - Метод, отвечающий за спавн яиц
Update - внутри метода описано измение движения дракона при попадании в крайнии точки
FixedUpdate - внутри метода описано случайное изменение направления движения 

7.2) Установлены значения для скрипта "EnemyDragon" в инспекторе 
![Alt text](Images/5.png?raw=true "Title")

8) Создать скрипт "DragonEgg" для управления партиклами яйца;
```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class DragonEgg : MonoBehaviour
{
    public static float bottomY = -30f;

    void Start()
    {
        
    }

    private void OnTriggerEnter(Collider other)
    {
        ParticleSystem ps = GetComponent<ParticleSystem>();
        var em = ps.emission;
        em.enabled = true;

        Renderer rend;
        rend = GetComponent<Renderer>();
        rend.enabled = false;
    }

    void Update()
    {
        if (transform.position.y < bottomY)
        {
            Destroy(this.gameObject);
        }
    }
}
```

9) Создать скрипт "DragonPicker" для создания щитов на сцене
```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class DragonPicker : MonoBehaviour
{
    public GameObject energyShieldPrefab;
    public int numEnergyShield = 3;
    public float energyShieldBottomY = -6f;
    public float energyShieldRadius = 1.5f;

    void Start()
    {
        for (int i = 1; i <= numEnergyShield; i++)
        {
            GameObject tShieldGo = Instantiate<GameObject>(energyShieldPrefab);
            tShieldGo.transform.position = new Vector3(0, energyShieldBottomY, 0);
            tShieldGo.transform.localScale = new Vector3(1*i, 1*i, 1*i);
        }
    }

    void Update()
    {
        
    }
}
```

## Задание 2
### В проект, выполненный в предыдущем задании, добавить систему проверкитого, что SDK подключен (доступен в режиме онлайн и отвечает на запросы);

Меняем платформу билда с Windows, Mac, Linux на WebGL.
Добавляем открытую сцену в билд.
![Alt text](Images/6.png?raw=true "Title")

Формируется билд игры с html-файлом в который мы интегрируем SDK 
Вставляем следующие строчки кода...
```html
    <script src="https://yandex.ru/games/sdk/v2"></script>
    <script>
      var sdk = null;
      YaGames.init().then(ysdk => {
        console.log('Yandex SDK initialized');
        sdk = ysdk;
        sdk.adv.showFullscreenAdv()
    });
    </script>
```

Готовый билд собираем в ZIP-файл и заливаем на Яндекс.Игры.
Получаем ссылку на черновик игры, запускаем и проверяем логи.
![Alt text](Images/7.png?raw=true "Title")

SDK успешно установлен в билд игры!


## Задание 3
### Произвести сравнительный анализ игровых сервисов Яндекс Игрыи VK Game - Реферат

Ссылка на реферат на Яндекс Диске:
https://disk.yandex.ru/d/kOOYCPPXrFenXA


## Выводы

Создал основу для игры, разоьрался с принципами интеграции в приложение игровых сервисов.
Провел сравнительный анализ платформ Яндекс.Игры и VK.Games