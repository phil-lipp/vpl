<template>
  <div>
    <div :style="{'height': ht, 'width': wd}">
      <q-img
      :src="image"
      style="height: 180px; max-width: 400px"
      v-if="blockArt === 'image'"></q-img>
      <q-card dark bordered class="bg-grey-9 my-card" v-if="blockArt === 'txt1' || blockArt === 'txt2' || blockArt === 'txt3'">
        <q-card-section class="text-center" v-if="blockArt === 'txt1'">
          OCR not possible - error code: 0x122934b
          <br/>wrong image resolution
        </q-card-section>
        <q-card-section class="text-center" v-if="blockArt === 'txt2'">
          Lazze haglagolo       connic  . lureb zubih
          zloderu . luzemrego   deuuih . luzeb b moki
          delom . luzem rego    Da mirartte napomoki
          lepocam . Tofe uue    bru . Kibogu moih gre
          ruiu ubog uze mo      chou . Dabim cifto 12
          goki . lu iega zin    pouued ziuoril . lod
          lu zuueu dub.dara     puzrie orboga priel.
          tri imena . edin bog  Bogu uhe mogu Kemu
          gozpod zuueri .       12puuede uhe moie
          ife zuori nebo . lz   greche . lrece marie .
          emlo . Tofe izco ie   Y zeh nepraudrub del .
          ga milobu . Ifee      in epraudnega pomif lena
          mariae . Ifee mie     E re iechem uuede Ztuo
          hahela . Iscee pe     ril . ili neuuede . nudmi
          tra . lureb bofih Zil ili lubmi Zpe ili bde?
          lubeb borih mofe-     Y hpttnib rorab . V lifnih
          nic . lufeh b za      refih . vtatbinah . Y hnici
        </q-card-section>
        <q-card-section class="text-center" v-if="blockArt === '3'">
          OCR not possible - error code: 0x122934b
          <br/>wrong image resolution
        </q-card-section>
      </q-card>
    </div>
  </div>
</template>

<script>
export default {
  // name: 'ComponentName',
  props: ['ht', 'wd', 'color', 'image', 'blockArt'],
  data () {
    return {
    }
  },
  methods: {

  }
}
</script>

<style>
</style>
