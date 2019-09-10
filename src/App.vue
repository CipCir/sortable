<template>
  <div id="app">
    <div class="answerContainer">
      <div
        class="answerRow"
        v-for="(finInd,indx) in finalpos"
        :key="indx"
        v-bind:style="{ 'min-height': maxh+'px' }"
        v-bind:class="{ move_first: move_first==indx, move_second: move_second==indx }"
      >
        <div
          @click="moveAnsw(true,indx)"
          class="srtBtn left"
          v-bind:class="{ first_last: (indx==0)||(indx==finalpos.length-1) }"
        >&#8593;</div>
        <div class="answer">{{answers[finInd]}}</div>
        <div
          @click="moveAnsw(false,indx)"
          class="srtBtn right"
          v-bind:class="{ first_last: (indx==finalpos.length-2||indx==finalpos.length-1) }"
        >&#8595;</div>
      </div>
    </div>
    <button
      type="button"
      id="fakeNext"
      class="mrNext"
      @click="next_click()"
      v-html="dimObj.text_next"
    ></button>
  </div>
</template> 

<script>
export default {
  name: "app",
  data() {
    return {
      dimObj,
      answers: [],
      finalpos: [],
      attempts: 0,
      maxh: 40,
      move_first: -1,
      move_second: -1,
      animation_delay: 500,
      warning_displayed: false
    };
  },
  created() {
    var vueObj = this;
    $(".mrGridCategoryText").each(function(indx) {
      vueObj.answers.push($(this).text());
      vueObj.finalpos.push(indx);
    });

    this.add_vals();
    this.resize_ev();
  },
  beforeDestroy: function() {
    window.removeEventListener("resize", this.handleWindowResize);
  },
  mounted() {
    window.addEventListener("resize", this.handleWindowResize);
  },
  methods: {
    next_click() {
      let vueObj=this;
        vueObj.attempts++;
        // debugger;
        if (vueObj.attempts == 1) {
          var errMsg = new OverlayMaster({
            Message: vueObj.dimObj.error_text,
            OkButton: vueObj.dimObj.error_button
          });
          errMsg.show();
          vueObj.warning_displayed=true;
          vueObj.add_vals();
        } else {
          // console.log("submit");
          if (vueObj.attempts == 2 && vueObj.warning_displayed) {
            $('#_Q1_C2').prop('checked','checked')
          }else if (vueObj.attempts > 2 && vueObj.warning_displayed){
            $('#_Q1_C1').prop('checked','checked')
          }else{
            $('#_Q1_C0').prop('checked','checked')
          }
          $('#mrForm').submit();
        }
      
    },
    handleWindowResize(event) {
      this.resize_ev();
    },
    resize_ev(event) {
      //debugger;
      // console.log(this.maxh);
      // var vueOBJ=this;
      // this.maxh=0;
      var maximum = 0;
      $(".answerRow").each(function(indx) {
        if (this.offsetHeight > maximum) {
          maximum = this.offsetHeight;
        }
      });
      // console.log(this.maxh, maximum);
      this.maxh = maximum;
    },
    add_vals() {
      // debugger;
      // console.log("addvals");
      var vueObj = this;
      $(".mrGridCategoryText").each(function(indx) {
        // console.log(indx)
        $(".mrEdit")
          .eq(indx * 2)
          .val(indx);
        $(".mrEdit")
          .eq(indx * 2 + 1)
          .val(vueObj.finalpos[indx]);
      });
    },
    update_vals(indx, new_val) {
      $(".mrEdit")
        .eq(indx * 2 + 1)
        .val(new_val);
    },
    animate_switch(pos1, pos2) {
      // console.log("start");
      this.move_first = pos1;
      this.move_second = pos2;
      var vueOBJ = this;
      setTimeout(function() {
        // console.log("end");
        vueOBJ.move_first = pos2;
        vueOBJ.move_second = pos1;
      }, vueOBJ.animation_delay);
      setTimeout(function() {
        // console.log("end");
        vueOBJ.move_first = -1;
        vueOBJ.move_second = -1;
      }, 2 * vueOBJ.animation_delay);
    },
    moveAnsw(isUpInList, currentPos) {
      if (this.attempts == 0) this.add_vals();
      this.attempts++;
      if (isUpInList) {
        // console.log('up att');
        if (currentPos == this.finalpos.length-1){
          // console.log('last clicked for up');
          return false;
        }
        if (currentPos > 0) {
          this.animate_switch(currentPos, currentPos - 1);
          var vueOBJ = this;
          setTimeout(function() {
            // console.log('up in list',currentPos, currentPos-1);
            var temp_val = vueOBJ.finalpos[currentPos];
            // this.finalpos[currentPos]=this.finalpos[currentPos-1];
            vueOBJ.$set(
              vueOBJ.finalpos,
              currentPos,
              vueOBJ.finalpos[currentPos - 1]
            );
            vueOBJ.update_vals(currentPos, vueOBJ.finalpos[currentPos - 1]);
            // this.finalpos[currentPos-1]=temp_val;
            vueOBJ.$set(vueOBJ.finalpos, currentPos - 1, temp_val);
            vueOBJ.update_vals(currentPos - 1, temp_val);
          }, vueOBJ.animation_delay);
        } else {
          // console.log("cannot move up first elem");
        }
      } else {
        // console.log('down att');
        if (currentPos == this.finalpos.length-2){
          // console.log('before last clicked for down');
          return false;
        }
        var vueOBJ = this;
        if (currentPos < this.finalpos.length - 1) {
          this.animate_switch(currentPos, currentPos + 1);
          setTimeout(function() {
            // console.log('up in list',currentPos, currentPos+1);
            var temp_val = vueOBJ.finalpos[currentPos];
            // this.finalpos[currentPos]=this.finalpos[currentPos-1];
            vueOBJ.$set(
              vueOBJ.finalpos,
              currentPos,
              vueOBJ.finalpos[currentPos + 1]
            );
            vueOBJ.update_vals(currentPos, vueOBJ.finalpos[currentPos + 1]);
            // this.finalpos[currentPos-1]=temp_val;
            vueOBJ.$set(vueOBJ.finalpos, currentPos + 1, temp_val);
            vueOBJ.update_vals(currentPos + 1, temp_val);
          }, vueOBJ.animation_delay);
        } else {
          // console.log("cannot move down last elem");
        }
      }
    }
  }
};
</script>
<style >
#nav-controls .mrNext {
  display: none !important;
}
.question-controls-container{
  display: none !important;
}
</style>
<style scoped>
.answerRow {
  /* background: #aaa; */
  display: flex;
  flex-wrap: wrap;
  align-items: center;
  justify-content: center;
}
.answerRow > .srtBtn {
  flex-grow: 1;
  /* flex: 1 1 30%; */
}
.answerRow > .answer {
  flex-grow: 1;
  /* flex: 1 1 30%; */
}
.srtBtn {
  /* background-color: #fff4e6; */
  /* border: 1px solid #f76707; */
  font-size: 30px;
  background-color: #49bfbc;
  color: white;
}
.first_last {
  background-color: #aaa;
}
.answer {
  display: inline-flex;
  /* background-color: bisque; */
  justify-content: center;
  text-align: center;
  width: 81%;
  max-width: 600px;
}
.answerRow {
  border: 2px solid #42bcb9;
  border-radius: 5px;
  margin: 5px;
}
.answerContainer {
  max-width: 750px;
  width: 75%;
  margin: 0 auto;
  text-align: left;
}
@media only screen and (max-width: 600px) {
  .answerContainer {
    width: 100%;
  }
}

