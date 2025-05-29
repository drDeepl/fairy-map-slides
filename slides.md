---
# You can also start simply with 'default'
theme: academic
title: сказки народов России
# apply unocss classes to the current slide
class: text-center
# https://sli.dev/features/drawing
drawings:
  persist: false
transition: slide-left
layout: intro
mdc: true
---
<style>

 .mermaid {
  max-width: 100%;
  max-height: 80vh;
  overflow: auto;
  transform: scale(0.5);
  transform-origin: top center;
}

  .quote{
    padding: 0.5rem;
    border-radius: 7px;
    border-left: 4px solid #fd746e
  }

  .mermaid svg {
  max-width: 600px;
  height: 400px;
}

  h1 {
    padding-bottom: 0.7rem;
    border-bottom: 1.5px solid #D6D4Df;
  }
</style>

<div class="flex flex-col space-y-4">
  <p class="text-5xl">Разработка веб-приложения</p>
<p class="text-4xl">"Сказки народов России"</p>
</div>


<div class="absolute bottom-10 right-5">
  <div clss="flex flex-col text-start justify-start -space-y-2">
    <p class="font-400 text-start">
    Подготовил:
  </p>
    <p>Студент ПМИ-21: Никитин В.Е</p>
  </div>
</div>

---

# Цель

<div class="quote text-2xl">

<span class="font-semibold">Целью</span> выпускной квалификационной работы является создание веб-платформы, предоставляющую пользователям доступ к сказкам народов России через интерактивную карту, с возможностью прослушивания аудиозаписей и участия в наполнении контентом.

</div>

---

# Описание

<div class="quote text-2xl italic">
Проект "Сказки народов России" нацелен на создание веб-приложения,которое позволит пользователям ознакомиться с культурным наследием   различных этнических групп России через сказки на их родных языках.
</div>


---

# Функиональные требования

1. Интерактивное исследование карты России:

   - Отображение карты с границами субъектов федерации.

   - Возможность выбора региона для получения детальной информации.

2. Доступ к культурному наследию:

   - Отображение информации об этнических группах, проживающих в выбранном регионе.
   - Предоставление текстов сказок на русском языке.
   - Прослушивание аудиозаписей сказок на различных языках

---

# Функциональные требования

3. Взаимодействие пользователй с веб-приложением:

   - Авторизация и создание личного кабинета.
   - Возможность загрузки собственных аудиозаписей сказок.
   - Система рейтингов для оценки аудиозаписей.
   - Поиск сказок.
   - Обратная связь с администрацией.

4. Модерация загружаемых аудиозаписей
5. Одобренный контент находится в публичном доступе.

---

# Инфологическая схема приложения

```mermaid
classDiagram
    %% --- Основные сущности ---
    class User {
        +Int id
        +String firstName
        +String lastName
        +Role role
        +String email
        +String passwordHash

    }
    class Story {
        +Int id
        +String name
        +Int ethnicGroupId
        +Int textStoryId
        +Int imgStoryId

    }
    class TextStory {
        +Int id
        +String text

    }
    class ImgStory {
        +Int id
        +String url
        +String altText

    }
    class EthnicGroup {
        +Int id
        +String name
        +Int languageId

    }
    class Language {
        +Int id
        +String name
    }

    class ConstituentsRF {
        +Int id
        +String name
        +String type

    }

    %% --- Пользовательский контент и аудио ---
    class UserAudioStory {
        +Int id
        +String nameByUser
        +String? originalName
        +Int userId
        +Int languageId
        +String pathAudioFile
    }

    class StoryAudio {
        +Int id
        +Int storyId
        +Int languageId
        +Int userAudioStoryId
        +Float averageRating
        +Boolean isPublished

    }
    class RatingAudio {
        +Int id
        +Int userId
        +Int storyAudioId
        +Float ratingValue (1-5)
        +DateTime createdAt
        +DateTime? updatedAt
    }

    %% --- Запросы от пользователей ---
    class AddStoryRequest {
        +Int id
        +String storyName
        +String storyText
        +Int ethnicGroupId
        +Status status
        +String? adminComment
        +Int requestedByUserId

    }
    class StoryAudioRequest {
        +Int id
        +Int userAudioStoryId
        +Int? targetStoryId
        +TypeRequest typeRequest
        +Status status
        +String? adminComment
        +Int requestedByUserId

    }


    %% --- Определение связей ---
    User "1" -- "*" UserAudioStory : "загружает"
    User "1" -- "*" AddStoryRequest : "создает запрос на добавление сказки"
    User "1" -- "*" StoryAudioRequest : "создает запрос по аудио"
    User "1" -- "*" RatingAudio : "оставляет оценку"
    User "1" -- "*" StoryAudio : "отправляет на модерацию" (submittedByUserId)

    Story "1" -- "*" StoryAudio : "имеет озвучки"
    Story "1" -- "1" TextStory : "имеет текст"
    Story "1" -- "0..1" ImgStory : "имеет обложку"

    EthnicGroup "1" -- "*" Story : "принадлежат"
    %% EthnicGroup "1" -- "*" EthnicGroupMapPoint : "имеет точки на карте"
    EthnicGroup "1" -- "*" ConstituentsRF : "проживает в"



    %% ConstituentsRF "1" -- "*" ConstituentsRFOnEthnicGroup : "включает этногруппы"
        Language "1" -- "*" EthnicGroup : "является языком для"
    Language "1" -- "*" StoryAudio : "используется в аудио (сказки)"


    UserAudioStory "1" -- "0..1" StoryAudio : "может стать основой для"
    UserAudioStory "1" -- "*" StoryAudioRequest : "утверждается"

    StoryAudio "1" -- "*" RatingAudio : "получает оценки"
```

