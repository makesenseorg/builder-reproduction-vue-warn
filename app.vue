<template>
  <div class="page-list-articles">
    <!-- title : {{ content.data.title }} -->
    <template v-if="!canShowContent">
      <div>Content not Found</div>
    </template>
    <template v-else>
      <RenderContent
        class="render-content"
        model="page"
        :content="content"
        :apikey="apiKey"
        :customComponents="getCustomComponents"
      />
    </template>
  </div>
</template>
<script>import { RenderContent, isPreviewing, getContent } from '@builder.io/sdk-vue/vue3';
// import { REGISTERED_COMPONENTS } from '@/init-builder.ts';
import { useAsyncData } from 'nuxt/app';

export default defineNuxtComponent({
  components: {RenderContent},
  data() {
    return {
      loading: true
    }
  },
  computed: {
    isPreviewing() { return isPreviewing },
    apiKey() {
      return '6699e11b753c40d5a84d90175d459363';
    },
    // getCustomComponents() {
    //   return REGISTERED_COMPONENTS;
    // },
  },
  async setup() {
    const nuxtApp = useNuxtApp();

    try {
      // const route = useRoute();

      const { data: content } = await useAsyncData('content', () => getContent({
        apiKey: '6699e11b753c40d5a84d90175d459363',
        includeRefs: true,
        noTraverse: false,
        model: 'page',
        userAttributes: {
          urlPath: '/media',
        },
      }));

      const canShowContent = !!content.value || isPreviewing();

      if (!content) {
        console.log('no content');

        throw createError({ statusCode: 404, statusMessage: 'Page Not Found', fatal: true });
        // todo: redirect to 404 not working?
      }
      return { content: content.value, canShowContent} ;
    }
    catch (err) {
      console.log('in setup error');
      console.log(err);
    }
    
  },
  async mounted() {
    // Re-run this check on the client in case of SSR
    this.canShowContent = this.content || isPreviewing();
  },

});
</script>