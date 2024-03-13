<template>
  <!--Comment component-->
  <article class="media">

    <!--Profile photo links to the user's profile-->
    <figure class="media-left">
      <RouterLink :to="{ name: 'profile', params: { id: props.comment.author_id } }" class="image is-48x48">
        <ProfilePhotoItem :userId="props.comment.author_id"/>
      </RouterLink>
    </figure>
    <div class="media-content">
      <div class="content">
        <p>
          <!--User name that has a link to the user's profile-->
          <RouterLink :to="{ name: 'profile', params: { id: props.comment.author_id } }">
            <strong>{{ author?.getName() }} </strong>
          </RouterLink>
          <!--User's handle-->
          <small class="handle">@{{ author?.personalData.online_handle }}</small>
          <br>
          <!--Comment text comes from here-->
            {{ props.comment.comment }}
          <br>
        </p>
      </div>
      <!--Social Media basic actions (reply, retweet, like)-->
      <nav class="level is-mobile">
        <div class="level-left">
          <a class="level-item">
            <span class="icon is-small"><i class="fas fa-reply"></i></span>
          </a>
          <a class="level-item">
            <span class="icon is-small"><i class="fas fa-retweet"></i></span>
          </a>
          <a class="level-item">
            <span @click="like" :class="{'liked':User.hasLiked(props.comment, props.userState.currentUser.id)}" class="icon is-small"><i class="likes fas fa-heart">&nbsp{{ props.comment.numOfLikes }}</i></span>
          </a>
        </div>
      </nav>
    </div>
  </article>
</template>

<script setup lang="ts">
import ProfilePhotoItem from './ProfilePhotoItem.vue';
import { computed, defineProps, type PropType } from 'vue'
import router from '@/router'
import { type ActivityComment, User } from '@/components/User'

//Define props here for the component
const props = defineProps({
  comment: {
    type: Object as PropType<ActivityComment>,
    required: true,
  },
  users: {
    type: Array<User>,
    required: true,
  },
  userState: {
    type: Object as PropType<{ currentUser: User }>,
    required: true,
  },
});

//Here is how the like a comment works!
const like = () => {
  User.like(props.comment, props.userState.currentUser.id)
}

//Find the author of the comment
const author = computed(() => {
  // find by id
  let user = props.users.find((user) => {
    return user.id == props.comment.author_id;
  });
  return user;
})
</script>

<style scoped>
.likes {
  padding-left: 1em;
}
.handle {
  margin: 0.5em;
}
</style>