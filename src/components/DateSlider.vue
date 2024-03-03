<script>
export default {
  data() {
    return {
      minDate: '',
      maxDate: '',
      prevMinDate: '',
      prevMaxDate: '',
      newMinDate: '',
      newMaxDate: '',
      minSelectedDate: '',
      maxSelectedDate: '',
      prevSelectedMinDate: '',
      prevSelectedMaxDate: '',
      newMinSelectedDate: '',
      newMaxSelectedDate: '',
      displayMinSelectedDate: '',
      displayMaxSelectedDate: '',
      years: [],
      months: ['фев', 'мар', 'апр', 'май', 'июн', 'июл', 'авг','сен', 'окт','ноя','дек'],
      yearsRange: Array.from({ length: 51 }, (v, k) => 2040 - k),
      minSelectedYearPosition: 0,
      maxSelectedYearPosition: 0,
      isSlider: false,
      isFill: false,
      isActiveYears: true,
      isAdaptiveSize: !window.matchMedia('(max-width: 650px)').matches,
    };
  },
  mounted() {
    window.matchMedia('(max-width: 650px)').addEventListener('change', e => {
      this.isAdaptiveSize = !e.matches;
    });
  },
  methods: {
    /**
     * Обработчик нажатия на ползунки слайдера
     */
    onMouseDown(event) {
        event.preventDefault();
  
        this.slider = this.$refs.slider;
  
        this.leftThumb = this.$refs.leftThumb;
        this.leftCircle = this.$refs.leftCircle;
  
        this.rightThumb = this.$refs.rightThumb;
        this.rightCircle = this.$refs.rightCircle;

        if (event.target === document.getElementById('leftSliderThumb')) { //обработчик для левого ползунка

          const shiftX = event.clientX - this.leftThumb.getBoundingClientRect().left;
      
          const onMouseMove = (event) => {
            let newLeft = event.clientX - shiftX - this.slider.getBoundingClientRect().left;
      
            if (newLeft < 0) {
              newLeft = 0;
            }
    
            const rightEdge = this.rightThumb.getBoundingClientRect().left - this.slider.getBoundingClientRect().left - this.leftCircle.offsetWidth;
            if (newLeft > rightEdge) {
             newLeft = rightEdge;
            }
      
            this.leftThumb.style.left = newLeft + 'px';
    
            let position = (this.leftThumb.getBoundingClientRect().left - this.slider.getBoundingClientRect().left)/this.slider.offsetWidth * 100;
            
            this.minSelectedYearPosition = position;
          };
          const onMouseUp = () => {
            document.removeEventListener('mouseup', onMouseUp);
            document.removeEventListener('mousemove', onMouseMove);
          };
    
          document.addEventListener('mousemove', onMouseMove);
          document.addEventListener('mouseup', onMouseUp);

        } else if (event.target === document.getElementById('rightSliderThumb')) { //обработчик для правого ползунка ползунка

          const shiftX = event.clientX - this.rightThumb.getBoundingClientRect().left;
      
          const onMouseMove = (event) => {
            let newLeft = event.clientX - shiftX - this.slider.getBoundingClientRect().left;
      
            const leftEdge = this.leftThumb.getBoundingClientRect().left - this.slider.getBoundingClientRect().left + this.rightCircle.offsetWidth;

            if (newLeft < leftEdge) {
              newLeft = leftEdge;
            }
    
            const rightEdge = this.slider.offsetWidth - this.rightCircle.offsetWidth;
            if (newLeft > rightEdge) {
             newLeft = rightEdge;
            }
      
            this.rightThumb.style.left = newLeft + 'px';
    
            let position = (this.rightThumb.getBoundingClientRect().left - this.slider.getBoundingClientRect().left)/this.slider.offsetWidth * 100;
            
            this.maxSelectedYearPosition = position;
          };
          const onMouseUp = () => {
            document.removeEventListener('mouseup', onMouseUp);
            document.removeEventListener('mousemove', onMouseMove);
          };
    
          document.addEventListener('mousemove', onMouseMove);
          document.addEventListener('mouseup', onMouseUp);
        }
    },
    /**
     * Рассчет позиций выбранной даты на слайдере
     * @param {Выбранная дата} selectedDate 
     * @param {Минимальная/максимальная дата} isMinSelectionPosition 
     */
    positionDate(selectedDate, isMinSelectionPosition) {
      const totalYears = this.years.length;
    
      const startOfYear = new Date(selectedDate.getFullYear(), 0, 0);
      const diff = selectedDate - startOfYear; 
      const oneDay = 1000 * 60 * 60 * 24; 
      const dayOfYear = Math.floor(diff / oneDay); 
      
      const selectedYear = selectedDate.getFullYear();
      const minYear = this.minDate.getFullYear();
      
      if (isMinSelectionPosition) {
        this.minSelectedYearPosition = (((dayOfYear / 365) * 100) / totalYears) + (((selectedYear - minYear) / totalYears) * 100) - 0.5;
        this.countDate(this.minSelectedYearPosition, true);
      } else {
        this.maxSelectedYearPosition = (((dayOfYear / 365) * 100) / totalYears) + (((selectedYear - minYear) / totalYears) * 100) - 1.1;
        this.countDate(this.minSelectedYearPosition, false);
      }
    },
    /**
     * Рассчет даты потносительно позиции выбранной даты на слайдере
     * @param {Координаты расположения даты на слайдере} selectedPosition 
     * @param {Минимальная/максимальная дата} isMinSelectionPosition 
     */
    countDate(selectedPosition, isMinSelectionPosition) {
      const totalYears = this.years.length; 
      const minYear = this.minDate.getFullYear();

      const daysFromStartOfYear = Math.floor(((selectedPosition - 0.5)/ 100) * totalYears * 365);

      const startOfYear = new Date(minYear, 0, 0);
      const selectedDate = new Date(startOfYear.getTime() + daysFromStartOfYear * 24 * 60 * 60 * 1000);

      selectedDate.setMonth(selectedDate.getMonth() + 1);

      if (isMinSelectionPosition) this.displayMinSelectedDate = selectedDate;
      else this.displayMaxSelectedDate = selectedDate;
    },
    /**
     * Отображение временного отрезка на слайдере
     */
    applyNewDates() {
    if (this.newMinDate && this.newMaxDate) {
      const currentMinDate = new Date(this.newMinDate, 0, 1);
      const currentMaxDate = new Date(this.newMaxDate, 0, 1);

      const currentMinSelectedDate = new Date(this.newMinSelectedDate);
      const currentMaxnSelectedDate = new Date(this.newMaxSelectedDate);

      if (currentMinDate >= currentMaxDate) {
        alert('Минимальная дата больше или равна максимальной!');
        this.newMinDate = this.prevMinDate;
        this.newMaxDate = this.prevMaxDate;
      } else if (this.isFill == true && currentMinDate >  currentMinSelectedDate) {
        alert("Начало даты не может быть меньше минимальной даты");
        this.newMinDate = this.prevMinDate;
        this.newMaxDate = this.prevMaxDate;
      } else if (this.isFill == true && currentMaxDate < currentMaxnSelectedDate) {
        alert("Конец даты не может быть больше максимальной даты");
        this.newMinDate = this.prevMinDate;
        this.newMaxDate = this.prevMaxDate;
      } else {
          this.minDate = currentMinDate;
          this.maxDate = currentMaxDate;

          this.prevMinDate = this.newMinDate;
          this.prevMaxDate = this.newMaxDate;

          this.years.length = 0;

          const minYear = this.minDate.getFullYear();
          const maxYear = this.maxDate.getFullYear();

          for (let year = minYear; year <= maxYear - 1; year++) {
            this.years.push(year);
          }

          if (this.years.length >= 3 || window.matchMedia('(max-width: 650px)').matches) this.isAdaptiveSize = false;
          else this.isAdaptiveSize = true;

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
    /**
     * Обновление выбранной даты
     */
    updateSelectedDate() {
      if (this.newMinSelectedDate && this.newMaxSelectedDate) {
        const currentMinSelectedDate = new Date(this.newMinSelectedDate);
        const currentMaxnSelectedDate = new Date(this.newMaxSelectedDate);
        if (currentMinSelectedDate >= currentMaxnSelectedDate) {
          alert("Начало даты не может быть больше конца даты!");
          this.newMinSelectedDate = this.prevSelectedMinDate;
          this.newMaxSelectedDate = this.prevSelectedMaxDate;
        } else if (currentMinSelectedDate < this.minDate) {
          alert("Начало даты не может быть меньше минимальной даты");
          this.newMinSelectedDate = this.prevSelectedMinDate;
          this.newMaxSelectedDate = this.prevSelectedMaxDate;
        } else if (currentMaxnSelectedDate > this.maxDate) {
          alert("Конец даты не может быть больше максимальной даты");
          this.newMinSelectedDate = this.prevSelectedMinDate;
          this.newMaxSelectedDate = this.prevSelectedMaxDate;
        } else {
          this.prevSelectedMinDate = this.newMinSelectedDate;
          this.prevSelectedMaxDate = this.newMaxSelectedDate;

          this.minSelectedDate = currentMinSelectedDate;
          this.maxSelectedDate = currentMaxnSelectedDate;
          this.positionDate(this.minSelectedDate, true);
          this.positionDate(this.maxSelectedDate, false);
          
          this.isFill = true;
          console.log(`minSelectedYearPosition = ${this.minSelectedYearPosition}`);
        }
      } else {
          this.isFill = false;
      }
    },
  },
  watch: {
    /* newMinDate: function () {
        this.applyNewDates();
    },
    newMaxDate: function () {
        this.applyNewDates();
    }, */
   /*  newMinSelectedDate: function () {
      this.updateSelectedDate();
    },
    newMaxSelectedDate: function () {
      this.updateSelectedDate();
    }, */
    minSelectedYearPosition: function () {
      this.countDate(this.minSelectedYearPosition, true);
    },
    maxSelectedYearPosition: function () {
      this.countDate(this.maxSelectedYearPosition, false);
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
            <select v-model="newMinDate" @click="applyNewDates()" title="2024">
              <option v-for="year in yearsRange" :value="year" :key="year">{{ year }}</option>
            </select>
          </div>
          <div class="panel-parameters__input">
            <p>Введите Максимальный год</p>
            <select v-model="newMaxDate" @click="applyNewDates()" title="2027">
              <option v-for="year in yearsRange" :value="year" :key="year">{{ year }}</option>
            </select>
          </div>
        </div>
      </div>
      <div class="panel-parameters__selected">
        <div class="panel-parameters__date">
          <div class="panel-parameters__inputs">
            <div class="panel-parameters__input">
              <p>выбрать начало даты</p>
              <input type="month" v-model="newMinSelectedDate" @input="updateSelectedDate" placeholder="Введите дату">
            </div>
            <div class="panel-parameters__input">
              <p>выбрать конец даты</p>
              <input type="month" v-model="newMaxSelectedDate" @input="updateSelectedDate" placeholder="Введите дату">
            </div>
          </div>
        </div>
      </div>
    </div>
    
    <div class="slider" ref="slider" v-if="this.isSlider">
      <div class="slider-buttons">
        <button @click="isActiveYears = true" class="slider-button" :class="{ 'isActive': isActiveYears }">Все года</button>
        <button @click="isActiveYears = false" class="slider-button" :class="{ 'isActive': !isActiveYears }">Все месяца</button>
      </div>
          <div v-for="year in years" :key="year" class="year">
            <p class="year-text">{{ year }}</p>
            <div v-if="!isActiveYears" class="months" 
            :class="{ 'one-year': years.length === 1, 'two-years': years.length === 2 }">
             <p v-for="month in months" :key="month" class="month-text">{{ isAdaptiveSize ? month : month.charAt(0)}}</p>
            </div>
          </div>
          <span>{{ maxDate.getFullYear() }}</span>
          <div class="slider-selected" v-if="isFill">
            <div class="selected-date" ref="leftThumb" :style="{ left: minSelectedYearPosition + '%' }">
                <div class="selected-date__circle" id="leftSliderThumb" @mousedown="onMouseDown" ref="leftCircle"></div>
                <div class="tooltip-up">
                  <div class="tooltip-down__text">
                    <p>{{displayMinSelectedDate.toLocaleString("ru-RU", { month: "long" })}}</p>
                    <p>{{displayMinSelectedDate.getFullYear()}}</p>
                  </div>
                </div>
            </div>
            <div class="selected-date" ref="rightThumb" :style="{ left: maxSelectedYearPosition + '%' }">
                <div class="selected-date__circle" id="rightSliderThumb" @mousedown="onMouseDown" ref="rightCircle"></div>
                <div class="tooltip-down">
                  <div class="tooltip-down__text">
                    <p>{{displayMaxSelectedDate.toLocaleString("ru-RU", { month: "long" })}}</p>
                    <p>{{displayMaxSelectedDate.getFullYear()}}</p>
                  </div>
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

.panel-parameters__input select {
  width: 70px;
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

  margin-top: 90px;

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
  padding-left: 50px;
  padding-right: 44px;
}

.month-text {
  margin-top: 25px;
  font-size: 14px;
}

/* .one-year {
  padding-left: 100px;
  padding-right: 100px;
}

.two-years {
  padding-left: 50px;
  padding-right: 44px;
} */


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
    cursor: pointer;
}

.tooltip-down {
    position: relative;
    top: 5px; 
    transform: translateX(-50%); 
    background-color: rgba(255, 255, 255, 0.8); 
    padding: 5px; 
    border-radius: 5px; 
    box-shadow: 1px 1px 5px 0px #000000;
    z-index: 999;
}

.tooltip-down:after{
    content:'';
    display:block;
    width:0;
    height:0;
    position: absolute;
    top: -5px;
    left: 58%;
    border-left: 5px solid transparent;
    border-right: 5px solid transparent;
    border-bottom: 5px solid rgb(255, 255, 255);
}

.tooltip-down__text {
  display: flex;
  flex-direction: column;
  text-align: center;
  padding: 3px 5px;
}

.tooltip-down__text p {
  color: rgb(41, 95, 243);
}

.tooltip-up {
    position: relative;
    top: -83px; 
    transform: translateX(-50%); 
    background-color: rgba(255, 255, 255, 0.8); 
    padding: 5px; 
    border-radius: 5px; 
    box-shadow: 1px 1px 5px 0px #000000;
    z-index: 999;
}

.tooltip-up:after{
    content:'';
    display:block;
    width:0;
    height:0;
    position: absolute;
    top: 52px;
    left: 58%;
    border-left: 5px solid transparent;
    border-right: 5px solid transparent;
    border-top: 5px solid rgb(255, 255, 255);
}
.slider-info {
  text-align: center;
  color: rgb(70, 70, 70);
}

@media (max-width: 1200px) {
  .one-year {
    padding-left: 83px;
    padding-right: 75px;
  }

  .two-years {
    padding-left: 38px;
    padding-right: 32px;
  }
}

@media (max-width: 1000px) {
  .one-year {
    padding-left: 63px;
    padding-right: 71px;
  }

  .two-years {
    padding-left: 30px;
    padding-right: 22px;
  }
}

@media (max-width: 768px) {
  .panel-parameters {
    flex-direction: column;
    row-gap: 10px;
  }
  .container {
    height: 400px;
  }

  .slider {
    margin-left: 30px;
    margin-top: 130px;
  }

  .selected-date {
    top: -5px;
  }

  .selected-date__circle {
    width: 20px;
    height: 20px;
  }  

  .slider-buttons {
    display: flex;
    flex-direction: row;
    column-gap: 10px;
    position: absolute;
    left: unset;
    top: -100px;
  }
  .one-year {
    padding-left: 50px;
    padding-right: 40px;
  }

  .two-years {
    padding-left: 29px;
    padding-right: 15px;
  }

}

@media (max-width: 650px) {
  .month-text {
    margin-top: 45px;
  }
  .two-years {
    padding-left: 29px;
    padding-right: 0px;
  }
  .two-years p{
    font-size: 12px;
  }
}

@media (max-width: 500px) {
  .panel-parameters__input p {
    font-size: 14px;
  }

  .panel-parameters__input input {
    width: 100px;
  }

  .slider-button {
    font-size: 12px;
  }
  .one-year {
    padding-left: 35px;
    padding-right: 15px;
  }

  .slider span {
    right: -25px;
    font-size: 12px;
  }

  .year-text {
    font-size: 12px;
  }

  .month-text {
    font-size: 12px;
  }
}

@media (max-width: 400px) {
  .one-year {
    padding-left: 25px;
    padding-right: 6px;
  }

  .two-years {
    padding-left: 19px;
    padding-right: 10px;
  }
}
</style>
