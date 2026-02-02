# STM32F091 LCD 20x4 I2C Driver

A comprehensive LCD driver for 20x4 character displays with I2C interface (PCF8574) designed for STM32F091RCT6 microcontroller.

## Features

- **Full 20x4 LCD Support**: Complete control over a 20x4 character LCD display via I2C
- **I2C Communication**: Uses PCF8574 I2C expander for efficient 4-bit LCD control
- **Custom Characters**: Support for up to 8 custom characters including arrows, degree symbol, bell, heart, and battery icons
- **Animation Support**: Built-in animations including:
  - Arrow animations (left, right, bounce)
  - Spinner animation
  - Progress bar
- **Advanced Display Functions**:
  - Text scrolling
  - Row clearing
  - Cursor positioning
  - Box drawing
  - Separator lines
  - Printf-style formatted output
- **Backlight Control**: Toggle LCD backlight on/off
- **Cursor Control**: Display/hide cursor and blinking cursor

## Hardware Requirements

- **Microcontroller**: STM32F091RCT6
- **LCD Display**: 20x4 character LCD with I2C interface (PCF8574 backpack)
- **I2C Pins**:
  - PB8: I2C1_SCL (Clock)
  - PB9: I2C1_SDA (Data)

## Software Requirements

- STM32CubeMX (v6.15.0 or later)
- ARM GCC Toolchain
- CMake (v3.22 or later)
- STM32 HAL Library

## Installation

### 1. Clone the Repository

```bash
git clone https://github.com/Matthew-Oduamafu/lcd_20x4_driver.git
cd lcd_20x4_driver
```

### 2. Build the Project

```bash
mkdir build
cd build
cmake ..
make
```

### 3. Flash to STM32

Use your preferred flashing tool (ST-Link, OpenOCD, etc.) to flash the generated binary to your STM32F091RCT6 board.

## Pin Configuration

| STM32 Pin | Function | LCD Connection |
|-----------|----------|----------------|
| PB8       | I2C1_SCL | PCF8574 SCL    |
| PB9       | I2C1_SDA | PCF8574 SDA    |

**Note**: The default I2C address is `0x27`. If your LCD uses a different address (e.g., `0x3F`), modify the `LCD_I2C_ADDR` definition in `lcd_i2c_driver.h`.

## Usage

### Basic Initialization

```c
#include "lcd_i2c_driver.h"

LCD_Handle lcd;
I2C_HandleTypeDef hi2c1;

// Initialize I2C peripheral (handled by HAL)
MX_I2C1_Init();

// Initialize LCD
if (LCD_Init(&lcd, &hi2c1) != HAL_OK) {
    Error_Handler();
}
```

### Display Text

```c
// Clear the display
LCD_Clear(&lcd);

// Print text at specific position
LCD_PrintAt(&lcd, 0, 0, "Hello World!");

// Print with formatting
LCD_Printf(&lcd, 1, 0, "Value: %d", 42);
```

### Cursor Control

```c
// Set cursor position
LCD_SetCursor(&lcd, 2, 5);

// Show/hide cursor
LCD_Cursor(&lcd, true);  // Show cursor
LCD_Blink(&lcd, true);   // Enable blinking

// Backlight control
LCD_Backlight(&lcd, true);  // Turn on backlight
```

### Custom Characters

```c
// Load default custom characters
LCD_LoadDefaultChars(&lcd);

// Print custom character
LCD_SetCursor(&lcd, 0, 0);
LCD_PrintCustomChar(&lcd, CHAR_HEART);

// Create your own custom character
uint8_t my_char[8] = {
    0x00, 0x0A, 0x1F, 0x1F,
    0x0E, 0x04, 0x00, 0x00
};
LCD_CreateCustomChar(&lcd, 0, my_char);
```

### Animations

```c
// Initialize animation
LCD_Animation arrow_anim;
LCD_InitAnimation(&arrow_anim, ANIM_ARROW_RIGHT, 0, 0, 500);

// Update animation in main loop
while (1) {
    LCD_UpdateAnimation(&lcd, &arrow_anim);
    HAL_Delay(50);
}

// Draw progress bar
LCD_DrawProgressBar(&lcd, 2, 0, 20, 75); // 75% progress

// Draw spinner
LCD_DrawSpinner(&lcd, 3, 10, 0);
```

### Advanced Functions

```c
// Clear specific row
LCD_ClearRow(&lcd, 1);

// Draw box
LCD_DrawBox(&lcd, 0, 0, 20);

// Draw separator line
LCD_DrawSeparator(&lcd, 2);

// Scroll text
LCD_ScrollText(&lcd, 0, "This is a very long text that will scroll", 100);
```

