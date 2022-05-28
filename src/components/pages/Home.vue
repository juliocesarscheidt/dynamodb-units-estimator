<template>
  <section class="flex flex-column flex-align-center pt-5 pb-5">
    <article class="flex flex-column flex-justify-center flex-align-center">
      <p>
        Maximum size for an item in a table is 400 KBs
      </p>

      <textarea name="message" id="message" cols="30" rows="10" class="form-control" style="resize: none;" v-model="message" maxlength="400"></textarea>
      <code class="mb-4">
        Message Size: {{ messageSize }} KBs
      </code>

      <div class="input-group-text mb-4">
        <label class="form-check-label mr-2" for="messagesPerSecond">Messages Per Second</label>
        <input type="number" name="messagesPerSecond" id="messagesPerSecond" v-model.number="messagesPerSecond">
      </div>

      <div class="container-data">
        <h3>RCU (Read Capacity Units)</h3>
        <div class="input-group-text">
          <input class="form-check-label mr-2" type="checkbox" name="stronglyConsistentRead" id="stronglyConsistentRead" v-model="stronglyConsistentRead">
          <label class="form-check-label" for="stronglyConsistentRead">Strongly Consistent</label>
        </div>
        <p>{{ calculatedReadUnits }} RCU ({{ stronglyConsistentRead ? 'Strongly' : 'Eventually' }} Consistent)</p>
      </div>

      <div class="container-data">
        <h3>WCU (Write Capacity Units)</h3>
        <p>{{ calculatedWriteUnits }} WCU</p>
      </div>
    </article>
  </section>
</template>

<script>
import { mapState, mapGetters, mapActions, mapMutations } from 'vuex'

export default {
  components: {
  },
  directives: {
  },
  filters: {
  },
  mixins: [
  ],
  props: {
  },
  data() {
    return {
      stronglyConsistentRead: false,
      messagesPerSecond: 1,
      message: '{"message": "SAMPLE MESSAGE"}',
    }
  },
  computed: {
    ...mapState([]),
    ...mapGetters([]),
    messageSize() {
      return new Blob([this.message]).size;
    },
    calculatedReadUnits() {
      let messageSizeCalculated = this.messageSize;
      const remnant = messageSizeCalculated % 4;
      if (remnant !== 0) {
        messageSizeCalculated = messageSizeCalculated + (4 - remnant);
      }
      const messages = Math.ceil(messageSizeCalculated / 4);
      const stronglyConsistentReadMultiplier = this.stronglyConsistentRead ? 1 : 2;
      return Math.ceil((this.messagesPerSecond * (messages)) / stronglyConsistentReadMultiplier);
    },
    calculatedWriteUnits() {
      return Math.ceil(this.messageSize * this.messagesPerSecond);
    },
  },
  watch: {
  },
  beforeMount() {
  },
  mounted() {
  },
  methods: {
    goTo(name) {
      this.$router.push({ name });
    },
  },
  beforeDestroy() {
  },
  destroyed() {
  },
}
</script>

<style scoped>
section {
  min-height: calc(100vh - 42px);
  width: auto;
}

section > article {
  height: auto;
  padding: 20px;
}

.container-data {
  padding: 20px;
  background: #eee; /* fallback for old browsers */
  background: -webkit-linear-gradient(to bottom, #eee, #fff); /* Chrome 10-25, Safari 5.1-6 */
  background: linear-gradient(to bottom, #eee, #fff); /* W3C, IE 10+/ Edge, Firefox 16+, Chrome 26+, Opera 12+, Safari 7+ */
  border-radius: 0.5rem;
}

@media only screen and (max-width: 991px) {
}
</style>