# FreeRTOS Tasks & Scheduling 
## What is a Task?
A task is an independent function with its own stack and priority.

## Imporatant APIs
- xTaskCreate()
- xTaskCreatePinnedToCore()
- vTaskDelay
- vTaskSuspend
- vTaskResume()
- vTaskDelete()

## Scheduling Rules
- Highest priority ready task runs
- Same priority->round-robin
- ESP32 supports running tasks on 2 cores

## Task States
- Running
- Ready
- Blocked
- Suspended
- Deleted

## Example Code
```c
#include <Arduino.h>

void TaskBlink(void *pvParameters) {
pinMode(2, OUTPUT);
while (1) {
digitalWrite(2, HIGH);
Serial.printf("Blink Task on Core %d\n", xPortGetCoreID());
vTaskDelay(pdMS_TO_TICKS(500));
digitalWrite(2, LOW);
vTaskDelay(pdMS_TO_TICKS(500));
}
}

void TaskSerial(void *pvParameters) {
while (1) {
Serial.printf("Serial Task on Core %d\n", xPortGetCoreID());
vTaskDelay(pdMS_TO_TICKS(1000));
}
}

void setup() {
Serial.begin(115200);
xTaskCreatePinnedToCore(TaskBlink, "Blink", 2048, NULL, 2, NULL, 1);
xTaskCreatePinnedToCore(TaskSerial, "Serial", 2048, NULL, 1, NULL, 1);
}

void loop()
{
}
```
## Expected Output
- LED blinks every 500ms
- Serial prints from both tasks

