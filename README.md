**Проекты Keil uVision 5**

**МК: STM32F107VC (отладочная плата STM3210C-EVAL)**

# Лабораторная работа №0 (Lab 0 - ASM)
## Введение в Assembler Cortex-M3

Проект "Hello World!" на ассемблере, реализует мигание светодиодом на отладочной плате.

# Лабораторная работа №1 (Lab 1 - Hello World)
## Hello World!

Проект "Hello World!", реализует мигание светодиодами на отладочной плате.

В папке `StdPeriphLib Version` находится версия исходников с использованием библиотеки **StdPeriphLib**.

# Лабораторная работа №2 (Lab 2 - Interrupts)
## Введение в прерывания

Реализовано два обработчика прерываний:
- по переполненю таймера **TIM6** (раз в секунду меняется состояние светодиода);
- по внешнему импульсу на линии **EXTI9** (к **PB9** подключена кнопка, по нажатию меняется состояние светодиода).

# Лабораторная работа №3 (Lab 3 - UART+DMA)
## Модуль USART, DMA

Демонстрирует работу с модулем USART и два подхода к передаче данных:
- через процессор (`#define USE_DMA` должен быть закомментирован);
- через модуль DMA (`#define USE_DMA` должен быть раскомментирован).

Параметры UART:
- Скорость обмена: 115200 бод;
- 8 бит данных;
- 1 стоп-бит.

В папке `Waveforms` лежат осциллограммы для двух варинтов передачи данных.
- [USART without DMA.png](Lab%203%20-%20UART%2BDMA/Waveforms/USART%20without%20DMA.png) - передача по USART осуществляется через процессор.
- [USART with DMA.png](Lab%203%20-%20UART%2BDMA/Waveforms/USART%20with%20DMA.png) - передача по USART осуществляется через модуль DMA.

На двух картинках:
- **Канал А (синий)** - сигнал на выводе **PD7** (светодиод **LED1**).
- **Канал B (красный)** - сигнал на выводе **PD5** (USART2 TX Remapped).

# Лабораторная работа №4 (Lab 4 - ADC)
## Работа с модулем АЦП

Реализована работа с регулярной группой каналов АЦП ADC1:
- **ADC1_IN14** - подключен к потенциометру.
- **ADC1_IN16** - внутренний канал, подключен к встроенному термодатчику.

Реализован простейший обработчик SCPI-подобных команд:
- __*IDN?__ - считать идентификатор устройства. В ответ МК вернёт строку `Lab 4 - ADC`.
- **LED1 ON|OFF** - включить/выключить светодиод **LED1**. В случае успешного выполнения команды МК вернёт строку `OK`. В случае неверного параметра (если это не `ON` или `OFF`) - `Invalid Parameter`.
- **ADC?** - измерить напряжение на входе **ADC1_IN14**. В ответ МК вернёт значение напряжения в Вольтах.
- **TEMPER?** - измерить температуру микроконтроллера. В ответ МК вернёт значение температуры в градусах Цельсия.

В случае получения неизвестной команды МК вернёт сообщение об ошибке `Invalid Command`.

# Лабораторная работа №5 (Lab 5 - DAC)
## Работа с модулем ЦАП

Реализованы режимы работы ЦАП:
- установка постоянного напряжения;
- генерирование синусоидального сигнала (с помощью модуля DMA);
- генерирование шума;
- генерирование треугольного сигнала.

Структура папок:
- **WaveGenerator** - содержит скрипты генерирования массива значений для синусоиды:
  - `DAC_Sine - Swift.playground` - playground для Xcode (язык Swift);
  - `DAC_Sine - MATLAB.m` - скрипт Matlab. Генерирует только числовые значения.
- **Waweforms** - содержит осциллограммы с выхода ЦАП:
  - [Command U 1.5.png](Lab%205%20-%20DAC/Waveforms/Command%20U%201.5.png) - постоянное напряжение 1.5В (команда `U 1.5`).
  - [Command SIN 200.png](Lab%205%20-%20DAC/Waveforms/Command%20SIN%20200.png) - синусоида, *TIM6->ARR = 200* (команда `SIN 200`).
  - [Command SIN 1500.png](Lab%205%20-%20DAC/Waveforms/Command%20SIN%201500.png) - синусоида, *TIM6->ARR = 1500* (команда `SIN 1500`).
  - [Command TRIANGLE 1.png](Lab%205%20-%20DAC/Waveforms/Command%20TRIANGLE%201.png) - треугольный сигнал, *TIM6->ARR = 1* (команда `TRIANGLE 1`).
  - [Command TRIANGLE 5.png](Lab%205%20-%20DAC/Waveforms/Command%20TRIANGLE%205.png) - треугольный сигнал, *TIM6->ARR = 5* (команда `TRIANGLE 5`).
- **Project** - сам проект программы.

