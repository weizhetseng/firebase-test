

<template>
  <nav>
    <RouterLink to="/">Home</RouterLink>
    <RouterLink to="/about">About</RouterLink>
  </nav>
  <ul>
    <li v-for="item in country" :key="item.name">{{ item.name }}{{ item.capital }}</li>
  </ul>

  <RouterView />
</template>

<script setup>
import { RouterLink, RouterView } from 'vue-router'
import { collection, addDoc, doc, setDoc, updateDoc, getDoc, query, getDocs } from "firebase/firestore"
import db from "./firebase/init"
import { onMounted, ref } from 'vue';
import { async } from '@firebase/util';
const country = ref([])

// 建立資料
async function createUser() {
  const colRef = collection(db, "users")
  const dataObj = {
    firstName: 'John',
    lastName: 'Doe',
    dob: '1990'
  }

  const docRef = await addDoc(colRef, dataObj)

  console.log(docRef.id)
}
// 建立有id的資料
async function createCountry() {
  await setDoc(doc(db, 'countries', 'JP'), {
    name: 'Japan'

  })
}
// 新增欄位資料
async function createCountryMerge() {
  await setDoc(doc(db, 'countries', 'JP'), {
    captial: 'Tokyo'

  }, { merge: true })
}
// 更新資料
async function updateCountry() {
  await updateDoc(doc(db, 'countries', 'JP'), {
    capital: 'tokyo'
  })
}
// 取得資料
async function getCountry() {
  const docSnap = await getDoc(doc(db, 'countries', 'JP'))

  if (docSnap.exists()) {
    console.log(docSnap.data())
    country.value = docSnap.data()
  } else {
    console.log('no')
  }
}
//取得數組資料
async function getCountryQurey() {
  const querySnap = await getDocs(query(collection(db, 'countries')))

  querySnap.forEach((doc) => {
    country.value.push(doc.data())
  })
}

onMounted(() => {
  getCountryQurey()
})

</script>