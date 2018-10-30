<template>
    <div class="container-fluid">
        <div class="row">
            <h2 style="width: 100%; text-align: center">Калькулятор HSI</h2>
        </div>
        <div class="row">
            <div class="col-md-3">
                <h2>Входные данные</h2>
                <div class="input-group input-group-sm mb-3">
                    <div class="input-group-prepend">
                        <span class="input-group-text" id="densityAddon">Плотность р-ра, г/см3</span>
                    </div>
                    <input type="number" class="form-control" aria-label="densityAddon"
                           aria-describedby="densityAddon" v-model="inputData.solutionDensity">
                </div>
                <div class="input-group input-group-sm mb-3">
                    <div class="input-group-prepend">
                        <span class="input-group-text" id="bitDiameter">Диаметр долота, мм</span>
                    </div>
                    <input type="number" class="form-control" aria-label="bitDiameter"
                           aria-describedby="bitDiameter" v-model="inputData.bitDiameter">
                </div>
                <hr>
                <table class="table table-borderless table-sm">
                    <tr>
                        <td></td>
                        <td style="text-align: left">Диаметр, мм</td>
                        <td></td>
                    </tr>
                    <tr v-for="(nozzle, index) in inputData.nozzles">
                        <td>Насадка</td>
                        <td>{{nozzle}}</td>
                        <td>
                            <div class="btn btn-sm btn-light" v-on:click="deleteNozzle(index)"><span
                                    class="fa fa-minus"></span></div>
                        </td>
                    </tr>
                    <tr v-if="!isAdding">
                        <td>Добавить</td>
                        <td></td>
                        <td>
                            <div class="btn btn-sm btn-light" v-on:click="toggleAdding()"><span
                                    class="fa fa-plus"></span></div>
                        </td>
                    </tr>
                    <tr v-if="isAdding">
                        <td>Добавить</td>
                        <td><input type="number" class="form-control-sm" v-model="newNozzle"></td>
                        <td>
                            <div class="btn btn-sm btn-light" v-on:click="addNozzle()"><span class="fa fa-check"></span>
                            </div>
                        </td>
                    </tr>
                </table>
                <hr>
                <div class="btn btn-success" @click="generateTable()">Сгенерировать</div>
            </div>
            <div class="col-md-9">
                <table class="table table-sm table-striped table-responsive">
                    <!--HEADER-->
                    <tr>
                        <td rowspan="30">
                            <div class="vertical-text">TFA, мм2</div>
                        </td>
                    </tr>
                    <tr>
                        <td></td>
                        <td v-for="(body, perf) in resultData[2]">{{perf}}</td>
                    </tr>
                    <tr v-for="(row, tfa) in resultData">
                        <td>{{tfa}}</td>
                        <td v-for="(cell, perf) in row" :style="{'background-color':cell.color}"
                            v-on:click="toggleValue(tfa, perf)">
                            {{cell.value}}
                        </td>
                    </tr>
                </table>
            </div>
        </div>
    </div>
</template>

