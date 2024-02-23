<script>
export default {
  data() {
    return {
      minDate: '',
      maxDate: '',
      newMinDate: '',
      newMaxDate: '',
      minSelectedDate: '',
      maxSelectedDate: '',
      newMinSelectedDate: '',
      newMaxSelectedDate: '',
      years: [],
      months: ['фев', 'мар', 'апр', 'май', 'июн', 'июл', 'авг','сен', 'окт','ноя','дек'],
      minSelectedYearPosition: 0,
      maxSelectedYearPosition: 0,
      isSlider: false,
      isFill: false,
      isActiveYears: true
    };
  },
  methods: {
    positionDate(selectedDate, isMinSelectionPosition) {
      const totalYears = this.years.length;
    
      const startOfYear = new Date(selectedDate.getFullYear(), 0, 0); // Начало текущего года
      const diff = selectedDate - startOfYear; // Разница между текущей датой и началом года в миллисекундах
      const oneDay = 1000 * 60 * 60 * 24; // Один день в миллисекундах
      const dayOfYear = Math.floor(diff / oneDay); // Номер текущего дня в году
      
      const selectedYear = selectedDate.getFullYear();
      const minYear = this.minDate.getFullYear();

      const marchDate = new Date(selectedDate.getFullYear(), 5, 1);
      
      if (isMinSelectionPosition) {
        this.minSelectedYearPosition = (((dayOfYear / 365) * 100) / totalYears) + (((selectedYear - minYear) / totalYears) * 100) - 0.5; // Позиция точки внутри текущего года на слайдере
        if (this.isActiveYears && selectedDate > marchDate) this.minSelectedYearPosition += 1.1;
      } else {
        this.maxSelectedYearPosition = (((dayOfYear / 365) * 100) / totalYears) + (((selectedYear - minYear) / totalYears) * 100) - 1.1;
        if (this.isActiveYears && selectedDate > marchDate) this.maxSelectedYearPosition += 1.1;
      }
    },
    applyNewDates() {
    if (this.newMinDate && this.newMaxDate) {
      if (this.newMinDate >= this.newMaxDate) {
        alert('Минимальная дата больше максимальной!');
      } else if (this.isFill == true && this.newMinDate >  this.newMinSelectedDate) {
        alert("Начало даты не может быть меньше минимальной даты");
      } else if (this.isFill == true && this.newMaxDate < this.newMaxSelectedDate) {
        alert("Конец даты не может быть больше максимальной даты");
      } else {
          this.minDate = new Date(this.newMinDate);
          this.maxDate = new Date(this.newMaxDate);

          this.years.length = 0;

          const minYear = this.minDate.getFullYear();
          const maxYear = this.maxDate.getFullYear();

          for (let year = minYear; year <= maxYear - 1; year++) {
            this.years.push(year);
          }

          if (this.isFill) {
            this.positionDate(this.minSelectedDate, true);
            this.positionDate(this.maxSelectedDate, false);
          }

          this.isSlider = true;
       }
      }
      else {
        this.isSlider = false;
      }
    },
    updateSelectedDate() {
      if (this.newMinSelectedDate && this.newMaxSelectedDate) {
        if (this.newMinSelectedDate >= this.newMaxSelectedDate) {
          alert("Начало даты не может быть больше конца даты!");
        } else if (this.newMinSelectedDate < this.newMinDate) {
          alert("Начало даты не может быть меньше минимальной даты");
        } else if (this.newMaxSelectedDate > this.newMaxDate) {
          alert("Конец даты не может быть больше максимальной даты");
        } else {
          this.minSelectedDate = new Date(this.newMinSelectedDate);
          this.maxSelectedDate = new Date(this.newMaxSelectedDate);
          this.positionDate(this.minSelectedDate, true);
          this.positionDate(this.maxSelectedDate, false);
          
          this.isFill = true;
        }
      } else {
          this.isFill = false;
      }
    }
  }
};
</script>

