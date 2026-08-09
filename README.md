# Lab3 — Shunting Yard y Árbol Sintáctico de Expresiones Regulares

## Contenido

- `shunting_yard.py` — Problema 1 (laboratorio anterior): convierte expresiones
  regulares de infix a postfix, mostrando cada paso del algoritmo.
- `arbol_sintactico.py` — Problema 1 (este laboratorio): reutiliza
  `shunting_yard.py` para construir y dibujar el árbol sintáctico a partir de
  la postfix, aplicando la simplificación de `+` (→ `r·r*`) y `?` (→ `r|ε`).
- `expresiones_arbol.txt` — archivo de expresiones de ejemplo (una por línea).
- `explicacion_shunting_yard.md` — explicación escrita de cómo funciona el
  algoritmo de Shunting Yard.

## Requisitos

```
pip install matplotlib
```

## Ejecución

Ambos scripts deben estar en la misma carpeta (uno importa al otro).

```
python3 shunting_yard.py expresiones_arbol.txt
python3 arbol_sintactico.py expresiones_arbol.txt
```

El segundo comando genera, además de la salida en consola, un archivo
`arbol_N.png` por cada expresión procesada, con el árbol sintáctico dibujado.
