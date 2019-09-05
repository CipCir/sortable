<template>
  <div id="app">
    <div class="answerContainer">
      <div
        class="answerRow"
        v-for="(finInd,indx) in finalpos"
        :key="indx"
        v-bind:style="{ 'min-height': maxh+'px' }"
      >
        <div @click="moveAnsw(true,indx)" class="srtBtn left"  v-bind:class="{ first_last: indx==0 }">&#8593;</div>
        <div class="answer">{{answers[finInd]}}</div>
        <div @click="moveAnsw(false,indx)" class="srtBtn right" v-bind:class="{ first_last: indx==finalpos.length-1 }">&#8595;</div>
      </div>
    </div>
  </div>
</template> 

<script>
export default {
  name: "app",
  data() {
    return {
      answers: [],
      finalpos: [],
      attempts: 0,
      error_text: "Errors-MissingAnswer",
      error_button: "X",
      maxh: 40
    };
  },
  created() {
    $(".question-controls-container").hide();
    var vueObj = this;
    $(".mrGridCategoryText").each(function(indx) {
      vueObj.answers.push($(this).text());
      vueObj.finalpos.push(indx);
    });
    $(document).ready(function() {
      vueObj.add_vals();
    });
    $(".mrNext").on("click", function(e) {
      vueObj.attempts++;
      if (vueObj.attempts == 1) {
        var errMsg = new OverlayMaster({
          Message: vueObj.error_text,
          OkButton: vueObj.error_button
        });
        errMsg.show();
        e.preventDefault();
        return false;
      } else {
        // console.log("submit");
      }
    });
  },
  beforeDestroy: function() {
    window.removeEventListener("resize", this.handleWindowResize);
  },
  mounted() {
    window.addEventListener("resize", this.handleWindowResize);
  },
  methods: {
    handleWindowResize(event) {
      // debugger;
      // console.log("resize");
      // console.log(this.maxh); 
      // var vueOBJ=this;
      // this.maxh=0;
      var maximum=0;
        $(".answerRow").each(function(indx) {
         if (this.offsetHeight>maximum) {
             maximum=this.offsetHeight;
           }
      });
      // console.log(this.maxh, maximum);
      this.maxh=maximum;
    },
    add_vals() {
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
    moveAnsw(isUpInList, currentPos) {
      this.attempts++;
      if (isUpInList) {
        if (currentPos > 0) {
          // console.log('up in list',currentPos, currentPos-1);
          var temp_val = this.finalpos[currentPos];
          // this.finalpos[currentPos]=this.finalpos[currentPos-1];
          this.$set(this.finalpos, currentPos, this.finalpos[currentPos - 1]);
          this.update_vals(currentPos, this.finalpos[currentPos - 1]);
          // this.finalpos[currentPos-1]=temp_val;
          this.$set(this.finalpos, currentPos - 1, temp_val);
          this.update_vals(currentPos - 1, temp_val);
        } else {
          // console.log("cannot move up first elem");
        }
      } else {
        // debugger
        if (currentPos < this.finalpos.length - 1) {
          // console.log('up in list',currentPos, currentPos+1);
          var temp_val = this.finalpos[currentPos];
          // this.finalpos[currentPos]=this.finalpos[currentPos-1];
          this.$set(this.finalpos, currentPos, this.finalpos[currentPos + 1]);
          this.update_vals(currentPos, this.finalpos[currentPos + 1]);
          // this.finalpos[currentPos-1]=temp_val;
          this.$set(this.finalpos, currentPos + 1, temp_val);
          this.update_vals(currentPos + 1, temp_val);
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
.first_last{
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
  /* background: lightgrey; */
  /* margin: 0 10px;
  width: 30px;*/
  text-align: center;
}
#app {
  color: black;
  text-align: center;
  font-family: robotolight,Arial,sans-serif;
  
}
</style>
