# HNF 2026 Python Reverse Writeup

## 1. Python Reversing

**Solution Python:** [Reverse Script Written On Original Python Script](https://github.com/hypeworker20/Python/blob/main/hnf2026_python_rev.py)

Flag as intergers in an array:
```python
TARGET = [98, 101, 110, 27, 32, 86, 74, 32, 100, 34, 98, 128, 106,
          101, 109, 46, 110, 134, 91, 89, 47, 46, 90, 117, 141, 114,
          53, 105, 53, 116]
```

Encoding by the original script. Flag is encoded through ascii conversion of characters XOR key and then added with index position:
```python
def check(code: str) -> bool:
    if len(code) != len(TARGET):
       return False
    for i, ch in enumerate(code):
        # each character is XOR'd with KEY, then index position is added to the XOR'd value, forming the numbers in array
        if (ord(ch) ^ KEY) + i != TARGET[i]:
           return False
        print((ord(ch) ^ KEY) + i)
        print(TARGET[i])
```

To reverse, it is simple math. We first, based on position, subtract the index number. Then we XOR it ( A XOR B = C, C XOR B = A). Finally we convert ASCII to character.
```python
num = 0
for ch in TARGET:
  temp_offset = ch - num
  print(chr(temp_offset ^ KEY), end="")
  num += 1
```
---
