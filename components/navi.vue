<template>
  <v-app-bar app color="white" fix>
    <v-container class="py-0 fill-height">
      <v-btn v-for="item in items" :key="item.text" :to="item.link" text depressed>
        {{ item.text }}
      </v-btn>
      <v-spacer></v-spacer>
    </v-container>
  </v-app-bar>
</template>

<script>
export default {
  data: () => ({
    items: [
      {
        text: 'メイン',
        link: '/',
      },
      {
        text: '突き',
        link: '/tuki',
      },
      {
        text: '受け',
        link: '/uke',
      },
      {
        text: '蹴り',
        link: '/keri',
      },
      {
        text: '型',
        link: '/kata',
      },
      {
        text: 'GLBのテスト',
        link: '/glb',
      },
    ],
  }),
};
</script>
