---
title: STM32 Firmware Design Patterns
date: 2022-03-21 14:45:00 +0800
categories: [embedded]
tags: [stm32f10x, firmware, programming-techniques, system-configuration]
---
This article explores essential design patterns and programming techniques commonly used in STM32F10x firmware development.

## 1. Robust Clock Stability Check
```c
do {
    HSEStatus = RCC->CR & RCC_CR_HSERDY;
    StartUpCounter++;  
} while((HSEStatus == 0) && (StartUpCounter != HSE_STARTUP_TIMEOUT));
```

This pattern shows an elegant approach to clock stability verification:
- Uses a timeout counter to prevent infinite waiting
- Continuously monitors the ready flag
- Combines timeout and status check in a single condition
- Smart use of do-while to ensure at least one check

## 2. Careful Status Verification
```c
if ((RCC->CR & RCC_CR_HSERDY) != RESET) {
    HSEStatus = (uint32_t)0x01;
} else {
    HSEStatus = (uint32_t)0x00;
}
```

Instead of using the previous HSEStatus directly, the code:
- Re-checks the register status for extra safety
- Uses explicit casting to ensure type safety
- Employs the RESET constant for better readability
- Avoids potential race conditions in status checking

## 3. Smart Flash Configuration
```c
#if !defined STM32F10X_LD_VL && !defined STM32F10X_MD_VL && !defined STM32F10X_HD_VL
    /* Enable Prefetch Buffer */
    FLASH->ACR |= FLASH_ACR_PRFTBE;
    /* Flash wait state */
    FLASH->ACR &= (uint32_t)((uint32_t)~FLASH_ACR_LATENCY);
    
    #ifndef STM32F10X_CL
        FLASH->ACR |= (uint32_t)FLASH_ACR_LATENCY_0;
    #else
        if (HSE_VALUE <= 24000000) {
            FLASH->ACR |= (uint32_t)FLASH_ACR_LATENCY_0;
        } else {
            FLASH->ACR |= (uint32_t)FLASH_ACR_LATENCY_1;
        }
    #endif
```

This section demonstrates sophisticated configuration handling:
- Uses nested preprocessor conditions for different device families
- Enables prefetch buffer for performance optimization
- Adjusts flash wait states based on clock frequency
- Maintains clear register manipulation despite complex conditions

## 4. Clock Switch Verification
```c
while ((RCC->CFGR & (uint32_t)RCC_CFGR_SWS) != (uint32_t)0x04) {
}
```

A simple but crucial check that:
- Ensures clock switching is complete
- Uses a busy-wait loop for timing-critical operations
- Explicitly checks the status bits
- Guarantees system stability before proceeding

## Key Takeaways

1. **Defensive Programming**
   - Multiple layers of status checking
   - Timeout mechanisms
   - Register re-verification

2. **Performance Optimization**
   - Smart use of prefetch buffer
   - Dynamic flash wait states
   - Efficient register operations

3. **Hardware Abstraction**
   - Device family conditional compilation
   - Clock frequency-based adjustments
   - Clear register manipulation patterns
