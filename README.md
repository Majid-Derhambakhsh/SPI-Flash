# SPI-Flash
The SPI Flash library to use the serial flash memories in MCU

```diff  
- Notice : This library has not free in this time
```

## Release
- #### Version : 0.0.0

- #### Type : Embedded Software.

- #### Support :  
               - ARM LPC/STM32 series  

- #### Program Language : C/C++

- #### Properties :

- #### Changes :  

- #### Required Library/Driver :
               - CMSIS and SPL driver fo LPC series
               - HAL driver for STM32 series  

## Overview 
### Initialization and de-initialization functions:
```c++
uint8_t SPI_Flash_Init(SPI_Flash_TypeDef *_flash);
``` 

### Operation functions:
```c++
/* ::::::::::::::::: Flash Information ::::::::::::::::: */
uint8_t SPI_Flash_GetID(SPI_Flash_TypeDef *_flash);

uint8_t SPI_Flash_GetUniqID(SPI_Flash_TypeDef *_flash);

/* ::::::::::::::::::: Flash Control ::::::::::::::::::: */
uint8_t SPI_Flash_WriteEnable(SPI_Flash_TypeDef *_flash);

uint8_t SPI_Flash_WriteDisable(SPI_Flash_TypeDef *_flash);

/* :::::::::::::::::: Flash Registers :::::::::::::::::: */
uint8_t SPI_Flash_ReadStatusRegister(SPI_Flash_TypeDef *_flash, SPI_Flash_RegTypeDef _register);

uint8_t SPI_Flash_WriteStatusRegister(SPI_Flash_TypeDef *_flash, SPI_Flash_RegTypeDef _register, uint8_t _data);

/* :::::::::::::::::::: Flash Status ::::::::::::::::::: */
uint8_t SPI_Flash_WaitForWriteEnd(SPI_Flash_TypeDef *_flash);

/* :::::::::::::::::::: Flash Erase :::::::::::::::::::: */
uint8_t SPI_Flash_ChipErase(SPI_Flash_TypeDef *_flash);

uint8_t SPI_Flash_SectorErase(SPI_Flash_TypeDef *_flash, uint32_t _sector);

uint8_t SPI_Flash_BlockErase(SPI_Flash_TypeDef *_flash, uint32_t _block);

/* :::::::::::::::::::: Flash Write :::::::::::::::::::: */
uint8_t SPI_Flash_Write(SPI_Flash_TypeDef *_flash, uint8_t _data, uint32_t _address);

uint8_t SPI_Flash_PageWrite(SPI_Flash_TypeDef *_flash, uint8_t *_pData, uint32_t _page, uint32_t _offset, uint32_t _size);

uint8_t SPI_Flash_SectorWrite(SPI_Flash_TypeDef *_flash, uint8_t *_pData, uint32_t _sector, uint32_t _offset, uint32_t _size);

uint8_t SPI_Flash_BlockWrite(SPI_Flash_TypeDef *_flash, uint8_t* _pData, uint32_t _block, uint32_t _offset, uint32_t _size);

uint8_t SPI_Flash_BurstWrite(SPI_Flash_TypeDef *_flash, uint8_t *_pData, uint32_t _address, uint32_t _size);

/* :::::::::::::::::::: Flash Read ::::::::::::::::::::: */
uint8_t SPI_Flash_Read(SPI_Flash_TypeDef *_flash, uint8_t *_data, uint32_t _address);

uint8_t SPI_Flash_PageRead(SPI_Flash_TypeDef *_flash, uint8_t *_pData, uint32_t _page, uint32_t _offset, uint32_t _size);

uint8_t SPI_Flash_SectorRead(SPI_Flash_TypeDef *_flash, uint8_t *_pData, uint32_t _sector, uint32_t _offset, uint32_t _size);

uint8_t SPI_Flash_BlockRead(SPI_Flash_TypeDef *_flash, uint8_t *_pData, uint32_t _block, uint32_t _offset, uint32_t _size);

uint8_t SPI_Flash_BurstRead(SPI_Flash_TypeDef *_flash, uint8_t *_pData, uint32_t _address, uint32_t _size);

``` 

### Macros:
```diff  
None   
```

## Guide

#### This library can be used as follows:
#### 1.  Config MCU SPI and initialize it
#### 2.  Add library Header and Source file in your project  
#### 3.  Config the library in “spi_flash_conf.h”, for example:     
  ```c++
  /* ---------- Controller Series --------- */
  #define _LPC17XX

  /* ------ Required Driver.Library ------- */
  #include "lpc_spi_ex.h"
  #include "lpc_gpio_ex.h"

  /* ----------- Configuration ------------ */
  /* ............ SPI ............ */
  #define _SPI_FLASH_PERIPHERAL LPC_SPI
  #define _SPI_FLASH_TIMEOUT    100

  /* ............ RTOS ........... */
  #define _SPI_FLASH_USE_RTOS   0

  /* ..... Flash manufacturer .... */
  #define _SPI_FLASH_WINBOND
  ``` 
  
