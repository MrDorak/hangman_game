<template>
  <div>
    <canvas ref="canva" style="border: 1px solid black;">
      Désolé, votre navigateur ne prend pas en charge &lt;canvas&gt;.
    </canvas>
  </div>
</template>

<script>

export default {
  name: 'Game',
  data: () => ({
    canva: null,
  }),
  mounted () {
    this.initCanva()
  },
  methods: {
    initCanva () {
    const canva = this.$refs.canva
    canva.getContext('2d');
    }
  }
}
</script>