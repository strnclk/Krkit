<template>
  <q-layout view="lHh Lpr lFf">
    <q-page-container class="q-pa-md flex flex-center">
      <q-card class="form-card">
        <q-card-section class="text-center">
          <h4>📦 Stok Kaydı Yap</h4>
        </q-card-section>

        <q-separator />

        <q-card-section class="q-gutter-md">
          <q-input
            filled
            v-model="barcodeNumber"
            label="Barkod Numarası"
            class="full-width"
            dense
          />
          <q-input
            filled
            v-model="quantity"
            label="Miktar"
            type="number"
            class="full-width"
            dense
          />
          <q-input
            filled
            v-model="productDescription"
            label="Ürün Açıklaması"
            class="full-width"
            dense
          />
          <q-input
            filled
            v-model="price"
            label="Fiyat"
            type="number"
            class="full-width"
            dense
          />
        </q-card-section>

        <q-card-section class="q-pa-none">
          <q-btn
            color="primary"
            label="Kaydet"
            class="save-button"
            @click="saveStock"
          />
        </q-card-section>
      </q-card>
    </q-page-container>
  </q-layout>
</template>

<script setup>
import { ref } from 'vue'
import { useQuasar } from 'quasar'
import { product } from 'src/composables/product'

const { addProduct } = product()
const $q = useQuasar()

const barcodeNumber = ref('')
const productDescription = ref('')
const price = ref('')
const quantity = ref(1)
const defaultCompanyName = "ABC Şirketi" // Default şirket ismi

const saveStock = async () => {
  if (!barcodeNumber.value || !productDescription.value || !price.value) {
    $q.notify({
      type: 'negative',
      message: 'Lütfen tüm alanları doldurun!',
    })
    return
  }

  try {
    const productRequest = {
      barcode: barcodeNumber.value,
      description: productDescription.value,
      price: Number(price.value),
      companyName: defaultCompanyName, // Default değeri kullanıyoruz
      quantity: Number(quantity.value)
    }

    const response = await addProduct(productRequest)

    if (!response.ok) throw new Error('Stok kaydedilemedi')

    $q.notify({
      type: 'positive',
      message: 'Stok başarıyla kaydedildi!',
    })

    // Formu temizle
    barcodeNumber.value = ''
    productDescription.value = ''
    price.value = ''
    quantity.value = 1
  } catch (error) {
    $q.notify({
      type: 'negative',
      message: 'Bir hata oluştu: ' + error.message,
    })
    console.error(error)
  }
}
</script>

<style scoped>
/* Stiller aynı kalıyor */
.q-page-container {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 80vh;
}

.form-card {
  width: 100%;
  max-width: 400px;
  padding: 1.5rem;
  border-radius: 12px;
  background: #ffffff;
  box-shadow: 0px 4px 10px rgba(0, 0, 0, 0.1);
}

h4 {
  font-size: 1.5rem;
  font-weight: bold;
  color: #333;
}

.save-button {
  width: 100%;
  padding: 12px;
  font-size: 1rem;
  font-weight: bold;
  border-radius: 8px;
  transition: 0.3s ease-in-out;
}

.save-button:hover {
  background: #1976d2;
  color: white;
}

.full-width {
  width: 100%;
}
</style>