---

# GeoJSON

<div class="quote">
Данные GeoJSON — это представление точек на поверхности земного эллипсоида через угловые координаты долготы и широты в градусах. Эти координаты задают положение точки на эллипсоиде, который является стандартом WGS84 для глобальной геодезической системы координат.
</div>

---

# Преобразование GeoJSON в SVG формат

$$\text{SVG} = G(P(\text{GeoJSON})) = G \circ P (\text{GeoJSON})$$

где:
- $P$ - отображение из сферических координат на эллипсоиде Земли в плоские декартовы координаты.
  $$P: (\lambda, \varphi) \mapsto (x, y)$$
- $G$ - генератор строки SVG-пути из спроецированных координат.

$$G: \{(x_1, y_1), (x_2, y_2), ..., (x_n, y_n)\} \mapsto \text{stringPath}$$

$$\text{stringPath} = "M x_1 y_1 L x_2 y_2 ... L x_n y_n Z"$$
- $"M x y"$ — команда $\text{"moveto"}$ (перемещение к точке $(x,y)$)
- $"L x y"$ — команда $\text{P"lineto"}$ (рисование линии к точке $(x,y)$)
- $"Z"$ — команда $\text{"closepath"}$ (замыкание пути к начальной точке)

---

Для отображения карты в формате GeoJSON на двумерную плоскость $SVG$, каждая географическая координата  отображается в двумерную декартову систему координат $(x, y)$ через проекцию:

 $$(x, y) = P(\lambda, \varphi)$$

 где: 
 - $\lambda$ - долгота
 - $\phi$ - широта
 - $P$ - проекция($Mercator$, $Albers$, $Ortographic$ и д.р)

$$x = R \cdot (\lambda - \lambda_0)$$
$$y = R \cdot \ln\left(\tan\left(\frac{\pi}{4} + \frac{\varphi}{2}\right)\right)$$

---

Преобразования карты из GeoJSON формата в SVG строку происходит путём композиции функций:

```mermaid
flowchart LR
A["GeoJSON координаты (широта, долгота)"]
B["Географическая проекция P"]
C["Генерация SVG-пути G"]
D["SVG-графика"]
A --> B
B --> C
C --> D
```

---

# ERD диаграмма базы данных веб-приложения
![](./fairy-map-erd.svg)

---

# Проектирование REST API 

![](./api-auth.svg)

---

![](./images/api-conent.svg)

---

![](./images/api-ethnic.svg)



```mermaid
graph TB    
    subgraph "Управление контентом"
        S[Контроллер сказок] --> S1[CRUD операции со сказками]
        S --> S2[Управление аудиофайлами]
        S --> S3[Поиск и фильтры]
        S --> S4[Система рейтингов]
        
        Admin[Административная панель] --> Admin1[Управление сказками]
        Admin --> Admin2[Модерация аудио]
        Admin --> Admin3[Загрузка изображений]
    end
```

```mermaid
graph TB    
    subgraph "Географические и этнические данные"
        C[Контроллер регионов] --> C1[Управление субъектами РФ]
        E[Контроллер этнических групп] --> E1[Этнические группы]
        E --> E2[Языки народов]
        M[Контроллер карты] --> M1[Данные для карты]
        M --> M2[Географические точки]
    end
    
    subgraph "Модерация и заявки"
        ASR[Заявки на озвучки] --> ASR1[Модерация аудио]
        ASSR[Заявки на добавление сказок] --> ASSR1[Обработка заявок]
        R[Контроллер статусов] --> R1[Статусы и типы заявок]
    end
```

