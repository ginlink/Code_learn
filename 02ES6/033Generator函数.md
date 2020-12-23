### Generator函数

Generator是一个生产器，而Generator函数就是用于制造生产器的函数

```javascript
function* gen(){
    yield 1
    yield 2
    ...
}
    
for const i of gen()
```