<script>
  export default {
    name: 'home',
    data () {
      return {
        inputData: {
          nozzles: [],
          solutionDensity: '',
          bitDiameter: ''
        },
        newNozzle: '',
        isAdding: false,
        resultData: {}
      }
    },
    methods: {
      deleteNozzle: function (index) {
        this.inputData.nozzles.splice(index, 1)
      },
      addNozzle: function () {
        this.newNozzle.trim()
        if (this.newNozzle === '') {
          this.toggleAdding()
          return
        }
        this.inputData.nozzles.push(this.newNozzle * 1.0)
        this.newNozzle = ''
        this.toggleAdding()
      },
      toggleAdding () {
        this.isAdding = !this.isAdding
      },
      toggleValue (tfa, perf) {
        let cell = this.resultData[tfa][perf]
        if (cell.value * 1.0 === cell.hsi) {
          this.resultData[tfa][perf]['value'] = cell.roundedHsi
        }
        else {
          this.resultData[tfa][perf]['value'] = cell.hsi
        }
      },
      generateTable () {
        console.log('generation')
        let self = this
        let customTfa = toTfa(this.inputData.nozzles)
        let result = {mainData: {}, custom: {}}
        TFA_SQUARED_ROWS.forEach(function (tfa, i) {
          result[tfa] = {}

          PERFORMANCE_COLS.forEach(function (perf, k) {

            let hsiValue = hsi(perf, self.inputData.solutionDensity, tfa, self.inputData.bitDiameter)

            result['mainData'][tfa][perf] = {
              roundedHsi: hsiValue >= 5 ? '>5' : (Math.round(hsiValue * 100) / 100).toFixed(2),
              hsi: hsiValue >= 5 ? '>5' : hsiValue,
              value: hsiValue >= 5 ? '>5' : (Math.round(hsiValue * 100) / 100).toFixed(2),
              color: perc2color(hsiValue * 100.0 / 5)
            }

            let customHsi = hsi(perf, self.inputData.solutionDensity, customTfa, self.inputData.bitDiameter)
            result['custom'][customTfa][perf] = {
              hsi: customHsi >= 5 ? '>5' : customHsi,
              roundedHsi: hsiValue >= 5 ? '>5' : (Math.round(customHsi * 100) / 100).toFixed(2),
              value: hsiValue >= 5 ? '>5' : (Math.round(customHsi * 100) / 100).toFixed(2),
              color: perc2color(customHsi * 100.0 / 5)
            }

          })
        })

        console.log(result)
        this.resultData = result
      }
    }
  }

  // TFA, мм2
  let TFA_SQUARED_ROWS = [
    50,
    100,
    150,
    200,
    250,
    300,
    350,
    400,
    450,
    500,
    550,
    600,
    650,
    700,
    750,
    800,
    850,
    900,
    950,
    1000,
    1050,
    1100,
    1150,
    1200,
    1250,
    1300,
    1350,
    1400,
    1450,
    1500
  ]

  // Производительность, л/сек
  let PERFORMANCE_COLS = [
    2,
    4,
    6,
    8,
    10,
    12,
    14,
    16,
    18,
    20,
    22,
    24,
    26,
    28,
    30,
    32,
    34,
    36,
    38,
    40,
    42,
    44,
    46,
    48,
    50,
    52,
    54,
    56,
    58,
    60,
    62,
    64,
    66,
    68,
    70
  ]

  function densityToPoundPerGallon (gramPerCubicCentimeter) {
    return gramPerCubicCentimeter * 1.0 / 0.1198
  }

  function diameterToInches (millimeters) {
    return millimeters * 1.0 / 25.4
  }

  function toTfa (diameters) {
    let s = 0.0
    for (let diameter in diameters) {
      s += Math.pow(diameter * 1.0, 2)
    }
    return s
  }

  function tfaToSquareInches (tfa) {
    return tfa / (25.4 * 25.4)
  }

  function performanceToGallonPerMinute (litersPerSecond) {
    return litersPerSecond * 60.0 / 3.785
  }

  function hsi (performance, solutionDensity, tfa, bitDiameter) {

    let perfTr = performanceToGallonPerMinute(performance)
    let tfaTr = tfaToSquareInches(tfa)
    let bitDiamTr = diameterToInches(bitDiameter)
    let solDensTr = densityToPoundPerGallon(solutionDensity)

    return 1.27 *
      (perfTr *
        (
          (solDensTr * Math.pow(perfTr, 2))
          /
          (10858 * Math.pow(tfaTr, 2))
        )
        / 1740
      ) / Math.pow(bitDiamTr, 2)
  }

  function perc2color (perc) {
    if (perc >= 100) return '#FFFFFF'
    var r, g, b = 0
    if (perc < 50) {
      r = 255
      g = Math.round(5.1 * perc)
    }
    else {
      g = 255
      r = Math.round(510 - 5.10 * perc)
    }
    var h = r * 0x10000 + g * 0x100 + b * 0x1
    return '#' + ('000000' + h.toString(16)).slice(-6)
  }
</script>

<style>
    .input-group > .input-group-prepend {
        flex: 0 0 60%;
    }

    .input-group .input-group-text {
        width: 100%;
    }

    .vertical-text {
        transform: rotate(90deg);
        transform-origin: left top 0;
    }
</style>
