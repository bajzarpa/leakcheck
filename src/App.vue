<template>
  <div id="app">

    <div class="min-w-screen min-h-screen bg-gray-800 flex items-center justify-center px-5 py-5">
      <div class="w-full mx-auto rounded-xl bg-gray-100 shadow-lg p-10 text-gray-800 relative overflow-hidden min-w-80 max-w-3xl">
        <h1 class="font-semibold text-gray-800 dark:text-gray-200 sm:text-lg mb-5">Facebook adatszivárgás ellenörző</h1>
        <p class="mb-5">Ezen a felületen biztonságosan ellenőrizheted, hogy a felhasználói fiókod érintett-e a korábbi adatszivárgásokban.
          <small>
            <br><em>Disclaimer:</em> ez az adatbázis az interneten fellelhető,
            <a href="https://raketa.hu/legalabb-377-ezer-magyar-facebookozo-adatait-tettek-kozze-nyilvanosan-a-neten" target="_blank" class="underline">ellopott</a>
            377.000 magyar adatainak <strong>butított</strong> változata, mely semmilyen személyes adatot nem tartalmaz.
            Az oldal kizárólagos célja, hogy a felhasználóknak lehetősége legyen ellenőrizni érintett-e az adatszivárgásban.</small>
        </p>
        <p class="mb-5">Keresni a <a class="underline" href="https://findmyfbid.in/" target="_blank">felhasználói azonosítódra</a>, vagy a telefonszámodra tudsz, a következő formában: 361234567</p>
        <div class="relative mt-1">
          <input
            v-model="queryText"
            type="text"
            id="src"
            class="w-full pl-3 pr-10 py-2 border-2 border-gray-200 rounded-xl hover:border-gray-300 focus:outline-none focus:border-blue-500 transition-colors"
            placeholder="Telefonszám, vagy felhasználó azonosító"
          >
          <button
            class="block w-7 h-7 text-center text-xl leading-0 absolute top-2 right-2 text-gray-400 focus:outline-none hover:text-gray-900 transition-colors"
            @click="check"
          >
            <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24"><path fill="#ccc" d="M9.145 18.29c-5.042 0-9.145-4.102-9.145-9.145s4.103-9.145 9.145-9.145 9.145 4.103 9.145 9.145-4.102 9.145-9.145 9.145zm0-15.167c-3.321 0-6.022 2.702-6.022 6.022s2.702 6.022 6.022 6.022 6.023-2.702 6.023-6.022-2.702-6.022-6.023-6.022zm9.263 12.443c-.817 1.176-1.852 2.188-3.046 2.981l5.452 5.453 3.014-3.013-5.42-5.421z"/></svg>
          </button>
        </div>
        <div class="py-3 px-2 mt-4 mb-0 bg-green-300 text-green-800 rounded border border-green-600">Nem szerepel</div>
        <div class="py-3 px-2 my-2 bg-red-300 text-red-800 rounded border border-red-600">Szerepel. <a class="underline" href="#">Mi a teendő?</a></div>
      </div>
    </div>

  </div>
</template>

<script>
import firebase from 'firebase/app';
import 'firebase/firestore';
import { sha256 } from 'js-sha256';

const firebaseConfig = {
  apiKey: process.env.VUE_APP_API_KEY,
  authDomain: process.env.VUE_APP_AUTH_DOMAIN,
  databaseURL: process.env.VUE_APP_DATABASE_URL,
  projectId: process.env.VUE_APP_PROJECT_ID,
  storageBucket: process.env.VUE_APP_STORAGE_BUCKET,
  messagingSenderId: process.env.VUE_APP_MESSAGING_SENDER_ID,
  appId: process.env.VUE_APP_APP_ID
};

export default {
  name: 'LeakCheck',

  data () {
    return {
      queryText: 'bf974226d2c47745ce5905fa963d8d4eb062053bbbd1367d48ca7d74294dc3ba',
      isSubmitted: false,
      isLoading: true,
      isHit: false,
      data: []
    }
  },

  created () {
    firebase.initializeApp(firebaseConfig);

    sha256('')
  },

  methods: {
    async check () {
      const db = firebase.firestore();
      const users = db.collection('users')
      const phone =  users.where('phone', '==', (this.queryText)).get()
      const id = users.where('id', '==', (this.queryText)).get()

      users.get().then(console.log)

      const [phoneSnapshot, idSnapshot] = await Promise.all([phone, id])

      const phoneSize = phoneSnapshot.size
      const idSize = idSnapshot.size

      console.log({ phoneSize, idSize })

      this.isHit = phoneSize > 0 || idSize > 0
    }
  }
}

</script>

<style>
@import url('https://cdnjs.cloudflare.com/ajax/libs/tailwindcss/2.0.4/tailwind.min.css');
</style>
