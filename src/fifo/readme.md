## Deription ##

This is a function give buffer. That can be used to store byte array or one byte. It is written in C languare and you can compiled with any software. When init dynamic memory and retrun feedback true or false, you can check it.
The library have some functio:
+ Give one byte or multi byte to memory temporary. You can puts over size compare to size buffer temporary init, and now the library run a function callback overload
+ Read one byte or multi byte from memory temporary


## Example use: ## 

```c
#include "myLib.h"

fifo_t fifo_example;

void cb_overload_ram(void)
{
    Serial.println("overload size of buffer temporary");

    uint16_t length = fifo_availeble(&fifo_example);
    uint8_t *buffer = (uint8_t *)malloc(length + 1);
    memset(buffer, 0, sizeof(buffer));

    fifo_read_array(&fifo_example, buffer, length);

    Serial.printf("==> %d: %s\r\n", length, (char *)buffer);
    free(buffer);
}

void serialReciver(void)
{
    uint8_t size_data;
    if ((size_data = Serial.available()) != 0)
    {
        uint8_t *buffer = (uint8_t *)malloc(size_data + 1);
        memset(buffer, 0, size_data + 1);

        Serial.read(buffer, size_data);
        fifo_give_array(&fifo_example, buffer, size_data);

        free(buffer);
    }
}

void readDataInFifo(void)
{
    uint8_t size_data;
    if ((size_data = fifo_availeble(&fifo_example) != 0))
    {
        uint8_t *buffer = (uint8_t *)malloc(size_data + 1);
        memset(buffer, 0, size_data + 1);

        fifo_read_array(&fifo_example, buffer, size_data);

        Serial.printf("data read: %s", (char *)buffer);

        free(buffer);
    }
}

void setup(void)
{
    Serial.begin(115200);

    fifo_init(&fifo_example, 10, cb_overload_ram); // length of buffer temporary are 10 byte
}

void loop(void) 
{
    //read data in serial and write fifo library
    serialReciver();

    //read data in fifo library write serial
    readDataInFifo();
}
```