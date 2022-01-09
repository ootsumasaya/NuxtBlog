<template>
  <v-app >
    <navi />
    <v-main>
    <div>
    <h1>メインページ</h1>
      <v-row>
    <v-col>
      <video
        class="preview"
         :src="videoID + '/sample.mp4'"
        controls
        preload="metadata"
      />
    </v-col>
  </v-row>
    </div>
    </v-main>
  </v-app>
</template>

<script>
import navi from '/components/navi';

export default {
  components: {
    navi,
  },
};
</script>
