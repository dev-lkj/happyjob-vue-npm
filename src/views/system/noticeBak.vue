
<template>
  <div id="notice">
    <p class="Location">
      <a href="/dashboard/home" class="btn_set home"></a>
      <span class="btn_nav bold">기준정보</span>
      <span class="btn_nav bold">공지사항 관리</span>
      <a href="../system/notice.do" class="btn_set refresh">새로고침</a>
    </p>

    <p class="conTitle">
      <span>공지사항</span>
      <span>
        <table
          style="collapse; border: 1px #50bcdf;"
          width="100%"
          cellpadding="5"
          cellspacing="0"
          border="1"
          align="left"
        >
          <tr style="border: 0px; border-color: blue">
            <td
              width="50"
              height="25"
              style="font-size: 100%; text-align: left"
            >
              <div id="searchArea" class="d-flex justify-content-around">
                <select
                  id="searchKey"
                  class="form-control"
                  style="width: 150px"
                  v-model="searchKey"
                >
                  <option value="all">전체</option>
                  <option value="title">제목</option>
                  <option value="content">내용</option>
                </select>
                
                <input
                  type="text"
                  style="width: 200px"
                  class="form-control"
                  v-model="sname"
                />

                <input
                  type="date"
                  style="width: 15%"
                  class="form-control"
                  v-model="formerDate"
                />
                ~
                <input
                  type="date"
                  style="width: 15%"
                  class="form-control"
                  v-model="latterDate"
                />

                <span class="fr">
                  <a
                    @click="searchList"
                    class="btn btn-primary mx-2"
                    id="btnSearchGrpcod"
                    name="btn"
                  >
                    <span>검 색</span>
                  </a>
                  <a
                    class="btn btn-primary mx-2"
                    @click="writeNotice"
                    name="modal"
                  >
                    <span>신규등록</span>
                  </a>
                </span>
              </div>
            </td>
          </tr>
        </table>
      </span>
    </p>


    <div class="divComGrpCodList">
      <table class="col">
        <caption>
          caption
        </caption>
        <colgroup>
          <col width="10%" />
          <col width="50%" />
          <col width="30%" />
          <col width="20%" />
        </colgroup>

        <thead>
          <tr>
            <th scope="col">글번호</th>
            <th scope="col">제목</th>
            <th scope="col">작성일</th>
            <th scope="col">조회수</th>
          </tr>
        </thead>
        <tbody>
          <template v-if="totalCnt == 0">
            <tr>
              <td colspan="4">일치하는 검색 결과가 없습니다</td>
            </tr>
          </template>
          <template v-else>
            <tr v-for="item in listitem" :key="item.notice_id">
              <td>{{ item.notice_id }}</td>
							<td @click="searchDetail(item.notice_id)" style="cursor : pointer">{{ item.title }}</td>
							<td>{{ item.date }}</td>
							<td>{{ item.view_cnt }}</td>              
            </tr>
          </template>
        </tbody>
      </table>
    </div>

    <div id="noticePagination">
      <paginate
        class="justify-content-center"
        v-model="currentPage"
        :page-count="totalPage"
        :page-range="5"
        :margin-pages="0"
        :click-handler="clickCallback"
        :prev-text="'Prev'"
        :next-text="'Next'"
        :container-class="'pagination'"
        :page-class="'page-item'"
      >
      </paginate>
    </div>

  </div>

</template>

<script>
import { openModal } from "jenesius-vue-modal";
import {noticeDetailModal} from "@/components/system/noticeDetailModal.vue";
import Paginate from "vuejs-paginate-next";

export default {
  data: function () {
    return {
      listitem: [],
      option: '',
      searchKey : 'all',
      keyword: '',
      formerDate : '',
      latterDate : '',

      
      currentPage: 1,
      pageSize: 5,
      totalPage: 1,
      totalCnt: 0,
      action: '',
      groupCode:'',
      groupCodeName: '',
      countShow: true,
      title: ''


    };
  },
  computed: {},
  components: {
    paginate: Paginate,
  },
  mounted() {
    this.searchList();
  },

  methods: {
    searchDetail: async function (notice_id) {
      if (notice_id == null || notice_id == "") {
        this.action = "I";

        const modal = await openModal(noticeDetailModal, {
          title: "공통코드 등록",
          noticeId: "",
          action: this.action,
        });

        modal.onclose = (popupparam) => {
          console.log("Close : " + popupparam);
          this.listsearch();
          //return false; //Modal will not be closed
        };
      } else {
        console.log(notice_id);

        this.action = "U";
        const modal = await openModal(noticeDetailModal, {
          title: "공통코드 수정",
          noticeId: notice_id,
          action: this.action,
        });

        modal.onclose = (popupparam) => {
          console.log("Close : " + popupparam);
          this.searchList();
          //return false; //Modal will not be closed
        };
      }
    },
    searchButton: function (pageno) {
      this.currentPage = pageno;
      this.searchList();
    },
  
    rowDtlClicked: async function (grpcd, dtlcod) {
        if (grpcd == null || grpcd == "") {
          alert("그룹 코드를 선택해 주세요.");
          return;
        }


      if (dtlcod == null || dtlcod == "") {

        this.action = "I";

        const modal = await openModal(noticeDetailModal, {
          title: "상세코드 관리",
          grpcd: this.groupCode,
          grpcdnm: this.groupCodeName,
          dtlcod: "",
          action: this.action,
        });

        modal.onclose = (popupparam) => {
          console.log("Close : " + popupparam);
          this.searchdetail(this.groupCode, this.groupCodeName);
          //return false; //Modal will not be closed
        };
      } else {
        console.log(grpcd);
        console.log(dtlcod);

        this.action = "U";
        const modal = await openModal(noticeDetailModal, {
          title: "상세코드 관리",
          grpcd: grpcd,
          dtlcod: dtlcod,
          action: this.action,
        });

        modal.onclose = (popupparam) => {
          console.log("Close : " + popupparam);
          this.searchdetail(this.groupCode, this.groupCodeName);
          //return false; //Modal will not be closed
        };
      }
    },
    searchList: function () {
      let vm = this;

      let params = new URLSearchParams();
      params.append("currentPage", this.currentPage);
      params.append("pageSize", this.pageSize);
      params.append("option", this.searchKey);
      params.append("sname", this.sname);

      this.axios
        .post("/system/noticeListVue.do", params)
        .then(function (response) {
          console.log(response);
          vm.totalCnt = response.data.totalCount;
          vm.listitem = response.data.noticeList;
          vm.totalPage = vm.page();
        })
        .catch(function (error) {
          alert("에러! API 요청에 오류가 있습니다. " + error);
        });
    },
    clickCallback: function (pageNum) {
      console.log(pageNum);

      this.currentPage = pageNum;
      //this.Paginate.pageNum = 10;
      this.saerchList();
    },
    page: function () {
      var total = this.totalCnt;
      var page = this.pageSize;
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

<style >

</style>