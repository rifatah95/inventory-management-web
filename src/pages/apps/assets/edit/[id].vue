<script setup>
const form = ref({

  name: '',
  details: '',
  purchase_date: '',
  price: '',
})

const statusList = ref([
  {
    title: 'Active',
    value: 'active',
  },
  {
    title: 'Inactive',
    value: 'inactive',
  },
  
])

const isSnackbarVisible = ref(false)
const message = ref()
const router = useRouter()
const route = useRoute()

const updateAssets = async data => {
  const res = await $api(`${baseUrl}/assets/${route.params.id}`, {
    method: "PUT",
    body: data,
  })

  isSnackbarVisible.value = true
  message.value = res.message
  router.push({ name: 'apps-assets-list' })
  
}

onMounted(async() => {
  const res = await $api(`${baseUrl}/assets/${route.params.id}`, {
    method: "GET",
  })

  const data = res


  form.value = {
    name: data?.name,
    details: data?.details,
    purchase_date: data?.purchase_date,
    price: data?.price,
    
  }
})
</script>

<template>
  <section>
    <VSnackbar
      v-model="isSnackbarVisible"
      :timeout="2000"
      location="top end"
    >
      {{ message }}
    </VSnackbar>
    <VCard>
      <VCardText>
        <VForm @submit.prevent="updateAssets(form)">
          <VRow>
            <VCol cols="12">
              <VRow no-gutters>
                <!-- 👉 category_id -->
                <VCol
                  cols="12"
                  md="3"
                  class="d-flex align-items-center"
                >
                  <label
                    class="v-label text-body-2 text-high-emphasis"
                    for="name"
                  >Assets Name</label>
                </VCol>
                <VCol
                  cols="12"
                  md="9"
                >
                  <AppTextField
                    id="name"
                    v-model="form.name"
                    type="text"
                    placeholder=""
                    persistent-placeholder
                  />
                </VCol>
              </VRow>
            </VCol>

            <VCol cols="12">
              <VRow no-gutters>
                <!-- 👉 category_id -->
                <VCol
                  cols="12"
                  md="3"
                  class="d-flex align-items-center"
                >
                  <label
                    class="v-label text-body-2 text-high-emphasis"
                    for="details"
                  >Details</label>
                </VCol>
                <VCol
                  cols="12"
                  md="9"
                >
                  <AppTextField
                    id="short_order"
                    v-model="form.details"
                    type="text"
                    placeholder=""
                    persistent-placeholder
                  />
                </VCol>
              </VRow>
            </VCol>
            <VCol cols="12">
              <VRow no-gutters>
                <!-- 👉 category_id -->
                <VCol
                  cols="12"
                  md="3"
                  class="d-flex align-items-center"
                >
                  <label
                    class="v-label text-body-2 text-high-emphasis"
                    for="purchase_date"
                  >Purchase Date</label>
                </VCol>
                <VCol
                  cols="12"
                  md="9"
                >
                  <AppTextField
                    id="purchase_date"
                    v-model="form.purchase_date"
                    type="date"
                    placeholder=""
                    persistent-placeholder
                  />
                </VCol>
              </VRow>
            </VCol>
            <VCol cols="12">
              <VRow no-gutters>
                <!-- 👉 category_id -->
                <VCol
                  cols="12"
                  md="3"
                  class="d-flex align-items-center"
                >
                  <label
                    class="v-label text-body-2 text-high-emphasis"
                    for="price"
                  >Price</label>
                </VCol>
                <VCol
                  cols="12"
                  md="9"
                >
                  <AppTextField
                    id="price"
                    v-model="form.price"
                    type="number"
                    placeholder=""
                    persistent-placeholder
                  />
                </VCol>
              </VRow>
            </VCol>
        
            <!-- 👉 submit and reset button -->
            <VCol
              offset-md="3"
              cols="12"
              md="9"
              class="d-flex gap-4"
            >
              <VBtn type="submit">
                Submit
              </VBtn>
              <VBtn
                color="secondary"
                variant="tonal"
                type="reset"
              >
                Reset
              </VBtn>
            </VCol>
          </VRow>
        </VForm>
      </VCardText>
    </VCard>
  </section>
</template>
