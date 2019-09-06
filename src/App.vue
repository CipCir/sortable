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
          v-bind:class="{ first_last: indx==0 }"
        >&#8593;</div>
        <div class="answer">{{answers[finInd]}}</div>
        <div
          @click="moveAnsw(false,indx)"
          class="srtBtn right"
          v-bind:class="{ first_last: indx==finalpos.length-1 }"
        >&#8595;</div>
      </div>
    </div>
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
      animation_delay: 500
    };
  },
  created() {
    // $(".question-controls-container").hide();
    var vueObj = this;
    $(".mrGridCategoryText").each(function(indx) {
      vueObj.answers.push($(this).text());
      vueObj.finalpos.push(indx);
    });
    $(document).ready(function() {
      vueObj.add_vals();
    });
    var gigi='x';
    function everythingReady(){
      // debugger;
      vueObj.add_vals();
      $('body').trigger('fakeReady');
      };
    $(".mrNext").on("click", function(e) {
      vueObj.attempts++;
      if (vueObj.attempts == 1) {
        var errMsg = new OverlayMaster({
          Message: vueObj.dimObj.error_text,
          OkButton: vueObj.dimObj.error_button
        });
        errMsg.show();
        e.preventDefault();
        return false;
      } else {
        // console.log("submit");
      }
    });
    this.resize_ev();
  },
  beforeDestroy: function() {
    window.removeEventListener("resize", this.handleWindowResize);
  },
  mounted() {
    window.addEventListener("resize", this.handleWindowResize);
  },
  methods: {
    handleWindowResize(event) {
this.resize_ev();
    },
    resize_ev(eent){
      // debugger;
      console.log("resize");
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
      //  debugger;
      var vueObj = this;
      $(".mrGridCategoryText").each(function(indx) {
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
      this.attempts++;
      if (isUpInList) {
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
        // debugger
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
  opacity:0.7;

  /* background: #f5f5f5;  */
}
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
