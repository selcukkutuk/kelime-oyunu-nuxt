<template>
  <v-layout column justify-center align-center>
    <v-flex xs12>
      <v-card v-if="tamamlandi" class="mb-4" width="1000">
        <v-card-text>
          <span v-text="puan <= 0 ? 'Üzgünüm!' : 'Tebrikler!'"></span>
          yarışmayı
          <v-chip
            :color="puan <= 0 ? 'red darken-1' : 'light-green darken-1'"
            v-text="puan"
          ></v-chip
          >puan ile tamamladınız!
        </v-card-text>
      </v-card>
      <v-card v-if="!mevcutSoru" class="mb-4" width="1000">
        <v-card-title>
          <h4>Kelime Oyunu Yarışmasına Hoşgeldiniz!</h4>
        </v-card-title>
        <v-divider />
        <v-card-text
          >Yarışmaya başlamak için yarışmaya başla butonuna basın.</v-card-text
        >
        <v-card-actions>
          <v-spacer />
          <v-btn color="primary" @click="basla">Yarışmaya Başla</v-btn>
        </v-card-actions>
      </v-card>
      <v-card v-else width="1000">
        <v-card-title class="headline">
          <h4 v-text="mevcutSoru.soru"></h4>
          <v-spacer />
          <v-chip pill class="mr-2">
            <v-avatar left color="green">TP</v-avatar>
            {{ puan }}
          </v-chip>
          <v-chip pill class="mr-2">
            <v-avatar left color="blue">KP</v-avatar>
            {{ harfPuan }}
          </v-chip>
          <v-chip pill class="mr-2">
            <v-avatar left color="red">KS</v-avatar>
            {{ kalanSure }}
          </v-chip>
        </v-card-title>
        <v-divider />
        <v-card-text class="d-flex">
          <harf
            v-for="(harf, index) in harfler"
            :key="'harf-' + index"
            class="mr-2"
            :deger="harf.harf"
            :acik="harf.acildi"
          />
        </v-card-text>
        <v-divider />
        <v-card-actions>
          <v-text-field
            v-model="yarismaciCevap"
            light
            single-line
            solo
            clearable
            :hide-details="true"
            label="Cevabınız ?"
            class="mr-2"
            @keyup="yarismaciCevap = yarismaciCevap.toLocaleUpperCase('tr')"
          >
            <template v-slot:append>
              <v-btn color="error" @click="bitir">Bitir</v-btn>
              <v-btn color="primary" @click="harfver">Harf Ver</v-btn>
              <v-btn color="success" @click="cevapver">Cevap Ver</v-btn>
            </template>
          </v-text-field>
        </v-card-actions>
      </v-card>
      <v-alert
        v-if="mesaj"
        :color="mesajClass"
        class="mt-4"
        v-text="mesaj"
      ></v-alert>
    </v-flex>
  </v-layout>
</template>

<script>
import Harf from '@/components/Harf'
export default {
  components: { Harf },
  data() {
    return {
      sorular: [
        {
          soru: 'Siyah ile aynı anlama gelen bir renk.',
          cevap: 'KARA',
          soruldu: false
        },
        {
          soru: 'Sık kullanılan bir isim.',
          cevap: 'AHMET',
          soruldu: false
        },
        {
          soru: "Türkiye'nin başkenti",
          cevap: 'ANKARA',
          soruldu: false
        },
        {
          soru: 'Karadenizde bir ilimiz',
          cevap: 'TRABZON',
          soruldu: false
        }
      ],
      mesaj: null,
      mesajClass: '',
      mesajSure: null,
      mevcutSoru: null,
      harfler: [],
      puan: 0,
      harfPuan: 0,
      yarismaciCevap: '',
      tamamlandi: false,
      sure: null,
      kalanSure: 0
    }
  },
  methods: {
    mesajgoster(mesaj, tur) {
      if (this.mesajSure) {
        clearTimeout(this.mesajSure)
        this.mesajSure = null
      }
      this.mesaj = mesaj
      if (tur === 'hata') {
        this.mesajClass = 'red darken-1'
      } else if (tur === 'basari') {
        this.mesajClass = 'light-green darken-1'
      } else {
        this.mesajClass = 'blue-grey darken-1'
      }
      this.mesajSure = setTimeout(() => {
        this.mesaj = null
      }, 3000)
    },
    basla() {
      this.tamamlandi = false
      this.mevcutSoru = null
      this.puan = 0
      this.sorular.map((x) => {
        x.soruldu = false
      })
      this.kalanSure = 240
      this.sure = setInterval(() => {
        this.kalanSure--
        if (this.kalanSure === 0) {
          this.bitir()
        }
      }, 1000)
      this.soruver()
      this.mesajgoster('İyi yarışmalar!')
    },
    bitir() {
      clearInterval(this.sure)
      this.mevcutSoru = null
      this.tamamlandi = true
    },
    soruver() {
      this.yarismaciCevap = ''
      this.mevcutSoru = this.sorular.find((x) => !x.soruldu)
      if (!this.mevcutSoru) {
        this.bitir()
        return
      }
      this.harfler = []
      this.mevcutSoru.cevap.split('').map((x) => {
        this.harfler.push({
          harf: x,
          acildi: false
        })
      })
      this.harfPuan = this.harfler.length * 100
      this.mevcutSoru.soruldu = true
    },
    harfver() {
      let rastgeleHarfIndex = Math.floor(Math.random() * this.harfler.length)
      if (this.harfPuan <= 100) {
        return
      }
      let harf = this.harfler[rastgeleHarfIndex]
      while (harf.acildi) {
        rastgeleHarfIndex = Math.floor(Math.random() * this.harfler.length)
        harf = this.harfler[rastgeleHarfIndex]
      }
      harf.acildi = true
      this.harfPuan -= 100
    },
    cevapver() {
      if (!this.yarismaciCevap.length) {
        return
      }
      if (this.yarismaciCevap.length !== this.harfler.length) {
        this.mesajgoster('Harf sayıları tutmuyor!')
        return
      }
      const cevap = this.yarismaciCevap.toLocaleUpperCase('tr')
      this.yarismaciCevap = cevap
      if (
        this.yarismaciCevap === this.mevcutSoru.cevap.toLocaleUpperCase('tr')
      ) {
        this.puan += this.harfPuan
        this.mesajgoster('Tebrikler, doğru bildiniz!', 'basari')
      } else {
        this.puan -= this.harfPuan
        this.mesajgoster(
          `Yanlış cevap, doğrusu '${this.mevcutSoru.cevap}' olmalıydı!`,
          'hata'
        )
      }
      this.soruver()
    }
  }
}
</script>
