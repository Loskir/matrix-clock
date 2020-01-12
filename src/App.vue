<template>
  <div id="app" :class="theme">
    <div class="content">
      <div v-for="hour in range(0, 23)" class="row">
        <div v-for="minute in range(0, 55, 5)" class="cell" :class="{cell_active: currentHour === hour && currentMinute === minute}">
          <span class="cell-content">
            {{prependZero(hour)}}:{{prependZero(minute)}}
          </span>
        </div>
      </div>
    </div>
    <footer class="footer">
      <span>Made with ❤️ by <a href="https://loskir.ru" target="_blank">@Loskir</a></span>
      <label>
        Theme:
        <select v-model="theme">
          <option value="dark">Dark</option>
          <option value="light">Light</option>
        </select>
      </label>
      <span><a href="https://github.com/Loskir/matrix-clock" target="_blank">GitHub</a></span>
    </footer>
  </div>
</template>

<script>
  export default {
    name: 'app',
    components: {},
    data() {
      return {
        theme: 'dark',

        currentHour: 0,
        currentMinute: 0,
      }
    },
    methods: {
      updateTime() {
        const date = new Date()

        this.currentHour = date.getHours()
        this.currentMinute = Math.round((date.getMinutes() + date.getSeconds() / 60) / 5) * 5

        while (this.currentMinute >= 60) {
          this.currentMinute -= 60
          this.currentHour += 1
        }

        setTimeout(() => this.updateTime(), 5000 - date.getMilliseconds())
      },

      range(f, t, step = 1) {
        if (step === 0) {
          throw new Error('Step must be non-zero')
        }

        let reverseFlag = false
        if (t < f) {
          reverseFlag = !reverseFlag
          let temp = t
          t = f
          f = temp
        }
        if (step < 0) {
          reverseFlag = !reverseFlag
          step *= -1
        }

        let v = f
        let a = []
        while (v <= t) {
          a.push(v)
          v += step
        }
        return a
      },

      prependZero(v) {
        if (v.toString().length < 2) {
          return '0' + v.toString()
        }
        return v.toString()
      },
    },
    computed: {},
    mounted() {
      this.updateTime()
    }
  }
</script>

<style lang="stylus">
  *, *:before, *:after
    box-sizing border-box

  body {
    margin: 0;
  }

  #app
    font-family: 'Poppins', sans-serif;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    height: 100vh;
    overflow: hidden;

    select
      font-family monospace

  a
    color #007bff
    text-decoration none
    transition .2s

    &:hover
      color #0056b3

  .content
    width 100%
    height 100%
    display flex
    flex-direction column
    justify-content space-around
    padding 30px
    margin-bottom 47px

    @media (max-width 600px)
      font-size .9rem
    @media (max-width 550px)
      font-size .8rem
    @media (max-width 500px)
      font-size .7rem

  .row
    display flex
    flex-direction row
    justify-content space-around

  .cell
    display flex
    flex-direction row
    justify-content center
    align-items center
    transition color 2s

  .footer
    position: absolute;
    z-index: 100;
    width: 100%;
    bottom: 0;
    padding: 15px 50px;
    box-sizing: border-box;
    display: flex;
    flex-direction: row;
    justify-content: space-between;
    font-family monospace

  select
    border none

  .dark
    .content
      background-color black
      color #202020
    .cell_active
      color white
    .footer
      background-color black
      color #202020
    select
      color #202020
      background-color black

    a
      color hsla(211, 100%, 25%, 1)
      &:hover
        color #003166

  .light
    .content
      color #dadada
    .cell_active
      color black

</style>
