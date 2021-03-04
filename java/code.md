### practise in idea


## 1. 代码经验


1. 线上出现

```
"java.lang.ArithmeticException: Non-terminating decimal expansion; no exact representable decimal result.
	at java.math.BigDecimal.divide(BigDecimal.java:1693)"

```

原代码是这样写的

```

lastShopRedPacket = NumberUtils.max((
                            (shopRedPacketRatio.divide(apartRate)).multiply(new BigDecimal(lastRedPacket)).setScale(4,RoundingMode.UP).longValue()),
                             0L);
```
原因是当shopRedPacketRatio除apartRate是无限循环小数时会出现这样的问题，添加上精度就可以了

```

lastShopRedPacket = NumberUtils.max((
                            (shopRedPacketRatio.divide(apartRate,6, BigDecimal.ROUND_HALF_UP)).multiply(new BigDecimal(lastRedPacket)).setScale(4,RoundingMode.UP).longValue()),
                          0L);
```
