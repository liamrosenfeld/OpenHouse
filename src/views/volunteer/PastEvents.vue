<template>
  <div
    v-if="events != null"
    id="events"
  >
    <h1>Your Past Events</h1>
    <div
      v-for="event in regristeredEvents"
      :key="event.id"
    >
      <EventPreview :event="event" />
    </div>
    <div v-if="regristeredEvents.length == 0">
      <p>You haven't participated in any events... Yet.</p>
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
import { EventInfo, RSVP, UserAttributes } from "@/models";

import EventPreview from "@/components/volunteer/EventPreview.vue";

@Component({
  components: { EventPreview }
})
export default class PastEvents extends Vue {
  get events(): EventInfo[] {
    return this.$store.getters["events/sortedPastEvents"];
  }

  get attributes(): UserAttributes {
    return this.$store.state.user.attributes;
  }

  get signedUpEvents(): RSVP[] {
    if (this.attributes == null) {
      return [];
    }
    return this.attributes.events;
  }

  get regristeredEvents() {
    return this.events.filter(event =>
      this.signedUpEvents.some(
        signedUpEvent => signedUpEvent.eventID == event.id
      )
    );
  }
}
</script>

<style scoped lang="scss">
@import "@/shared-style/variables.scss";
@import "@/shared-style/mixins.scss";

#events {
  @include std-size;
  @include std-position;
  color: black;

  h2 {
    padding-bottom: 0.25em;
  }

  p {
    font-size: 20px;
    padding-top: 1em;
    padding-bottom: 2em;
  }
}
</style>
