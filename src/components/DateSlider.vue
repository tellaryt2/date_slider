<script>
export default {
  data() {
    return {
      minDate: new Date('2015-04-15'),
      maxDate: new Date('2018-06-20'),
      selectedDate: new Date('2017-12-31'),
      years: [],
      selectedYearPosition: 0
    };
  },
  mounted() {
    const minYear = this.minDate.getFullYear();
    const maxYear = this.maxDate.getFullYear();
    for (let year = minYear; year <= maxYear; year++) {
      this.years.push(year);
    }
    const totalYears = this.years.length;

    const startOfYear = new Date(this.selectedDate.getFullYear(), 0, 0); // Начало текущего года
    const diff = this.selectedDate - startOfYear; // Разница между текущей датой и началом года в миллисекундах
    const oneDay = 1000 * 60 * 60 * 24; // Один день в миллисекундах
    const dayOfYear = Math.floor(diff / oneDay); // Номер текущего дня в году

    const selectedYear = this.selectedDate.getFullYear();


    this.selectedYearPosition = (((dayOfYear / 365) * 100) / totalYears) + (((selectedYear - minYear) / totalYears) * 100) - 0.1;  // Позиция точки внутри текущего года на слайдере
  }
};
</script>

<template>
  <div class="container">
    <div class="slider">
      <div v-for="year in years" :key="year" class="year"> <p>{{ year }}</p></div>
      <div class="selected-date" :style="{ left: selectedYearPosition + '%' }"></div>
    </div>
  </div>
</template>

<style scoped>
.container {
    background-color: rgb(235, 238, 238);
    padding-top: 15px;
    height: 100px;

    border: 1px solid rgb(54, 54, 54);
    border-radius: 10px;
}

.slider {
  display: flex;
  position: relative;

  border: 1px solid rgb(54, 54, 54);
  height: 20px;
  border-radius: 10px;

  margin-left: 10px;
  margin-right: 10px;

  background-color: rgb(250, 252, 252);
}

.slider p {
    position: absolute;
    top: 25px;
}

.year {
  flex: 1;
  text-align: center;
}

.selected-date {
  position: absolute;
  top: -3px;
  width: 25px;
  height: 25px;
  border: 5px solid rgb(47, 220, 243);
  border-radius: 90%;
  box-sizing: border-box;
}


</style>
