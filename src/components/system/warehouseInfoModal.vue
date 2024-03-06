<template>
    <!-- 모달! -->
        <div id="layer1" class="layerPop layerType2" style="width: 600px;">
            <dl>
                <dt>
                <strong>창고 관리</strong>
                </dt>
                <dd class="content">
                    <table class="row">
                        <caption>caption</caption>
                        <colgroup>
                        <col width="120px">
                        <col width="*">
                        <col width="120px">
                        <col width="*">
                        </colgroup>
                        <tbody>
                        <tr>
                            <th scope="row">창고코드<span class="font_red">*</span></th>
                            <td><input type="text" class="inputTxt p100"
                            name="warehouse_cd" id="warehouse_cd" maxlength="20" v-model="warehouse_cd" /></td>
                            <th scope="row">창고명 <span class="font_red">*</span></th>
                            <td><input type="text" class="inputTxt p100"
                            name="warehouse_nm" id="warehouse_nm" maxlength="50" v-model="warehouse_nm"/></td>  
                        </tr>
                        <tr>
                            <th scope="row">담당자ID<span class="font_red">*</span></th>
                            <td>
                              <input type="text" class="inputTxt p100" name="wh_mng_id" id="wh_mng_id" maxlength="50" v-model="wh_mng_id"  />
                            </td>
                            <th scope="row">담당자명</th>
                            <td>
                              <input type="text" class="inputTxt p100" name="wh_mng_nm" id="wh_mng_nm" maxlength="50" v-model="wh_mng_nm"  />
                              <!-- <select id="wh_mng_nm" name="wh_mng_nm" v-model="wh_mng_nm"></select>-->
                            </td>
                        </tr>
                        <tr>
                            <th scope="row">우편번호 <span class="font_red">*</span></th>
                            <td colspan="2"><input type="text" class="inputTxt p100" name="zip_cd" id="zip_cd" maxlength="6" v-model="zip_cd" /></td>
                            <td><input type="button" value="우편번호 찾기" @click="daumPostcode" style="width: 130px; height: 20px;" /></td>
                        </tr>
                        <tr>
                            <th scope="row">주소 <span class="font_red">*</span></th>
                            <td colspan="3"><input type="text" class="inputTxt p100"
                            name="addr" id="addr" maxlength="200" v-model="addr" /></td>
                        </tr>
                        <tr>
                            <th scope="row">상세주소 <span class="font_red">*</span></th>
                            <td colspan="3"><input type="text" class="inputTxt p100"
                            name="addr_detail" id="addr_detail" maxlength="200" v-model="addr_detail"/></td>
                        </tr>

                        </tbody>
                    </table>

                    <div class="btn_areaC mt30">
                        <a href="javascript:void(0)" class="btnType blue" id="btnSaveWarehouse" name="btn" @click="save"><span>저장</span></a>
                        <a href="javascript:void(0)" class="btnType blue" id="btnDeleteWarehouse" name="btn" @click="delete"><span>삭제</span></a>  
                        <a href="javascript:void(0)" class="btnType gray" id="btnCloseWarehouse" name="btn" @click="cancel"><span>취소</span></a>
                    </div>
                </dd>
            </dl>
            <a href="javascript:void(0)" @click="cancel" class="closePop"><span class="hidden">닫기</span></a>
        </div>
</template>


<script>
import { closeModal } from "jenesius-vue-modal";


