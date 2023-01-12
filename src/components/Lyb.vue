<template>
  <!-- <h1>{{ msg }}</h1>
  <button @click="count++">count is: {{ count }}</button>
  <p>Edit <code>components/HelloWorld.vue</code> to test hot module replacement.</p> -->
  <div class="row">
    <div class="'col-md-8'">
      <table class="table">
        <thead>
          <tr>
            <th>title</th>
            <th>author</th>
            <th>content</th>
            <th></th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="item in ly_list" :key="item.url">
            <td>{{item.title}}</td>
            <td>{{ item.author}}</td>
            <td>{{ item.content }}</td>
            <td>
              <button class="btn btn-success" title="edit" @click="editLyb(item)">
                <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" class="bi bi-pencil-square" viewBox="0 0 16 16">
                  <path d="M15.502 1.94a.5.5 0 0 1 0 .706L14.459 3.69l-2-2L13.502.646a.5.5 0 0 1 .707 0l1.293 1.293zm-1.75 2.456-2-2L4.939 9.21a.5.5 0 0 0-.121.196l-.805 2.414a.25.25 0 0 0 .316.316l2.414-.805a.5.5 0 0 0 .196-.12l6.813-6.814z"/>
                  <path fill-rule="evenodd" d="M1 13.5A1.5 1.5 0 0 0 2.5 15h11a1.5 1.5 0 0 0 1.5-1.5v-6a.5.5 0 0 0-1 0v6a.5.5 0 0 1-.5.5h-11a.5.5 0 0 1-.5-.5v-11a.5.5 0 0 1 .5-.5H9a.5.5 0 0 0 0-1H2.5A1.5 1.5 0 0 0 1 2.5v11z"/>
                </svg>
                edit
              </button>
            </td>
            <td>
              <button class="btn btn-warning" title="delete" @click="deleteLyb(item)">
                <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" class="bi bi-trash3" viewBox="0 0 16 16">
                  <path d="M6.5 1h3a.5.5 0 0 1 .5.5v1H6v-1a.5.5 0 0 1 .5-.5ZM11 2.5v-1A1.5 1.5 0 0 0 9.5 0h-3A1.5 1.5 0 0 0 5 1.5v1H2.506a.58.58 0 0 0-.01 0H1.5a.5.5 0 0 0 0 1h.538l.853 10.66A2 2 0 0 0 4.885 16h6.23a2 2 0 0 0 1.994-1.84l.853-10.66h.538a.5.5 0 0 0 0-1h-.995a.59.59 0 0 0-.01 0H11Zm1.958 1-.846 10.58a1 1 0 0 1-.997.92h-6.23a1 1 0 0 1-.997-.92L3.042 3.5h9.916Zm-7.487 1a.5.5 0 0 1 .528.47l.5 8.5a.5.5 0 0 1-.998.06L5 5.03a.5.5 0 0 1 .47-.53Zm5.058 0a.5.5 0 0 1 .47.53l-.5 8.5a.5.5 0 1 1-.998-.06l.5-8.5a.5.5 0 0 1 .528-.47ZM8 4.5a.5.5 0 0 1 .5.5v8.5a.5.5 0 0 1-1 0V5a.5.5 0 0 1 .5-.5Z"/>
                </svg>
                <i class="bi bi-pencil-square"></i>
                delete
              </button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
    <div class="'col-md-4'">
      <input type="hidden" v-model="lyb.url">
      <div class="form-group">
        <label for="title">title</label>
        <input type="text" id="title" v-model="lyb.title">
      </div>
      <div class="form-group">
        <label for="author">author</label>
        <input type="text" id="author" v-model="lyb.author">
      </div>
      <div class="form-group">
        <label for="content">content</label>
        <textarea id="content" cols="30" rows="10" v-model="lyb.content"></textarea>
      </div>
      <div class="form-group">
        <button class="btn btn-default" @click="saveLyb">confirm</button>
      </div>

    </div>
  </div>

</template>
<!-- <script src="https://unpkg.com/axios/dist/axios.min.js"></script> -->

<!-- <script>
  export default {
  name: 'Lyb',
  }
</script> -->

<script>
import axios from 'axios'
import {reactive, onMounted, toRefs} from 'vue'

export default {
  name: 'Lyb',
  
  setup(){
    let base_url = 'http://127.0.0.1:8000/api/lyb/';

    const lyb_blank = {url:'', title:'', author:'', content:''}
    const state = reactive({
      ly_list: [],
      lyb: Object.assign({}, lyb_blank)
    });

    const getLyb = ()=> {
      axios.get(base_url).then(
        res => {
          state.ly_list = res.data;
          // 清空
          state.lyb = Object.assign({}, lyb_blank)
        }
      ).catch(err=>{
        console.log(err)
      })
    }

    const editLyb = (item)=>{
      state.lyb.url = item.url
      state.lyb.title = item.title
      state.lyb.author = item.author
      state.lyb.content = item.content

    }

    const deleteLyb = (item)=> {
      axios.delete(item.url).then(()=>{
        getLyb()
      }).catch(err=>{
        console.log(err)
      })
    }

    const saveLyb = () => {
      let newdata = {
        title: state.lyb.title,
        author: state.lyb.author,
        content: state.lyb.content,

      }
      if (state.lyb.url ==''){
        // 新增
        axios.post(base_url, newdata).then(()=>{
          getLyb()
        }).catch(err=>{
          console.log(err)
        })

      }else{
        // edit
        axios.put(state.lyb.url, newdata).then(()=>{
          getLyb()
        }).catch(err=>{
          console.log(err)
        })
      }
    }


    onMounted(()=>{
      getLyb();
    })

    return {
      ...toRefs(state),
      editLyb,
      saveLyb,
      deleteLyb,
    }

  }

  
}
</script>
