<template>
  <div id="popupTabMenu">
    <div class="inner">
      <ul class="tabTitle">
        <li
          v-for="(tabTitle, tabTitleIdx) in tabDataList"
          :key="`tabTitle-${tabTitleIdx}`"
          @click="showPopup(tabTitleIdx)"
        >
          <span>{{tabTitle.title}}</span>
        </li>
      </ul>
      <transition name="fadeToggle" mode="out-in">
        <div class="tabContents"
          v-if="isTabShow"
        >
          <div>
            <div
              v-for="(tabContent, tabContentIdx) in tabDataList"
              :key="`tabContent-${tabContentIdx}`"
              :class="{active : tabContentNum == tabContentIdx}"
            >
              <img :src="`${tabContent.content}`" alt="">
            </div>
            <button type="button" @click="popupToggle()">닫기</button>
          </div>
        </div>
      </transition>
    </div>
  </div>
</template>
<script>
export default {
  data(){
    return {
      tabDataList: [
        {
          title: '만성질환이란?',
          content: require('@/assets/images/img_popup_01.png')
        },
        {
          title: '만성질환 관리',
          content: require('@/assets/images/img_popup_02.png')
        },
        {
          title: '추천대상',
          content: require('@/assets/images/img_popup_03.png')
        },
        {
          title: '유의사항',
          content: require('@/assets/images/img_popup_04.png')
        }
      ],
      tabContentNum:0,
      isTabShow:false,
    }
  },
  methods:{
    showPopup(index){
      this.tabContentNum = index;
      this.popupToggle();
    },
    popupToggle(){
      this.isTabShow = !this.isTabShow;
    }
  }
}
</script>