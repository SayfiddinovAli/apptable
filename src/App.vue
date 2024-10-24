<template>
  <div>
    <input type="text" placeholder="Ismini kiriting" v-model="person.name">
    <input type="number" placeholder="Yoshini kiriting" v-model="person.age">
    <button @click="add()" v-show="showBtn">Kiritish</button>
    <button @click="save()" v-show="!showBtn">Saqlash</button>
  </div>
  <hr>

  <table width="100%" border="5px">
    <thead>
      <tr>
        <th>Id</th>
        <th>Ismi</th>
        <th>Yoshi</th>
        <th>Amallar</th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="(human, index) in persons" :key="human.id">
        <td>{{ index + 1 }}</td>
        <td>{{ human.name }}</td>
        <td>{{ human.age }}</td>
        <td width="50px">
          <button @click="remove(human.id)">X</button>
          <button @click="edit(human.id)">E</button>
        </td>
      </tr>
    </tbody>
  </table>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      showBtn: true, // Dastlab Kiritish tugmasi ko'rinadi
      person: {}, // Input maydonlari uchun bo'sh object
      persons: [], // Barcha odamlar ro'yxati
      url: 'http://localhost:3000/persons', // API bazasi
    };
  },
  methods: {
    save() {
      // PUT so'rov orqali o'zgartirishni saqlash
      axios.put(`${this.url}/${this.person.id}`, this.person)
        .then((res) => {
          this.persons = this.persons.map((p) =>
            p.id === res.data.id ? res.data : p
          );
          this.person = {}; // Input maydonlarini tozalaymiz
          this.showBtn = true; // Kiritish tugmasini ko'rsatamiz
        })
        .catch((error) => {
          console.error('Xatolik yuz berdi: ', error);
        });
    },

    // Tahrirlash funksiyasi
    edit(id) {
      axios.get(`${this.url}/${id}`)
        .then((res) => {
          this.showBtn = false; // Saqlash tugmasini ko'rsatamiz
          this.person = res.data; // Ma'lumotlarni input maydonlariga yuklaymiz
        })
        .catch((error) => {
          console.error('Xatolik yuz berdi: ', error);
        });
    },

    // O'chirish funksiyasi
    remove(id) {
      if (confirm('Qaroringiz qat\'iymi?')) {
        axios.delete(`${this.url}/${id}`)
          .then(() => {
            this.persons = this.persons.filter((p) => p.id !== id); // Ro'yxatdan o'chiramiz
          })
          .catch((error) => {
            console.error('Xatolik yuz berdi: ', error);
          });
      }
    },

    // Qo'shish funksiyasi
    add() {
      axios.post(this.url, this.person)
        .then((res) => {
          this.persons = [res.data, ...this.persons]; // Yangilangan ro'yxatga kiritamiz
          this.person = {}; // Input maydonlarini tozalaymiz
        })
        .catch((error) => {
          console.error('Xatolik yuz berdi: ', error);
        });
    }
  },

  // Sahifa yuklanishi bilan barcha shaxslarni olish
  mounted() {
    axios.get(this.url)
      .then((res) => {
        this.persons = res.data;
      })
      .catch((error) => {
        console.error('Xatolik yuz berdi: ', error);
      });
  }
};
</script>

<style>
/* Stilni bu yerda belgilashingiz mumkin */
</style>
