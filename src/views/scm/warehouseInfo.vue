<template>
    <div id="warehouseInfo">
        <p class="Location">
            <a href="/#/dashboard/home" class="btn_set home">메인으로</a> <a href="javascript:void(0)" class="btn_nav">기준 정보</a> <span class="btn_nav bold">창고정보 관리</span> <a href="/#/dashboard/scm/warehouseInfo" class="btn_set refresh">새로고침</a>
        </p>

        <p class="conTitle">
            <span>창고정보</span> 
            <span class="fr" > 
                <a href="javascript:void(0)" @click="modalNew()" class="btnType blue" name="modal">      
                    <span>신규등록</span>
                </a>
            </span>
        </p>
        <div class="WarehouseList">
            <div class="conTitle" style="margin: 0 25px 10px 0; float: left;">
                <select id="searchKey" name="searchKey" style="width: 100px;" v-model="oname">
                    <option value="all" selected="selected">전체</option>
                    <option value="warehouse_nm">창고명</option>
                    <option value="wh_mng_nm">담당자명</option>
                </select>
                <input type="text" style="width: 300px; height: 30px;" id="sname" v-model="sname" name="sname" @keyup.enter="board_search">
                <a href="javascript:void(0)" class="btnType blue" id="searchBtn" name="btn" @click="board_search()"> 
                    <span>검 색</span>
                </a> 
            </div>

            <table class="col">
                <caption>caption</caption>
                <colgroup>
                <col width="12%">
                <col width="12%">
                <col width="12%">
                <col width="12%">
                <col width="12%">
                <col width="12%">
                <col width="12%">
                <col width="12%">
                </colgroup>
                <thead>
                <tr>
                    <th scope="col">창고코드</th>
                    <th scope="col">창고명</th>
                    <th scope="col">담당자명</th>
                    <th scope="col">담당자 연락처</th>
                    <th scope="col">담당자 이메일</th>
                    <th scope="col">우편번호</th>
                    <th scopt="col">주소</th>
                    <th scope="col">비고</th>
                </tr>
                </thead>
                <tbody id="listInf" v-if="listitem.length">
                <tr v-for="(item) in listitem" :key="item.warehouse_cd" >
                    <td>{{ item.warehouse_cd }}</td>
                    <td @click="callfListProduct(item.warehouse_nm, item.warehouse_cd)" style="cursor : pointer">
                        <a href="javascript:void(0)" title="창고명" style="text-decoration:underline; color:#868686;">{{ item.warehouse_nm }}</a>
                    </td>
                    <td>{{ item.wh_mng_nm }}</td>
                    <td>{{ item.tel }}</td>									
                    <td>{{ item.email }}</td>
                    <td>{{ item.zip_cd }}</td>
                    <td>{{ item.addr }} <br/> {{ item.addr_detail }}</td>
                    <td @click="modalEdit(item.warehouse_cd, item.wh_mng_nm)" style="cursor : pointer">
                        <a href="javascript:void(0)" class="btnType3 color1">
                            <span>수정</span>
                        </a>
                    </td>                      
                </tr>
                </tbody>
            </table>
        </div>
        <div class="paging_area" id="warehousePagination">
            <paginate
                class="justify-content-center"
                v-model="currentPage"
                :page-count="totalPage"
                :page-range="5"
                :margin-pages="0"
                :click-handler="clickCallback"
                :prev-text="'<'"
                :next-text="'>'"
                :container-class="'pagination'"
                :page-class="'page-item'"
                :first-last-button="true"
                :first-button-text="'<<'"
                :last-button-text="'>>'"
            >
            </paginate>
        </div>

        <p class="conTitle mt50">
            <span>창고 세부정보<span id="subTitle">{{ subTitle }}</span></span>
        </p>

        <div class="ProductList">
            <table class="col">
                <caption>caption</caption>
                <colgroup>
                <col width="15%">
                <col width="15%">
                <col width="15%">
                <col width="15%">
                <col width="15%">
                </colgroup>
                <thead>
                <tr>
                    <th scope="col">제품코드</th>
                    <th scope="col">제품명</th>
                    <th scope="col">품목명</th>
                    <th scope="col">공급처명</th>
                    <th scope="col">재고수량(EA)</th>
                </tr>
                </thead>

                <tbody id="listWarehouseProduct"  v-if="detailList.length">
                <tr v-for="(item) in detailList"  :key="item.product_cd">
                    <td>{{ item.product_cd }}</td>
                    <td>{{ item.prod_nm }}</td>
                    <td>{{ item.l_ct_nm }}</td>
                    <td>{{ item.supply_nm }}</td>
                    <td>{{ item.stock }}</td>
                </tr>
                </tbody>
            </table>
        </div>

        <div class="paging_area" id="productPagination">
            <paginate
                class="justify-content-center"
                v-model="currentPage2"
                :page-count="totalPage2"
                :page-range="5"
                :margin-pages="0"
                :click-handler="clickCallback"
                :prev-text="'<'"
                :next-text="'>'"
                :container-class="'pagination'"
                :page-class="'page-item'"
                :first-last-button="true"
                :first-button-text="'<<'"
                :last-button-text="'>>'"
            >
            </paginate>
        </div>        

    </div>

    
