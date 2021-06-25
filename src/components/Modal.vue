<template>
  <div class="text-center">
    <v-dialog v-model="dialog" width="500">
      <template v-slot:activator="{ on, attrs }">
        <div v-bind="attrs" v-on="on" small>{{name}}s</div>
      </template>

      <v-card>
        <v-card-title class="text-h5 grey lighten-2">Select a {{name}}</v-card-title>

        <v-card-text>
          <div :key="item.id" v-for="item in items">
            <p
              @click="$emit('addItem', itemData={name:name,id:item.id}), dialog=false"
            >{{ item.url }}</p>
          </div>
        </v-card-text>

        <v-divider></v-divider>

        <v-card-actions>
          <v-spacer></v-spacer>
          <div v-if="this.loading===false">
            <v-btn text @click="upload">Add {{name}}s</v-btn>
          </div>
          <div v-else>
            <v-btn text>Loading</v-btn>
          </div>

          <v-btn color="primary" text>Close</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </div>
</template>

<script>
export default {
  name: "Modal",

  props: ["items", "name"],

  methods: {
    upload() {
      var selectedUrl;

      if (this.name === "Image") {
        selectedUrl = "https://www.somerandomimagesite.com/";
      } else if (this.name === "Video") {
        selectedUrl = "https://www.somerandomvideosite.com/";
      } else {
        selectedUrl = "https://www.somerandomfilesite.com/";
      }

      this.loading = true;
      setTimeout(
        function (scope) {
          scope.loading = false;
          scope.$emit("upload", { url: selectedUrl, name: scope.name });
        },
        2000,
        this
      );
    },
  },

  data() {
    return {
      dialog: false,
      loading: false,
    };
  },
};
</script>

<style scoped>
</style>