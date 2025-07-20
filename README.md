# Lab_task_1# ОТЧЕТ ПО ЛАБОРАТОРНОЙ РАБОТЕ

### Титульный
---

## 1. ЗАДАНИЕ

Написать программу для вычисления значений функции:

f(x) = -0.0001[|sin(x) × sin(A) × exp(|100-√(x²+A²)/π|)|+1]^0.1

где:
- A = 1.34941
- x ∈ [-10, 10]
- Количество точек: 2000

Программа должна:
1. Вычислить значения функции на заданном интервале
2. Сохранить результаты в различных форматах (TXT, CSV, JSON, XML)
3. Построить график функции
4. Провести статистический анализ

## 2. ИСХОДНЫЙ КОД ПРОГРАММЫ

```python
import numpy as np
import matplotlib.pyplot as plt
import os
import json
import csv
from scipy.signal import find_peaks


def calculate_function(x, A=1.34941):
    """
    Вычисляем значение функции f(x) = -0.0001[|sin(x) * sin(A) * exp(|100-√(x²+A²)/π|)|+1]^0.1

    Parameters:
        x: значение аргумента
        A: константа (по умолчанию 1.34941)

    Returns:
        значение функции f(x)
    """
    # Вычисляем √(x²+A²)
    sqrt_term = np.sqrt(x ** 2 + A ** 2)

    # Вычисляем |100 - √(x²+A²)/π|
    abs_term = np.abs(100 - sqrt_term / np.pi)

    # Вычисляем sin(x) * sin(A) * exp(|100-√(x²+A²)/π|)
    sin_exp_term = np.sin(x) * np.sin(A) * np.exp(abs_term)

    # Вычисляем |sin(x)sin(A)exp(|100-√(x²+A²)/π|)|
    abs_sin_exp = np.abs(sin_exp_term)

    # Вычисляем финальное значение функции
    result = -0.0001 * ((abs_sin_exp + 1) ** 0.1)

    return result


def create_results_directory():
    """Создаем директорию results, если она не существует"""
    results_dir = "results"
    if not os.path.exists(results_dir):
        os.makedirs(results_dir)
        print(f"Создана директория: {results_dir}")
    return results_dir


def save_txt_format(x_values, y_values, results_dir):
    """Сохраняем данные в текстовом формате (два столбца, разделенные 4 пробелами)"""
    filename = os.path.join(results_dir, "input.txt")
    with open(filename, 'w', encoding='utf-8') as f:
        for x, y in zip(x_values, y_values):
            f.write(f"{x:.6f}    {y:.10f}\n")
    print(f"Данные сохранены в файл: {filename}")


def save_csv_format(x_values, y_values, results_dir):
    """Сохраняет данные в CSV формате (номер строки, x, y)"""
    filename = os.path.join(results_dir, "input.csv")
    with open(filename, 'w', newline='', encoding='utf-8') as f:
        writer = csv.writer(f)
        for i, (x, y) in enumerate(zip(x_values, y_values), 1):
            writer.writerow([i, f"{x:.6f}", f"{y:.10f}"])
    print(f"Данные сохранены в файл: {filename}")


def save_json_format_arrays(x_values, y_values, results_dir):
    """Сохраняем данные в JSON формате: {"x": [...], "y": [...]}"""
    filename = os.path.join(results_dir, "input.json")
    data = {
        "x": [float(f"{x:.6f}") for x in x_values],
        "y": [float(f"{y:.10f}") for y in y_values]
    }
    with open(filename, 'w', encoding='utf-8') as f:
        json.dump(data, f, indent=2, ensure_ascii=False)
    print(f"Данные сохранены в файл: {filename}")


def save_json_format_objects(x_values, y_values, results_dir):
    """Сохраняем данные в JSON формате: {"data": [{"x": x1, "y": y1}, ...]}"""
    filename = os.path.join(results_dir, "input.json")
    data = {
        "data": [
            {"x": float(f"{x:.6f}"), "y": float(f"{y:.10f}")}
            for x, y in zip(x_values, y_values)
        ]
    }
    with open(filename, 'w', encoding='utf-8') as f:
        json.dump(data, f, indent=2, ensure_ascii=False)
    print(f"Данные сохранены в файл: {filename}")


def save_xml_format_separated(x_values, y_values, results_dir):
    """Сохраняем данные в XML формате: отдельные секции для x и y"""
    filename = os.path.join(results_dir, "input.xml")

    with open(filename, 'w', encoding='utf-8') as f:
        f.write('<?xml version="1.1" encoding="UTF-8" ?>\n')
        f.write('<data>\n')

        # Блок xdata с количеством точек
        f.write('  <xdata>\n')
        f.write(f'    {len(x_values)}\n')
        for x in x_values:
            f.write(f'    <x>{x:.6f}</x>\n')
        f.write('  </xdata>\n')

        # Блок ydata
        f.write('  <ydata>\n')
        for y in y_values:
            f.write(f'    <y>{y:.10f}</y>\n')
        f.write('  </ydata>\n')

        f.write('</data>\n')

    print(f"Данные сохранены в файл: {filename}")


def save_xml_format_rows(x_values, y_values, results_dir):
    """Сохраняем данные в XML формате: строки с парами x, y"""
    filename = os.path.join(results_dir, "input.xml")

    with open(filename, 'w', encoding='utf-8') as f:
        f.write('<?xml version="1.1" encoding="UTF-8" ?>\n')
        f.write('<data>\n')

        for x, y in zip(x_values, y_values):
            f.write('  <row>\n')
            f.write(f'    <x>{x:.6f}</x>\n')
            f.write(f'    <y>{y:.10f}</y>\n')
            f.write('  </row>\n')

        f.write('</data>\n')

    print(f"Данные сохранены в файл: {filename}")


def save_all_formats(x_values, y_values, results_dir):
    """Сохраняем данные во всех требуемых форматах"""
    save_txt_format(x_values, y_values, results_dir)
    save_csv_format(x_values, y_values, results_dir)
    save_json_format_arrays(x_values, y_values, results_dir)
    save_json_format_objects(x_values, y_values, results_dir)
    save_xml_format_separated(x_values, y_values, results_dir)
    save_xml_format_rows(x_values, y_values, results_dir)


def plot_function(x_values, y_values, results_dir):
    """Строим график функции"""
    plt.figure(figsize=(12, 8))
    plt.plot(x_values, y_values, 'b-', linewidth=1.5, label='f(x)')
    plt.grid(True, alpha=0.3)
    plt.xlabel('x', fontsize=12)
    plt.ylabel('f(x)', fontsize=12)
    plt.title('График функции f(x) = -0.0001[|sin(x) * sin(A) * exp(|100-√(x²+A²)/π|)|+1]^0.1',
              fontsize=14, pad=20)
    plt.legend(fontsize=12)

    # Добавляем сетку
    plt.grid(True, which='major', alpha=0.5)
    plt.grid(True, which='minor', alpha=0.3)
    plt.minorticks_on()

    # Сохраняем график
    plot_filename = os.path.join(results_dir, "function_plot.png")
    plt.savefig(plot_filename, dpi=300, bbox_inches='tight')
    plt.show()
    print(f"График сохранен в файл: {plot_filename}")


def analyze_function(x_values, y_values):
    """Анализируем функцию и выводит статистику"""
    print("Статистика функции:")
    print(f"Минимальное значение f(x): {np.min(y_values):.10f}")
    print(f"Максимальное значение f(x): {np.max(y_values):.10f}")
    print(f"Среднее значение f(x): {np.mean(y_values):.10f}")
    print(f"Стандартное отклонение: {np.std(y_values):.10f}")

    # Поиск экстремумов
    try:
        peaks_max, _ = find_peaks(y_values)
        peaks_min, _ = find_peaks(-y_values)

        print(f"Найдено локальных максимумов: {len(peaks_max)}")
        print(f"Найдено локальных минимумов: {len(peaks_min)}")

        if len(peaks_max) > 0:
            print("Первые 5 локальных максимумов:")
            for i in range(min(5, len(peaks_max))):
                idx = peaks_max[i]
                print(f"  x = {x_values[idx]:.6f}, f(x) = {y_values[idx]:.10f}")

        if len(peaks_min) > 0:
            print("Первые 5 локальных минимумов:")
            for i in range(min(5, len(peaks_min))):
                idx = peaks_min[i]
                print(f"  x = {x_values[idx]:.6f}, f(x) = {y_values[idx]:.10f}")
    except ImportError:
        print("Для анализа экстремумов требуется библиотека scipy")


def main(direct=None):
    """Основная функция программы"""
    # Параметры
    A = 1.34941
    x_min, x_max = -10, 10

    # Выбираем достаточно малый шаг для гладкого графика
    num_points = 2000  # Увеличиваем количество точек для большей гладкости
    x_values = np.linspace(x_min, x_max, num_points)

    print(f"Расчет функции на интервале [{x_min}, {x_max}]")
    print(f"Константа A = {A}")
    print(f"Количество точек: {len(x_values)}")
    print(f"Шаг дискретизации: {(x_max - x_min) / (num_points - 1):.6f}")

    # Вычисляем значения функции
    y_values = np.array([calculate_function(x, A) for x in x_values])

    # Создаем директорию для результатов
    results_dir = create_results_directory() if not direct else direct

    # Сохраняем результаты во всех форматах
    save_all_formats(x_values, y_values, results_dir)

    # Строим график
    plot_function(x_values, y_values, results_dir)

    # Анализируем функцию
    analyze_function(x_values, y_values)


if __name__ == "__main__":
    main()
```

