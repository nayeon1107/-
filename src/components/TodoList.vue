<template>
  <section>
    <ol name="list_cate" >
      <div v-for="(cate,cateidx) in propsCate" :key="cate">
        <p :key="cateidx"> <span class='catebox' :style={color:propsColor[cateidx]}> {{cate}} </span>
          <span class="categorydetailBtn fas fa-cog" type="button" @click="showEditCategoryModal(cate)"></span>
        </p>
        <draggable>
          <transition-group name="list" tag="ul">
            <li v-for="(todoItem, index) in propsdata" :key="todoItem" class="shadow" v-show='propsTodoCate[index]==cate'>
              <span type="button" aria-hidden="true" @click="updateState(index)"><img v-if=propsDone[index] src="..\src\assets\flower.png" width="25" height="25" align='center'><img v-else src="..\src\assets\seed.png" width="25" height="25" align='center'></span>
              <input :class="{textCompleted:propsDone[index]}" style="outline: none;border-style: none;" :placeholder="todoItem" v-model="editedTodoItem[index]" @keyup.enter="editTodo(index)">
              <div class="dday"> {{propsDate[index]}} </div>
              <span class="detailBtn" type="button" @click="showDetailModal(index)">
                <i class="fas fa-ellipsis-v"></i>
              </span>
              <span class="removeBtn" type="button" @click="removeTodo(index)">
                <i class="far fa-trash-alt" aria-hidden="true"></i>
              </span>
            </li>
          </transition-group></draggable>
    </div>
    </ol>

      <EditAlertModal v-if="showEditAlertModal" @close="showEditAlertModal = false">
        <h3 slot="header">경고</h3>
        <p slot="content">할 일을 입력하세요.</p>
        <span slot="footer" class="closeModalBtn" @click="showEditAlertModal = false">닫기</span>
      </EditAlertModal> 

      <DetailModal v-if="TFDetailModal" @close="TFDetailModal = false">
        <div slot="header">
          <h2> {{propsdata[DetailIndex]}} </h2></div>
        <div slot="content">
          <span style="padding-right:85px;float:none">카테고리 </span>
            <select class="categorybox" v-model="category">
              <option disabled value="" >카테고리를 선택하세요.</option>
              <option :key=index :value=value  v-for="(value,index) in propsCate">{{propsCate[index]}}</option>
            </select> 
          <br>마감기한
            <input type="date" id="deadline" v-model="deadline" style="float:right;">
          <br>장소
            <input type="text" v-model="place" style="float:right">
          <br>메모
            <input type="text" v-model="memo"  style="float:right">
          <br>알림
            <input type="time" v-model="alarm"  style="float:right">
        </div>
        <div slot="footer" style="margin-top:0;">
          <span class="saveDetailBtn" @click="addDetailTodo(DetailIndex,deadline,place,memo,category,alarm)">저장하기</span>
          <span class="closeDetailBtn" @click="TFDetailModal = false"> 닫기</span>
        </div>
      </DetailModal>

      <EditCategoryModal v-if="TFEditCategoryModal" @close="TFEditCategoryModal=false">
        <div slot="header">
          <h3 >{{editpastCate}}</h3>
        </div>
        
        <div slot="content">
          <input type="text" v-model="editedCate" placeholder="카테고리 이름 수정">
        </div>
        <div slot="footer">
          <span class="EditCategoryBtn" @click="editCategory()">수정하기</span>
          <span class="closeEditModalBtn fas fa-times" @click="TFEditCategoryModal = false"></span>
          <span class="DeleteCategoryBtn" @click="goAlertCategoryModal()">삭제하기</span>
        </div>
      </EditCategoryModal>

      <AlertCategoryModal v-if="TFAlertCategoryModal" @close="TFAlertCategoryModal=false">
        <h3 slot="header">{{editpastCate}} 항목을 정말 삭제하겠습니까?</h3>
        <div slot="footer">
          <span class="noAllDeleteBtn" @click="goEditCategoryModal()">닫기</span>
          <span class="allDeleteBtn" @click="clearCategory()">삭제하기</span>
        </div>            

      </AlertCategoryModal>
  </section>
</template>

<script>
import draggable from 'vuedraggable'

import DetailModal from './common/DetailModal.vue'
import EditAlertModal from './common/EditAlertModal.vue'
import EditCategoryModal from './common/EditCategoryModal.vue'
import AlertCategoryModal from './common/AlertCategoryModal.vue'