#### 4.  Create flash memory object, set specific GPIO, and initialize it, for example:    
* Initializer:
  ```c++
  SPI_Flash_Init(SPI_Flash_TypeDef *_flash);
  ``` 
* Parameters:  
     * _flash: pointer to flash struct
          
          
* Example:
  ```c++  
  SPI_Flash_TypeDef   Flash1;

  Flash1.CS_GPIO_Port = 0;
  Flash1.CS_GPIO_Pin  = (1 << 16);
  
  SPI_Flash_Init(&Flash1);
  ``` 
  
#### 5.  Use flash memory operation functions  
```c++
SPI_Flash_ChipErase(&Flash1);
SPI_Flash_SectorWrite(&Flash1, Tx_Buf, 0, 10, 44);
```  
      
## Examples  

#### Example 1: Initialize and use external flash memory with LPC1768
```c++  
#include "lpc17xx.h"
#include "lpc17xx_gpio.h"
#include "lpc17xx_spi.h"
#include "lpc17xx_libcfg.h"
#include "lpc17xx_pinsel.h"

#include "lpc_spi_ex.h"
#include "spi_flash.h"

SPI_CFG_Type SPI_ConfigStruct;

uint8_t Tx_Buf[44] = "Hello from master!, this is an test program\n";
uint8_t Rx_Buf[50];

int main()
{
	
	SystemInit();
	
	/* ---------- Setup GPIO --------- */
	PINSEL_CFG_Type PinCfg;
	
	/*
	* Initialize SPI pin connect
	* P0.15 - SCK;
	* P0.0 / P0.1 - SSEL - used as GPIO
	* P0.17 - MISO
	* P0.18 - MOSI
	*/
	
	PinCfg.Funcnum   = 3;
	PinCfg.OpenDrain = 0;
	PinCfg.Pinmode   = 0;
	PinCfg.Portnum   = 0;
	PinCfg.Pinnum    = 15;
	PINSEL_ConfigPin(&PinCfg);
	
	PinCfg.Pinnum = 17;
	PINSEL_ConfigPin(&PinCfg);
	
	PinCfg.Pinnum = 18;
	PINSEL_ConfigPin(&PinCfg);
	
	/* Set GPIO Direction */
	GPIO_SetDir(0, (1 << 16), 1);
	GPIO_SetDir(0, (1 << 19), 1);
	GPIO_SetDir(0, (1 << 7), 1);
	
	GPIO_SetValue(0, (1 << 16));
	GPIO_SetValue(0, (1 << 19));
	GPIO_SetValue(0, (1 << 7));
	
	/* ---------- Setup SPI ---------- */
	SPI_ConfigStruct.CPHA      = SPI_CPHA_FIRST;
	SPI_ConfigStruct.CPOL      = SPI_CPOL_HI;
	SPI_ConfigStruct.ClockRate = 300000;
	SPI_ConfigStruct.DataOrder = SPI_DATA_MSB_FIRST;
	SPI_ConfigStruct.Databit   = SPI_DATABIT_8;
	SPI_ConfigStruct.Mode      = SPI_MASTER_MODE;
	
	SPI_Init(LPC_SPI, &SPI_ConfigStruct);

	/* --------- Wait to init --------- */
	SPI_Delay(1);

	/* ~~~~~~~~~~~~~~~~~~ Flash Example ~~~~~~~~~~~~~~~~~ */
	/* --------- Setup Flash ---------- */
	SPI_Flash_TypeDef   Flash1;
	SPI_Flash_TypeDef   Flash2;
	SPI_Flash_TypeDef   Flash3;

	Flash1.CS_GPIO_Port = 0;
	Flash1.CS_GPIO_Pin  = (1 << 16);
	
	Flash2.CS_GPIO_Port = 0;
	Flash2.CS_GPIO_Pin  = (1 << 19);

	Flash3.CS_GPIO_Port = 0;
	Flash3.CS_GPIO_Pin  = (1 << 7);
	
	/* ----------- Commands ----------- */
	SPI_Flash_Init(&Flash1);
	SPI_Flash_Init(&Flash2);
	SPI_Flash_Init(&Flash3);
	
	SPI_Flash_ChipErase(&Flash1);
	SPI_Flash_ChipErase(&Flash2);
	SPI_Flash_ChipErase(&Flash3);
	
	SPI_Flash_SectorWrite(&Flash2, Tx_Buf, 0, 10, 44);
	SPI_Flash_SectorRead(&Flash2, Rx_Buf, 0, 10, 44);
	
	while(1)
	{
		
	}
	/* Loop forever */
	
}
   
``` 

## Tests performed:
- [x] Run on LPC17 xx cores 
- [x] Run on STM32 Fx cores 

## Developers: 
- ### Majid Derhambakhsh
