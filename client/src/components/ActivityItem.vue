<template>
  <article class="media">
    <figure class="media-left">
      <RouterLink :to="{ name: 'profile', params: { id: props.user.id } }" class="image is-64x64">
        <ProfilePhotoItem :userId="props.user.id" />
      </RouterLink>
    </figure>
    <div class="media-content">
      <div class="content">
        <p>
          <RouterLink :to="{ name: 'profile', params: { id: props.user.id } }">
          <!--<a :href="previewHrefLocation" @click.prevent="goToProfile(props.user.id)">-->
            <strong>{{ props.user.getName() }}
            </strong> 
          
        </RouterLink>
          <small class="handle">@{{ props.user.personalData.online_handle }}</small>
          <small class="tooltip">{{ formatTime }}
            <span class="tooltiptext">{{ timeString }}</span>
          </small>
          <br>
          <ActivityPhotoItem :userId="props.user.id" :activityIndex="indexOfActivity" />
          {{ props.activity.notes }}
        <div class="columns activity">
          <div class="column has-text-centered" v-if="isDistanceActivity">
            <span class="icon">
              <i class="fas fa-road">
                {{ props.activity.distanceInMeters }} m
              </i></span>
            <span class="icon">
              <i class="fas fa-stopwatch">
                {{ props.activity.durationInMinutes }} min
              </i></span>
            <span class="icon">
              <i class="fas fa-shoe-prints">
                {{ User.averagePace(props.activity).toFixed(2) }} m/s
              </i></span>
          </div>
          <div class="column has-text-centered" v-if="isStrengthActivity">
            <span class="icon"><i class="fas fa-dumbbell">{{ props.activity.weightInPounds }} lbs</i></span>
            <span class="icon"><i class="fas fa-redo">{{ props.activity.reps }} reps</i></span>
            <span class="icon"><i class="fas fa-redo-alt">{{ props.activity.sets }} sets</i></span>
          </div>
          <div class="column has-text-centered">
            <span class="icon">
              <i class="fas fa-fire">
                {{ props.user.calculateCaloriesBurned(props.activity).toFixed(0) }} cal
              </i></span>

            <span class="icon">
              <i class="fas fa-heartbeat">{{ props.activity.avgHeartRate }} bpm</i></span>

            <span class="icon difficulty">
              <i class="fas fa-star">
                {{ props.activity.wasDifficult ? "Difficult" : "Easy" }}
              </i></span>
          </div>
        </div>
        </p>
      </div>
      <nav class="level is-mobile">
        <div class="level-left">
          <a class="level-item">
            <span class="icon is-small"><i class="fas fa-reply"></i></span>
          </a>
          <a class="level-item">
            <span class="icon is-small"><i class="fas fa-retweet"></i></span>
          </a>
          <a class="level-item">
            <span @click="like" :class="{ 'liked': User.hasLiked(props.activity, props.userState.currentUser.id) }"
              class="icon is-small"><i class="likes fas fa-heart">&nbsp{{ props.activity.numOfLikes }}</i></span>
          </a>
        </div>
      </nav>
      <CommentItem v-for="comment in props.activity.comments" :comment="comment" :users="props.users"
        :userState="userState" />
    </div>
    <div class="media-right">
      <button v-if="props.user == props.userState.currentUser" class="delete"></button>
    </div>

  </article>
</template>

<script setup lang="ts">
import { User, type Activity } from '@/components/User';
import ProfilePhotoItem from './ProfilePhotoItem.vue';
import CommentItem from './CommentItem.vue';
import { computed, type PropType } from 'vue';
import router from '@/router';
import ActivityPhotoItem from '@/components/ActivityPhotoItem.vue';

const props = defineProps({
  user: {
    type: User,
    required: true,
  },
  activity: {
    type: Object as PropType<Activity>,
    required: true,
  },
  userState: {
    type: Object as PropType<{ currentUser: User }>,
    required: true,
  },
  users: {
    type: Array<User>,
    required: true,
  },
});

const indexOfActivity = computed<number>(() => {
  return props.user.personalData.activities.indexOf(props.activity);
})

const isDistanceActivity = computed<boolean>(() => {
  return User.isLooselyDistanceActivity(props.activity.type, props.activity.notes);
})
const isStrengthActivity = computed<boolean>(() => {
  return User.isLooselyStrengthActivity(props.activity.type, props.activity.notes);
})

// find a user who has both isDistanceActivity and isStrengthActivity in one of their activities
const findUsersWithBoth = () => {
  const users = props.users.filter((user) => {
    return user.personalData.activities.some((activity) => {
      return User.isLooselyDistanceActivity(activity.type, activity.notes) && User.isLooselyStrengthActivity(activity.type, activity.notes);
    });
  });
  return users;
}


const like = () => {
  console.log("like", props.activity, props.userState.currentUser.id)
  User.like(props.activity, props.userState.currentUser.id)
}

// Setting the Days Ago  
const formatTime = computed(() => {
  const numDaysAgo = props.activity.numDaysAgo;
  if (numDaysAgo == 0) return "Today";
  if (numDaysAgo == 1) return "Yesterday";
  if (numDaysAgo < 7) return numDaysAgo + " days ago";
  if (numDaysAgo < 14) return "Last week";
  if (numDaysAgo == 14) return "1 fortnight ago";
  if (numDaysAgo < 21) return "2 weeks ago";
  if (numDaysAgo < 28) return "3 weeks ago";
  if (numDaysAgo < 60) return "Last month";
  if (numDaysAgo < 180) return "Roughly 6 months ago";
  if (numDaysAgo < 365) return "Roughly a year ago";
  if (numDaysAgo < 730) return "Roughly two years ago";
  return "Over two years ago :O";
});

// Here for the computed time the calculation is (Now - numDaysAgo)
const timeString = computed(() => {
  const now = new Date();
  //console.log(props.activity)
  const time = new Date(now.getTime() - props.activity.numDaysAgo * 24 * 60 * 60 * 1000);
  return time.toLocaleDateString();
})
</script>

<style scoped>
.activity {
  margin-bottom: 0px;
}

.activity .icon {
  align-items: center;
  display: flex;
  justify-content: center;
  height: 1.5rem;
  width: auto;
}
.activity .icon i::before {
  margin-right: 0.5rem;
}

.difficulty {
  justify-content: initial;
}

.handle {
  margin: 0.5em;
}

.likes {
  padding-left: 1.5em;
}

.tooltip {
  position: relative;
  display: inline-block;
  border-bottom: 1px dotted black;
}

.tooltip .tooltiptext {
  visibility: hidden;
  width: 120px;
  background-color: #555;
  color: #fff;
  text-align: center;
  border-radius: 6px;
  padding: 5px 0;

  /* Position the tooltip */
  position: absolute;
  z-index: 1;
  bottom: 100%;
  left: 50%;
  margin-left: -60px;
}

.tooltip:hover .tooltiptext {
  visibility: visible;
}
</style>