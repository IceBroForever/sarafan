<template lang="pug">
  div
    div.box
      a.button.is-primary(@click.stop="handleLoad") Оновити
    div.container.columns.is-multiline
      div.column.is-4-desktop.is-6-tablet(v-for="(position,i) in positions", :key="i")
        position-short(:position="position")
</template>
<script>
  import PositionShort from './PositionShort';
  import PositionAPI from '#/PositionAPI';

  export default {
    data () {
      return {
        positions: []
      }
    },
    components: {
      PositionShort
    },
    methods: {
      async handleLoad () {
        this.$bus.$emit('load-start')
        try {
          let result = await PositionAPI.loadByCompany(this.company.id || this.company._id);
          console.log(result.data)
          if (result.data.success) {
            this.positions = result.data.item;
          } else {
            throw result.data.message;
          }
        } catch (err) {
          this.$messages.error(err, this);
        }
        this.$bus.$emit('load-end')
      },
    },
    computed: {},
    props: {
      company: {
        required: true,
        type: Object
      }
    },
    mounted () {
      this.handleLoad();
      this.$bus.$on('positions-update', this.handleLoad);
      this.$bus.$on('invites-update', this.handleLoad)
    },
    destroyed () {
      this.$bus.$off('positions-update', this.handleLoad);
      this.$bus.$off('invites-update', this.handleLoad)
    },
  }
</script>
<style scoped lang="scss">
</style>