export default {
  props: {action : String},
    // props: { title: String, noticeId: Number, action: String },
  components : {

  },
  data: function () {
    return {
      action: this.action,
      warehouse_cd: '',
      warehouse_nm: '',
      wh_mng_id : '',
      wh_mng_nm : '',      
      zip_cd : '',
      addr : '',
      addr_detail : '',
    };
  },
  computed: {},
  // html 로딩, 가상 dom 실행, 이 두 개 연결 시 작동
  mounted() {
    // this.warehouse_nm : props.warehouse_nm,
    // this.warehouse_cd : props.warehouse_cd,
    let vm = this;
    // 신규 등록 시
    if (this.action == null || this.noticeNo == "I") {
      vm.warehouse_cd = "";
      vm.warehouse_nm = "";
      vm.wh_mng_id = "";
      vm.wh_mng_nm = "";
      vm.zip_cd = "";
      vm.addr = "";
      vm.addr_detail = "";
    } else {
      //  수정 시 (noticeNo 에 해당하는 상세정보 가져오기)
      let params = new URLSearchParams();
      //alert(this.noticeNo);
      params.append("action", this.action);        
      params.append("warehouse_cd", this.warehouse_cd);
      // params.append("warehouse_nm", this.warehouse_nm);
      // params.append("wh_mng_id", this.wh_mng_id);
      // params.append("wh_mng_nm", this.wh_mng_nm);
      // params.append("zip_cd", this.zip_cd);
      // params.append("addr", this.addr);
      // params.append("addr_detail", this.addr_detail);

      this.axios
      .post("/scm/selectWarehouse.do",params)
      .then(function (response) {
          alert("resultMsg", response.data.resultMsg);
          alert("result", response.data.result);
          alert("warehouseInfoModel", response.data.warehouseInfoModel);
      
      }).catch(function (error){
          alert("에러! API 요청에 오류가 있습니다. (modal) " + error);
      });

      // this.axios
      //   .post("/system/detailNotice.do", params)
      //   .then(function (response) {
      //     console.log(response);

      //     vm.noticeNo = response.data.notice_id;
      //     vm.noticeTitle = response.data.title;
      //     vm.noticeContent = response.data.content;
      //     vm.noticeRegdate = response.data.date;
      //     vm.noticeAuth = response.data.auth;
      //     vm.noticeViewCnt = response.data.view_cnt;
      //     vm.fileName = response.data.file_ofname;
      //     vm.fileRelativePath = response.data.file_relative_path;
      //     vm.delshow = true;
      //   })
      //   .catch(function (error) {
      //     alert("에러! API 요청에 오류가 있습니다. " + error);
      //   });
    }
  },
  methods: {
    daumPostcode: function() {
      new daum.Postcode({
        oncomplete: (data) => {
          this.zip_cd = data.zonecode;

          if (data.userSelectedType === 'R') {
            this.addr = data.roadAddress;
          } else {
            this.addr = data.jibunAddress;
          }
        }
      }).open();
    },
  
    save: function () {
      //alert(this.action);
      if(!this.validateIsNull()) {
        return;
      }

      if (confirm("저장하시겠습니까?")) {
        let vm = this;
        
        let params = new URLSearchParams();

        params.append("action", this.action);        
        params.append("warehouse_cd", this.warehouse_cd);
        params.append("warehouse_nm", this.warehouse_nm);
        params.append("wh_mng_id", this.wh_mng_id);
        params.append("wh_mng_nm", this.wh_mng_nm);
        params.append("zip_cd", this.zip_cd);
        params.append("addr", this.addr);
        params.append("addr_detail", this.addr_detail);
        
        this.axios
        .post("/scm/saveWarehouse.do", params)
        .then(function (response) {
          console.log("save response", response);
          let status = response.status;
          if (status == 200) {
            alert("저장이 완료되었습니다.");
            closeModal(vm);
          } else {
            alert("API 요청에 오류가 있습니다.");
          }
        })
        .catch(error => {
          alert("에러! API 요청에 오류가 있습니다. (save) " + error);
          console.log(error);
        });
        
      }
    },    
    delete: function () {
      if (confirm("정말 삭제하시겠습니까?")) {
        let vm = this;
        let params = new URLSearchParams();
        params.append("notice_id", this.noticeNo);
        params.append("file_no", 0);
        params.append("file_nm", "");

        this.axios
          .post("/system/deleteNotice.do", params)
          .then(function (response) {
            console.log(response);
            //alert("삭제 되었습니다.");
            closeModal(vm);
          })
          .catch(function (error) {
            alert("에러! API 요청에 오류가 있습니다. " + error);
          });
      }
    },
    cancel: function() {
      let vm = this;
      closeModal(vm);
    },
    validateIsNull: function () {
      let chk = this.checkNotEmpty([        
        ["warehouse_cd", "창고코드를  입력해주세요."],
        ["warehouse_nm", "창고명을  입력해주세요."],
        ["wh_mng_id", "담당자ID를  입력해주세요."],
        ["wh_mng_nm", "담당자명을  입력해주세요."],
        ["zip_cd", "우편번호를  입력해주세요."],
        ["addr", "주소를  입력해주세요."],
        ["addr_detail", "상세주소를  입력해주세요."],
      ]);
      return chk;
    },
    checkNotEmpty: function (arr) {
      for (var i = 0, len = arr.length; i < len; i++) {
        var elem = document.getElementById(arr[i][0]);
        console.log("elem is...");
        console.log(elem);
        if (elem.length <= 0) {
          continue;
        }
        var elemValue = elem.value;
        var alertMsg = arr[i][1];

        console.log(elemValue);
        if (elemValue == "") {
          alert(alertMsg);
          elem.focus();
          return false;
        }
      }
      return true;
    }


  }


    
}
</script>

<style scoped>
#layer1 {
  display:flex;
  justify-content:center;
  align-items:center;
}

</style>