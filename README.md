# printf_to_uart
Send printf via UART on a STM32 MCU

# How to send printf via UART

## 1. Override _write function 

A UART write function to call from printf needs to be defined in `syscalls.cpp`
```
extern int _writeUart( char *ptr, int len );
```
The UART write is called from the system `_write` function
```
int _write(char *ptr, int len ) {
  return _writeUart(ptr, len);
}
```

## 2. Redirect _write output to UART 

In the main.cpp the UART connection and the write Function need to be implemented
```UART_HandleTypeDef huart3;
```
The printf function will call the write function that will send the data via USB to a Serial Monitor ()moserial
```
extern "C" {
  int _writeUart( char *ptr, int len )
  {
      HAL_UART_Transmit(&huart3, (uint8_t*) ptr, len, 0xFFFF);
      return 0;
  }
}
```
To receive messages you can use e.g. grabserial (see utils/GrabSerial.sh)

## 3. My printf isn't working, why?

* The correct port is `/dev/ttyACM0`
* Always end your printf with a linebreak `(\n)`.
* Printf needs quite a bit of memory, make sure the executing thread has a big enough stack size.

