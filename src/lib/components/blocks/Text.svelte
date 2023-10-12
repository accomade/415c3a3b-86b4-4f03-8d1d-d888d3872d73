<script lang="ts">
  import { currentLang } from '$lib/stores/lang';
  import { i18n } from '$lib/conf';
 
  export let ref:string
  
  $: {
    console.log('Text lang', $currentLang)
    console.log('trans', i18n.translations[$currentLang].calendar.weekendLabel)
  }

  $: trans = i18n.translations[$currentLang];
  $: out = trans.site[ref] ?? ref
  
  const innerHtml = (node:HTMLDivElement) => {
    node.innerHTML = out;
    return {
      update: (html:string) => {
        node.innerHTML = out
      }
    }
  }
</script>

<div class="content" use:innerHtml>

</div>

<style>
  .content {
    text-align: left;
    margin-left: 0.2rem;
    margin-right: 0.2rem;
  }
</style>