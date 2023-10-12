<script lang="ts">
  import type { PricingRange, PricingEntry, PricingColumn} from "$lib/types/accos";
  import { DateTime } from 'luxon';
  import PricingNucleus from './PricingNucleus.svelte'

  import { i18n } from '$lib/conf';
  import { currentLang } from '$lib/stores/lang';
  $: trans = i18n.translations[$currentLang];
  $: dict = trans.site

  const t = ( c:string, d:typeof dict ) => d[c] ? d[c] : c

  export let global:PricingEntry|undefined = undefined;
  export let entries:PricingRange[] = [];

  let filteredEntries:PricingRange[] = [];
  $: {
    let now = DateTime.now()
    filteredEntries = entries.filter( e => {
      if (! e.to) return true
      return e.to > now 
    })
  }

  export let columns:PricingColumn[] = [];
  export let footnote:string = "";

  const colHeaderStyle = {
    timeRange: 'width: 20%;', 
    firstNight: 'width: 12%',
    eachNight: 'width: 12%',
    peopleNum: 'width: 9%',
    extraPerson: 'width: 35%',
    minNumNights: 'width: 12%'
  }

  const colCellStyle = {
    timeRange: 'text-align:center;', 
    firstNight: 'text-align:right;',
    eachNight: 'text-align:right;',
    peopleNum: 'text-align: center;',
    extraPerson: 'text-align:left;',
    minNumNights: 'text-align:right;'
  }

  let w:number = 801;

</script>

<figure bind:clientWidth={w} class="pricing-wrapper">
  {#if global}
    <table class="pricing-table-global">
      <thead>
        <tr>
          <th>{t("globalPricing", dict)}</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>
            {#if global.firstNightPrice}
              <strong>{@html t("firstNight", dict)}:</strong> <PricingNucleus pricingSpec={global} pricingColumn="firstNight" />
            {/if}
            {#if global.perNightPrice}
              <strong>{@html t("eachNight", dict)}:</strong> <PricingNucleus pricingSpec={global} pricingColumn="eachNight" />
            {/if}
            {#if global.additionalPersonPrice1}
              <br><hr>
              <strong>{@html t("extraPerson", dict)}:</strong> <PricingNucleus pricingSpec={global} pricingColumn="extraPerson" />
            {/if}
            {#if global.minNumberOfNights}
              <br><hr>
              <strong>{@html t("minNumNights", dict)}:</strong> <PricingNucleus pricingSpec={global} pricingColumn="minNumNights" />
            {/if}
          </td>
        </tr>
      </tbody>
    </table>
  {/if}
  {#if w > 799}
    <table class="pricing-table">
      <thead>
        <tr>
        {#each columns as h} 
          <th scope="col" style="{colHeaderStyle[h]}">{@html t( h, dict)}</th>
        {/each}
        </tr>
      </thead>
      <tbody>
      {#each filteredEntries as e}
        <tr>
          {#each columns as h}
          <td style="{colCellStyle[h]}">  
            <PricingNucleus pricingSpec={e} pricingColumn={h} />
          </td>
          {/each}
        </tr>
      {/each}
      </tbody>
      {#if footnote}
      <tfoot>
        <tr>
          <td colspan="{columns.length}">
            {@html t(footnote, dict)}
          </td>
        </tr>
      </tfoot>
      {/if}
    </table>
  {:else if w > 400 && w < 800 }
    <table class="pricing-table">
      {#each filteredEntries as e}
        <thead>
          <tr>
            <th colspan="2" scope="col">
              <PricingNucleus pricingSpec={e} pricingColumn="timeRange" />
            </th>
          </tr>
        </thead>
        <tbody>
          {#each columns as h}
            {#if h !== "timeRange"}
            <tr>
              <th scope="row">{@html t(h, dict)}</th>
              <td><PricingNucleus pricingSpec={e} pricingColumn={h} /></td>
            </tr>
            {/if}
          {/each}
        </tbody>
      {/each}
      {#if footnote}
      <tfoot>
        <tr>
          <td colspan="2">
            {@html t(footnote, dict)}
          </td>
        </tr>
      </tfoot>
      {/if}
    </table>
  {:else}
  <table class="pricing-table">
    {#each filteredEntries as e}
      <thead>
        <tr>
          <th scope="col" class="main-header">
            <PricingNucleus pricingSpec={e} pricingColumn="timeRange" />
          </th>
        </tr>
      </thead>
      <tbody>
        {#each columns as h}
          {#if h !== "timeRange"}
          <tr>
            <th scope="row">{@html t(h, dict)}</th>
          </tr>
          <tr>
            <td><PricingNucleus pricingSpec={e} pricingColumn={h} /></td>
          </tr>
          {/if}
        {/each}
      </tbody>
    {/each}
    {#if footnote}
    <tfoot>
      <tr>
        <td>
          {@html t(footnote, dict)}
        </td>
      </tr>
    </tfoot>
    {/if}
  </table>
  {/if}
</figure>


<style>
  figure {
    width: 100%;
    margin: 0;
    border: var(--main-border);
  }

  table {
    width: 100%;
    table-layout: fixed;
    border-collapse: collapse;
  }

  .pricing-table-global td {
    padding: 1.5rem;
  }

  th {
    text-overflow: ellipsis;
    overflow: clip;
    letter-spacing: 2px;
    padding-top: 1rem;
    padding-bottom: 1rem;
  }

  td {
    padding-top: 0.8rem;
    padding-bottom: 0.8rem;
    padding-right: 1rem;
    padding-left: 0.2rem;
  }

  @media(max-width: 400px) {
    td {
      text-align: center;
    }
  }

  thead {
    background-color: var(--table-header-bg-color);
    color: var(--table-header-font-color);
  }

  tfoot {
    background-color: var(--table-footer-bg-color);
    color: var(--table-footer-font-color);
  }
  tfoot td {
    padding: 1rem;
  }

  tbody tr:nth-child(odd) {
    background-color: var(--table-bg-color-odd, white);
    color: var(--table-font-color-odd, black)
  }

  tbody tr:nth-child(even) {
    background-color: var(--table-bg-color-even, black);
    color: var(--table-font-color-even, white)
  }

  .main-header {
    font-weight: bolder;
  }

</style>