# XLSum Summarization with T5-small

Дообучение T5-small (60M) на 50k новостных статей (XLSum) для генерации кратких summary. ROUGE-1: 0.29, ROUGE-L: 0.23 на test. Включает анализ успехов и провалов по метрике.

## Данные

XLSum (English), 50 000 статей. Вход: `text` (статья), выход: `summary` (краткое содержание). Разделение: 45k train / 5k val / 1000 test.

## Подход

1. T5-small (60M), префикс `summarize: ` для входа.
2. `max_length=728` для статьи, `max_length=128` для summary.
3. 2 эпохи, batch_size=8, lr=2e-4, fp16.
4. Генерация с `num_beams=4`, `early_stopping=True`.

## Результаты

| Метрика | val | test |
|---|---|---|
| ROUGE-1 | 0.2706 | 0.2938 |
| ROUGE-2 | 0.0758 | 0.0847 |
| ROUGE-L | 0.2106 | 0.2316 |


## Интерпретация (по метрике ROUGE-L)

### Провал (ROUGE-L = 0.00)
**Статья:** Свадьба принца Гарри.
**Reference:** From Charles taking Doria's hand, to "thank you Pa", here are some moments to remember...
**Generated:** The Prince of Wales has been a tumultuous week for the Markles

### Успех (ROUGE-L = 0.68)
**Статья:** Аресты после убийства в Уэст-Йоркшире.
**Reference:** Three more men have been arrested on suspicion of murdering a man who was stabbed to death in a fight in West Yorkshire.
**Generated:** Four men have been arrested on suspicion of murder after a man was stabbed in

**Вывод:** Модель хорошо справляется с простыми новостями (аресты, награды), но проваливается на сложных (история, эмоции). При этом высокий ROUGE не гарантирует правильность: в одном из «успехов» (ROUGE 0.66) модель перепутала имя (Bowie вместо Blake), а ROUGE этого не заметил. Это ограничение метрики, а не только модели.

## Ограничения

- T5-small (60M) не хватает для идеальных summary.
- Обрезка статьи на 728 токенах теряет конец текста.
- Модель допускает фактические ошибки (путает имена, числа, время).

## Как запустить

```bash
git clone https://github.com/MaksimPryakhin/xlsum-t5-summarization.git
cd xlsum-t5-summarization
pip install -r requirements.txt
jupyter notebook notebooks/pet-T5.ipynb
