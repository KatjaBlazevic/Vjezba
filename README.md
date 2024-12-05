# Vjezba

# Quasar
npm i -g @quasar/cli
npm init quasar

# GitHub 
git clone "url od git repozitorija"
git config user.email "katjablaz55@gmail.com"
git config user.user "KatjaBlazevic"
git add *
git commit -m "Poruka"
git pull
git push

# Baza podataka
Može se uvest u folder di je projekt
Zatim ići na stranicu (http://student.veleri.hr/phpmyadmin/), tu upisati podatke od baze podataka
Zatim import, učitati bazu, odznačiti "Enable foreign keys"

# Kreiranje stranica
quasar new page NazivStranice.vue
u MainLayout je izmijenjuje naziv izbornika i putanja
u routes.js se dodaje { path: '/putanja', component: () => import('pages/NazivStranice.vue') },

# Stranica o nama

<template>
  <q-card dark bordered class="bg-grey-9 my-card">
    <q-card-section>
      <q-img src="https://www.rijeka.hr/wp-content/uploads/2023/12/Nova-knji%C5%BEnica-4-901x600.jpg" />
      <div class="text-h3 text-weight-bolder absolute-bottom text-center">O nama</div>
    </q-card-section>
    <q-card-section class="text-h4 text-justify">
      {{ o_nama }}
    </q-card-section>
  </q-card>
</template>

<script>
import { ref } from 'vue';

export default {
  setup() {
    const o_nama = ref('Gradska knjižnica Rijeka središnja je narodna knjižnica grada Rijeke i matična knjižnica za narodne i školske knjižnice Primorsko-goranske županije. Ona je informacijsko, obrazovno, kulturno, komunikacijsko i socijalno središte Rijeke i okolice usmjereno prema novom stvaralaštvu, održivom razvoju i kvaliteti razvoja zajednice i čitateljske kulture.');

    return {
      o_nama
    };
  }
}
</script>

<style lang="sass" scoped>
.my-card
  width: 100%
</style>

<style lang="sass" scoped>
.my-card
  width: 100%
</style>

# Lokacija - skinuti leaflet za mapu
npm install leaflet

Unutar script napraviti import leaflet paketa, a u onMounted() definirati poziciju na karti i uvećanje.
U template postaviti id atribut za poziv karte:
<template>
<div id="map" style="height:500px;"></div>
</template>

<script>
import { ref, onMounted } from 'vue';
import * as L from 'leaflet';
import "leaflet/dist/leaflet.css";
export default {
  // name: 'PageName',
  setup(){
    const initialMap = ref('');
    onMounted(() => {
        initialMap.value = L.map('map').setView([45.3312,14.4322], 13);
        L.tileLayer('https://tile.openstreetmap.org/{z}/{x}/{y}.png', {
            maxZoom: 19,
            attribution: '&copy;<a href="http://www.openstreetmap.org/copyright">OpenStreetMap</a>'
        }).addTo(initialMap.value);
      })
      return {
        initialMap
      }
    }
}
</script>

# Popis knjiga

<template>

  <div class="popis-knjiga-page">
    <q-table
      :rows="books"
      :columns="columns"
      row-key="id"
      :pagination="pagination"
    />
  </div>
</template>

<script setup>
import { ref } from 'vue';

const columns = [
  {
    name: 'id',
    label: 'ID',
    align: 'left',
    field: row => row.id,
    sortable: true
  },
  {
    name: 'title',
    label: 'Naziv',
    align: 'left',
    field: row => row.title,
    sortable: true
  },
  {
    name: 'author',
    label: 'Autor',
    align: 'left',
    field: row => row.author,
    sortable: true
  },
  {
    name: 'year',
    label: 'Godina izdanja',
    align: 'left',
    field: row => row.year,
    sortable: true
  }
];

const books = ref([
  { id: 1, title: 'A Court of Thorns and Roses', author: 'Sarah J. Maas', year: 2021 },
  { id: 2, title: 'A Court of Mist and Fury', author: 'Sarah J. Maas', year: 2020 },
  { id: 3, title: 'Never Lie', author: 'Freida McFadden', year: 2019 },
  { id: 4, title: 'It Ends With Us', author: 'Coleen Hoover', year: 2022 }
]);

const pagination = ref({
  page: 1,
  rowsPerPage: 5
});
</script>

<style scoped>
.popis-knjiga-page {
  padding: 20px;
}
</style>

# Pretrazivanje

<template>
  <q-page padding>
    <!-- Pretraga -->
    <div class="q-mb-md">
      <q-input
        v-model="searchQuery"
        label="Unesite pojam za pretragu"
        filled
        class="q-mb-md"
        debounce="300"
      />

      <div class="q-mb-md">
        <q-checkbox
          v-model="searchByAuthor"
          label="Pretražuj po autoru"
        />
        <q-checkbox
          v-model="searchByTitle"
          label="Pretražuj po naslovu"
        />
      </div>

      <q-btn @click="performSearch" label="Traži" color="primary" />

      <!-- Tablica sa rezultatima -->
      <q-table
        :rows="filteredBooks"
        :columns="columns"
        row-key="id"
        class="q-mt-md"
      >
        <template v-slot:body="props">
          <q-tr :props="props">
            <q-td :props="props" prop="title" />
            <q-td :props="props" prop="author" />
          </q-tr>
        </template>
      </q-table>
    </div>
  </q-page>
</template>

<script>
import { ref, computed, watch } from "vue";

export default {
  name: "PretrazivanjePage",
  setup() {
    // Dummy podaci za testiranje
    const books = ref([
      { id: 1, title: 'A Court of Thorns and Roses', author: 'Sarah J. Maas' },
      { id: 2, title: 'A Court of Mist and Fury', author: 'Sarah J. Maas' },
      { id: 3, title: 'Never Lie', author: 'Freida McFadden' },
      { id: 4, title: 'It Ends With Us', author: 'Coleen Hoover' },
    ]);

    // Statička pretraga
    const searchQuery = ref("");
    const searchByAuthor = ref(false);
    const searchByTitle = ref(true);

    // Kolone tablice
    const columns = [
      { name: "title", label: "Naslov", required: true, align: "left", field: "title" },
      { name: "author", label: "Autor", required: true, align: "left", field: "author" }
    ];

    // Funkcija za obavljanje pretrage
    const performSearch = () => {
      console.log("Performing search with query:", searchQuery.value);
      filteredBooks.value = books.value.filter((book) => {
        const searchText = searchQuery.value.toLowerCase();
        let matches = false;

        // Ako je odabrano pretraživanje po naslovu
        if (searchByTitle.value && book.title.toLowerCase().includes(searchText)) {
          matches = true;
        }

        // Ako je odabrano pretraživanje po autoru
        if (searchByAuthor.value && book.author.toLowerCase().includes(searchText)) {
          matches = true;
        }

        return matches;
      });
      console.log("Filtered books:", filteredBooks.value);
    };

    // Reaktivno praćenje promjena unosa u pretragu
    const filteredBooks = ref(books.value);

    // Watch za praćenje promjena unosa i checkboxova
    watch([searchQuery, searchByAuthor, searchByTitle], () => {
      performSearch(); // Automatski pokreće pretragu kad god se promijene ovi podaci
    });

    return {
      searchQuery,
      searchByAuthor,
      searchByTitle,
      columns,
      filteredBooks,
      performSearch,
    };
  },
};
</script>

<style scoped>
/* Stilizacija inputa i tablice */
.q-table {
  min-height: 300px;
}

.q-checkbox {
  margin-right: 20px;
}
</style>

# Login

<template>
  <q-page padding>
    <!-- Login Form -->
    <q-card class="q-pa-md" bordered>
      <q-card-section>
       <div class="text-h6 text-center q-mb-md">Prijava</div>
        <p class="text-center q-mb-md">Unesite svoje korisničko ime i lozinku kako biste pristupili svom računu.</p>

        <!-- Korisničko ime -->
        <q-input
          v-model="username"
          label="Korisničko ime"
          filled
          class="q-mb-md"
          :dense="true"
        />

        <!-- Lozinka -->
        <q-input
          v-model="password"
          label="Lozinka"
          type="password"
          filled
          class="q-mb-md"
          :dense="true"
        />

        <!-- Gumb za potvrdu -->
        <q-btn
          @click="loginUser"
          label="Potvrdi"
          color="primary"
          class="full-width q-mt-md"
        />

        <!-- Prikazivanje greške -->
        <q-banner v-if="errorMessage" class="q-mt-md" color="negative" dense>
          {{ errorMessage }}
        </q-banner>

      </q-card-section>
    </q-card>
  </q-page>
</template>

<script>
import { ref } from "vue";

export default {
  name: "LoginPage",
  setup() {
    // Reaktivni objekat za prijavu
    const username = ref("");
    const password = ref("");

    // Poruka o grešci (ako se prijava ne uspije)
    const errorMessage = ref("");

    // Funkcija za prijavu korisnika
    const loginUser = () => {
      // Resetiranje poruka
      errorMessage.value = "";

      // Provjera da li su uneseni korisničko ime i lozinka
      if (!username.value || !password.value) {
        errorMessage.value = "Molimo unesite korisničko ime i lozinku.";
        return;
      }

      // Simulacija prijave korisnika (u stvarnom slučaju, trebate poslati podatke na backend)
      console.log("Korisnik prijavljen:", { username: username.value, password: password.value });

      // Ako prijava bude uspješna, preusmjeriti na drugi ekran (npr. dashboard ili homepage)
      // U stvarnoj aplikaciji obično šaljete zahtjev na backend za provjeru podataka i tokena.

      // Ako je prijava uspješna, možete napraviti preusmjeravanje (na primjer):
      // this.$router.push('/dashboard');
    };

    return {
      username,
      password,
      loginUser,
      errorMessage
    };
  }
};
</script>

<style scoped>
/* Stilizacija forme i inputa */
.q-card {
  max-width: 400px;
  margin: 0 auto;
}

.q-input {
  width: 100%;
}

.q-btn {
  width: 100%;
  max-width: 400px;
}

.q-banner {
  margin-top: 20px;
}

.text-center {
  text-align: center;
}

.q-card-section {
  padding: 20px;
}

.q-pa-md {
  padding: 30px;
}

.full-width {
  width: 100%;
}
</style>

# Registracija

<template>
  <q-page padding>
    <!-- Registracija Form -->
    <q-card class="q-pa-md" bordered>
      <q-card-section>
        <div class="text-h6 text-center q-mb-md">Registracija</div>

        <!-- Ime -->
        <q-input
          v-model="firstName"
          label="Ime"
          filled
          class="q-mb-md"
          :dense="true"
        />

        <!-- Prezime -->
        <q-input
          v-model="lastName"
          label="Prezime"
          filled
          class="q-mb-md"
          :dense="true"
        />

        <!-- Korisničko ime -->
        <q-input
          v-model="username"
          label="Korisničko ime"
          filled
          class="q-mb-md"
          :dense="true"
        />

        <!-- Lozinka -->
        <q-input
          v-model="password"
          label="Lozinka"
          type="password"
          filled
          class="q-mb-md"
          :dense="true"
        />

        <!-- Potvrda lozinke -->
        <q-input
          v-model="confirmPassword"
          label="Potvrdi lozinku"
          type="password"
          filled
          class="q-mb-md"
          :dense="true"
        />

        <!-- Gumb za registraciju -->
        <q-btn
          @click="registerUser"
          label="Registriraj se"
          color="primary"
          class="full-width q-mt-md"
        />

        <!-- Prikazivanje greške -->
        <q-banner v-if="errorMessage" class="q-mt-md" color="negative" dense>
          {{ errorMessage }}
        </q-banner>

        <!-- Poruka uspjeha -->
        <q-banner v-if="successMessage" class="q-mt-md" color="positive" dense>
          {{ successMessage }}
        </q-banner>

      </q-card-section>
    </q-card>
  </q-page>
</template>

<script>
import { ref } from "vue";

export default {
  name: "RegistracijaPage",
  setup() {
    // Reaktivni objekti za unos korisničkih podataka
    const firstName = ref("");
    const lastName = ref("");
    const username = ref("");
    const password = ref("");
    const confirmPassword = ref("");

    // Poruke o grešci i uspjehu
    const errorMessage = ref("");
    const successMessage = ref("");

    // Funkcija za registraciju korisnika
    const registerUser = () => {
      // Resetiranje poruka
      errorMessage.value = "";
      successMessage.value = "";

      // Provjera da li su uneseni svi podaci
      if (!firstName.value || !lastName.value || !username.value || !password.value || !confirmPassword.value) {
        errorMessage.value = "Molimo unesite ime, prezime, korisničko ime, lozinku i potvrdu lozinke.";
        return;
      }

      // Provjera da li se lozinka i potvrda lozinke poklapaju
      if (password.value !== confirmPassword.value) {
        errorMessage.value = "Lozinke se ne podudaraju.";
        return;
      }

      // Simulacija registracije korisnika (u stvarnom slučaju, trebate poslati podatke na backend)
      console.log("Korisnik registriran:", { firstName: firstName.value, lastName: lastName.value, username: username.value, password: password.value });

      // Ako registracija bude uspješna, postavljanje uspješne poruke
      successMessage.value = "Registracija uspješna! Sada se možete prijaviti.";

      // Resetiranje unosa
      firstName.value = "";
      lastName.value = "";
      username.value = "";
      password.value = "";
      confirmPassword.value = "";
    };

    return {
      firstName,
      lastName,
      username,
      password,
      confirmPassword,
      registerUser,
      errorMessage,
      successMessage
    };
  }
};
</script>

<style scoped>
/* Stilizacija forme i inputa */
.q-card {
  max-width: 400px;
  margin: 0 auto;
}

.q-input {
  width: 100%;
}

.q-btn {
  width: 100%;
  max-width: 400px;
}

.q-banner {
  margin-top: 20px;
}

.text-center {
  text-align: center;
}

.q-card-section {
  padding: 20px;
}

.q-pa-md {
  padding: 30px;
}

.full-width {
  width: 100%;
}
</style>

# Backend, svi apiji se pišu prije app.listen

Kreirati mapu Backend
npm install express cors body-parser mysql
npm install -g nodemom
kreirati indeks.js
nodemon indeks.js
u nju:

const express = require("express");
const cors = require("cors");
const bodyParser = require("body-parser");
const mysql = require("mysql");

const app = express();
const port = 3000;

// Parser za JSON podatke
app.use(bodyParser.json());

// Parser za podatke iz formi
app.use(bodyParser.urlencoded({ extended: true }));

const connection = mysql.createConnection({
    host: host
    user: user,
    password: password,
    database: database
  });
  
app.use(express.urlencoded({ extended: true }));
  
connection.connect(function(err) {
    if (err) throw err;
    console.log("Connected!");
  });

  app.listen(port, () => {
    console.log("Server running at port: " + port);
});

# Primjer API-ja za knjige
URL - /api/knjige, /api/knjige/naslov/:naslov, /api/korisnici/:id_korisnik
imamo GET, PUT, POST, DELETE

 app.get('/api/knjige', (req,res)=>{
    connection.query('SELECT * FROM knjiga', (error, results)=>{
        if (error) throw error;
        res.send(results);
    })})

    app.get('/api/knjige/naslov/:naslov', (req, res) => {
        const naslov = req.params.naslov;
        connection.query(
            'SELECT * FROM knjiga WHERE naslov LIKE ?', 
            [`%${naslov}%`], // Dodaj % za LIKE sintaksu
            (error, results) => {
                if (error) throw error;
                res.send(results);
            }
        );
    });

app.post("/api/rezerv_knjige", (req, res) => {
        const data = req.body;
    
        // Provjera ulaznih podataka
        if (!data.today || !data.id_knjiga || !data.id_korisnik) {
            return res.status(400).send({ error: "Svi podaci moraju biti dostavljeni." });
        }
    
        const rezervacija = [[data.today, data.id_knjiga, data.id_korisnik]];
        
        connection.query(
            "INSERT INTO rezervacija (datum_rez, knjiga, korisnik) VALUES ?",
            [rezervacija],
            (error, results) => {
                if (error) {
                    return res.status(500).send({ error: "Pogreška prilikom dodavanja rezervacije." });
                }
                res.send({ success: true, results });
            }
        );
    });

Zatim se u RESTED plugin ili u PostMan-a upisuje http://localhost:"PORT" i onda URL od API-ja