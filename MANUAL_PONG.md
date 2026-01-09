# Manual del Juego PONG en Assembly
## Guía Técnica y de Usuario

---

## 1. Descripción General

**PONG** es un juego clásico de arcade implementado en **Lenguaje Ensamblador (Assembly x86)** para el simulador **emu8086**. Dos jugadores compiten moviendo paletas verticales para golpear una pelota de un lado a otro de la pantalla.

**Objetivo:** Ser el primero en anotar **2 goles** para ganar la partida.

---

## 2. Controles del Juego

| Acción | Tecla | Jugador |
|--------|-------|---------|
| Mover arriba | **W** | Izquierdo (Score 1) |
| Mover abajo | **S** | Izquierdo (Score 1) |
| Mover arriba | **↑ (Flecha Arriba)** | Derecho (Score 2) |
| Mover abajo | **↓ (Flecha Abajo)** | Derecho (Score 2) |
| Salir del juego | **ESC** | Ambos |

---

## 3. Interfaz del Juego

```
┌─────────────────────────────────────────────┐
│ SCORE 1    ║                    ║    SCORE 2 │
│─────────────────────────────────────────────│
│ │          ║        *        ║          │  │
│ │          ║                 ║          │  │
│ │          ║                 ║          │  │
│ │          ║                 ║          │  │
│ │  JUGADOR ║     PELOTA      ║  JUGADOR │  │
│ │    1     ║                 ║    2     │  │
│ │          ║                 ║          │  │
│ │          ║                 ║          │  │
│ │          ║                 ║          │  │
│ │          ║ (Línea Central) ║          │  │
│─────────────────────────────────────────────│
└─────────────────────────────────────────────┘
```

---

## 4. Diagrama de Flujo Principal

```
┌──────────────────┐
│   INICIO JUEGO   │
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│ INICIALIZAR      │
│ - Pantalla       │
│ - Posiciones     │
│ - Velocidades    │
└────────┬─────────┘
         │
         ▼
    ┌────────────────────┐
    │  GAME LOOP         │
    │ (Bucle Principal)  │
    └────────┬───────────┘
             │
             ├──► ¿ESC presionado? ─► SÍ ──► SALIR
             │
             │ NO
             │
             ├──► Procesar Teclas (W, S, Flechas)
             │
             ├──► Mover Pelota
             │    ├─ Actualizar posición X
             │    ├─ Actualizar posición Y
             │    ├─ Detectar rebotes en bordes
             │    ├─ Detectar colisión con paletas
             │    └─ Detectar goles
             │
             ├──► Dibujar Juego
             │    ├─ Limpiar pantalla
             │    ├─ Dibujar paletas
             │    ├─ Dibujar pelota
             │    └─ Mostrar puntuación
             │
             ├──► ¿Alguien llegó a 2 puntos? ─► SÍ ──► MOSTRAR GANADOR ──► SALIR
             │
             │ NO
             │
             └──► Repetir bucle

```

---

## 5. Variables y Datos

| Variable | Tipo | Rango | Descripción |
|----------|------|-------|-------------|
| `ball_x` | byte | 0-79 | Posición X de la pelota (columna) |
| `ball_y` | byte | 0-24 | Posición Y de la pelota (fila) |
| `ball_vx` | byte | -2 a 2 | Velocidad horizontal de la pelota |
| `ball_vy` | byte | -2 a 2 | Velocidad vertical de la pelota |
| `paddle1_y` | byte | 2-18 | Posición Y del Jugador 1 (izquierdo) |
| `paddle2_y` | byte | 2-18 | Posición Y del Jugador 2 (derecho) |
| `score1` | byte | 0-2 | Puntos del Jugador 1 |
| `score2` | byte | 0-2 | Puntos del Jugador 2 |

---

## 6. Funciones Principales

### 6.1 `main` - Programa Principal
**Propósito:** Inicializa el juego y controla el bucle principal

**Pasos:**
1. Inicializa el segmento de datos (DS)
2. Establece modo de texto (80x25)
3. Dibuja el marco del juego
4. Inicia el bucle del juego
5. Verifica condición de victoria (Score = 2)

