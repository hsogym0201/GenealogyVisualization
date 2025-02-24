<template>
  <div class="XingShiXuanZe" style="width: 700px; height: 400px; border: 0px solid gray;">
    <div class="container">
    <div class="header">
      <div class="label">按姓氏首字母选择：</div>
      <div class="alphabet">
        <div
            class="letter"
            :style="{ color: curLetter === v ? '#f40' : '#333' }"
            v-for="v of alphabet"
            :key="v"
            @click="handleLetterClick(v)">
            {{ v }}
          </div>
      </div>
    </div>
    <div class="name-list">
      <div class="name-item" v-for="item of filteredFamilyNames" :key="item" @click="onNameClick(item.label)">
        <i class="name-value">{{ item.value }}</i>
        <span class="name-label">{{ item.label }}</span>
      </div>
    </div>
  </div>  
  </div>
    <div
    v-if="isNoFamilyNamePopupVisible"
    class="no-family-name-popup"
    @click="isNoFamilyNamePopupVisible = false">
    <div class="popup-content" @click.stop>
      <p>{{ noFamilyNameMessage }}</p>
      <button @click="isNoFamilyNamePopupVisible = false">关闭</button>
    </div>
  </div>
</template>
 


<script setup>
import list from "../assets/new.json";
import emitter from "../utils/emitter";
import { pinyin } from "pinyin-pro";
import { computed, ref } from "vue";

const alphabet = Array.from({ length: 26 }, (_, i) => String.fromCharCode(65 + i));
const uniqueFamilyNames = [...new Set(list.map(item => item['姓氏']))].map((item) => {
  return {
    label: item,
    value: pinyin(item)
  };
});

const curName = ref("");
const curLetter = ref("");

const isNoFamilyNamePopupVisible = ref(false); 
const noFamilyNameMessage = ref(""); 

const filteredFamilyNames = computed(() => {
  return uniqueFamilyNames.filter(item => item.value.startsWith(curLetter.value.toLowerCase()));
});


const handleLetterClick = (letter) => {
  curLetter.value = letter; // 当前选中的字母
  const filteredNames = uniqueFamilyNames.filter(item => item.value.startsWith(letter.toLowerCase()));
  
  if (filteredNames.length === 0) {
    // 没有姓氏，则显示弹窗
    isNoFamilyNamePopupVisible.value = true;
    noFamilyNameMessage.value = `字母“${letter}”暂无姓氏数据`;
  } else {
    // 否则隐藏弹窗
    isNoFamilyNamePopupVisible.value = false;
  }
};


const onNameClick = (value) => {
  if (curName.value === value) {
    return;
  }
  curName.value = value;
  emitter.emit("data", list.filter((item) => item["姓氏"] === value));
}
</script>


<style scoped>
.container {
  padding: 16px;
}

.header {
  display: flex;
  align-items: flex-start;
  margin-bottom: 32px;
}

.label {
  color: #6d6969;
  font-size: 18px;
  white-space: nowrap;
  font-weight: bold;
}

.alphabet {
  display: flex;
  flex-wrap: wrap;
  gap: 16px;
}

.letter {
  width: 30px;
  height: 30px;
  text-align: center;
  line-height: 30px;
  font-weight: bold;
  cursor: pointer;
  border-radius: 50%;
  transition: background-color 0.3s;
}

.letter:hover {
  background-color: #eee;
}

.name-list {
  display: flex;
  flex-wrap: wrap;
  align-content: flex-start;
  gap: 40px;
  padding-left: 16px;
  height: 150px;
  padding-top: 24px;
  padding-bottom: 24px;
  overflow-y: auto;
}

.name-item {
  position: relative;
  padding: 4px;
  border-radius: 50%;
  border: 4px solid #835754;
  cursor: pointer;
}

.name-value {
  position: absolute;
  top: -30px;
  left: 50%;
  transform: translateX(-50%);
  white-space: nowrap;
}

.name-label {
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 20px;
  font-weight: bold;
  width: 40px;
  height: 40px;
  background-color: #835754;
  color: white;
  border-radius: 50%;
  white-space: nowrap;
}


/* 弹窗样式 */
.no-family-name-popup {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
}

.popup-content {
  background-color: white;
  padding: 20px;
  border-radius: 8px;
  text-align: center;
}

.popup-content p {
  margin-bottom: 10px;
}

.popup-content button {
  padding: 5px 10px;
  border: none;
  background-color: #f40;
  color: white;
  border-radius: 4px;
  cursor: pointer;
}

.popup-content button:hover {
  background-color: #d30;
}
</style>