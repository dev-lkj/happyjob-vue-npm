<template>
    <div id="container">

        <div class="content">
            <p class="Location">
                <a href="/#/dashboard/home" class="btn_set home">메인으로</a> <a class="btn_nav">기준 정보</a> <span class="btn_nav bold">제품정보 관리</span> <a href="/#/dashboard/scm/mainProductInfo" class="btn_set refresh">새로고침</a>
            </p>
            <p class="conTitle">
                <span>제품정보</span> 
                <span class="fr"> 
                    <a href="javascript:void(0)" @click="modalNew()" class="btnType blue" name="modal"> 
                        <span>신규등록</span>
                    </a>
                </span>
            </p>
            <div class="MainProductList">
                <div class="conTitle" style="margin: 0 25px 10px 0; float: left;">
                    <select id="searchKey" name="searchKey" v-model="oname" style="width: 100px;">
                        <option value="all">전체</option>
                        <option value="product_cd">제품코드</option>
                        <option value="prod_nm">제품명</option>
                        <option value="l_ct_nm">품목명</option>
                        <option value="m_ct_nm">상호명</option>
                    </select>
                    <input type="text" style="width: 300px; height: 30px;" id="sname" name="sname" v-model="sname" @keyup.enter="board_search">
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
                        <th scope="col">제품코드</th>
                        <th scope="col">제품명</th>
                        <th scope="col">품목명</th>
                        <th scope="col">상호명</th>
                        <th scope="col">창고명</th>
                        <th scope="col">장비구매액(원)/EA</th>
                        <th scope="col">단가(원)/EA</th>
                        <th scope="col">비고</th>
                    </tr>
                    </thead>
                    <tbody v-if="listitem.length" id="listMainProduct"  >
                    <tr v-for="(item) in listitem" :key="item.product_cd">
                        <td>{{item.product_cd }}</td>
                        <td @click="modalView(item.product_cd)" style="cursor : pointer">
                            <a href="javascript:void(0)">{{item.prod_nm}}</a>
                        </td>
                        <td>{{item.l_ct_nm}}</td>
                        <td>{{item.m_ct_nm}}</td>
                        <td>{{item.warehouse_nm}}</td>
                        <td>{{ formatNumber(item.purchase_price) }}</td>
                        <td>{{ formatNumber(item.price) }}</td>
                        <td @click="modalEdit(item.product_cd)" style="cursor : pointer">
                            <a href="javascript:void(0)" class="btnType3 color1">
                                <span>수정</span>
                            </a>
                        </td>
                    </tr>
                    </tbody>
                </table>
            </div>
            <div class="paging_area" id="mainProductPagination">
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
        </div> <!--// content -->
    </div>

</template>

<script>
import { openModal } from "jenesius-vue-modal";
import mainProductInfoModal from "@/components/system/mainProductInfoModal.vue";
import Paginate from "vuejs-paginate-next";

export default {
    data() {
        return {
            listitem: [],
            detailList: [],
            sname : '',
            oname : 'all',
            currentPage: 1,
            pageSize: 5,
            totalPage: 1,
            totalCnt: 0,
        }
    },
    components: {
        paginate: Paginate,
    },
    mounted() {
        this.searchList();
    },
    methods: {
        formatNumber: function(value){
            if (!value) return ''; // 값이 없으면 빈 문자열 반환
            return value.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ','); // 천 단위로 쉼표 추가
        },
        modalNew: async function (){
            const modal = await openModal(mainProductInfoModal, {
                action_parent : 'I',
            });
            modal.onclose = () => {
                this.searchList();
            }
        },
        modalEdit: async function (product_cd){
            const modal = await openModal(mainProductInfoModal, {
                action_parent : 'U',
                product_cd_parent : product_cd,
            });
            modal.onclose = () => {
                this.searchList();
            }
        },
        modalView: async function (product_cd){
            const modal = await openModal(mainProductInfoModal, {
                action_parent : 'V',
                product_cd_parent : product_cd,
            });
            modal.onclose = () => {
                this.searchList();
            }
        },

        searchList: function (currentPage) {
            let vm = this;

            let params = new URLSearchParams();
            params.append("currentPage", this.currentPage);
            params.append("pageSize", this.pageSize);
            params.append("sname", this.sname);
            params.append("oname", this.oname);
            
        this.axios
            .post("/scm/listMainProductVue.do", params)
            .then(function (response) {
            console.log("listMainProductModel.do",response);
            vm.listitem = response.data.listMainProductModel;
            vm.totalCnt = response.data.totalMainProduct;
            vm.pageSize = response.data.pageSize;
            vm.currentPage = response.data.currentPageMainProduct;
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
            .post("/scm/listMainProductVue.do",params)
            .then(function (response) {
                vm.listitem = response.data.listMainProductModel;
                vm.totalCnt = response.data.totalMainProduct;
                vm.pageSize = response.data.pageSize;
                vm.currentPage = response.data.currentPageMainProduct;
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