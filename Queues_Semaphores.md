# Queues and Semaphores

## Queue
- Used to send data between tasks
- FIFO structure
- Produces->Queue->Consumer

## Semaphore
- Used for event signaling
- Binary semaphore->ON/OFF event
- Used to signal from ISR to task

 ## Example 1-Binary Semaphote (Button->LED)
 ```c
#include <Arduino.h>
#include "freertos/FreeRTOS.h"
#include "freertos/semphr.h"

#define LED_PIN 2
#define BUTTON_PIN 13

SemaphoreHandle_t xButtonSemaphore;

void IRAM_ATTR handleButtonISR() {
BaseType_t xWoken = pdFALSE;
xSemaphoreGiveFromISR(xButtonSemaphore, &xWoken);
if (xWoken) portYIELD_FROM_ISR();
}

void TaskLED(void *pv) {
pinMode(LED_PIN, OUTPUT);
bool state = false;
while (1) {
if (xSemaphoreTake(xButtonSemaphore, portMAX_DELAY)) {
state = !state;
digitalWrite(LED_PIN, state);
}
}
}

void setup() {
Serial.begin(115200);
xButtonSemaphore = xSemaphoreCreateBinary();
pinMode(BUTTON_PIN, INPUT_PULLUP);
attachInterrupt(digitalPinToInterrupt(BUTTON_PIN), handleButtonISR, FALLING);
xTaskCreatePinnedToCore(TaskLED, "LED", 2048, NULL, 2, NULL, 1);
}

void loop() {}
```

## Example 2 — Queue (Producer → Consumer)
```c
#include <Arduino.h>
#include "freertos/queue.h"

QueueHandle_t xDataQueue;

void TaskProducer(void *pv) {
int value = 0;
while (1) {
value++;
xQueueSend(xDataQueue, &value, portMAX_DELAY);
vTaskDelay(pdMS_TO_TICKS(1000));
}
}

void TaskConsumer(void *pv) {
int rx;
while (1) {
if (xQueueReceive(xDataQueue, &rx, portMAX_DELAY)) {
Serial.printf("Received: %d\n", rx);
}
}
}

void setup() {
Serial.begin(115200);
xDataQueue = xQueueCreate(10, sizeof(int));
xTaskCreatePinnedToCore(TaskProducer, "Prod", 2048, NULL, 1, NULL, 1);
xTaskCreatePinnedToCore(TaskConsumer, "Cons", 2048, NULL, 1, NULL, 1);
}

void loop() {}
```

