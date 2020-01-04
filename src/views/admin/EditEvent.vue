<template>
  <div v-if="event" class="box">
    <div class="inside">
      <h1>Edit Event</h1>
      <form action="#" @submit.prevent="saveChanges">
        <div class="group">
          <label for="name">
            <h2>Event Name</h2>
          </label>

          <div>
            <input
              v-model="event.name"
              type="text"
              class="form-control"
              name="name"
              placeholder="Name"
            />
          </div>
        </div>

        <div class="group">
          <label for="blurb">
            <h2>Blurb</h2>
          </label>

          <div>
            <ResizeAuto>
              <template v-slot:default="{ resize }">
                <textarea
                  ref="blurb"
                  v-model="event.blurb"
                  class="form-control"
                  name="blurb"
                  @input="resize"
                />
              </template>
            </ResizeAuto>
          </div>
        </div>

        <div class="group">
          <label for="details">
            <h2>Details</h2>
          </label>

          <div>
            <MDEditor v-model="event.details" />
            <p>Please don't use headings (#) here</p>
          </div>
        </div>

        <div class="group">
          <label for="shifts">
            <h2>Shifts</h2>
          </label>

          <div>
            <ShiftsEditor v-model="event.shifts" />
          </div>

          <div>
            <input
              v-model="event.wholeShift"
              type="checkbox"
              name="wholeShift"
            />
            <label for="wholeShift">Must Attend Entire Shift</label>
          </div>
        </div>

        <div class="action-button save">
          <button type="submit" class="submit">
            <p>Save Changes</p>
          </button>
        </div>
      </form>

      <div class="action-button delete">
        <button @click="deleteEvent">
          <p>Delete Event</p>
        </button>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Vue, Watch } from "vue-property-decorator";
import { EventInfo } from "@/models";
import { deepCopy } from "@/util";

import MDEditor from "@/components/MDEditor.vue";
import ShiftsEditor from "@/components/ShiftsEditor.vue";
import ResizeAuto from "@/components/ResizeAuto.vue";

@Component({
  components: {
    MDEditor,
    ShiftsEditor,
    ResizeAuto
  }
})
export default class EditEvent extends Vue {
  event: EventInfo | null = null;

  get events(): EventInfo[] {
    return this.$store.state.events.events;
  }

  get index(): number {
    return this.events.findIndex(
      event => event.id === this.$route.params["id"]
    );
  }

  // buttons
  saveChanges() {
    this.$store.dispatch("events/setEvent", {
      index: this.index,
      event: this.event
    });
  }

  deleteEvent() {
    // TODO: Cloud Function
  }

  // load copy of event
  created() {
    this.event = deepCopy<EventInfo>(this.events[this.index]);
  }

  @Watch("events")
  indexChanged() {
    this.event = this.events[this.index];
  }
}
</script>

<style scoped lang="scss">
@import "@/shared-style/variables.scss";
@import "@/shared-style/mixins.scss";

.box {
  @include shadow-box;
  @include std-size;
  @include std-position;

  textarea {
    @include rounded;
    font-family: "Avenir", Helvetica, Arial, sans-serif;
    font-size: 15px;
    background-color: #f6f6f6;
    width: 100%;
    resize: none;
    box-sizing: border-box;
    padding: 10px 20px;
  }

  .group {
    margin-bottom: 1em;
  }

  .action-button {
    @include button;
    margin-top: 1.6em;
    margin-left: auto;
    margin-right: auto;

    p {
      color: white;
    }
  }

  .delete {
    background-color: $scaryLinkColor;

    &:hover {
      background-color: $hoverScaryLinkColor;
    }
  }

  .save {
    background-color: $linkColor;

    &:hover {
      background-color: $hoverLinkColor;
    }
  }
}
</style>