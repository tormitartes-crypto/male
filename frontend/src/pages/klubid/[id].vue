<template>
  <v-container>
    <v-row v-if="club">
      <v-col>
        <h1 class="mb-2">
          <a href="/klubid">Klubid</a> / {{ club.name }}
        </h1>
      </v-col>
    </v-row>

    <div v-if="club">
      <v-row dense>
        <v-col cols="12" md="6" lg="4">
          <v-card outlined>
            <v-card-title>{{ club.membersCount }}</v-card-title>
            <v-card-text>
              MÄNGIJAID
            </v-card-text>
          </v-card>
        </v-col>

        <v-col cols="12" md="6" lg="4">
          <v-card outlined>
            <v-card-title>{{ club.averageRating }}</v-card-title>
            <v-card-text>
              KESKMINE REITING
            </v-card-text>
          </v-card>
        </v-col>
      </v-row>

      <!-- BOONUSÜLESANNE: klubi top 3 mängijat -->
      <v-row class="mt-6 mb-2">
        <v-col cols="12">
          <h2>Klubi top 3 mängijat</h2>
        </v-col>
      </v-row>

      <v-row v-if="bestPlayers.length > 0" dense>
        <v-col
          v-for="(player, index) in bestPlayers"
          :key="player.name"
          cols="12"
          md="4"
        >
          <v-card outlined class="pa-4 text-center">
            <v-card-title class="justify-center">
              {{ index + 1 }}. koht
            </v-card-title>

            <v-card-text>
              <h3>{{ player.name }}</h3>
              <p class="mb-0">
                {{ player.points }} punkti
              </p>
            </v-card-text>
          </v-card>
        </v-col>
      </v-row>

      <v-row v-else>
        <v-col cols="12">
          <p>Selle klubi mängijate poodiumit ei leitud.</p>
        </v-col>
      </v-row>

      <v-row cols="12" md="8">
        <v-col>
          <v-divider :thickness="3"></v-divider>
        </v-col>
      </v-row>

      <v-row>
        <v-col cols="12" md="6" lg="4">
          <ModifyClubForm
            :is-update="true"
            :club-id="clubId"
            @club-updated="fetchClubData"
          />
        </v-col>
      </v-row>

      <v-row cols="12" md="8">
        <v-col>
          <v-divider :thickness="3"></v-divider>
        </v-col>
      </v-row>

      <v-row>
        <v-col>
          <PlayersSearchTable :club-id="clubId" />
        </v-col>
      </v-row>
    </div>

    <div v-else>
      <h2>Klubi ei leitud</h2>
      <p>Vabandame, antud klubi ei eksisteeri või on andmed puudulikud.</p>
      <v-btn
        color="primary"
        @click="this.$router.push('/klubid')"
      >
        Tagasi klubide lehele
      </v-btn>
    </div>
  </v-container>
</template>

<script>
import { fetchClubById, fetchClubBestPlayers } from "@/wrapper/clubsApiWrapper.js";

import PlayersSearchTable from "@/components/clubs/PlayersSearchTable.vue";
import AddClubDialog from "@/components/clubs/AddClubDialog.vue";
import ModifyClubForm from "@/components/clubs/ModifyClubForm.vue";

export default {
  name: "ClubDetailsPage",

  components: {
    ModifyClubForm,
    AddClubDialog,
    PlayersSearchTable
  },

  data() {
    return {
      club: null,
      clubId: null,
      bestPlayers: [],
      showModifyClubDialog: false,
    }
  },

  created() {
    this.clubId = this.$route.params.id;

    this.$watch(
      () => this.$route.params.id,
      this.fetchClubData,
      { immediate: true }
    )
  },

  methods: {
    async fetchClubData() {
      this.clubId = this.$route.params.id;
      this.club = await fetchClubById(this.clubId);
      this.bestPlayers = await fetchClubBestPlayers(this.clubId);
    },

    openModifyClubDialog() {
      this.showModifyClubDialog = true;
    },

    updateShowModifyDialog(value) {
      this.showModifyClubDialog = value;
    },
  }
}
</script>

<style scoped>
.club-title {
  margin-bottom: 1.5rem;
  font-size: 2rem;
  font-weight: bold;
}
</style>