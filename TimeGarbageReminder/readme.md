## Time and Garbage Reminder

![timegarbage](timegarb.png)

Create a markdown card and insert this as the code
```
type: markdown
content: >-
  <font size=4px>
  {{ now().strftime("%H:%M %a %b %d") }}
  {% set garbageDays =  ["もやすごみ(生)", "もやさない","ペット","もやすごみ(生)","その他プラ","",""] %} 
  {{garbageDays[now().weekday()] }} </font>
```
