<template>
  <div>
    <div v-if="loading">Loading Fantasy Data of Samajik Reunion...</div>
    <div v-else>
    <nav class="bg-gray-800">
      <div class="max-w-screen-xl mx-auto px-4 sm:px-6 lg:px-8">
        <div class="flex items-center justify-between h-16">
          <div class="flex items-center">
            <div class="">
              <div class="ml-10 flex items-baseline">
                  <div
                    class="px-3 py-2 rounded-md text-sm font-medium text-white bg-gray-900 focus:outline-none focus:text-white focus:bg-gray-700"
                    >Home of Fantasy Samajik</div>
              </div>
            </div>
          </div>

      
        </div>
      </div>


    </nav>
        <header class="bg-white shadow" v-if="$route.meta.title">
      <div class="max-w-screen-xl mx-auto py-6 px-4 sm:px-6 lg:px-8">
        <h3 class="text-2xl font-bold leading-tight text-gray-900" v-if="week.champion">
          🏆 Manager of the Week: <i> {{ week.champion.mgr_name }} ({{week.champion.live_points}} points) </i>
        </h3>
        <h3 class="text-2xl font-bold leading-tight text-gray-900" v-if="week.flop">
          🐔 Flop of the Week: <i>{{ week.flop.mgr_name }} ({{ week.flop.live_points }} Point)</i>
        </h3>
        <h3 class="text-2xl font-bold leading-tight text-gray-900" v-if="week.flop">
        🧠 Mastermind of the Week: <i>{{ mastermind[0].name }} (Transfer points: {{ mastermind[0].net_transfer_points }} Point, Transfers: {{ mastermind[0].transfers}})</i>
        </h3>
        <h3 class="text-2xl font-bold leading-tight text-gray-900" v-if="week.flop">
        🤖 Overthinker of the Week: <i>{{ mastermind[mastermind.length - 1].name }} (Transfer points: {{ mastermind[mastermind.length -1].net_transfer_points }} Point, Transfers: {{ mastermind[mastermind.length - 1].transfers}})</i>
        </h3>
      </div>
    </header>


  <main>
    <div class="px-4 py-6 sm:px-0 flex flex-col">
    <div
        class="xs:w-full w-full overflow-x-scroll mr-12 border-4 border-dashed border-gray-200 rounded-lg h-96 p-4 text-center text-gray-400"
      >
      <span>League Standings</span>

      <table class="w-full overscroll-auto mt-4 border-2 border-green-800 border-collapse table-auto text-blue-800" border="" >
        <thead>
          <tr class="border-2 border-gray-200 p-1">
            <th>Manager </th>
            <th>Total Point</th>
            <th>GW Point</th>
            <th>Diffrence from top</th>
            <th>Global Ranking</th>

          </tr>
          </thead>
        <tbody>
          <tr class="" v-for="manager in managers" :key="manager.entry">
            <td class="border-2 border-gray-200 p-1">{{ manager.name }}</td>
            <td class="border-2 border-gray-200 p-1">{{  manager.live_overall_points }}</td>
            <td class="border-2 border-gray-200 p-1">{{ manager.gw_points - manager.gw_transfers_cost }}</td>
            <td class="border-2 border-gray-200 p-1">{{  topPoint - manager.live_overall_points }}</td>
            <td class="border-2 border-gray-200 p-1">{{  manager.current_overall_rank }}</td>

          </tr>
        </tbody>
      </table>


      </div>

      <div
        class="xs:w-full overflow-x-scroll mt-4 w-full border-4 border-dashed border-gray-200 rounded-lg h-96 p-4 text-center text-gray-600"
      >
      <span>GW{{ gw}} Captain Picks</span>

      <table class="w-full mt-4 border-2 border-green-800 border-collapse table-auto text-blue-800" border="" >
        <thead>
          <tr class="border-2 border-gray-200 p-1">
            <th>Manager </th>
            <th>Captain</th>
            <th>GW Point </th>
          </tr>
          </thead>
        <tbody>
          <tr class="" v-for="manager in managers" :key="manager.entry">
            <td class="border-2 border-gray-200 p-1">{{ manager.name }}</td>
            <td class="border-2 border-gray-200 p-1">{{  manager.captain.display_name }}</td>0
            <td class="border-2 border-gray-200 p-1">{{ getCaptainScore(manager.captain) }} Point</td>
          </tr>
        </tbody>
      </table>


      </div>
    </div>
  </main>
  </div>
  </div>
</template>

<script>
import { defineComponent } from 'vue'
import axios from "axios";
import apiData from './../data/api';
export default defineComponent({
  mounted() {
    this.loading = true;

      console.log(apiData);
        this.$data.players = apiData.player_data;

        this.$data.managers = apiData.managers.map(manager => ({
          captain: this.getCaptain(manager),
          name: manager.mgr_name,
          net_transfer_points: manager.gw_net_transfer_points,
          transfers: manager.gw_transfers,
          gw_points: manager.gw_points,
          live_points: manager.live_points,
          gw_transfers_cost: manager.gw_transfers_cost,
          picks: manager.picks,
          current_overall_rank: manager.current_overall_rank,
          live_overall_points: manager.live_overall_points
        }));
        this.$data.gw = apiData.managers[0].gw;
        let sortedManager = apiData.managers.sort((a,b) => (b.live_points - a.live_points));
        this.$data.week =  {
          champion: sortedManager[0],
          flop: sortedManager[sortedManager.length-1] 
        }        
        this.loading=false;
  },
  computed: {
    topPoint() {
      // return 200;
      return this.managers.sort((a,b) => (a.live_overall_points>b.live_overall_points))[0].live_overall_points
    },
    mastermind() {
      console.log(this.managers.sort((a,b) => (b.net_transfer_points - a.net_transfer_points))[0]);
      return this.managers.sort((a,b) => (b.net_transfer_points - a.net_transfer_points));
    }
  },
  data: () => ({
    loading: false,
    managers: {},
    week: {},
    gw: 0
  }),
  methods: {
    getCaptain(manager) {
      let captainId = manager.picks.find(pick => pick.is_captain == true);
      let player = this.players.find(player => (player.id == captainId.element));
      return player;
    },
    getCaptainScore(captain) {
      let gw = this.gw;
      let gwkey = "points_gw" + gw;
      return captain[gwkey]
    },
  }
})
</script>
