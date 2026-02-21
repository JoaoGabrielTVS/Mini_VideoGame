# 🎮 Mini Video Game (ATmega328P)

A bare-metal mini video game developed in **C** for the **ATmega328P** microcontroller, using an **8x8 LED Matrix** and push buttons as input devices.

This project demonstrates low-level embedded programming concepts without using high-level libraries or operating systems.

---

## 🧠 Description

This project implements a mini video game system running directly on the ATmega328P.  
It was built as a practical embedded systems project focusing on:

- Direct register manipulation
- GPIO configuration
- Game loop implementation
- Hardware control without abstraction layers
- Efficient memory usage in C

The system currently includes two classic games:

- 🏓 Pong  
- 🐍 Snake  

---

## 🧰 Hardware Used

- ATmega328P
- 8x8 LED Matrix
- Push Buttons (user input)
- Resistors
- Power supply (5V)

---

## ⚙️ Technologies

- **Language:** C
- **Programming Style:** Bare-metal
- **Microcontroller:** ATmega328P
- **Compiler:** avr-gcc
- **Programming Tool:** USBasp / Arduino as ISP (or compatible)

---

## 🔧 How to Compile

Make sure you have `avr-gcc` installed.

### Compile:

```bash
avr-gcc -mmcu=atmega328p -Os main.c -o main.elf
avr-objcopy -O ihex main.elf main.hex
```

### Upload to Microcontroller:

```bash
avrdude -c usbasp -p m328p -U flash:w:main.hex
```

> ⚠️ Adjust the programmer (`-c`) parameter if you are using a different one.

---

## 🎮 How It Works

- The LED matrix is multiplexed to display game frames.
- Buttons are read through digital input pins.
- A main game loop updates the game state.
- Timers (if used) control refresh rate and movement speed.
- No delay-based blocking structure (optimized real-time behavior).

---

## 📁 Project Structure

```
Mini_VideoGame/
│
├── main.c        # Main game logic
├── README.md     # Project documentation
└── .gitignore
```

---

## 🚀 Features

- Direct hardware manipulation
- Real-time input handling
- Frame-based game logic
- Multiple mini-games
- Low memory footprint
- Educational embedded systems design

---

## 📈 Future Improvements

- Add score system
- Add sound feedback (buzzer)
- Improve animation smoothness
- Add game selection menu
- Refactor into modular files

---

## 📸 Demo
# MENU GAME
<img width="1102" height="725" alt="Captura de tela de 2026-02-20 22-18-00" src="https://github.com/user-attachments/assets/e24bb7b3-0385-47c9-9362-4990a8fca5b9" />


# PONG GAME

<img width="1102" height="725" alt="Captura de tela de 2026-02-20 22-15-38" src="https://github.com/user-attachments/assets/f9cbea5e-d3af-480f-ae6f-a7a9531ebdf9" />

# SNAKE GAME

<img width="1102" height="725" alt="Captura de tela de 2026-02-20 22-17-26" src="https://github.com/user-attachments/assets/d49514c3-acb9-4a1e-806d-697dfaedb6d5" />





## 📄 License

This project is open-source and available for educational purposes.

---

## 👨‍💻 Author

Developed by **João Gabriel Tavares V. Souza**  
Computer Engineering Student  
Focused on Embedded Systems & Low-Level Programming

---

⭐ If you liked this project, consider giving it a star on GitHub!
