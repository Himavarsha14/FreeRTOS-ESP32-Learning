## Software Timers and Event Groups

## Software Timer
- Executes callback function at regular intervals
- No task needed for periodic work

## Example -LED Toggler Timer
```c
#include <Arduino.h>
#include "freertos/timers.h"

#define LED_PIN 2
TimerHandle_t xBlinkTimer;

void BlinkCallback(TimerHandle_t t) {
static bool state = false;
state = !state;
digitalWrite(LED_PIN, state);
}

void setup() {
Serial.begin(115200);
pinMode(LED_PIN, OUTPUT);
xBlinkTimer = xTimerCreate("Blink", pdMS_TO_TICKS(1000), pdTRUE, 0, BlinkCallback);
xTimerStart(xBlinkTimer, 0);
}

void loop() {}
```
## Event Groups
- Used for synchronizing tasks with multiple events

## Example -Two Tasks->one Coordinator
```c
#include <Arduino.h>
#include "freertos/event_groups.h"

EventGroupHandle_t xEvents;
#define BIT_TASK1 (1<<0)
#define BIT_TASK2 (1<<1)

void Task1(void *pv) {
vTaskDelay(2000);
xEventGroupSetBits(xEvents, BIT_TASK1);
vTaskDelete(NULL);
}

void Task2(void *pv) {
vTaskDelay(4000);
xEventGroupSetBits(xEvents, BIT_TASK2);
vTaskDelete(NULL);
}

void Coordinator(void *pv) {
xEventGroupWaitBits(xEvents, BIT_TASK1 | BIT_TASK2, pdTRUE, pdTRUE, portMAX_DELAY);
Serial.println("Both tasks completed");
vTaskDelete(NULL);
}

void setup() {
Serial.begin(115200);
xEvents = xEventGroupCreate();
xTaskCreate(Task1, "T1", 2048, NULL, 1, NULL);
xTaskCreate(Task2, "T2", 2048, NULL, 1, NULL);
xTaskCreate(Coordinator, "C", 2048, NULL, 2, NULL);
}

void loop() {}
```

