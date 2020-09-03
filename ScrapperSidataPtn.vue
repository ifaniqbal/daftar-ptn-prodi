<template>
  <button
    class="rounded shadow p-3 bg-blue-600 text-white font-bold"
    @click="scrap">
    Scrap
  </button>
</template>

<script>
import axios from 'axios'
import cheerio from 'cheerio'

export default {
  methods: {
    async scrap() {
      let res = await axios.get('https://sidata-ptn.ltmpt.ac.id/ptn_sb.php')
      const $ = cheerio.load(res.data)
      let ptn = $('#wilayah_all table tbody tr td:nth-child(3) a:first-child')
      let universities = []
      let departments = []
      ptn.each(async function(i, el) {
        let id = $(this).attr('href').substring(5)
        let name = $(this).text()
        universities.push({ id, name })
      })

      for (let i = 0; i < universities.length; i++) {
        let university_id = universities[i].id
        let resProdi = await axios.get('https://sidata-ptn.ltmpt.ac.id/ptn_sb.php?ptn='+university_id)
        const $$ = cheerio.load(resProdi.data)
        let prodi = $$('#myTabContent table tbody tr td:nth-child(3) a:first-child')
        prodi.each(function(i, el) {
          let id = $$(this).attr('href').split('&')[1].split('=')[1]
          let name = $$(this).text()
          departments.push({ id, name, university_id })
        })
      }

      console.log(universities)
      console.log(departments)
    }
  }
}
</script>