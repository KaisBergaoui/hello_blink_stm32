# STM32 FreeRTOS Dual-Task Example

![alt text](https://github.com/KaisBergaoui/hello_blink_stm32/blob/main/media/stm32_freertos_banner.png "STM32_freeRTOS_Banner")

A minimal STM32 project demonstrating how to run **multiple FreeRTOS tasks** on an STM32 microcontroller using **STM32CubeIDE**.

Just like the ESP32 version, this example combines the two classic beginner steps in embedded development:

* **Blink an LED**
* **Print “Hello World”**

…but instead of running them one after the other, they run **simultaneously**, each inside its own FreeRTOS task.

---

## 🧩 Project Overview

This project defines two simple, independent tasks:

### **1. LED Blink Task**

* Toggles the onboard LED (usually **LD2 on PA5** for Nucleo boards) every 500ms
* Uses HAL GPIO functions
* Demonstrates periodic RTOS task execution with `osDelay();`

### **2. Hello World Task**

* Sends `"Hello World"` through BSP VCOM every 800ms
* Runs independently from the LED task, scheduled by FreeRTOS

Both tasks are created in `main.c` using `MX_FREERTOS_Init();` and run under the FreeRTOS scheduler started using `osKernelStart();`

---

## 📦 Requirements

* **STM32 Nucleo or Discovery Board** (e.g., F401RE, L476RG, etc.) - In this example I am using the U385RG-Q
* **STM32CubeIDE** (any recent version)
* ST-Link driver installed
* USB cable for power & programming

---

## 🚀 How to Build & Flash using the STM32NUCLEO-U385RG-Q

```text
1. Open STM32CubeIDE
2. File → Import → Existing STM32 Project
3. Select this folder
4. Build the project
5. Run or Debug on your STM32 board
6. Open a serial terminal at the configured UART baud rate = 115200
```

## 🚀 Pinout Viwe

![alt text](https://github.com/KaisBergaoui/hello_blink_stm32/blob/main/media/pinout_view.jpg "STM32_freeRTOS_Banner")

---

## 🧠 Core Concepts Demonstrated

### ✔️ FreeRTOS Task Management

Creating two tasks with different responsibilities and timing.

### ✔️ HAL GPIO Control

Toggling the onboard LED via:

```c
HAL_GPIO_WritePin(GPIOA, GPIO_PIN_5, GPIO_PIN_SET);
HAL_GPIO_WritePin(GPIOA, GPIO_PIN_5, GPIO_PIN_RESET);

```

### ✔️ UART Output in FreeRTOS using BSP_COM

Using BSP VCOM (UART) inside a task to send strings.

### ✔️ Timing with `osDelay();`

Non-blocking delays allow both tasks to run freely.

### ✔️ STM32 + FreeRTOS Integration

CubeMX-generated initialization + user-defined tasks.

---

## 📝 Code snippet (app_freertos.c excerpt)

```c
void StartTask01(void *argument)
{
  /* Infinite loop */
  for(;;)
  {
	HAL_GPIO_WritePin(GPIOA, GPIO_PIN_5, GPIO_PIN_SET);
	printf("Task01 - led ON\n\r"
			"");
    osDelay(500);
    HAL_GPIO_WritePin(GPIOA, GPIO_PIN_5, GPIO_PIN_RESET);
    printf("Task01 - led OFF\n\r");
    osDelay(500);
  }
}

void StartTask02(void *argument)
{
  /* Infinite loop */
  for(;;)
  {
	printf("Task02 - Hello World\n\r");
    osDelay(800);
  }
}

int main(void)
{
    HAL_Init();
    SystemClock_Config();
    MX_GPIO_Init();
    MX_USART2_UART_Init();
    MX_FREERTOS_Init();

    // Create Tasks
    xTaskCreate(StartBlinkTask, "BlinkTask", 128, NULL, 1, NULL);
    xTaskCreate(StartHelloTask, "HelloTask", 128, NULL, 1, NULL);

    vTaskStartScheduler();

    while (1) {}
}
```

## 📝 Code snippet (main.c excerpt)

```c
int main(void)
{
  HAL_Init();
  SystemClock_Config();
  MX_GPIO_Init();
  osKernelInitialize();
  MX_FREERTOS_Init();

  /* Initialize COM1 port (115200, 8 bits (7-bit data + 1 stop bit), no parity */
  BspCOMInit.BaudRate   = 115200;
  BspCOMInit.WordLength = COM_WORDLENGTH_8B;
  BspCOMInit.StopBits   = COM_STOPBITS_1;
  BspCOMInit.Parity     = COM_PARITY_NONE;
  BspCOMInit.HwFlowCtl  = COM_HWCONTROL_NONE;
  if (BSP_COM_Init(COM1, &BspCOMInit) != BSP_ERROR_NONE)
  {
    Error_Handler();
  }

  /* Start scheduler */
  osKernelStart();

  /* Infinite loop */
  while (1)
  {

  }
}
```

---

## 📘 Terminal Output

![alt text](https://github.com/KaisBergaoui/hello_blink_stm32/blob/main/media/stm32_freeRTOS_terminal_output.gif "Terminal Output")

---

---

## 📘 Notes

* BSP VCONM & UART configuration may vary depending on your board.
* Stack sizes may need adjusting depending on CubeIDE defaults.
* If using a custom board, verify the LED pin mapping.
* FreeRTOS requires proper interrupt priority configuration—CubeMX handles this automatically.

---

## 🧪 Future Extensions

A few things you can add to build on this example:

* Button interrupt task that sends a message over BSP_COM & UART
* Use FreeRTOS queues for inter-task communication
* Run tasks on different priorities to test scheduling behavior

---

## 📄 License

This project is licensed under the **MIT License**.
See the `LICENSE` file for details.