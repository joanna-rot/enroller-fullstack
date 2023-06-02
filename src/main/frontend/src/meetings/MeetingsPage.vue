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
      meetings: Array,

    };
  },

  mounted() {
    this.getAllMeetings();
  },

  methods: {
    addNewMeeting(meeting) {
      axios.post('api/meetings', meeting)
          .then(response => {
            //meeting.id = response.data.id;
            // udało się
            console.log = ('Udało się dodać spotkanie');
            this.meetings.push(meeting);
          })
          .catch(resopnse => {
            // nie udało sie
            console.log = ('Nie udało się dodać spotkania');

          });

    },

    getAllMeetings() {
      axios.get('api/meetings')
          .then(response => {
            // udało się
            //console.log(response.data);
            this.meetings = response.data;
          })
          .catch(error => {
            // nie udało się
            console.error('Nie udało się pobnrać meetingów', error);
          });
    },

    addMeetingParticipant(meeting) {
      meeting.participants.push(this.username);
    },
    removeMeetingParticipant(meeting) {
      meeting.participants.splice(meeting.participants.indexOf(this.username), 1);
    },

    deleteMeeting(meeting) {
      axios.delete(`api/meetings/${meeting.id}`)
          .then(response => {
            // udało się
            console.log = (`Udało się usunąć spotkanie ${meeting.id}`);
            this.meetings.splice(this.meetings.indexOf(meeting), 1);
          })
          .catch(error => {
            console.error('Nie udało się usunąć spotkania', error);
          });

    },
  }
}
</script>
