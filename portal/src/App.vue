<template>
  <router-view/>

</template>

<script>
import { getLocalStorage, putLocalStorage, removeLocalStorage, tokenSessionKey } from '@/storage';
import { ElNotification } from 'element-plus';

export default {
  data() {
    return {
      sessionCookie: null,
      checkInterval: null,
    };
  },
  mounted() {
    this.setup();
    this.checkInterval = setInterval(this.checkSessionCookie, 3600000); // Check every 1 hr
  },
  watch: {
    sessionCookie(newVal, oldVal) {
      if (!newVal) {
        console.log('Session expired or user logged out.');
        // Handle session expiration or redirection here
        const isLoggedIn = getLocalStorage({ key: 'isLoggedIn' });
        if (isLoggedIn) {
          ElNotification({
            title: 'Session Timeout',
            message: 'Your session has timed-out, please login again',
            duration: 0,
          });
        }
      }
    },
  },
  methods: {
    async setup() {
      const isAuthed = await this.$http.get({ route: '/authenticated' });
      if (isAuthed.status === 200) {
        const { token } = getLocalStorage({ key: tokenSessionKey });
        const user = JSON.parse(atob(token.split('.')[1]));
        this.$store.commit('setUserData', user);
        const hasSession = this.$cookies.get('session');
        if (!hasSession) {
          removeLocalStorage({ key: tokenSessionKey });
          removeLocalStorage({ key: 'isLoggedIn' });
        }
      } else {
        this.$store.commit('isLoggedIn', false);
        putLocalStorage({ key: 'isLoggedIn', data: false });
      }
    },
  },
  checkSessionCookie() {
    const currentCookie = this.$cookies.get('session');
    if (currentCookie !== this.sessionCookie) {
      this.sessionCookie = currentCookie;
      // Additional actions can be triggered here if needed
    }
  },
  beforeDestroy() {
    // Clear the interval when the component is destroyed
    if (this.checkInterval) {
      clearInterval(this.checkInterval);
    }
  },
};
</script>

<!--
TODO: Read
[VueJS 3](https://v3.vuejs.org/guide/introduction.html)
[Vue-router](https://next.router.vuejs.org/)
[Vuex (state management)](https://next.vuex.vuejs.org/)
[Font Awesome Icons](https://fontawesome.com/v5.15/icons?d=gallery&p=2&m=free)
[Element Plus UI Controls](https://element-plus.org/en-US/component/border.html)
[TailwindCSS - bootstrap on steroids](https://tailwindcss.com/docs)
-->
