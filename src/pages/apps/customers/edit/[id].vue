<script setup>
const form = ref({

  name: '',
  address: '',
  mobile: '',
  email: '',
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

const updateCustomer = async data => {
  const res = await $api(`${baseUrl}/customers/${route.params.id}`, {
    method: "PUT",
    body: data,
  })

  isSnackbarVisible.value = true
  message.value = res.message
  router.push({ name: 'apps-customers-list' })
  
}

onMounted(async() => {
  const res = await $api(`${baseUrl}/customers/${route.params.id}`, {
    method: "GET",
  })

  const data = res


  form.value = {
    name: data?.name,
    address: data?.address,
    mobile: data?.mobile,
    email: data?.email,
    
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
        <VForm @submit.prevent="updateCustomer(form)">
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
                  >Customer Name</label>
                </VCol>
                <VCol
                  cols="12"
                  md="9"
                >
                  <AppTextField
                    id="name"
                    v-model="form.name"
                    type="text"
                    placeholder="Name"
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
                    for="address"
                  >Address</label>
                </VCol>
                <VCol
                  cols="12"
                  md="9"
                >
                  <AppTextField
                    id="address"
                    v-model="form.address"
                    type="text"
                    placeholder="Address"
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
                    for="mobile"
                  >Mobile</label>
                </VCol>
                <VCol
                  cols="12"
                  md="9"
                >
                  <AppTextField
                    id="mobile"
                    v-model="form.mobile"
                    type="text"
                    placeholder="Mobile"
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
                    for="email"
                  >E-mail</label>
                </VCol>
                <VCol
                  cols="12"
                  md="9"
                >
                  <AppTextField
                    id="email"
                    v-model="form.email"
                    type="email"
                    placeholder="E-mail"
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
                Update
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
