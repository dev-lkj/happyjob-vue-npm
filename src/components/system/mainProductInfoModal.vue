<template>
  <!-- 모달! -->
  <div id="layer1" class="layerPop layerType2" style="width: 1300px;">
    <dl>
      <dt>
        <strong>제품정보 관리</strong>
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
              <th scope="row">제품 이미지 <span class="font_red">*</span></th>
              
              <th scope="row" width="100px">제품코드 <span class="font_red">*</span></th>
              <td>
                <input type="text" class="inputTxt p100" name="product_cd" id="product_cd" maxlength="20" placeholder="제품코드" v-model="product_cd" :readonly="readonly"/>  
              </td>
              <th scope="row" width="100px">제품명 <span class="font_red">*</span></th>
              <td colspan="3">
                  <input type="text" class="inputTxt p100" name="prod_nm" id="prod_nm" maxlength="100" placeholder="제품명" v-model="prod_nm" :readonly="readonly" />
              </td>
            </tr>
            <tr>              
              <td rowspan="4" style="text-align:center; width:300px; height:300px;">
                <label for="tempImg">
                <img id="tempImg" :src="thumb_nail" style="object-fit: cover; max-width:100%; max-height:100%;" alt="제품사진미리보기">
                </label>
              </td>   
              
              <th scope="row">품목명<span class="font_red">*</span></th>
              <!-- <td width="40" height="25" style="font-size: 100%">상품 대분류&nbsp;</td> -->
              <td>
                <input type="text" name="l_ct_nm" id="l_ct_nm" v-model="l_ct_nm" v-if="l_ct_nm && action =='V'" :readonly="readonly" />
                <select id="l_ct_cd" name="l_ct_cd" v-model="l_ct_cd" v-if="action!=='V'">
                  <option :value="item" v-for="item in uniqueLctnmList" :key="item" :selected="item == l_ct_cd">{{item}}</option>
                  
                </select>
              </td>
                
              <th scope="row">상호명<span class="font_red">*</span></th>
              <!-- <td width="40" height="25" style="font-size: 100%">상품 중분류&nbsp;</td> -->
              <td> 
                <input type="text" name="m_ct_nm" id="m_ct_nm" v-model="m_ct_nm" v-if="m_ct_nm && action =='V'" :readonly="readonly"/>
                <select id="m_ct_cd" name="m_ct_cd" v-model="m_ct_cd" v-if="action!=='V'">
                  <option :value="item" v-for="item in uniqueMctnmList" :key="item" :selected="item == m_ct_cd">{{item}}</option>
                </select>
              </td>                  
              <th scope="row">공급처명 <span class="font_red">*</span></th>
              <td>
                <input type="text" name="supply_nm" id="supply_nm" v-model="supply_nm" v-if="supply_nm && action =='V' " :readonly="readonly"/>
                <select id="supply_cd" name="supply_cd" v-model="supply_cd" v-if="action!=='V'" >
                  <option :value="item" v-for="item in uniqueSupplynmList" :key="item" :selected="item == supply_cd" >{{item}}</option>
                </select>
              </td>                  
              </tr>
              <tr>
              <th scope="row">장비구매액(원)/EA <span class="font_red">*</span></th>
              <td>
                <input type="text" class="inputTxt p100" name="purchase_price" id="purchase_price" maxlength="11" placeholder="장비구매액" v-model="purchase_price" :readonly="readonly"/>
              </td>
              <th scope="row">단가(원)/EA<span class="font_red">*</span></th>
              <td colspan="3">
                <input type="text" class="inputTxt p100" name="price" id="price" maxlength="11" placeholder="단가" v-model="price" :readonly="readonly"/>
              </td>
            </tr>
            <tr> 
              <th scope="row">창고명 <span class="font_red">*</span></th>
              <td>
                <input type="hidden" name="warehouse_cd" id="warehouse_cd" v-model="warehouse_cd" :readonly="readonly" />
                <input type="text" class="inputTxt p100" name="warehouse_cd" id="warehouse_nm" v-model="warehouse_cd" placeholder="창고명"  :readonly="readonly"/>
              </td>
              <th scope="row">재고개수(EA)<span class="font_red">*</span></th>
              <td colspan="3">
                <input type="text" class="inputTxt p100" name="stock" id="stock" maxlength="11" placeholder="재고개수" v-model="stock" :readonly="readonly" />
              </td>
            </tr>
            <tr>
              <th rowspan="2" scope="row">상세정보 <span class="font_red">*</span></th>
              <td rowspan="2" colspan = "5">
                <textarea class = "ui-widget ui-widget-content ui-corner-all" id="detail" maxlength="500" name="detail" 
                  style="height:130px; outline:none; resize:none;" placeholder="여기에 상세정보를 적어주세요.(500자 이내)" v-model="detail" :readonly="readonly"></textarea>
              </td>
            </tr>
            <tr>

            <td class="thumb"  >
              <span v-if="action !='V' || file_status == 'D'"> 
                <input name="file" type="file" ref="attachImage" @change="handleFileChange" id="file" accept="image/* " required/>
                </span>
                
              

            <span class="thumb"  v-if="action !='V' && file_status != 'D' && file_no != 0" >
              
                <input type="text" class="" readonly v-model="thumb_nail"/>&nbsp;
                &nbsp;
                <a class="" id="download" :href="thumb_nail" download>
                    <button class="">다운로드</button>
                </a>
                <a class="mx-2" href="javascript:void(0)" @click="deleteFile()" >
                    <button class="">삭제</button>
                </a>
              </span>
              </td>
            </tr>
          </tbody>
        </table>
        
        
        <div class="btn_areaC mt30">
          <a href="javascript:void(0)" class="btnType blue" id="btnSaveMainProduct" name="btn" @click="save()" v-if="action == 'I' || action =='U'"><span>저장</span></a>
          <a href="javascript:void(0)" class="btnType blue" id="btnDeleteMainProduct" name="btn" @click="deleteBtn()" v-if="action == 'U'"><span>삭제</span></a>   
          <a href="javascript:void(0)" class="btnType gray" id="btnCloseMainProduct" @click="cancel()" name="btn"><span>닫기</span></a>
        </div>
        
      </dd>
    </dl>
    <a href="javascript:void(0)" class="closePop" @click="cancel()"><span class="hidden">닫기</span></a>
  </div>
