# HSI-calculator
Расчёт гидравлической мощности на долоте (HSI) для эффективного предотвращения сальникообразования и очистки долота


```
Входные данные:
плотность р-ра (г/см3) -> разделить на 0.1198
диаметр долота (мм) -> разделить на 25.4 (в дюймах)
диаметр насадок
кол-во насадок (20 макс)


Рассчет:
TFA (мм2) = суммарная площадь истечения (0.785 * sum(диам.насадок))


Предопределенные данные:
- Производительность (л/сек) -> гал/мин (х * 60 / 3.785)
```



> hsi calculator

#### Build Setup

``` bash
# install dependencies
npm install

# serve with hot reload at localhost:9080
npm run dev

# build electron application for windows
npm run build:win32


```

---

This project was generated with [electron-vue](https://github.com/SimulatedGREG/electron-vue)@[8fae476](https://github.com/SimulatedGREG/electron-vue/tree/8fae4763e9d225d3691b627e83b9e09b56f6c935) using [vue-cli](https://github.com/vuejs/vue-cli). Documentation about the original structure can be found [here](https://simulatedgreg.gitbooks.io/electron-vue/content/index.html).
