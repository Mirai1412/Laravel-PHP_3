<template>
    <div>
        <label class="block text-left" style="max-width: 400px">
            <button @click="addComment" class="text-gray-700">댓글등록</button>
            <textarea v-model="newComment"
                class="form-textarea mt-1 block w-full"
                rows="3"
                placeholder="Enter some comment."
            ></textarea>
        </label>

        <button @click="getComments"
            class="btn btn-default">댓글 불러오기</button>

        <comment-item v-for="comment in comments.data"
                :key="comment.id" :comment="comment"
                :login_user_id="loginuser"
                @deleted="getComments" />

        <pagination  @pageClicked="getPage($event)"
                v-if="comments.links != null" :links="comments.links"/>
    </div>
</template>

<script>
import CommentItem from './CommentItem.vue';
import Pagination from './Pagination.vue';
export default {
    props: ['post', 'loginuser'],
    components: {CommentItem, Pagination},
    data() {
        return {
            comments : [],
            newComment : '',
        }
    },

    methods: {
        addComment() {
            if(this.newComment == '') {
                 Swal.fire('한글자라도 쓰십시요.')
                return;
            }
            axios.post('/comments/'+this.post.id,
                    {'comment':this.newComment})
                    .then(response=>{
                        // console.log(response.data);

                        this.getComments();
                        this.newComment='';
                    })
                    .catch(error=>{console.log(error)})
        },
        getPage(url) {
            console.log(url);
            axios.get(url)
                .then(response=>{
                    // console.log(response);
                    this.comments = response.data;
                })
                .catch(error=>{
                    console.log(error);
                });
        },
        getComments() {
            // this.comments=['1st comment', '2nd comment',
            //     '3rd comment', '4th comment', '5th comment'];
            // 서버에 현재 게시글의 댓글 리스트를 비동기적으로 요청
            // 즉, axios를 이용해서 요청
            // 서버가 댓글 리스트를 주면 그놈을 어디에 할당해?
            // this.comments에 할당.
            axios.get('/comments/'+this.post.id)
            .then(response=>{
                // console.log(response);
                this.comments = response.data;
            }).catch(error=>{
                console.log(error);
            });
        }
    }
}
</script>
