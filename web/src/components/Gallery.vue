<template>
  <v-container fluid>
    <v-expansion-panels>
      <v-expansion-panel
          v-for="repoId in Object.keys(repos).sort((a, b) => repos[a].user.localeCompare(repos[b].user))"
          :key="repoId">
        <v-expansion-panel-header>
          <v-row align="center" justify="start" no-gutters>
            <v-col>
              <v-avatar
                  size="35px"
                  class="elevation-3"
              >
                <img
                    alt="Avatar"
                    :src="repos[repoId].avatar_url"
                >
              </v-avatar>
            </v-col>
            <v-col>
              <v-btn
                  :href="repos[repoId].repo_url"
                  target="_blank"
                  small
                  text
              >
                <span>{{limitString(repos[repoId].user + "/" + repos[repoId].repo, 20)}}</span>
              </v-btn>
            </v-col>
            <v-spacer/>
          </v-row>
        </v-expansion-panel-header>
        <v-expansion-panel-content>
          <v-list dense nav class="ma-n4">
            <v-list-item three-line
                         v-for="(plugin, idx) in plugins[repoId]"
                         :key="idx">
              <v-list-item-avatar class="elevation-3" size="35px" @click="open(plugin.url)">
                <v-icon v-if="plugin.icon === undefined">fa-question</v-icon>
                <v-img :src="plugin.icon" v-else/>
              </v-list-item-avatar>
              <v-list-item-content>
                <v-list-item-title v-text="plugin.name"/>
                <v-list-item-subtitle v-text="plugin.description"/>
                <v-list-item-subtitle>
                  <a :href="plugin.homepage">{{plugin.homepage}}</a>
                </v-list-item-subtitle>
              </v-list-item-content>
              <v-list-item-action>
                <v-row dense no-gutters>
                  <v-col>
                    <v-btn icon @click="install(plugin.url)">
                      <v-icon>mdi-download</v-icon>
                    </v-btn>
                  </v-col>

                  <v-col v-if="false">
                    <v-menu bottom left>
                      <template #activator="{ on }">
                        <v-btn icon v-on="on">
                          <v-icon>mdi-dots-vertical</v-icon>
                        </v-btn>
                      </template>
                    </v-menu>
                  </v-col>
                </v-row>
              </v-list-item-action>
            </v-list-item>
          </v-list>
        </v-expansion-panel-content>
      </v-expansion-panel>
    </v-expansion-panels>
  </v-container>
</template>

<script>
import {axios} from "@/utils";

export default {
  name: "GalleryComponent",
  created() {
    axios.get("/official").then(resp => {
      const {data} = resp;
      this.repos = data.repos;
      this.plugins = data.plugins;
    }).catch(err => {
      console.log(err.response.data.error);
    });
  },

  data: function () {
    return {
      repos: {},
      plugins: {}
    }
  },
  methods: {
    install(url) {
      window.location.href = `loon://import?plugin=${encodeURIComponent(url)}`;
    },

    open(url) {
      window.location.href = url;
    },

    limitString(str, sz) {
      return str.slice(0, sz) + (str.length > sz ? "..." : "");
    }
  }
}
</script>