---

### 6.2 `check_keys` - Detección de Teclado
**Propósito:** Captura las teclas presionadas y mueve las paletas

**Teclas procesadas:**
- **W/S:** Mueven la paleta 1 (izquierda) ±2 píxeles
- **Flechas Arriba/Abajo:** Mueven la paleta 2 (derecha) ±2 píxeles
- **ESC:** Cierra el programa

---

### 6.3 `move_ball` - Movimiento de la Pelota
**Propósito:** Actualiza posición y detecta colisiones

**Procesos:**
1. Suma velocidad a posición (X y Y)
2. Detecta rebotes en bordes superior/inferior
3. Verifica colisión con paleta izquierda
4. Verifica colisión con paleta derecha
5. Detecta goles (Score +1)
6. Reinicia pelota si hay gol

---

### 6.4 `draw_game` - Renderizar Pantalla
**Propósito:** Dibuja todos los elementos en pantalla

**Elementos dibujados:**
- Paleta 1 (5 líneas verticales `|`)
- Paleta 2 (5 líneas verticales `|`)
- Pelota (`*`)
- Puntuación ambos jugadores
- Línea central punteada

---

### 6.5 `show_winner` - Pantalla de Victoria
**Propósito:** Muestra al ganador y cierra el programa

**Muestra:**
- "SCORE 1 GANADOR!" o "SCORE 2 GANADOR!"
- Espera 3 segundos
- Cierra automáticamente

---

## 7. Parámetros Técnicos

| Parámetro | Valor | Descripción |
|-----------|-------|-------------|
| Ancho pantalla | 80 columnas | Resolución horizontal |
| Alto pantalla | 25 filas | Resolución vertical |
| Velocidad pelota | ±2 píxeles/frame | Velocidad de movimiento |
| Tamaño paleta | 5 líneas | Alto de cada raqueta |
| Goles para ganar | 2 puntos | Condición de victoria |
| Área colisión paleta | ±5 píxeles | Zona donde detecta impacto |
| Bordes seguros | Filas 2-22 | Área de juego válida |

---

## 8. Modificaciones Realizadas

| # | Característica | Estado | Descripción |
|---|-----------------|--------|-------------|
| 1 | Velocidad de pelota | ✓ | Velocidad constante: -2 píxeles |
| 2 | Movimiento paletas | ✓ | Movimiento de ±2 píxeles por tecla |
| 3 | Condición victoria | ✓ | Gana quien llega a 2 puntos |
| 4 | Mensaje ganador | ✓ | Muestra "SCORE X GANADOR!" |
| 5 | Cierre automático | ✓ | Se cierra tras mostrar ganador |
| 6 | Tecla ESC | ✓ | Cierra juego en cualquier momento |
| 7 | Línea central | ✓ | Se redibuja automáticamente |
| 8 | Sonidos | ✓ | Beep al golpear/anotar |

---

## 9. Instrucciones de Compilación y Ejecución

### En emu8086:

1. **Abrir archivo:** `Archivo > Abrir > pin2.asm`
2. **Compilar:** Click en botón `Compile`
3. **Ejecutar:** Click en botón `Emulate`
4. **Jugar:** Usar controles del teclado
5. **Salir:** Presionar ESC o esperar a que alguien gane

---

## 10. Tabla de Instrucciones Assembly Utilizadas

| Instrucción | Tipo | Descripción |
|-------------|------|-------------|
| `mov` | Movimiento | Mueve datos entre registros/memoria |
| `add` | Aritmética | Suma valores |
| `sub` | Aritmética | Resta valores |
| `inc` | Aritmética | Incrementa en 1 |
| `dec` | Aritmética | Decrementa en 1 |
| `cmp` | Comparación | Compara dos valores |
| `je` | Salto condicional | Salta si es igual |
| `jg` | Salto condicional | Salta si es mayor |
| `jl` | Salto condicional | Salta si es menor |
| `jmp` | Salto incondicional | Salta siempre |
| `loop` | Bucle | Repite hasta CX=0 |
| `int` | Interrupción | Llama función del sistema |
| `call` | Subrutina | Llama función |
| `ret` | Retorno | Regresa de función |