<template>
  <div class="container">
    <div class="panel-parameters">
      <div class="panel-parameters__date">
        <div class="panel-parameters__inputs">
          <div class="panel-parameters__input">
            <p>Введите Минимальный год</p>
            <input type="month" v-model="newMinDate" placeholder="Введите дату">
          </div>
          <div class="panel-parameters__input">
            <p>Введите Максимальный год</p>
            <input type="month" v-model="newMaxDate" placeholder="Введите дату">
          </div>
        </div>
        <button @click="applyNewDates">Применить новые данные</button>
      </div>
      <div class="panel-parameters__selected">
        <div class="panel-parameters__date">
          <div class="panel-parameters__inputs">
            <div class="panel-parameters__input">
              <p>выбрать начало даты</p>
              <input type="month" v-model="newMinSelectedDate" placeholder="Введите дату">
            </div>
            <div class="panel-parameters__input">
              <p>выбрать конец даты</p>
              <input type="month" v-model="newMaxSelectedDate" placeholder="Введите дату">
            </div>
          </div>
          <button @click="updateSelectedDate">Применить</button>
        </div>
      </div>
    </div>
    
    <div class="slider" v-if="this.isSlider">
      <div class="slider-buttons">
        <button @click="isActiveYears = true" class="slider-button" :class="{ 'isActive': isActiveYears }">Все года</button>
        <button @click="isActiveYears = false" class="slider-button" :class="{ 'isActive': !isActiveYears }">Все месяца</button>
      </div>
          <div v-for="year in years" :key="year" class="year">
            <p class="year-text">{{ year }}</p>
            <div v-if="!isActiveYears" class="months">
             <p v-for="month in months" :key="month" class="month-text">{{ month }}</p>
            </div>
          </div>
          <span>{{ maxDate.getFullYear() }}</span>
          <div class="slider-selected" v-if="isFill">
            <div class="selected-date" :style="{ left: minSelectedYearPosition + '%' }">
                <div class="selected-date__circle"></div>
                <div class="tooltip">
                  {{minSelectedDate.getFullYear()}}
                </div>
            </div>
            <div class="selected-date" :style="{ left: maxSelectedYearPosition + '%' }">
                <div class="selected-date__circle"></div>
                <div class="tooltip">
                  {{maxSelectedDate.getFullYear()}}
                </div>
            </div>
            <div class="slider-fill" :style="{ left: minSelectedYearPosition + 1.37 + '%', width: (maxSelectedYearPosition - minSelectedYearPosition) - 1 + '%' }"></div>
          </div>
    </div>
    <div class="slider-info" v-if="!this.isSlider">
      <h1>Введите данные: Минимальная и максимальная дата</h1>
    </div>
  </div>
</template>

<style scoped>

.panel-parameters {
  display: flex;
  justify-content: space-between;
  margin: 10px 30px;
  padding-bottom: 20px;
  border-bottom: 1px solid rgb(189, 186, 186);
}

.panel-parameters__date {
  display: flex;
  align-items: center;
  flex-wrap: wrap;
  column-gap: 10px;
}

.panel-parameters__inputs {
  display: flex;
  flex-direction: column;
  row-gap: 5px;
}

.panel-parameters__input {
  display: flex;
  justify-content: space-between;
  column-gap: 5px;
}

.panel-parameters__selected {
  display: flex;
  flex-direction: column;
  row-gap: 5px;
}

.container {
    background-color: rgb(235, 238, 238);
    padding-top: 15px;
    height: 300px;

    border: 1px solid rgb(54, 54, 54);
    border-radius: 10px;
}

.slider {
  display: flex;
  position: relative;

  height: 10px;
  border-radius: 10px;

  margin-left: 150px;
  margin-right: 35px;

  margin-top: 70px;

  background-color: rgb(250, 252, 252);
}

.year-text {
    position: absolute;
    top: 25px;
    font-size: 14px;
}

.slider span {
    position: absolute;
    top: 25px;
    right: -30px;
}

.slider-fill {
  position: absolute;
  top: 0;
  bottom: 0;
  background-color: rgb(47, 220, 243); 
  z-index: 1;
}

.slider-buttons {
  display: flex;
  flex-direction: column;
  row-gap: 10px;
  position: absolute;
  left: -120px;
  top: -13px;
}

.slider-button {
  border: none;
  color: rgb(73, 125, 194);
  background-color: rgb(235, 238, 238);
  border-bottom: solid 1px rgb(125, 147, 175);
}

.slider-button:hover {
  cursor: pointer;
  color: rgb(0, 71, 165);
}

.isActive {
  color: rgb(2, 79, 180);
  font-size: 14px;
  border-bottom: none;
}

.year {
  flex: 1;
}

.months {
  display: flex;
  justify-content: space-between;
  text-align: center;
  padding-left: 53px;
  padding-right: 25px;
}

.month-text {
  margin-top: 25px;
  font-size: 14px;
}

.selected-date {
  position: absolute;
  top: -7px;
  z-index: 20;
}

.selected-date__circle {
    width: 25px;
    height: 25px;
    background-color: rgb(250, 252, 252);
    border: 5px solid rgb(47, 220, 243);
    border-radius: 90%;
    box-sizing: border-box;
}

.tooltip {
    position: relative;
    top: 5px; /* располагаем тултип под selected-date */
    transform: translateX(-50%); /* центрируем точку в середине */
    background-color: rgba(255, 255, 255, 0.8); /* цвет фона тултипа */
    padding: 5px; /* отступы внутри тултипа */
    border-radius: 5px; /* закругляем углы тултипа */
    z-index: 999;
}

.tooltip:after{
    content:'';
    display:block;
    width:0;
    height:0;
    position: absolute;
    top: -5px;
    left: 55%;
    border-left: 5px solid transparent;
    border-right: 5px solid transparent;
    border-bottom: 5px solid rgb(255, 255, 255);
}

.tooltip:after:before {
    border-left: 4px solid transparent;
    border-right: 4px solid transparent;
    border-bottom: 4px solid rgb(0, 0, 0);
}

.slider-info {
  text-align: center;
  color: rgb(70, 70, 70);
}


</style>
