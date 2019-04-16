---
tags: 書頁, Go語言學習筆記本
---
基本資料型態
===
[:arrow_up: 回到「Go 語言學習筆記本」目錄](/uomRrP1AQ661sCgFEK7Wew)

數值 ( *numeric types* )
---
|  Types | Bytes | Value ( -2^x-1^ ~ 2^x-1^-1 or 0 ~ 2^x^-1 )|
|:------:|:-----:|:------------:|
|   int8 |     1 |    -128 ~ 127|
|  uint8 |     1 |       0 ~ 255|
|  int16 |     2 |-32768 ~ 32767|
| uint16 |     2 |     0 ~ 65535|
|  int32 |     4 |-2,147,483,648 ~ 2,147,483,647|
| uint32 |     4 |0 ~ 4,294,967,295|
|  int64 |     8 | -2^63^ ~ 2^63^-1|
| uint64 |     8 | 0 ~ 2^64^-1 |
| int, uint, uintptr | 視平台而定 | 視平台而定 |
> The int, uint, and uintptr types are usually 32 bits wide on 32-bit systems and 64 bits wide on 64-bit systems. When you need an integer value you should use int unless you have a specific reason to use a sized or unsigned integer type.

`byte` is alias for `uint8`.
`rune` is alias for `int32`, but represents a Unicode code point.


### 浮點數 ( *floating-point* )
`float32` 及 `float64` 。

`float32` 與 `float64` 無法一起計算，須強制轉型。

若初始化變數未指明型態，初始值不含小數會被判斷為 `int`；而浮點數會被推判為 `float64` 。
```go
	var i = 1.0
	k := 3.5
	fmt.Printf("%T %T", i, k)    // %T  a Go-syntax representation of the type of the value
```
輸出：
```
float64 float64
```


### Complex Number
`complex64` 及 `complex128` 。

若初始化變數但未指名型態，會被判斷為 `complex128` 。
```go
	var complexValue complex64 = 1.2 + 12i
	complexValue2 := complex(3.2, 12)

	fmt.Println("complexValue =", complexValue)

	fmt.Println("complexValue2 實數 =", real(complexValue2))
	fmt.Println("complexValue2 虛數 =", imag(complexValue2))
```
輸出：
```
complexValue = (1.2+12i)
complexValue2 實數 = 3.2
complexValue2 虛數 = 12
```


字串
---
:construction:


Boolean
---
:construction:

Constants
---
只要在變數前面加個 `const` ，變數就可以變為 *Constants* 。*Constants* 不能夠以短變數宣告 ( `:=` ) 。

Example：
```go
const World = "世界"
```


Default Type
---
未指明型態之常量 ( *untyped constant* ) 具有 **默認型態 ( *default type* )** 。在[未指明型態之變數宣告](/qnWrPRsrR2m0eCqtfNnXoQ#Type-inference)，例如[短變數宣告](/qnWrPRsrR2m0eCqtfNnXoQ#Short-variable-declarations)， 變數會被賦予其初始化值之型態。

| untyped constant | default type |
|:----------------:|:------------:|
| boolean          | `bool`       |
| rune             | `rune`       |
| integer          | `int`        |
| floating-point   | `float64`    |
| complex          | `complex128` |
| string constant  | `string`     |