---

## 11. Interrupciones Utilizadas

| INT | Función | Descripción |
|-----|---------|-------------|
| 10h | Video | Manejo de pantalla (cursor, escritura) |
| 16h | Teclado | Lectura de teclado |
| 21h | DOS | Servicios del sistema (salir) |
| 43h | Timer | Control del speaker/sonido |

---

## 12. Tabla de Código ASCII Usados

| Caracter | Código Decimal | Uso |
|----------|----------------|-----|
| `\|` | 124 | Paletas |
| `*` | 42 | Pelota |
| ` ` | 32 | Espacios |
| `0-9` | 48-57 | Puntuación |
| `─` | 196 | Bordes horizontales |
| `\|` | 179 | Bordes verticales |
| `┌` | 218 | Esquina superior izquierda |
| `┐` | 191 | Esquina superior derecha |
| `└` | 192 | Esquina inferior izquierda |
| `┘` | 217 | Esquina inferior derecha |

---

## 13. Solución de Problemas

| Problema | Causa | Solución |
|----------|-------|----------|
| Juego muy lento | Delay muy grande | Reducir ciclos en `wait_time` |
| Juego muy rápido | Delay muy pequeño | Aumentar ciclos en `wait_time` |
| Pelota atraviesa paleta | Área colisión incorrecta | Ajustar comparaciones en `check_paddles_hit` |
| Sonido no funciona | Puerto speaker inactivo | Verificar instrucciones `out 61h` |
| Mensaje incompleto | Posición columna errónea | Aumentar `dl` en cada posicionamiento |
| ESC no funciona | Código de scan incorrecto | Código de ESC debe ser `1` en `ah` |

---

## 14. Especificaciones del Sistema

**Requerimientos:**
- Emulador: emu8086 v4.08 o superior
- Arquitectura: x86 real mode
- Modelo de memoria: Small (.model small)
- Pila: 256 bytes

**Registros principales usados:**
- `AX, AL, AH` - Operaciones generales
- `BX, BL, BH` - Índices
- `CX, CL` - Bucles y contador
- `DX, DL, DH` - Entrada/salida
- `DS` - Segmento de datos

---

## 15. Diagrama de Memoria

```
┌─────────────────┐
│  Segmento de    │
│  Datos (.data)  │  ◄── Variables del juego
├─────────────────┤
│  Segmento de    │
│  Código (.code) │  ◄── Instrucciones y funciones
├─────────────────┤
│  Pila (.stack)  │  ◄── Registro de llamadas (256 bytes)
└─────────────────┘
```

---

## 16. Flujo de Colisión

```
┌─────────────────────────┐
│  Ball X dentro rango    │
│  de paleta (3-5 o 75-77)│
└────────────┬────────────┘
             │
             ▼
┌─────────────────────────┐
│  Ball Y intersecta      │
│  con paleta (±5 píxeles)│
└────────────┬────────────┘
             │
             ▼
┌─────────────────────────┐
│  ¡COLISIÓN!             │
├─────────────────────────┤
│ • Invierte ball_vx      │
│ • Emite sonido          │
│ • Reposiciona pelota    │
└─────────────────────────┘
```

---

## 17. Conceptos Clave en Assembly

### Registros
- **Registros de propósito general:** AX, BX, CX, DX
- **Registros de segmento:** DS (datos), CS (código)
- **Registros especiales:** SP (pila), IP (instrucción)

### Modo de Direccionamiento
- **Inmediato:** `mov al, 5` (valor constante)
- **Directo:** `mov al, var` (desde memoria)
- **Indirecto:** `mov al, [bx]` (desde registro)

### Saltos Condicionales
- `cmp` establece flags
- `je` (jump if equal), `jg` (jump if greater), etc.

---

## Notas Finales

Este manual documenta completamente la estructura y funcionamiento del juego PONG implementado en Assembly. Cada componente ha sido diseñado para máxima eficiencia en modo real x86.

**Autor:** Diana Hernández  
**Fecha:** 8 de enero de 2026  
**Materia:** Lenguaje Ensamblador - Quinto Semestre
