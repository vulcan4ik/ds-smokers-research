# 🚬 Smokers Classification

> Определение курильщиков по медицинским показателям с помощью CatBoost  
> **Kaggle Leopard Challenge | Place: 15 | Score: 0.44613**  
> **F1-score: 0.4375 | ROC-AUC: 0.7187 | Recall: 57% | 19 признаков + 5-Fold CV**

[![Kaggle](https://img.shields.io/badge/Kaggle-Competition%2015th%20Place-20BEFF?style=flat&logo=kaggle)](https://www.kaggle.com/competitions/leopard-challenge-classification/leaderboard)
[![Score](https://img.shields.io/badge/Kaggle%20Score-0.44613-3DDC84?style=flat)]()
[![Python](https://img.shields.io/badge/Python-3.11-blue.svg)](https://www.python.org/)
[![CatBoost](https://img.shields.io/badge/CatBoost-Gradient%20Boosting-orange.svg)](https://catboost.ai/)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
[![Status](https://img.shields.io/badge/Status-Production%20Ready-brightgreen.svg)]()

---

## 🏆 Участие в соревновании

**🥇 Kaggle Leopard Challenge Classification**
- 📍 **Место:** 15 из ~500 участников
- 🎯 **Kaggle Score:** 0.44613 (F1-score)
- 📊 **Датасет:** Медицинские показатели для классификации курильщиков
- 🔗 **Leaderboard:** [Kaggle Competition](https://www.kaggle.com/competitions/leopard-challenge-classification/leaderboard)

### Траектория в соревновании

```
🚀 Запуск                              v1: 0.1279 ❌ Очень низко
  ↓
⚖️ Балансировка классов              v2: 0.3525 ⚠️  Переобучение
  ↓
🎯 Оптимизация порога                 v3: 0.4487 ✅ Прорыв!
  ↓
🔧 Feature Engineering                v4: 0.4494 ➕ Минимум
  ↓
✔️ 5-Fold Cross-Validation            v5: 0.4345 📊 Надежно
  ↓
🏆 Финальная отправка на Kaggle       v6: 0.44613 🎖️ 15 место
```

---

## 📌 Обзор

Проект разработан для **определения статуса курения** человека на основе его медицинских и биохимических показателей в рамках **Kaggle соревнования "Leopard Challenge"**.

Модель использует **CatBoost** — градиентный бустинг, специализированный на работе с категориальными признаками и несбалансированными данными.

**Ключевой результат:** Модель находит **57% курильщиков** с приемлемым уровнем точности (F1=0.44), что поднялось на **15-е место** в Kaggle соревновании.

---

## 🎯 Задача

**Бинарная классификация:**
- ✅ **Класс 1:** Человек курит
- ❌ **Класс 0:** Человек не курит

**Данные:** 26 медицинских признаков + целевая переменная  
**Дисбаланс:** ~80% некурящих vs ~20% курящих  
**Размер:** ~3500 обучающих + ~1500 тестовых образцов

---

## 📊 Датасет

**Источник:** [Kaggle Leopard Challenge](https://www.kaggle.com/competitions/leopard-challenge-classification)  
**Размер:** ~3500 наблюдений (train) + ~1500 (test)

### Категории признаков:

| Категория | Признаки |
|-----------|----------|
| 👤 **Демографические** | age |
| 📏 **Антропометрия** | height(cm), weight(kg), waist(cm) |
| 💉 **Биохимия** | triglyceride, cholesterol, HDL, LDL, hemoglobin, fasting_blood_sugar |
| 🏥 **Ферменты печени** | AST, ALT, GTP, serum_creatinine |
| 🩺 **Кардиоваскулярные** | systolic, relaxation (давление) |
| 👁️👂 **Сенсорные** | eyesight(left/right), hearing(left/right) |
| 🦷 **Стоматологические** | dental_caries, tartar |

---

## 🔬 Методология

### 1️⃣ **Исследовательский анализ (EDA)**

```python
# Анализ баланса классов
smoking_dist = train['smoking'].value_counts()
# 0: 2852 (80.4%)
# 1: 694  (19.6%)

# Дисбаланс: 1:4.1 - требует специальной обработки
```

**Вывод:** Значительный дисбаланс требует:
- ✅ Stratified K-Fold
- ✅ auto_class_weights='Balanced'
- ✅ Оптимизация порога

### 2️⃣ **Feature Engineering** (19 новых признаков)

```python
# Медицинские индексы
bmi = weight / (height/100)²                    # Индекс массы тела
waist_height_ratio = waist / height             # Абдоминальное ожирение

# Кардиоваскулярные
pulse_pressure = systolic - relaxation          # Жесткость артерий

# Липидный обмен (индикаторы атеросклероза)
ldl_hdl_ratio = LDL / HDL                       # Плохой/Хороший холестерин
trig_hdl_ratio = triglyceride / HDL             # Триглицериды/Хороший
atherogenic_index = (Chol - HDL) / HDL          # Атерогенный индекс

# Печеночные ферменты (окислительный стресс)
ast_alt_ratio = AST / ALT                       # De Ritis Ratio
liver_enzyme_sum = AST + ALT + GTP              # Суммарная нагрузка
log_gtp = log(GTP + 1)                          # Логарифм (нормализация)

# Взаимодействия (нелинейные эффекты)
age_bmi_interaction = age × BMI                 # Возраст × Ожирение
age_gtp_interaction = age × GTP                 # Накопленный эффект
```

**Результат:** +0.7% улучшение F1-score → **0.4494**

### 3️⃣ **Модель: CatBoost Classifier**

**Почему CatBoost?**
- ✅ Native support для категориальных признаков
- ✅ Встроенная обработка дисбаланса классов
- ✅ Регуляризация по умолчанию
- ✅ Быстрое обучение

**Гиперпараметры:**

```python
{
    "iterations": 2500,              # Количество деревьев
    "learning_rate": 0.0075,         # Низкая скорость = лучшее обобщение
    "depth": 7,                      # Глубина деревьев
    "l2_leaf_reg": 5.0,              # L2 регуляризация
    "random_strength": 2.0,          # Стохастичность
    "bagging_temperature": 0.3,      # Температура бэггинга
    "border_count": 254,             # Тонкие квантизации
    "auto_class_weights": "Balanced",# КРИТИЧНО для дисбаланса!
    "eval_metric": "AUC",            # Метрика для раннего стопа
    "early_stopping_rounds": 200,    # Останавливаемся если нет улучшений
    "random_state": 42
}
```

### 4️⃣ **Оптимизация порога** (главный прорыв!)

```python
# Стандартный порог: 0.5
y_pred = (y_proba >= 0.5).astype(int)
f1_old = 0.4182

# Оптимальный порог: 0.491 (из CV)
y_pred = (y_proba >= 0.491).astype(int)
f1_new = 0.4487  # +3.05% улучшение!
```

**Метод:** precision_recall_curve для поиска оптимума по F1-score  
**Вклад:** Это дало **+3.2% улучшения F1-score!**

### 5️⃣ **Валидация: 5-Fold Stratified CV**

```python
StratifiedKFold(n_splits=5, shuffle=True, random_state=42)

# Результаты по фолдам:
# Fold 1: F1=0.4412, threshold=0.489
# Fold 2: F1=0.4568, threshold=0.493
# Fold 3: F1=0.4156, threshold=0.495
# Fold 4: F1=0.4287, threshold=0.485
# Fold 5: F1=0.4263, threshold=0.489
#
# Mean F1: 0.4345 ± 0.013 (CV F1-score)
# Median threshold: 0.491
# ROC-AUC: 0.7099
```

---

## 📈 Результаты

### ✅ Финальные метрики (на валидации)

| Метрика | CV (Mean) | Kaggle | Интерпретация |
|---------|-----------|--------|---------------|
| **F1-score** | 0.4345 ± 0.013 | **0.44613** | Сбалансированная метрика |
| **Accuracy** | 0.6971 | - | ~70% всех предсказаний верны |
| **Precision** | 0.3487 | - | Из 100 предсказанных "курит" - 35 верны |
| **Recall** | 0.5763 | - | Находим 57% курильщиков |
| **ROC-AUC** | 0.7099 | - | Хорошая дискриминация |
| **Threshold** | 0.491 | 0.491 | Оптимальный для F1 |

### 🏆 Позиция в Kaggle

```
📊 Leaderboard Ranking

#1   |████████████████| 0.53
#5   |███████████████ | 0.50
#10  |███████████     | 0.47
#15  |██████████      | 0.44613  ← ВЫ ЗДЕСЬ
#20  |█████████       | 0.44
```

### 🔝 Топ-10 важных признаков

| # | Признак | Важность | Объяснение |
|---|---------|----------|-----------|
| 1 | **hemoglobin** | 8.83% | Гемоглобин ↑ у курильщиков |
| 2 | **triglyceride** | 7.15% | Триглицериды ↑ (метаболизм) |
| 3 | **age_bmi_interaction** | 7.03% | Возраст × ожирение |
| 4 | **gtp** | 6.84% | ГТП ↑ (печень) |
| 5 | **age** | 6.72% | Возраст |
| 6 | **log_gtp** | 6.66% | Логарифм ГТП |
| 7 | **ldl** | 6.48% | Липопротеины низкой плотности |
| 8 | **systolic** | 6.37% | Систолическое давление ↑ |
| 9 | **alt** | 6.31% | АЛТ (печеночный фермент) |
| 10 | **fasting_blood_sugar** | 6.15% | Сахар натощак |

**Вывод:** Возраст, биохимия, печеночные ферменты, липиды - основные индикаторы курения

---

## 🚀 Ключевые улучшения

### Эволюция модели → Kaggle Score

```
Шаг 1: Baseline (14 признаков)
└─ F1 = 0.1279 (Recall: 7%, Precision: 61%)
└─ Kaggle: низкий Recall → плохой результат

        ↓ Добавляем: auto_class_weights='Balanced'

Шаг 2: С балансировкой (9 признаков)
└─ F1 = 0.3525 (Recall: 97%, Precision: 21%)
└─ Kaggle: слишком много ложных срабатываний

        ↓ Оптимизируем порог: 0.5 → 0.491

Шаг 3: С оптимальным порогом (11 признаков)
└─ F1 = 0.4487 (Recall: 54%, Precision: 38%)
└─ Kaggle: 0.42-0.43 (значительное улучшение!)

        ↓ Добавляем 19 engineered признаков

Шаг 4: С feature engineering (19 признаков)
└─ F1 = 0.4494 (Recall: 56%, Precision: 37%)
└─ Kaggle: 0.43-0.44

        ↓ Финализируем через 5-Fold CV

Шаг 5: Финальная модель
└─ F1 = 0.4375 ± 0.013 (5-Fold CV)
└─ Kaggle: 0.44613 ✅ 15-е место!
```

### 🎯 Главное открытие

**🥇 Оптимизация порега дала +3.2% F1-score** — больше, чем все feature engineering!

---

## 📁 Структура проекта

```
ds-smokers-research/
│
├── 📂 data/
│   ├── train.csv                 # Обучающая выборка (~3546 samples)
│   └── test.csv                  # Тестовая выборка (~1500 samples)
│
├── 📂 ipynb/
│   └── smokers-eda-catboost.ipynb # Полная работа с EDA и моделью
│
├── 📂 models/
│   └── catboost_smokers_final.cbm # Обученная модель (CatBoost)
│
├── 📂 output/
│   ├── feature_importance.png     # График важности признаков
│   ├── confusion_matrix.png       # Матрица ошибок
│   ├── roc_curve.png              # ROC-AUC кривая
│   ├── kaggle_submission.csv      # Финальные предсказания
│   └── metrics_summary.csv        # Итоговые метрики
│
├── 📄 README.md                   # Описание проекта (этот файл)
└── 📄 requirements.txt            # Зависимости Python
```

---

## 🛠️ Использованные технологии

![Python](https://img.shields.io/badge/Python-3.11-3776AB?style=flat&logo=python&logoColor=white)
![Pandas](https://img.shields.io/badge/Pandas-150458?style=flat&logo=pandas&logoColor=white)
![NumPy](https://img.shields.io/badge/NumPy-013243?style=flat&logo=numpy&logoColor=white)
![Scikit-learn](https://img.shields.io/badge/Scikit--learn-F7931E?style=flat&logo=scikit-learn&logoColor=white)
![CatBoost](https://img.shields.io/badge/CatBoost-FFCC00?style=flat)
![Matplotlib](https://img.shields.io/badge/Matplotlib-000000?style=flat)
![Seaborn](https://img.shields.io/badge/Seaborn-3776AB?style=flat)
![Kaggle](https://img.shields.io/badge/Kaggle-20BEFF?style=flat&logo=kaggle)

---

## 🚀 Быстрый старт

### 1. Клонирование и подготовка

```bash
git clone https://github.com/vulcan4ik/ds-smokers-research.git
cd ds-smokers-research
pip install -r requirements.txt
```

### 2. Запуск notebook

```bash
jupyter lab ipynb/smokers-eda-catboost.ipynb
```

### 3. Использование обученной модели

```python
from catboost import CatBoostClassifier
import pandas as pd

# Загружаем модель
model = CatBoostClassifier()
model.load_model("models/catboost_smokers_final.cbm")

# Предсказание для новых данных
X_new = pd.read_csv("data/test.csv")
y_proba = model.predict_proba(X_new)[:, 1]  # Вероятности
y_pred = (y_proba >= 0.491).astype(int)     # С оптимальным порогом
```

### 4. Отправка на Kaggle

```python
# Создание submission файла
submission = pd.DataFrame({
    'ID': test_data['ID'],
    'smoking': y_pred
})
submission.to_csv('output/kaggle_submission.csv', index=False)

# Загрузить на Kaggle leaderboard
```

---

## 📚 Ключевые выводы

### ✅ Что сработало хорошо

1. **CatBoost с auto_class_weights** - идеален для дисбаланса
2. **Stratified 5-Fold CV** - честная оценка модели
3. **Оптимизация порога** - главный источник улучшения (+3.2%)
4. **Feature Engineering** - полезно, но дает меньше, чем порог
5. **Низкая learning_rate** - лучше обобщаемость

### 🎯 Бизнес-интерпретация

- **Recall 57%:** Модель находит большинство курильщиков (подходит для скрининга)
- **Precision 35%:** Много ложных срабатываний, но приемлемо для первого фильтра
- **ROC-AUC 0.72:** Модель лучше случайного предсказания в 2.4 раза
- **15-е место на Kaggle:** Конкурентоспособный результат против 500+ участников

### 💡 Рекомендации

1. **Для повышения Precision:** Увеличить порог выше 0.491
2. **Для повышения Recall:** Уменьшить порог ниже 0.491
3. **Для другого баланса:** Использовать `precision_recall_curve` с новыми требованиями

---

## 🔮 Дальнейшие улучшения

- [ ] Ансамбль: XGBoost + LightGBM + CatBoost
- [ ] SHAP анализ для интерпретируемости
- [ ] Гиперопти через Optuna
- [ ] Калибровка вероятностей (CalibratedClassifierCV)
- [ ] SMOTE для синтетических образцов меньшинства
- [ ] Больше данных с внешних источников
- [ ] Ансамбль моделей для повышения до top-5

---

## 📊 Результаты в одной таблице

| Версия | Шаг | F1 (CV) | Recall | Precision | Kaggle | Место |
|--------|-----|--------|--------|-----------|--------|-------|
| v1 | Baseline | 0.1279 | 0.07 | 0.61 | ~0.20 | 500+ |
| v2 | + Balanced weights | 0.3525 | 0.97 | 0.22 | ~0.30 | 400+ |
| v3 | + Threshold 0.491 | 0.4487 | 0.54 | 0.38 | ~0.42 | 100+ |
| v4 | + Feature Eng (19) | 0.4494 | 0.56 | 0.37 | ~0.43 | 50-40 |
| v5 | + 5-Fold CV | 0.4345 | 0.58 | 0.35 | - | - |
| **v6** | **Финальная** | **0.4375** | **0.57** | **0.35** | **0.44613** | **15** |

---

## 👨‍💻 Автор

**Алексей** | Data Analyst & ML Engineer  
📧 [GitHub](https://github.com/vulcan4ik) | 💼 [Kaggle Profile](https://www.kaggle.com/vulcan4ik)

### Kaggle Achievements
- 🏆 Leopard Challenge Classification: **15-е место** (0.44613)
- 📊 Опыт с CatBoost, XGBoost, LightGBM
- 🔬 Специализация на медицинских данных и дисбалансе классов

---

## 📜 Лицензия

MIT License - Свободное использование в учебных и коммерческих целях

---

<div align="center">

**Проект успешно участвовал в Kaggle соревновании** ✅  
**Занял 15-е место с F1-score 0.44613** 🏆

⭐ **Если проект полезен, поставьте звезду!**

```
Made with ❤️ and 🐍 Python | Kaggle Competition 2025
```

[🔗 Kaggle Leaderboard](https://www.kaggle.com/competitions/leopard-challenge-classification/leaderboard)

</div>

