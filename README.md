# Reverse Engineering Tips - Write ups - Challenges

## Tips (from [@va_start](https://twitter.com/va_start))

### Tip 1
Long branch-less functions w/many `xors` & `rols` are usually `hash` functions. (e.g MD5 hash)

### Tip 2
Building on **tip 1**, after finding a hash function, google its constant to identify the exact hash algorithm.
![constant](https://twitter.com/va_start/status/1245656402862854144/photo/1)
