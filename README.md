# PIC18F4550_Baremetal

### **1. Download XC8 Compiler in Ubuntu linux**

link: https://www.microchip.com/en-us/tools-resources/develop/mplab-xc-compilers/xc8
Reference: https://www.youtube.com/watch?v=_ENgn_pJ9Uk

Assuming you've downloaded `xc8-v3.00-full-install-linux-x64-installer.run` to your `Downloads` folder:

```bash
cd ~/Downloads
chmod +x xc8-v3.00-full-install-linux-x64-installer.run
```

---

### ⚙️ **2. Run the Installer**

```bash
./xc8-v3.00-full-install-linux-x64-installer.run
```

- This will start the graphical installer or a text-based installer, depending on your system.
- Follow the on-screen instructions to complete the installation.
- **Permission Issues?**  
  Use `sudo` if necessary:  
  ```bash
  sudo ./xc8-v3.00-full-install-linux-x64-installer.run
  ```
---

### 📦 **3. Add XC8 to the PATH**

Once installed (usually in `/opt/microchip/xc8/v3.00`):

```bash
echo 'export PATH=$PATH:/opt/microchip/xc8/v3.00/bin' >> ~/.bashrc
source ~/.bashrc
```
To add a directory to your PATH for current session only

```bash
export PATH=$PATH:/opt/microchip/xc8/v3.00/bin
echo $PATH
```

---
###  **4. Verify the Installation**
```bash
xc8 --version
```
You should see XC8 v3.00 listed.

---

===============================================================================================

Set up a **Blink project** for the **PIC18F4550** using the **bare-metal approach** with the **XC8 compiler**. This will include:  

1. **Setting up the project structure**  
2. **Writing the code**  
3. **Compiling the code**  
4. **Flashing the microcontroller**

---

## ⚙️ **1. Project Setup**

### 📁 **Project Directory Structure**  
```bash
mkdir /home/ezhil/Desktop/PIC18F4550_Baremetal/001_gpio_blinkled_simple
cd /home/ezhil/Desktop/PIC18F4550_Baremetal/001_gpio_blinkled_simple
```

---

## 💻 **2. Writing the Blink Code**

Open `src/main.c` using any editor (like `nano`, `vim`, or `gedit`):  
```bash
vim main.c
```

### ✅ **Code for Blinking an LED (Connected to PORTB0):**  
```c
#include <xc.h>       // Include XC8 header file
#include <stdint.h>   // For standard integer types

// Configuration bits
#pragma config FOSC = HS    // High-Speed Oscillator
//#pragma config PLLEN = ON   // Enable PLL (if using USB)
#pragma config CPUDIV = OSC1_PLL2
#pragma config VREGEN = OFF
#pragma config WDT = OFF
#pragma config LVP = OFF

#define _XTAL_FREQ 8000000     // Define oscillator frequency (8 MHz)

// Main function
int main(void) {
    TRISBbits.TRISB0 = 0;     // Set PORTB0 as output (0 = output)
    LATBbits.LATB0 = 0;       // Initially turn off the LED

    while (1) {
        LATBbits.LATB0 = 1;    // Turn LED ON
        __delay_ms(500);       // 500 ms delay
        LATBbits.LATB0 = 0;    // Turn LED OFF
        __delay_ms(500);       // 500 ms delay
    }
    return 0;
}
```

- **`TRISBbits.TRISB0 = 0;`** → Configures PORTB0 as an output.  
- **`LATBbits.LATB0 = 1;` / `0;`** → Controls the LED state.  
- **`__delay_ms(500);`** → Introduces a 500 ms delay.  

---

## 📦 **3. Create a Makefile for Building the Project**



In the project root folder (`~/pic18f4550_blink`), create a `Makefile`:  
```bash
vim Makefile
```

### ✅ **Sample Makefile:**  
```makefile
# XC8 Compiler and Flags
CC = xc8
CFLAGS = -mcpu=18F4550 -mno-lc -Wall

# Project Files
SRC = src/main.c
OBJ = $(SRC:.c=.o)
OUT = blink.hex

# Build Target
all: $(OUT)

# Compile .c to .o
%.o: %.c
	$(CC) $(CFLAGS) -c $< -o $@

# Link object files to create HEX
$(OUT): $(OBJ)
	$(CC) $(CFLAGS) $^ -o $(OUT)

# Clean up
clean:
	rm -f src/*.o $(OUT)
```

- **`-mcpu=18F4550`** → Specifies the target MCU.  
- **`-mno-lc`** → Disables low-power features (adjust if needed).  
- **`-Wall`** → Enables all warnings for better debugging.  

---

## 🛠️ **4. Build the Project**

```bash
make
```

If successful, you'll get `blink.hex` in the project directory.

---

## 🔌 **5. Flashing the Firmware to PIC18F4550**

### ⚡ **Using MPLAB X Programmer or PICkit**  
- Connect your PIC18F4550 to the programmer (e.g., PICkit 3, ICD 3).
- Open **MPLAB X IDE** or use **`pk2cmd`** (for PICkit 2):  
  ```bash
  pk2cmd -M -F blink.hex
  ```

### 💡 **Alternative (Using `avrdude` or `picprog`)**  
If you're using another tool, adjust accordingly.
