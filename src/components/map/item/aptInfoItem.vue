<template>
  <div class="aptInfo">
    <div class="interested">
      <img
        v-show="isFollow"
        src="@/assets/img/star_yes.png"
        alt=""
        @click="setInterestedLoc('d')"
      />
      <img
        v-show="!isFollow"
        src="@/assets/img/star_no.png"
        alt=""
        @click="setInterestedLoc('r')"
      />
    </div>
    <div class="aptlist" @click="aptDetail">
      <div class="pickimg">
        <img src="@/assets/img/location.png" alt="" />
      </div>
      <div class="apttext">
        <p class="title">{{ apartmentName }}</p>
        <p class="juso">{{ juso }}</p>
        <p class="money">{{ min | eok }}억 ~ {{ max | eok }}억</p>
      </div>
    </div>
  </div>
</template>

<script>
import { deleteInterestedAptList, registInterestedAptList } from "@/api/apt.js";
import { mapState, mapActions } from "vuex";
const memberStore = "memberStore";
const aptStore = "aptStore";

export default {
  name: "aptInfoItem",
  data() {
    return {
      isFollow: false,
    };
  },
  props: {
    aptCode: Number,
    apartmentName: String,
    juso: String,
    min: String,
    max: String,
  },
  computed: {
    ...mapState(memberStore, ["userInfo"]),
    ...mapState(aptStore, ["apts", "followapts"]),
  },
  created() {
    this.setImg();
  },
  watch: {
    apts() {
      this.setImg();
    },

    followapts() {
      this.setImg();
    },
  },
  methods: {
    ...mapActions(aptStore, ["detailApt", "getFollowAptList"]),
    aptDetail() {
      var aptDt = {
        apartmentName: this.apartmentName,
        juso: this.juso,
        aptCode: this.aptCode,
      };
      this.detailApt(aptDt);
    },
    setInterestedLoc(type) {
      var params = {
        aptCode: this.aptCode,
        memberId: this.userInfo.id,
      };
      if (type == "d") {
        deleteInterestedAptList(
          params,
          () => {
            this.getFollowAptList(params);
          },
          (error) => {
            console.log(error);
          },
        );
        this.isFollow = false;
      } else if (type == "r") {
        registInterestedAptList(
          params,
          () => {
            this.getFollowAptList(params);
          },
          (error) => {
            console.log(error);
          },
        );
        this.isFollow = true;
      }
    },
    setImg() {
      for (let apt of this.followapts) {
        if (apt.aptCode == this.aptCode) {
          this.isFollow = true;
          break;
        } else {
          this.isFollow = false;
        }
      }
    },
  },
  filters: {
    eok(val) {
      if (val.length > 4)
        return (
          val.substring(0, val.length - 4) +
          "." +
          val.substring(val.length - 4, val.length - 3)
        );
      else return "0." + val.substring(0, 1);
    },
  },
};
</script>

<style scoped>
.aptInfo {
  position: relative;
  align-items: center;
}
.pickimg {
  width: 60px;
  height: 60px;
  background-color: #fbf7ef;
  border-radius: 30px;
  margin-right: 15px;
  display: flex;
  flex-shrink: 0;
  justify-content: center;
  align-items: center;
}
.pickimg img {
  text-align: center;
  width: 30px;
}
.interested {
  position: absolute;
  top: 10px;
  right: 0;
  z-index: 3;
}
.interested img {
  width: 25px;
  height: 25px;
}
.apttext {
  width: 240px;
  padding: 30px 0 5px;
}
.apttext .title {
  font-size: 18px;
  font-weight: bold;
  margin-bottom: 10px;
}
.juso {
  font-size: 14px;
  color: gray;
  margin-bottom: 5px;
  padding-left: 10px;
}
.money {
  font-size: 14px;
  color: gray;
  padding-left: 10px;
}
.aptlist {
  display: flex;
  padding: 10px;
  border-bottom: 1px solid lightgray;
  position: relative;
  align-items: center;
}
</style>
