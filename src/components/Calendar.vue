<template>
    <div class="calendar">
        <div class="date-input">
            <input type="text" v-model="inputDate" placeholder="yyyy-mm-dd">
            <button @click="setCalendarDate">Выбрать</button>
        </div>
        <div class="language-dropdown">
            <select v-model="language">
                <option value="ru">RU</option>
                <option value="en">EN</option>
            </select>
        </div>
        <div class="header">
            <button @click="prevMonth">&lt;</button>
            <h2>{{ months[language][currentDate.getMonth()] }}</h2>
            <button @click="nextMonth">&gt;</button>
        </div>
        <div class="days">
            <div v-for="day in daysOfWeek[language]" :key="day" class="day">{{ day }}</div>
            <div v-for="blank in firstDayOffset" :key="blank" class="empty"></div>
            <div v-for="date in daysInMonth" :key="date" class="date" :class="{ 'selected': isSelected(date)}" @click="selectDate(date)">{{ date }}</div>
        </div>
    </div>
    <div>
        {{ selectedDate}}
    </div>
</template>

<script setup>
import { ref, computed } from 'vue';

const currentDate = ref(new Date());
const selectedDate = ref(null);
const inputDate = ref('');

const language = ref('ru');

const months = {
    ru: ['Январь', 'Февраль', 'Март', 'Апрель', 'Май', 'Июнь', 'Июль', 'Август', 'Сентябрь', 'Октябрь', 'Ноябрь', 'Декабрь'],
    en: ['January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December']
};

const daysOfWeek = {
    ru: ['Пн', 'Вт', 'Ср', 'Чт', 'Пт', 'Сб', 'Вс'],
    en: ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun']
};

const daysInMonth = computed(() => {
    const year = currentDate.value.getFullYear();
    const month = currentDate.value.getMonth();
    const daysInMonth = new Date(year, month + 1, 0).getDate();
    return Array.from({ length: daysInMonth }, (_, index) => index + 1);
});

const firstDayOffset = computed(() => {
    const year = currentDate.value.getFullYear();
    const month = currentDate.value.getMonth();
    const firstDay = new Date(year, month, 1).getDay();
    return Array.from({ length: firstDay }, (_, index) => index);
});

const prevMonth = () => {
    const newDate = new Date(currentDate.value);
    newDate.setMonth(newDate.getMonth() - 1);
    currentDate.value = newDate;
};

const nextMonth = () => {
    const newDate = new Date(currentDate.value);
    newDate.setMonth(newDate.getMonth() + 1);
    currentDate.value = newDate;
};

const selectDate = (date) => {
    const newDate = new Date(currentDate.value.getFullYear(), currentDate.value.getMonth(), date);
    if (selectedDate.value && selectedDate.value.getTime() === newDate.getTime()) {
        selectedDate.value = null;
    } else {
        selectedDate.value = newDate;
    }
};

const isSelected = (date) => {
    if (!selectedDate.value) return false;
    const selected = new Date(selectedDate.value);
    const current = new Date(currentDate.value.getFullYear(), currentDate.value.getMonth(), date);
    return selected.getTime() === current.getTime();
};

const setCalendarDate = () => {
    const [year, month, day] = inputDate.value.split('-').map(Number);
    if (!isNaN(year) && !isNaN(month) && !isNaN(day)) {
        currentDate.value = new Date(year, month - 1, day);
        selectedDate.value = currentDate.value
    } else {
        console.error('Invalid date format');
    }
};
</script>

<style scoped>
.calendar {
    max-width: 300px;
    margin: 0 auto;
}

.header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 10px;
}

.days {
    display: grid;
    grid-template-columns: repeat(7, 1fr);
    gap: 5px;
}

.day,
.date {
    padding: 5px;
    text-align: center;
    border: 1px solid #ccc;
    cursor: pointer;
}

.date.selected {
    border: 1px solid blue;
}

.empty {
    visibility: hidden;
}

.date-input {
    margin-bottom: 10px;
}

.date-input input {
    margin-right: 5px;
}

</style>
