#  Smokers Classification

> Определение курильщиков по медицинским показателям с помощью CatBoost  
> **Kaggle Leopard Challenge | Place: 15 | Score: 0.44613**  
> **F1-score: 0.4375 | ROC-AUC: 0.7187 | Recall: 57% | 19 признаков + 5-Fold CV**

[![Kaggle](https://img.shields.io/badge/Kaggle-Competition%2015th%20Place-20BEFF?style=flat&logo=kaggle)](https://www.kaggle.com/competitions/leopard-challenge-classification/leaderboard)
[![Score](https://img.shields.io/badge/Kaggle%20Score-0.44613-3DDC84?style=flat)]()

## 📌 Обзор

Проект разработан для **определения статуса курения** человека на основе его медицинских и биохимических показателей в рамках **Kaggle соревнования "Leopard Challenge"**.

Используемая модель **CatBoost** — градиентный бустинг, специализируется на работе с категориальными признаками и несбалансированными данными.

**Ключевой результат:** Модель находит **57% курильщиков** с приемлемым уровнем точности (F1=0.44), что соответсвует **15-му месту** в Kaggle соревновании.

**Планы по улучшению** использовать другие модели обучения и ансамбли для улучшения целевой метрики


- 🎯 **Kaggle Score:** 0.44613 (F1-score) Место: 15 
- 📊 **Датасет:** [Медицинские показатели для классификации курильщиков](https://drive.google.com/file/d/1XQ7LqTk9uP4lbu4wR9w0KnKF8qvv2XsE/view?usp=sharing)
- 🔗 **Leaderboard:** [Kaggle Competition](https://www.kaggle.com/competitions/leopard-challenge-classification/leaderboard)


##  Задача

**Бинарная классификация:**
-  **Класс 1:** Человек курит
-  **Класс 0:** Человек не курит

**Данные:** 26 медицинских признаков + целевая переменная  
**Дисбаланс:** ~80% некурящих vs ~20% курящих  
**Размер:** ~3500 обучающих + ~1500 тестовых образцов

##  Методология

### 1. **EDA: Анализ дисбаланса**
- 80% некурящих vs 20% курящих → требует Stratified K-Fold
- Решение: `auto_class_weights='Balanced'` в CatBoost

### 2. **Feature Engineering (19 признаков)**

```python
# Индексы
bmi = weight / (height/100)²
waist_height_ratio = waist / height

# Кардиоваскулярные
pulse_pressure = systolic - relaxation

# Липиды (атеросклероз)
ldl_hdl_ratio = LDL / HDL
trig_hdl_ratio = triglyceride / HDL
atherogenic_index = (cholesterol - HDL) / HDL

# Печень (окислительный стресс)
ast_alt_ratio = AST / ALT
log_gtp = log(GTP + 1)

# Взаимодействия
age_bmi_interaction = age × BMI
age_gtp_interaction = age × GTP
```

### 3. **CatBoost: Оптимальные гиперпараметры**

```python
{
    "iterations": 2500,
    "learning_rate": 0.0075,        # Низкая = лучше обобщение
    "depth": 7,
    "l2_leaf_reg": 5.0,             # Регуляризация
    "auto_class_weights": "Balanced",# Критично для дисбаланса
    "eval_metric": "AUC",
    "early_stopping_rounds": 200,
    "random_state": 42
}
```

### 4. **Оптимизация порога: +3.2% улучшение** 

```python
# Стандартный: 0.5 → F1 = 0.4182
# Оптимальный: 0.491 → F1 = 0.4487
```

**Метод:** `precision_recall_curve` для поиска максимума F1-score

### 5. **Валидация: 5-Fold Stratified CV**

```
Mean F1: 0.4345 ± 0.013
Median Threshold: 0.491
ROC-AUC: 0.7099
```

---

## 📈 Финальные метрики

| Метрика | Значение |
|---------|----------|
| F1-score | 0.4375 |
| Accuracy | 0.70 |
| Precision | 0.35 |
| Recall | 0.57 |
| ROC-AUC | 0.72 |


---

###  Топ-10 признаков

| Признак | Важность | 
|---------|----------|
| hemoglobin | 8.8% | 
| triglyceride | 7.2% | 
| age_bmi_interaction | 7.0% |
| gtp | 6.8% | 
| age | 6.7% |
| log_gtp | 6.7% | 
| ldl | 6.5% | 
| systolic | 6.4% |
| alt | 6.3% |
| fasting_blood_sugar |

---

##  Эволюция модели

```
v1: Baseline (14 признаков)        → F1 = 0.1279 
v2: + Balanced weights              → F1 = 0.3525
v3: + Threshold optimization        → F1 = 0.4487  
v4: + Feature Engineering (19)      → F1 = 0.4494 
v5: + 5-Fold CV                     → F1 = 0.4345 
v6: ФИНАЛЬНАЯ (Kaggle)              → Score = 0.44613  
```

**Инсайт:** Оптимизация порога дала **+3.2%** — больше, чем все FE!

---

## 📁 Структура

```
ds-smokers-research/
├── data/
│   ├── train.csv
│   └── test.csv
├── ipynb/
│   └── smokers-eda-catboost.ipynb
├── models/
│   └── catboost_smokers_final.cbm
├── output/
│   ├── feature_importance.png
│   ├── roc_curve.png
│   └── kaggle_submission.csv
├── README.md
└── requirements.txt
```

---

##  Быстрый старт

### Вариант 1: Локально (быстро)

```bash
# Клон
git clone https://github.com/vulcan4ik/ds-smokers-research.git
cd ds-smokers-research

# Установка
pip install -r requirements.txt

# Запуск Jupyter
jupyter lab ipynb/smokers-eda-catboost.ipynb
```

### Вариант 2: Docker (рекомендуется)

```bash
# Клон моего проекта
git https://github.com/vulcan4ik/ds-smokers-research.git
cd data-science-project-template

# Запуск контейнеров
docker-compose up

# Откройте в браузере: http://localhost:8888
# Переходите в папку: data/smokers/ipynb/
```

## ✅ Ключевые техники

1. **Stratified K-Fold** → честная оценка
2. **auto_class_weights='Balanced'** → борьба с дисбалансом
3. **Оптимизация порога** → +3.2% F1
4. **Feature Engineering** → 19 новых признаков
5. **Низкое значение learning_rate** → лучше обобщение

---

##  Бизнес-результат

- **Recall 57%** ✅ Находим большинство курильщиков
- **Precision 35%** ⚠️ Много ложных срабатываний, но приемлемо для скрининга
- **ROC-AUC 0.72** ✅ Хорошая дискриминация классов

---

##  Дальнейшие улучшения

- [ ] Ансамбль: XGBoost + LightGBM + CatBoost
- [ ] SHAP анализ для интерпретируемости
- [ ] Optuna для гиперпараметров
- [ ] Калибровка вероятностей
- [ ] SMOTE для синтеза образцов

---

##  Технологии

![Python](https://img.shields.io/badge/Python-3.11-3776AB?style=flat)
![CatBoost](https://img.shields.io/badge/CatBoost-FFCC00?style=flat)
![Pandas](https://img.shields.io/badge/Pandas-150458?style=flat)
![Scikit-learn](https://img.shields.io/badge/Scikit--learn-F7931E?style=flat)

---

<div align="center">

**Kaggle Leopard Challenge | 15-е место | F1 = 0.44613** 🏆



[🔗 Kaggle Leaderboard](https://www.kaggle.com/competitions/leopard-challenge-classification/leaderboard)


</div>