export default {
  data(){
    return{
      TFDetailModal: false,
      showEditAlertModal: false,
      TFEditCategoryModal: false,
      TFAlertCategoryModal: false,
      DetailTodo: '',
      place:'',
      deadline:'',
      memo:'',
      category:'',
      alarm:'',
      done:'',
      editedTodoItem: [],
      doneItems: [],
      ddate: [],
      editpastCate:'',
      editedCate:''
    }
  }
  ,
  props: ['propsdata','propsIdx','propsDone','propsDate','propsCate','propsTodoCate','propsColor'],

  methods: {
    removeTodo(index) {
      var keyIdx=this.propsIdx[index]
      this.$emit('removeTodo',keyIdx,index);
    },
    updateState(index){
      var keyIdx=this.propsIdx[index]
      this.$emit('updateState',keyIdx,index);
    },

    
    showDetailModal(index){
      this.TFDetailModal=!this.TFDetailModal
      this.DetailIndex=index
      
      var items =JSON.parse(localStorage.getItem(this.propsIdx[index]))
      this.done=items.done
      this.place=items.place
      this.deadline=items.deadline
      this.memo=items.memo
      this.category=items.category
      this.alarm=items.alarm
    },
    showEditCategoryModal(cate){
      this.TFEditCategoryModal=!this.TFEditCategoryModal
      this.editpastCate=cate

    },
    editCategory(){
      this.$emit('editCategory',this.editpastCate,this.editedCate)
      this.TFEditCategoryModal=!this.TFEditCategoryModal
    },
    clearCategory(){
      this.$emit('clearCategory',this.editpastCate)
      this.TFAlertCategoryModal=!this.TFAlertCategoryModal;
    },
    goEditCategoryModal(){
      this.TFAlertCategoryModal=!this.TFAlertCategoryModal;
      this.TFEditCategoryModal=!this.TFEditCategoryModal;

    },
    goAlertCategoryModal(){
      this.TFAlertCategoryModal=!this.TFAlertCategoryModal;
      this.TFEditCategoryModal=!this.TFEditCategoryModal;
    },
    addDetailTodo(DetailIndex){
      this.TFDetailModal=false
      var keyIdx=this.propsIdx[DetailIndex]

      this.ddate = this.propsDate
      if (this.deadline.length > 0) {
        var today = new Date().getTime()
        var deaddate = new Date(this.deadline.split("-")[0],this.deadline.split("-")[1]-1,this.deadline.split("-")[2]).getTime()
        this.dday = deaddate - today
        this.dday = Math.ceil((this.dday) / (1000*60*60*24))
        if (this.dday > 0) {
          this.ddate.splice(DetailIndex,1,("D-"+this.dday))
        } else if (this.dday < 0) {
          this.ddate.splice(DetailIndex,1,'')
        } else {this.ddate.splice(DetailIndex,1,'D-day')}
      } else {this.ddate.splice(DetailIndex,1,'')}

      var items={todo :this.propsdata[DetailIndex], done : this.done , deadline: this.deadline, dday: this.ddate[DetailIndex], place: this.place, memo: this.memo, category: this.category, alarm: this.alarm}
      localStorage.setItem(keyIdx, JSON.stringify(items))

      this.clearInput()
    
      location.reload();

    },
    clearInput(){
      this.place='';
      this.deadline='';
      this.done=''
      this.memo=''
      this.category=''
      this.alarm=''
    },
    editTodo(index){
      if (this.editedTodoItem[index] !== undefined) {
        var value = this.editedTodoItem[index] && this.editedTodoItem[index].trim();
        var keyIdx=this.propsIdx[index]
				this.$emit('editTodo',keyIdx,index,value)
        this.editedTodoItem= [];
      } else {
        this.showEditAlertModal=!this.showEditAlertModal
      }

  }}
  ,
  components: {
    DetailModal: DetailModal,
    EditAlertModal: EditAlertModal,
    EditCategoryModal: EditCategoryModal,
    AlertCategoryModal: AlertCategoryModal,
    draggable

  }
  

}
</script>

<style scope>
  ul {
    list-style-type: none;
    padding-left: 0px;
    margin-top: 0;
    text-align: left;
  }
  li {
    display: flex;
    min-height: 50px;
    height: 50px;
    line-height: 50px;
    margin: 0.5rem 0;
    padding: 0 0.9rem;
    background: white;
    border-radius: 5px;
  }
  ol {
    list-style-type: none;
    line-height: 50px;
    padding-left: 0px;
    margin:0;
    text-align: left;
  }





  .detailBtn {
    margin-left: auto;
    color: #de4343;
  }
  .removeBtn {
    margin-left: 10px;
    color: #de4343;
  }
  .textCompleted {
    text-decoration: line-through;
    color: #b3adad;
  }
  .categorydetailBtn{
    margin-top: 0;
    float: right;
    margin-right: 30px;
  }
  .closeEditModalBtn{
    margin-top:0;
  }






  .list-enter-active, .list-leave-active {
    /* transition: all 1s; */
  }
  .list-enter, .list-leave-to {
    opacity: 0;
    transform: translateY(30px);
  }
  .list-todoItem{
    outline: none;
    border-style: none;
    transition: opacity 1s;
  /* font-size: 0.9rem; */
  }
  .dday {
    font-size: 0.4rem;
    color:cadetblue;  
  }
  .catebox {
    font-size: 1rem;
    font-weight: 800;
    color: #6667ab;
  }
</style>