</template>

<script>
import { openModal } from "jenesius-vue-modal";
import warehouseInfoModal from "@/components/system/warehouseInfoModal.vue";
import Paginate from "vuejs-paginate-next";
    
export default {
    data() {
        return {
            listitem: [],
            detailList: [],
            sname : '',
            oname : 'all',
            subTitle : '',
            currentPage: 1,
            pageSize: 5,
            totalPage: 1,
            totalCnt: 0,
            currentPage2: 1,
            pageSize2: 5,
            totalPage2: 1,
            totalCnt2: 0,
        }
    },
    components: {
        paginate: Paginate,
    },
    mounted() {
        this.searchList();
    },
    methods: {
        modalNew: async function (){
            const pageSize = 100; // 페이지 크기를 100으로 설정
            let params = new URLSearchParams();
            params.append("currentPage", 1);
            params.append("pageSize", pageSize); // 페이지 크기를 100으로 설정하여 전체 리스트를 요청
            params.append("sname", '');
            params.append("oname", 'all');
            
            const response = await this.axios.post("/scm/listWarehouseVue.do", params);
            const fullList = response.data.listWarehouseModel; // 전체 리스트를 받아옴
            // const modalList = fullList.slice(0, pageSize); // 페이지 크기만큼만 잘라서 모달에 전달
            const modal = await openModal(warehouseInfoModal, {
                action_parent : 'I',
                itemlist_parent : fullList,
            });
            modal.onclose = () => {
                this.searchList();
            }
            
        },
        modalEdit: async function (warehouse_cd, wh_mng_nm){
            const modal = await openModal(warehouseInfoModal, {
                action_parent : 'U',
                warehouse_cd_parent : warehouse_cd,
            });
            modal.onclose = () => {
                this.searchList();
            }
        },
        callfListProduct: function(warehouse_nm, warehouse_cd){
            let vm = this;

            let params = new URLSearchParams();
            params.append("currentPage", this.currentPage2);
            params.append("pageSize", this.pageSize2);
            params.append("warehouse_nm", warehouse_nm);
            params.append("warehouse_cd", warehouse_cd);

            this.axios
            .post("/scm/listWarehouseProductVue.do", params)
            .then(function(response)  {
                console.log("listWarehouseProductVue.do", response);                
                vm.detailList = response.data.listWarehouseProductModel;
                vm.totalCnt2 = response.data.totalProduct;
                vm.pageSize2 = response.data.pageSize;
                vm.currentPage2 =response.data.currentPageProduct;
                vm.totalPage2 = vm.page(vm.totalCnt2, vm.pageSize2);
                vm.subTitle = ' - ' + warehouse_nm;
            })
            .catch(error => {
                alert("에러! API 요청에 오류가 있습니다.2 " + error);
                console.log(error);
            });
        },
        searchList: function (currentPage) {
            let vm = this;

            let params = new URLSearchParams();
            params.append("currentPage", this.currentPage);
            params.append("pageSize", this.pageSize);
            params.append("sname", this.sname);
            params.append("oname", this.oname);
            
        this.axios
            .post("/scm/listWarehouseVue.do", params)
            .then(function (response) {
            console.log("listWarehouseVue.do",response);
            // alert(response.data.listWarehouseModel[0].wh_mng_nm);
            // alert(vm.listitem[0].wh_mng_nm);
            vm.listitem = response.data.listWarehouseModel;
            vm.totalCnt = response.data.totalWarehouse;
            vm.pageSize = response.data.pageSize;
            vm.currentPage = response.data.currentPageWarehouse;
            vm.totalPage = vm.page(vm.totalCnt, vm.pageSize);

            })
            .catch(function (error) {
                alert("에러! API 요청에 오류가 있습니다. " + error);
            });
        },
        board_search: function () {
            let vm = this;

            this.detailList = [];
            let params = new URLSearchParams();
            
            params.append("currentPage", this.currentPage);
            params.append("sname", this.sname);
            params.append("oname", this.oname);
            params.append("pageSize", this.pageSize);
            
            this.axios
            .post("/scm/listWarehouseVue.do",params)
            .then(function (response) {
                vm.listitem = response.data.listWarehouseModel;
                vm.totalCnt = response.data.totalWarehouse;
                vm.pageSize = response.data.pageSize;
                vm.currentPage = response.data.currentPageWarehouse;
                vm.totalPage = vm.page(vm.totalCnt, vm.pageSize);
            }).catch(function (error){
                alert("에러! API 요청에 오류가 있습니다. (search) " + error);
            });
        },
        clickCallback: function (pageNum) {
            console.log("pageNum::",pageNum);

            this.currentPage = pageNum;
            this.searchList();
        },
        page: function (totalCnt, pageSize) {
            var total = totalCnt;
            var page = pageSize;
            var xx = total % page;
            var result = parseInt(total / page);

            if (xx == 0) {
                return result;
            } else {
                result = result + 1;
                return result;
            }
        },
        
    
    },

}

</script>

<style scoped>
#searchArea {
  margin-top: 25px;
  margin-bottom: 25px;
}
#searchArea > * {
  height: auto;
}

#groupTitle {
  text-decoration: underline;
  font-weight: bold;
  cursor: pointer;
}

</style>