</template>

<script>
import { closeModal } from "jenesius-vue-modal";

export default {
props: {action_parent : String, product_cd_parent : String},
data: function () {
  return {
    action: this.action_parent || 'I',
    l_ct_cd: '',
    l_ct_nm: '',
    product_cd: this.product_cd_parent || '',
    m_ct_cd: '',
    m_ct_nm: '',
    supply_cd: '',
    supply_nm: '',
    warehouse_cd : '',
    warehouse_nm : '',
    prod_nm : '',
    detail : '',      
    purchase_price : '',
    price : '',
    stock : '',
    uniqueLctnmList: [],
    uniqueMctnmList: [],
    uniqueSupplynmList: [],
    readonly: false,
    
    file_status : '',
    file: '',
    thumb_nail: require('@/assets/images/admin/comm/no_image.png'),
    file_no: '',
    file_local_path: '',
    file_relative_path: '',
    file_ofname: '',
    file_size: '',      
    
  };
},
created() {
  
  let vm = this;

  let params = new URLSearchParams();
  params.append("action", "I");        
  params.append("product_cd", this.product_cd);
  
  this.axios
  .post("/scm/selectSubProduct.do",params)
  .then(function (response){    
    const mainProductInfoModal = response.data.mainProductInfoModel;
    vm.uniqueLctnmList = [...new Set(mainProductInfoModal.map(item => item.l_ct_cd))];
    vm.uniqueMctnmList = [...new Set(mainProductInfoModal.map(item => item.m_ct_cd))];
    vm.uniqueSupplynmList = [...new Set(mainProductInfoModal.map(item => item.supply_cd))];
  }).catch(function (error){
      alert("에러! API 요청에 오류가 있습니다. (modalSelectCR) " + error);
  });

  // 신규 등록 시
  if (this.action == null || this.action == "I") {
    vm.product_cd = "";
    vm.m_ct_cd = "";
    vm.supply_cd = "";
    vm.warehouse_cd = "";
    vm.prod_nm = "";
    vm.detail = "";
    vm.purchase_price = "";
    vm.price = "";
    vm.stock = "";

    vm.l_ct_cd = "";
    vm.l_ct_nm = "";
    vm.m_ct_nm = "";
    vm.supply_nm = "";
    vm.warehouse_nm = "";
    vm.file_reative_path = "";
    vm.file_no = "";
    vm.file_local_path = "";
    vm.file_ofname = "";
    vm.file_size = "";       

    vm.uniqueLctnmList = "";
    vm.uniqueMctnmList = "";
    vm.uniqueSupplynmList = "";

    let params = new URLSearchParams();
    params.append("action", this.action);        
    params.append("product_cd", this.product_cd);

    this.axios
    .post("/scm/selectSubProduct.do",params)
    .then(function (response){     
      const mainProductInfoModal = response.data.mainProductInfoModel;
      // alert(mainProductInfoModal.l_ct_nm);
      vm.uniqueLctnmList = [...new Set(mainProductInfoModal.map(item => item.l_ct_cd))];
      vm.uniqueMctnmList = [...new Set(mainProductInfoModal.map(item => item.m_ct_cd))];
      vm.uniqueSupplynmList = [...new Set(mainProductInfoModal.map(item => item.supply_cd))];
    }).catch(function (error){
        alert("에러! API 요청에 오류가 있습니다. (modalSelectI) " + error);
    });

  } else 
  if(this.action == "V") {
    // 보기용 (product_cd 에 해당하는 상세정보 가져오기)
    let params = new URLSearchParams();
    params.append("action", this.action);        
    params.append("product_cd", this.product_cd);

    this.axios
    .post("/scm/mainProductModal.do",params)
    .then(function (response) {
      const mainProductInfoModal = response.data.mainProductModalModel;
      alert(JSON.stringify(mainProductInfoModal));
      // alert(mainProductInfoModal.supply_cd);
        vm.readonly="true";
        vm.product_cd = mainProductInfoModal.product_cd;
        vm.l_ct_cd = mainProductInfoModal.l_ct_cd;
        vm.m_ct_cd = mainProductInfoModal.m_ct_cd;
        vm.supply_cd = mainProductInfoModal.supply_cd;
        // vm.supply_cd = mainProductInfoModal.supply_nm;
        vm.warehouse_cd = mainProductInfoModal.warehouse_cd;
        vm.prod_nm = mainProductInfoModal.prod_nm;
        vm.detail = mainProductInfoModal.detail;
        vm.purchase_price = mainProductInfoModal.purchase_price;
        vm.price = mainProductInfoModal.price;
        vm.stock = mainProductInfoModal.stock;
        if(mainProductInfoModal.file_no != null) {
        vm.file_reative_path = mainProductInfoModal.file_reative_path;
        vm.file_no = mainProductInfoModal.file_no ? parseInt(mainProductInfoModal.file_no) : 0;
        vm.file_local_path = mainProductInfoModal.file_local_path;
        vm.file_ofname = mainProductInfoModal.file_ofname;
        vm.file_size = mainProductInfoModal.file_size;  
        vm.thumb_nail = require("@/assets/images/scm/product/" + vm.file_no + "/" + vm.file_ofname);     
        }

        vm.l_ct_nm = mainProductInfoModal.l_ct_nm;
        vm.m_ct_nm = mainProductInfoModal.m_ct_nm;
        vm.supply_nm = mainProductInfoModal.supply_nm;
        vm.warehouse_nm = mainProductInfoModal.warehouse_nm;
    }).catch(function (error){
        alert("에러! API 요청에 오류가 있습니다. (modalSelectV) " + error);
    });
  }else {
    //  수정 시 (product_cd 에 해당하는 상세정보 가져오기)
    let params = new URLSearchParams();
    params.append("action", this.action);        
    params.append("product_cd", this.product_cd);

    this.axios
    .post("/scm/mainProductModal.do",params)
    .then(function (response) {
      const mainProductInfoModal = response.data.mainProductModalModel;
      alert(JSON.stringify(mainProductInfoModal));
      
        vm.product_cd = mainProductInfoModal.product_cd;
        vm.l_ct_cd = mainProductInfoModal.l_ct_cd;
        vm.m_ct_cd = mainProductInfoModal.m_ct_cd;
        vm.supply_cd = mainProductInfoModal.supply_cd;
        vm.warehouse_cd = mainProductInfoModal.warehouse_cd;
        vm.prod_nm = mainProductInfoModal.prod_nm;
        vm.detail = mainProductInfoModal.detail;
        vm.purchase_price = mainProductInfoModal.purchase_price;
        vm.price = mainProductInfoModal.price;
        vm.stock = mainProductInfoModal.stock;
       
        if(mainProductInfoModal.file_no != null) {
        vm.file_reative_path = mainProductInfoModal.file_reative_path;
        vm.file_no = mainProductInfoModal.file_no ? parseInt(mainProductInfoModal.file_no) : 0;
        vm.file_local_path = mainProductInfoModal.file_local_path;
        vm.file_ofname = mainProductInfoModal.file_ofname;
        vm.file_size = mainProductInfoModal.file_size;
        vm.thumb_nail = require("@/assets/images/scm/product/" + vm.file_no + "/" + vm.file_ofname);
      }

        vm.l_ct_nm = mainProductInfoModal.l_ct_nm;
        vm.m_ct_nm = mainProductInfoModal.m_ct_nm;
        vm.supply_nm = mainProductInfoModal.supply_nm;
        vm.warehouse_nm = mainProductInfoModal.warehouse_nm;
    }).catch(function (error){
        alert("에러! API 요청에 오류가 있습니다. (modalSelectU) " + error);
    });
  }
},
methods: {
  handleFileChange: function(event){
    this.file = this.$refs.attachImage.files;
    // alert("file::1"+this.file);
    // alert("file::2"+this.file[0]);
    // alert("file::3"+JSON.stringify(this.file));
    // alert("file::4"+this.file[0].name);
    // alert("file::4"+this.file[0].size);
    console.log("file::4",this.file[0]);
    this.file_ofname = this.file[0].name;
    this.file_size= this.file[0].size;
    const file = event.target.files[0];
    
    
    // this.file = event.target.files[0]; // 선택한 파일 가져오기
    
    if (!file) return;

    // // FileReader 객체 생성
    const reader = new FileReader();
    // // 파일 읽기
    reader.readAsDataURL(file);
    // // 파일 읽기 완료 후 실행되는 이벤트 핸들러
    reader.onload = () => {
      const dataURI = reader.result; // 파일을 data URI로 변환
      this.createThumbnail(dataURI); // 썸네일 생성 함수 호출
    };

  },
  createThumbnail(dataURI) {
    // 이미지 객체 생성
    const tempImage = new Image();
    tempImage.src = dataURI; // 데이터 URI를 이미지 객체에 주입
    // 이미지 로드 완료 후 실행되는 이벤트 핸들러
    tempImage.onload = () => {
      // 캔버스 객체 생성
      const canvas = document.createElement('canvas');
      const canvasContext = canvas.getContext('2d');
      
      // 캔버스 크기 설정
      canvas.width = 300; // 가로 300px
      canvas.height = 300; // 세로 300px

      // 이미지를 캔버스에 그리기
      canvasContext.drawImage(tempImage, 0, 0, 300, 300);

      // 캔버스에 그린 이미지를 다시 data URI 형태로 변환
      const thumbnailURI = canvas.toDataURL('image/jpeg');

      // document.getElementById('tempImg').src = thumbnailURI;

      // 썸네일 이미지 URL 업데이트
      this.thumb_nail = thumbnailURI;
    };
  
  },
  save: function () {
    if(!this.validateIsNull()) {
      return;
    }

    if (confirm("저장하시겠습니까?")) {
      let vm = this;
      // alert("file2::"+this.file[0]);
      console.log("file2::",this.file[0]);
      let params = new FormData();

      params.append("action", this.action);        
      params.append("product_cd", this.product_cd);        
      params.append("prod_nm", this.prod_nm);        
      params.append("l_ct_cd", this.l_ct_cd);  
      params.append("m_ct_cd", this.m_ct_cd);        
      params.append("supply_cd", this.supply_cd);        
      params.append("purchase_price", this.purchase_price);        
      params.append("price", this.price);
      params.append("warehouse_cd", this.warehouse_cd);
      params.append("stock", this.stock);
      params.append("detail", this.detail);
      if(this.file.length > 0){
        params.append("file", this.file[0]);
        params.append("isFile", "isFile");
        if(this.action == 'U' && this.file_no == 0) {
          this.file_status = 'A';
        } else if (this.action == 'U' & this.file_no != 0) {
          this.file_status == 'M';
        }
      }

      if(this.action == "U") {
        if(this.file_status == 'D'){
          params.append("deleted", "deleted");
        } else if (this.file_status == 'M'){
          params.append("modified", "modified");
          params.append("file_nm", this.file[0].name);
        } else if(this.file_status == 'A'){
          params.append("added", "added");
          this.file_no = 0;
          params.append("file_nm", this.file[0].name);
        } else {
          params.append("noFile", "noFile");
        }
        params.append("file_no", this.file_no);
        params.append("file_nm", this.file_ofname);
        params.append("product_cd", this.product_cd);
      }

      
      this.axios
      ({
        method: "post",
        url: "/scm/saveMainProduct.do",
        headers:{
          'Content-Type': 'multipart/form-data',
        },
        data: params
      })
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
  deleteBtn: function () {

    if (confirm("정말 삭제하시겠습니까?")) {
      let vm = this;
      vm.action = 'D';

      let params = new URLSearchParams();

      params.append("action", this.action);        
      params.append("product_cd", this.product_cd);
      // params.append("file_no", this.file_no);

      this.axios
      .post("/scm/saveMainProduct.do", params)
      .then(function (response) {
        console.log("delete response", response);
        let status = response.status;
        if (status == 200) {
          alert("삭제가 완료되었습니다.");
          closeModal(vm);
        } else {
          alert("API 요청에 오류가 있습니다.");
        }
      })
      .catch(error => {
        alert("에러! API 요청에 오류가 있습니다. (delete) " + error);
        console.log(error);
      });

    }
  },
  cancel: function() {
    let vm = this;
    closeModal(vm);
  },
  validateIsNull: function () {
    let chk = this.checkNotEmpty([        
      [ "product_cd", "제품코드를 입력하세요." ],
      [ "prod_nm", "제품명을 입력하세요." ],
      [ "l_ct_cd", "품목코드를 입력하세요." ],
      [ "m_ct_cd", "상호 카테고리를 선택하세요." ],
      [ "supply_cd", "공급처를 입력하세요." ],
      [ "warehouse_nm", "창고명을 입력하세요." ],
      [ "purchase_price", "장비구매액을 입력하세요." ],
      [ "price", "단가를 입력하세요." ],
      [ "stock", "재고개수를 입력하세요." ],
      [ "detail", "상세정보를 입력하세요." ]
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
  },
  deleteFile: function() {
    this.thumb_nail= require('@/assets/images/admin/comm/no_image.png');
      this.file_status = 'D';
    }


},

}

</script>

<style scoped>
#layer1 {
display:flex;
justify-content:center;
align-items:center;
}


</style>