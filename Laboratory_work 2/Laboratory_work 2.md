# Комп'ютерні системи імітаційного моделювання

СПм-22-6, Борисов Станіслав Олегович
 
Лабораторна робота №2. Редагування імітаційних моделей у середовищі NetLogo

Варіант 1, модель у середовищі NetLogo: [Virus](https://www.netlogoweb.org/launch#https://www.netlogoweb.org/assets/modelslib/Sample%20Models/Biology/Virus.nlogo)


## Внесені зміни у вихідну логіку моделі, за варіантом:
*Додати вплив віку на інфікування та результат захворювання. Додати вплив ступеня поширення захворювання (поточного відсотка інфікованих) на вірогідність появи нових агентів. Додати можливість регулювання тривалості імунітету та вірогідності захворіти повторно.*


Перш за все було додано залежність віку на інфікування. Якщо істота прожила більше 80% життя час захворювання виростає з кожною хворобою:
``` NetLogo
to get-sick ;; turtle procedure
  set sick? true
  set remaining-immunity 0
  if age > lifespan * 0.8 [
     set sick-time sick-time + 1
  ]
end
```
У данному участку процедури, якщо істота прожила більше 80% життя, тоді є 10% відсотків шанс захворіти без передачі хвороби носіем:

``` NetLogo
to get-older ;; turtle procedure
  ;; Turtles die of old age once their age exceeds the
  ;; lifespan (set at 50 years in this model).
  set age age + 1
  if age > lifespan [ die ]
  if immune? [
    ifelse random-float 100 < chance-second-sick
    [ get-sick ] 
    [ set remaining-immunity remaining-immunity - 1 ]
  ]
  ifelse sick? 
  [ set sick-time sick-time + 1 ]
  [ if age > lifespan * 0.8 and random-float 100 < 10 ;; 10% chance old person get sick
    [ get-sick ] 
  ]
end

```

Додати вплив ступеня поширення захворювання (поточного відсотка інфікованих) на вірогідність появи нових агентів.
У данному фрагменті, чим більше істот хваріє тим більше шанс, що істота одужає та отримає імунітет

``` NetLogo
;; Once the turtle has been sick long enough, it
;; either recovers (and becomes immune) or it dies.
to recover-or-die ;; turtle procedure
  if sick-time > duration                        ;; If the turtle has survived past the virus' duration, then
  [ ifelse random-float 100 < (chance-recover + %infected / 3)     ;; either recover or di
      [ become-immune ]
      [ die ] ]
end

```

Додати можливість регулювання тривалості імунітету та вірогідності захворіти повторно.
На данному зображенні було добалено змінну immunity-duration та chance-second-sick

immunity-duration - тривалість імунітету виставляється у неділях (було раніше константою). Використовується у процедурі become-immune
chance-second-sick - шанс другий раз захворіти. Використовується у процедурі get-older. Поки віднімається імунітет є вірогідність отримати хворобу знову

<img width="870" alt="image" src="https://github.com/stasonn121/KSIM/assets/45041462/5ffd62d0-6450-4f70-897a-ee1ead7b36cb">

``` NetLogo

to become-immune ;; turtle procedure
  set sick? false
  set sick-time 0
  set remaining-immunity immunity-duration
end


;;Turtle counting variables are advanced.
to get-older ;; turtle procedure
  ;; Turtles die of old age once their age exceeds the
  ;; lifespan (set at 50 years in this model).
  set age age + 1
  if age > lifespan [ die ]
  if immune? [
    ifelse random-float 100 < chance-second-sick
    [ get-sick ] 
    [ set remaining-immunity remaining-immunity - 1 ]
  ]
  ifelse sick? 
  [ set sick-time sick-time + 1 ]
  [ if age > lifespan * 0.8 and random-float 100 < 10 ;; 10% chance old person get sick
    [ get-sick ] 
  ]
end


```

## Обчислювальний експеримент 

### Експеримент №1: Залежність кількості захворілих від вірогідності другим разом захворіти

*Початкові умови:*
- *immunity duration =* **2 weeks**
- *chance second sick =* **100 % / 0 %**  
- *number people =* **150**
- *infectiousness =* **65 %** 
- *chance recover =* **75 %**
- *duration=* **20 weeks**

*Результати експерименту:*
Експеримент №1 (chance second sick 100%)
<img width="881" alt="image" src="https://github.com/stasonn121/KSIM/assets/45041462/0c4cafce-e9f2-4e3b-91eb-986df821c1cb">

Експеримент №2 (chance second sick 0%)
<img width="881" alt="image" src="https://github.com/stasonn121/KSIM/assets/45041462/dd3151f4-c274-4cc7-a996-e7d187e24b4e">


*Висновки:* З графіку видно чітку картину - чим більше другий шанс захворіти, тим більше істот у кінці утерації хворі. Що виглядає досить адекватно. 

### Експеримент №2: Залежність кількості агентів від тривалості імунітету

*Початкові умови:*
- *immunity duration =* **2 weeks / 25 weeks**
- *chance second sick =* **10%**  
- *number people =* **150**
- *infectiousness =* **65 %** 
- *chance recover =* **75 %**
- *duration=* **20 weeks**

*Результати експерименту:*

Експеримент №1 (immunity duration 2 weeks)
<img width="881" alt="image" src="https://github.com/stasonn121/KSIM/assets/45041462/7132c70a-ddcc-4590-bdf7-64c2c26184a9">

Експеримент №2 (immunity duration 25 weeks)
<img width="881" alt="image" src="https://github.com/stasonn121/KSIM/assets/45041462/8d9f8ca1-e2e3-47e5-887a-f2da448e89bd">

*Висновки:* З графіку видно, що при тривалості імунітету 2 неділі кількість агенті мінімальна біля 5% від кількості усіх істот та 10% від кількості хворих. Однак коли імунітет 25 неділь тоді вже отримуємо біля 150 хворих та 50 агентів. що у співвідношенні дають 1 до 3. Отже бачимо, що чим більше тривалість імунітету тим більше осіб будуть агентами
