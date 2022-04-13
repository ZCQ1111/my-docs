!> 请考虑使用 "--resolveJsonModule" 导入带 ".json" 扩展的模块。

**tsconfig.json**
```
{
  "compilerOptions": {
     "resolveJsonModule": true,
  }
}
```

!> 此模块是使用 “export =“ 声明的，只能在使用 “allowSyntheticDefaultImports“ 标志时进行默认导入。

**tsconfig.json**
```
{
  "compilerOptions": {   
    "allowSyntheticDefaultImports": true
  }
}
```