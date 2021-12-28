<template>
  <div id="survey">
    <div class="inner">
      <div class="questionBox"
        :class="{hidden : showResultDataNo != -1}"
      >
        <ul class="listBox" ref="listBox">
          <li 
            v-for="(question, questionIdx) in questionList"
            :key="`questionList-${questionIdx}`"
            :class="{active : showQuestionNo == question.key}"
          >
            <div class="titleBox">
              <h2 v-html="question.question"></h2>
            </div>
            <div class="answerBox">
              <button type="button"
                v-for="(answer, answerIdx) in question.answer"
                :key="`answer-${questionIdx}-${answerIdx}`"
                @click="onClickAnswer(question.key, answer, answerIdx)"
                :class="{active : answer.isSelected}"
              >
                {{answer.title}}
              </button>
            </div>
          </li>
        </ul>
        <div class="stepBox"
          :class="'step' + stepCount"
        >
          <ul>
            <li
              v-for="(step, stepIdx) in 4"
              :key="`step-${stepIdx}`"
            ></li>
          </ul>
          <div class="stepBar"></div>
        </div>
      </div>

      <div class="resultBox"
        :class="{active : showResultDataNo != -1}"
      >
        <h2>시뮬레이션 결과</h2>
        <div
          v-for="(result, resultIdx) in resultDataList"
          :key="`resultDataList-${resultIdx}`"
          :id="`result${resultIdx+1}`"
          :class="{active : showResultDataNo == result.key}"
        >
          <div class="inner">
            <div class="visual"><h3 v-html="resultDataList[resultIdx].title"></h3></div>
            <ul class="noticeList">
              <li
                v-for="(text, textIndex) in result.text"
                :key="`text-${textIndex}`"
              >
                <p v-html="text.tit"></p>
                <ul>
                  <li
                    v-for="(txt, index) in text.txt"
                    :key="index"
                    v-html="txt"
                  >
                  </li>
                </ul>
              </li>
            </ul>
          </div>
        </div>
      </div>

      <div class="btnBox">
        <button type="button"
          v-if="showResultDataNo > 0 "
          @click="resetSurvey()"
        >
          처음으로
        </button>
        <button type="button"
          class="btnPrev"
          v-if="stepCount > 1 && showResultDataNo == -1"
          @click="onClickPrev()"
        >
          이전
        </button>
      </div>
      <PopupTabMenu />
    </div>
  </div>
</template>
<script>
import PopupTabMenu from "@/components/PopupTabMenu.vue";