.srtBtn {
  cursor: pointer;
  text-align: center;
}
#app {
  color: black;
  text-align: center;
  font-family: robotolight, Arial, sans-serif;

  -webkit-touch-callout: none;
  -webkit-user-select: none;
  -khtml-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;
}
.move_first {
  /* border-color: #0cb2b052;
  background: #0cb2b052; */
  border-color: rgba(12, 178, 175, 0.5);
  background: rgba(12, 178, 175, 0.5);
}
.move_second {
  /* border-color: #4b64ae52;
  background: #4b64ae52; */
  border-color: rgba(75, 100, 174, 0.5);
  background: rgba(75, 100, 174, 0.5);
}
.move_first,
.move_second {
  /* color: white; */
  font-weight: bold;
  -moz-transition: all 0.1s ease-in-out;
  -o-transition: all 0.1s ease-in-out;
  -webkit-transition: all 0.1s ease-in-out;
  transition: all 0.1s ease-in-out;
  opacity: 0.7;
}
#fakeNext {
  padding: 0.5em 0;
  min-width: 100px;
  width: 20%;
  margin-top: 30px;
}
/* background: #f5f5f5;  */

/* .move_first{
  border-color: red;
  background-color: white;
    animation: mover1 1s  alternate;
     -moz-animation: mover1 1s  alternate;
    -webkit-animation: mover1 1s  alternate;
}
@-webkit-keyframes mover1 {
    0% { transform: translateY(0); }
    50% {color: transparent;}
    100% { transform: translateY(-48px); color: black; }
 }

.move_second{
  border-color: blue;
  background-color: white;
    animation: mover2 1s alternate;
}

@-webkit-keyframes mover2 {
    0% { transform: translateY(0); }
     50% {color: transparent;}
    100% { transform: translateY(48px); color: black; }
 } */
</style>