Система команд по интерфейсу UART:
- __*IDN?__ - считать идентификатор устройства. В ответ МК вернёт строку `Lab 5 - DAC`.
- **U [val]** - установить постоянное напряжение на выходе ЦАП. `[val]` - значение напряжения в Вольтах, может находиться в диапазоне от 0.00 до 3.30В. В случае успешного выполнения команды МК вернёт ответ `OK`. Если значение `[val]` выходит за допустимый диапазон - `Out of Range`.
- **SIN [val]** - генерировать синусоидальный сигнал на выходе ЦАП. `[val]` - модуль счёта таймера TIM6, по которому генерируется запрос DMA. Может принимать значения от 0 до 65535. В случае успешного выполнения команды МК вернёт ответ `OK`.
- **NOISE [val]** - генерировать шум на выходе ЦАП. `[val]` - модуль счёта таймера TIM6, по которому запускается ЦАП. В случае успешного выполнения команды МК вернёт ответ `OK`.
- **TRIANGLE [val]** - генерировать сигнал треугольной формы на выходе ЦАП. `[val]` - модуль счёта таймера TIM6, по которому запускается ЦАП. В случае успешного выполнения команды МК вернёт ответ `OK`.

# Лабораторная работа №6 (Lab 6 - FreeRTOS)
## Введение в операционные системы реального времени

Проект сгенерирован с использованием **STM32CubeMX**.

Данный проект демонстрирует:
- Реализацию многозадачности.
- Работу с очередью.
- Работу с семафорами.

#### Очереди:
- `PressedBtn` - содержит номера нажатых кнопок (`1` для **UserKey** и `2` для **Tamper**).

#### Семафоры:
- `WakeUpKeyPressed` - счётный семафор, инкрементируется с каждым нажатием кнопки **Wakeup**.

#### Задачи:
- `DefaultTask` - просто мигает светодиодом **LED4**.
- `ScanUserKeys` - в цикле считывает состояние кнопок **UserKey** и **Tamper**, передаёт номер нажатой кнопки в очередь `PressedBtn`:
  - `1` - нажата кнопка  **UserKey**;
  - `2` - нажата кнопка  **Tamper**;
- `TaskLEDS` - принимает из очереди `PressedBtn` номер нажатой кнопки, в зависимости от этого инвертирует состояние светодиода **LED1** (если это **UserKey**) или **LED2** (если это **Tamper**).
- `ScanWakeUpKey` - выставляет семафор `WakeUpKeyPressed` в случае, если произошло нажатие на кнопку **Wakeup**.
- `LEDBlink` - ожидает семафор `WakeUpKeyPressed` и, по его установке, инвертирует состояние светодиода **LED3**.

# Лабораторная работа №7 (Lab 7 - FFT)
## Дискретное преобразование Фурье

Реализована периодическая дискретизация сигнала с частотой 100кГц с использованием связки АЦП+DMA. Алгоритм работы программы:

1. Производится заполнение первой половины буфера АЦП `ADC_Buffer` (256 из 512 элементов), после чего генерируется прерывание от DMA (`DMA_ISR_HTIF1`).
2. Производится ДПФ от первой половины буфера АЦП, в это время заполняется вторая половина буфера.
3. После заполнения всего буфера из 512 отсчётов DMA генерирует второе прерывание (`DMA_ISR_TCIF1`).
4. Производится ДПФ от второй половины буфера АЦП, в это время заполняется первая половина буфера.
5. Пункты 1-4 повторяются.

Все результаты записываются в массив `FFT_Results`. По запросу от ПК можно передать содержимое как буфера АЦП, так и результаты ДПФ.

Структура папок:
- **PC Soft** - содержит ПО для графического представления данных с АЦП и БПФ.
  - `ADC_Graph Source` - Проект Qt Creator, Qt v5.4.
  - `Lab 5-7 Prog - Windows.zip` - Скомпилированное под Windows приложение.
- **Waweforms** - содержит скриншоты программы для различных форм сигнала (временная область и спектр соответственно):
  - [Sine_1kHz_Time.png](Lab%207%20-%20FFT/Waveforms/Sine_1kHz_Time.png), [Sine_1kHz_FFT.png](Lab%207%20-%20FFT/Waveforms/Sine_1kHz_FFT.png) - Синусоида частотой 1кГц.
  - [Sine_10kHz_Time.png](Lab%207%20-%20FFT/Waveforms/Sine_10kHz_Time.png), [Sine_10kHz_FFT.png](Lab%207%20-%20FFT/Waveforms/Sine_10kHz_FFT.png) - Синусоида частотой 10кГц.
  - [Sine_90kHz_Time.png](Lab%207%20-%20FFT/Waveforms/Sine_90kHz_Time.png), [Sine_90kHz_FFT.png](Lab%207%20-%20FFT/Waveforms/Sine_90kHz_FFT.png) - Синусоида частотой 90кГц. **Обратить внимание, что частота синусоиды выше частоты Найквиста!**
  - [Meandr_10kHz_Time.png](Lab%207%20-%20FFT/Waveforms/Meandr_10kHz_Time.png), [Meandr_10kHz_FFT.png](Lab%207%20-%20FFT/Waveforms/Meandr_10kHz_FFT.png) - Меандр частотой 10кГц. Обратить внимание на гармоники.
- **Project** - сам проект программы.

Система команд по интерфейсу UART:
- __*IDN?__ - считать идентификатор устройства. В ответ МК вернёт строку `Lab 7 - FFT`.
- **ADC?** - считать массив отсчётов во временной области. В ответ вернёт 256 числовых значений в HEX-формате (ведущие нули опускаются).
- **FFT?** - считать массив отсчётов в частотной области. В ответ вернёт 256 числовых значений в HEX-формате (ведущие нули опускаются).