## API Reference

### Initialization Functions

- `HAL_StatusTypeDef LCD_Init(LCD_Handle *hlcd, I2C_HandleTypeDef *hi2c)` - Initialize LCD

### Basic Commands

- `void LCD_Clear(LCD_Handle *hlcd)` - Clear display
- `void LCD_Home(LCD_Handle *hlcd)` - Return cursor to home position
- `void LCD_SetCursor(LCD_Handle *hlcd, uint8_t row, uint8_t col)` - Set cursor position
- `void LCD_Display(LCD_Handle *hlcd, bool on)` - Turn display on/off
- `void LCD_Cursor(LCD_Handle *hlcd, bool on)` - Show/hide cursor
- `void LCD_Blink(LCD_Handle *hlcd, bool on)` - Enable/disable cursor blinking
- `void LCD_Backlight(LCD_Handle *hlcd, bool on)` - Control backlight

### Text Output

- `void LCD_Print(LCD_Handle *hlcd, const char *str)` - Print string at current position
- `void LCD_PrintAt(LCD_Handle *hlcd, uint8_t row, uint8_t col, const char *str)` - Print string at specific position
- `void LCD_Printf(LCD_Handle *hlcd, uint8_t row, uint8_t col, const char *fmt, ...)` - Print formatted string
- `void LCD_PrintChar(LCD_Handle *hlcd, char c)` - Print single character

### Custom Characters

- `void LCD_CreateCustomChar(LCD_Handle *hlcd, uint8_t location, const uint8_t *charmap)` - Create custom character
- `void LCD_LoadDefaultChars(LCD_Handle *hlcd)` - Load predefined custom characters
- `void LCD_PrintCustomChar(LCD_Handle *hlcd, uint8_t location)` - Print custom character

### Animation Functions

- `void LCD_InitAnimation(LCD_Animation *anim, LCD_AnimationType type, uint8_t row, uint8_t col, uint32_t speed)` - Initialize animation
- `void LCD_UpdateAnimation(LCD_Handle *hlcd, LCD_Animation *anim)` - Update animation state
- `void LCD_DrawProgressBar(LCD_Handle *hlcd, uint8_t row, uint8_t col, uint8_t width, uint8_t percent)` - Draw progress bar

### Advanced Display

- `void LCD_ClearRow(LCD_Handle *hlcd, uint8_t row)` - Clear specific row
- `void LCD_ScrollText(LCD_Handle *hlcd, uint8_t row, const char *text, uint8_t delay_ms)` - Scroll text
- `void LCD_DrawBox(LCD_Handle *hlcd, uint8_t row, uint8_t col, uint8_t width)` - Draw box
- `void LCD_DrawSeparator(LCD_Handle *hlcd, uint8_t row)` - Draw separator line

## Configuration

The driver can be configured by modifying the following defines in `lcd_i2c_driver.h`:

```c
#define LCD_I2C_ADDR     0x27U << 1  // I2C address (adjust if needed)
#define LCD_ROWS         4           // Number of rows
#define LCD_COLS         20          // Number of columns
```

## Project Structure

```
lcd_20x4_driver/
├── Core/
│   ├── Inc/
│   │   ├── lcd_i2c_driver.h       # LCD driver header
│   │   └── main.h
│   └── Src/
│       ├── lcd_i2c_driver.c       # LCD driver implementation
│       └── main.c                  # Main application
├── Drivers/                        # STM32 HAL drivers
├── CMakeLists.txt                  # CMake configuration
└── stm32f091rc_lcd_20x4.ioc       # STM32CubeMX project file
```

## License

This software contains code from STMicroelectronics which is licensed under terms that can be found in the LICENSE files in the respective directories. The LCD driver implementation is provided as-is.

## Author

Created by Matthew Oduamafu (November 2025)

## Acknowledgments

- STMicroelectronics for the STM32 HAL library
- PCF8574 I2C expander for simplifying LCD control

## Troubleshooting

### LCD not responding
- Check I2C connections (SDA, SCL)
- Verify I2C address (try 0x3F if 0x27 doesn't work)
- Ensure pull-up resistors are connected on I2C lines

### Garbled characters
- Check I2C timing configuration
- Verify 5V power supply to LCD
- Add delays if needed in high-speed operations

### No backlight
- Check PCF8574 jumper settings
- Verify backlight enable in code: `LCD_Backlight(&lcd, true)`

## Contributing

Contributions are welcome! Please feel free to submit pull requests or open issues for bugs and feature requests.
