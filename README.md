# Determining-the-handmade-number-using-PyTorch

Проект для распознавания рукописных цифр (от 0 до 9) с использованием библиотеки PyTorch и датасета MNIST.

## 📋 Содержание

- [О проекте](#о-проекте)
- [Технологии](#технологии)
- [Установка](#установка)
- [Объяснение кода](#объяснение-кода)
- [Архитектура модели](#архитектура-модели)
- [Результаты](#результаты)
- [Структура проекта](#структура-проекта)
- [Возможные улучшения](#возможные-улучшения)
- [Автор](#автор)

## 🎯 О проекте

Цель проекта — научить компьютер распознавать рукописные цифры с помощью нейронной сети. Используем датасет MNIST — популярный набор из 70,000 изображений цифр размером 28x28 пикселей.

**Что делает проект:**
- Загружает датасет MNIST
- Обучает простую нейронную сеть
- Тестирует модель на новых данных
- Показывает точность распознавания
- Визуализирует примеры цифр

## 🛠 Технологии

- **Python 3.x**
- **PyTorch** - библиотека для создания нейронных сетей
- **TorchVision** - загрузка и обработка изображений
- **NumPy** - работа с массивами и числами
- **Matplotlib** - создание графиков и визуализаций
- **tqdm** - красивый индикатор прогресса

## 📦 Установка

1. **Клонируйте репозиторий:**
```bash
git clone https://github.com/Gando4lapi/Determining-the-handmade-number-using-PyTorch
cd Determining-the-handmade-number-using-PyTorch
```

2. **Установите зависимости:**
```bash
pip install torch torchvision numpy matplotlib tqdm
```

## 📖 Объяснение кода

### 1. Загрузка данных MNIST

```python
from torchvision import datasets, transforms

# Загружаем обучающие данные
mnist_train = datasets.MNIST(root='./datasets', train=True, 
                           transform=transforms.ToTensor(), download=True)

# Загружаем тестовые данные  
mnist_test = datasets.MNIST(root='./datasets', train=False,
                          transform=transforms.ToTensor(), download=True)
```

**Что происходит:**
- Скачиваем датасет MNIST с 60,000 обучающих и 10,000 тестовых изображений
- `ToTensor()` превращает картинки в числовые массивы для PyTorch

### 2. Подготовка загрузчиков данных

```python
from torch.utils.data import DataLoader

train_loader = DataLoader(mnist_train, batch_size=100, shuffle=True)
test_loader = DataLoader(mnist_test, batch_size=100, shuffle=False)
```

**Что происходит:**
- Разбиваем данные на пачки по 100 изображений
- Перемешиваем обучающие данные для лучшего обучения
- Тестовые данные не перемешиваем

### 3. Создание модели

```python
import torch.nn as nn

class LogisticRegressionModel(nn.Module):
    def __init__(self):
        super().__init__()
        # Слой который превращает 784 пикселя в 10 цифр
        self.linear = nn.Linear(28*28, 10)

    def forward(self, x):
        # Превращаем картинку 28x28 в вектор из 784 чисел
        x = x.view(-1, 28*28)
        # Применяем линейное преобразование
        return self.linear(x)
```

**Что происходит:**
- Создаём простую модель - один слой
- Вход: 784 пикселя (28x28 картинка)
- Выход: 10 чисел (вероятности для цифр 0-9)

### 4. Настройка обучения

```python
import torch.optim as optim
import torch.nn.functional as F

model = LogisticRegressionModel()
optimizer = optim.SGD(model.parameters(), lr=0.1)
```

**Что происходит:**
- Создаём экземпляр нашей модели
- Выбираем оптимизатор SGD с шагом обучения 0.1
- Оптимизатор будет улучшать параметры модели

### 5. Процесс обучения

```python
from tqdm import tqdm

model.train()  # Переводим модель в режим обучения
for images, labels in tqdm(train_loader):
    optimizer.zero_grad()                    # Очищаем старые градиенты
    outputs = model(images)                  # Получаем предсказания
    loss = F.cross_entropy(outputs, labels)  # Считаем ошибку
    loss.backward()                          # Вычисляем градиенты
    optimizer.step()                         # Обновляем параметры
```

**Что происходит:**
1. Берём пачку изображений и правильных ответов
2. Пропускаем изображения через модель
3. Сравниваем предсказания с правильными ответами
4. Корректируем параметры модели, чтобы уменьшить ошибку

### 6. Проверка качества

```python
model.eval()  # Переводим в режим тестирования
correct = 0
total = 0

with torch.no_grad():  # Отключаем вычисление градиентов
    for images, labels in test_loader:
        outputs = model(images)
        _, predicted = torch.max(outputs, 1)  # Выбираем самый вероятный класс
        correct += (predicted == labels).sum().item()
        total += labels.size(0)

print(f'Test accuracy: {correct / total:.4f}')
```

**Что происходит:**
- Проверяем модель на тестовых данных
- Считаем сколько цифр угадали правильно
- Вычисляем точность в процентах

## 🏗 Архитектура модели

**Простыми словами:**
- Наша модель - это очень простая нейронная сеть
- Она берёт картинку 28x28 пикселей (784 числа)
- Превращает их в 10 чисел (оценки для каждой цифры 0-9)
- Самое большое число показывает, какая цифра на картинке

**Технические детали:**
- Тип: Логистическая регрессия (линейный классификатор)
- Входной размер: 784 (28×28 пикселей)
- Выходной размер: 10 (классы 0-9)
- Функция потерь: CrossEntropyLoss
- Оптимизатор: SGD с learning rate = 0.1

## 📊 Результаты

- **Точность на тестовых данных: ~91%**

## 👤 Автор

**Капник Данил**

- GitHub: [@Gando4lapi](https://github.com/Gando4lapi)
- Email: kapnik.dan@gmail.com

---

**⭐ Поставьте звездочку, если проект был полезен!**