## 3. ССЫЛКА НА GIT-РЕПОЗИТОРИЙ

**Ссылка:**  [(https://github.com/Edera2006/Lab_task_1)] 



## 4. СКРИНШОТ GIT-РЕПОЗИТОРИЯ


## 5. ГРАФИК ФУНКЦИИ


<img width="1280" height="914" alt="image" src="https://github.com/user-attachments/assets/fd77e4b1-540c-4d67-a885-4edbcd87e223" />

График показывает поведение функции f(x) на интервале [-10, 10]. Функция имеет сложный колебательный характер с быстро изменяющимися значениями.

## 6. ПЕРВЫЕ 20 СТРОК ФАЙЛА РЕЗУЛЬТАТА

### Файл input.txt:
```
-10.000000    -1.4994781540
-9.989995    -1.4976130580
-9.979990    -1.4956980737
-9.969985    -1.4937317084
-9.959980    -1.4917123885
-9.949975    -1.4896384540
-9.939970    -1.4875081518
-9.929965    -1.4853196285
-9.919960    -1.4830709230
-9.909955    -1.4807599577
-9.899950    -1.4783845289
-9.889945    -1.4759422969
-9.879940    -1.4734307739
-9.869935    -1.4708473115
-9.859930    -1.4681890865
-9.849925    -1.4654530853
-9.839920    -1.4626360857
-9.829915    -1.4597346377
-9.819910    -1.4567450408
-9.809905    -1.4536633196
```

## 7. АНАЛИЗ РЕЗУЛЬТАТОВ

Программа успешно выполнила все поставленные задачи:

1. **Вычисление функции:** Реализована функция для вычисления значений по заданной формуле
2. **Сохранение данных:** Результаты сохранены в форматах TXT, CSV, JSON и XML
3. **Визуализация:** Построен график функции с сеткой и подписями
4. **Анализ:** Выполнен статистический анализ с поиском экстремумов


## ЗАКЛЮЧЕНИЕ

Задание выполнено в полном объеме. Программа корректно вычисляет значения заданной функции, сохраняет результаты в требуемых форматах и предоставляет визуализацию и анализ данных.


