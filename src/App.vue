<template>
  <div id='app' class="main container" >
    <header class="main-header">
    </header>
    <transition name="fade" mode="out-in">
      <router-view :call_back="login_call_back"></router-view>
    </transition>
  </div>
</template>
<script>
  import $ from 'jquery'
  import store from './store'
  import NProgress from 'nprogress'
  export default {
    store,
    data () {
      return {
        key: '',
        scroll_wait: false // 不让checkBar因为触发太多次而影响效率
      }
    },
    components: {
    },
    watch: {
      'unread_message_count': function (val, oldVal) {
        if (this.unread_message_count === 0) {
          document.title = 'Follow Center'
        } else {
          document.title = `(${this.unread_message_count}) Follow Center`
        }
      },
      'loading': function (val, oldVal) {
        if (val) {
          NProgress.start()
        } else {
          NProgress.done()
        }
      }
    },
    computed: {
      show_bar () {
        return this.$store.state.show_bar
      },
      loading () {
        return store.state.p.loading
      },
      unread_message_count () {
        return store.state.unread_message_count
      },
      user_name () {
        return store.state.p.user_info.user_name
      },
      avatar () {
        return store.state.p.user_info.picture
      }
    },
    mounted () {
      this.$store.dispatch('getUserInfo')
      this.$nextTick(function () {
        $('.fix-bz').visibility(
          {
            type: 'fixed'
          }
        )
        $('.first-logo').visibility(
          {
            once: false,
            onTopPassed: function () {
              var y = $('.first-logo').offset().left
              $('.first-logo').css('left:' + y)
              $('.first-logo').addClass('fixed')
              $('#header').addClass('padding-left-bz')
              $('.move-left-bz').addClass('move-titile')
            },
            onTopPassedReverse: function () {
              // this.checkBar()
              // this.show_bar = true
              $('.first-logo').removeClass('fixed')
              $('#header').removeClass('padding-left-bz')
              $('.move-left-bz').removeClass('move-titile')
            }
          }
        )
        this.$on('checkBar',
          function () {
            if (this.scroll_wait) return
            else {
              this.checkBar()
              this.scroll_wait = true
              let _this = this
              setTimeout(
                function () {
                  _this.scroll_wait = false
                }
              , 200)
            }
          }
        )
      })
    },
    methods: {
      logout: function () {
        window.location.href = '/api_logout'
      },
      login_call_back: function () {
        this.$router.go({name: 'Main'})
      },
      search: function () {
        if (this.key) {
          store.state.search_messages = [] // 清空上次的查找
          this.$router.go({name: 'Search', params: {key: this.key}})
        } else {
          this.$router.go({name: 'Main'})
        }
      },
      backToMain: function () {
        this.$router.push({name: 'Main'})
      }
    }
  }
</script>
