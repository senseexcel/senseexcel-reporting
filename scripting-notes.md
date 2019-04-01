do not set empty properties in hjson config! Because in humanJSON can´t interpret this correctly.

do not use with empty values:
```
{
  myVariable:  
}
```

Even when using properties like this:
```
{
  myVariable: 'myValue'
}
```
