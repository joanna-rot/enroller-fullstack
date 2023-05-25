<template>
  <div>
    <NewMeetingForm @added="addNewMeeting($event)"></NewMeetingForm>
    <span v-if="meetings.length == 0">Brak zaplanowanych spotkań.</span>
    <h3 v-else>Zaplanowane zajęcia ({{ meetings.length }})</h3>
    <MeetingsList :meetings="meetings"
                  :username="username"
                  @attend="addMeetingParticipant($event)"
                  @unattend="removeMeetingParticipant($event)"
                  @delete="deleteMeeting($event)"></MeetingsList>
  </div>
</template>

<script>
import NewMeetingForm from "./NewMeetingForm";
import MeetingsList from "./MeetingsList";
import axios from "axios";

export default {
  components: {NewMeetingForm, MeetingsList},
  props: {username: String},
  data() {
    return {
      meetings: []
    };
  },
  methods: {
    addNewMeeting(meeting) {
      this.meetings.push(meeting);
      axios.post('api/meetings', meeting)
          .then(response => {
            // udało się
            this.message = 'Udało się dodać spotkanie'

          })
          .catch(response => {
            // nie udało sie
            this.message = 'Nie udało się dodać spotkania'
          });

    },
    addMeetingParticipant(meeting) {
      meeting.participants.push(this.username);
    },
    removeMeetingParticipant(meeting) {
      meeting.participants.splice(meeting.participants.indexOf(this.username), 1);
    },
    deleteMeeting(meeting) {
      this.meetings.splice(this.meetings.indexOf(meeting), 1);
      axios.delete('api/meetings', meeting)
          .then(response => {
            // udało się
            this.message = 'Udało się usunąć spotkanie'

          })
          .catch(response => {
            // nie udało sie
            this.message = 'Nie udało się usunąć spotkania'
          });
    },
  }
}
</script>