export default {
  components: {
    PopupTabMenu
  },
  data(){
    return{
      history : [],         // 선택한 설문 저장 이전 설문으로 돌아갈 때 사용
      showQuestionNo:1,     // 
      showResultDataNo:-1,
      questionList:[
        {
          key:1,
          question : "<strong>당뇨, 고혈압, 고지혈증</strong> 등<br>만성 질환을 보유하고 계십니까?",
          answer: [
            {title:"네",gotoQuestion:2, resultPage:-1, isSelected:false},
            {title:"아니오",gotoQuestion:-1, resultPage:1, isSelected:false}
          ]
        },
        {
          key:2,
          question : "3개월 이내에 <strong>의료행위<br>(입원, 수술, 재검사필요소견)</strong>를<br>받은 적이 있었습니까?</span>",
          answer: [
            {title:"네",gotoQuestion:-1, resultPage:2, isSelected:false},
            {title:"아니오",gotoQuestion:3, resultPage:-1, isSelected:false}
          ]
        },
        {
          key:3,
          question : "2년 이내에 <strong>입원, 수술<br>(제왕절개 포함)</strong> 경험이 있었습니까?",
          answer: [
            {title:"네",gotoQuestion:4, resultPage:-1, isSelected:false},
            {title:"아니오",gotoQuestion:5, resultPage:-1, isSelected:false}
          ]
        },
        {
          key:4,
          question : "5년 이내에 <strong>암, 협심증, 심근경색,<br>뇌졸중</strong> 진단을 받은 적이 있었습니까?",
          answer: [
            {title:"네",gotoQuestion:-1, resultPage:2, isSelected:false},
            {title:"아니오",gotoQuestion:-1, resultPage:3, isSelected:false}
          ]
        },
        {
          key:5,
          question : "5년 이내에 <strong>암, 간경화, 투석, 파킨슨,<br>루게릭</strong> 진단을 받은 적이 있었습니까?",
          answer: [
            {title:"네",gotoQuestion:-1, resultPage:2, isSelected:false},
            {title:"아니오",gotoQuestion:-1, resultPage:4, isSelected:false}
          ]
        },
      ],
      resultDataList: [
        {
          key : 1,
          title:"최근에 병원을 방문한 이력이 없으시면<br><strong>일반 보험</strong>으로 심사 받아보세요.",
        },
        {
          key : 2,
          title:"고객님의 <strong>자세한 건강 정보</strong>로 심사가<br>필요합니다."
        },
        {
          key : 3,
          title:"만성질환이 있어도 초간편보험<br><strong>(무)교보초간편가입건강보험(갱신형)</strong><br>가입이 가능합니다.",
          text: [
            {tit:'고지 항목 축소로 가입대상 확대', txt:['3.5 고지']},
            {tit:'<stonrg>3대 질병 중심</strong> 다양한 <strong>특약</strong>', txt:['28종<br>(중증 유병자 위한 보장 강화)']},
            {tit:'갱신 보장 개발로 부담 없는<br><strong>보험료</strong>'},
          ]
        },
        {
          key : 4,
          title:"질환이나 병력이 있어도 간편보험<br><strong>(무)교보간편가입건강보험</strong> 가입이<br>가능합니다.",
          text: [
            {tit:'고지 항목 3개로 완화된 보험 가입', txt:['3.2.5 고지']},
            {tit:'<strong>다양한 특약</strong> 부과로 맞춤설계 우수', txt:['51종<br>(갱신 37종 + 비갱신 14종)']},
            {tit:'경쟁력 있는 <strong>보장</strong>', txt: ['녹내장, 관절염 수술','넓은 납입면제 범위<br>(재해장해 50% + 3대 질병 진단)']},
          ]
        },
        
      ],
      stepCount:1,
      flag:false,
    }
  },
  methods:{
    /* 답변 선택 */
    onClickAnswer(questionKey, answer, answerIdx) {
      // 진행중, 결과 페이지 일 때 클릭 방지
      if(this.flag){return}
      this.flag = true;

      // 버튼 class추가, 제거
      var q = Array.prototype.slice.call(this.questionList).filter(function(d){
        return d.key == questionKey
      });
      q[0].answer[answerIdx].isSelected = true;

      this.history.push(questionKey);
      this.stepCount = this.history.length+1;
      // step 최대값 지정
      if(this.stepCount > this.resultDataList.length){this.stepCount = this.resultDataList.length}

      // stepCount가 3보다 작을 때 결과로 이동 시
      if(answer.resultPage == 1 || answer.resultPage == 2){this.stepCount = 4}
      
      setTimeout(() => {
        var isGoToResultPage = answer.resultPage > 0;
        var isGoToQuestion = answer.gotoQuestion > 0;
        this.showQuestionNo = isGoToQuestion ? answer.gotoQuestion : this.showQuestionNo;
        this.showResultDataNo = isGoToResultPage ? answer.resultPage : -1;
        this.flag = false;
      }, 500);
    },
    /* 이전 */
    onClickPrev() {
      var prevNo = this.history[this.history.length-1];

      if(prevNo) {
        this.showResultDataNo = -1;
        this.showQuestionNo = prevNo;
        this.history = this.history.slice(0, -1);
        this.stepCount--;
        this.resetBtnClass();
      }
    },
    // 답변 버튼 초기화
    resetBtnClass(){
      // class 초기화
      var resetBtnClass = this.$refs.listBox.querySelectorAll('button');
      resetBtnClass.forEach(d => {d.classList.remove('active')});

      Array.prototype.forEach.call(this.questionList, el => {
        el.answer.forEach(q => {q.isSelected = false});
      });
      
    },
    // 전체 초기화
    resetSurvey(){
      this.resetBtnClass();
      this.history = [];
      this.stepCount = 1;
      this.showQuestionNo = 1;
      this.showResultDataNo = -1;
    }
  },
}
</script>
