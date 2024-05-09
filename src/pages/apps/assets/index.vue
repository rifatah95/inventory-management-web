<script setup>
const form = ref({
  name: '',
  details: '',
  purchase_date: '',
  price: '',
})

const isSnackbarVisible = ref(false)
const message = ref()
const router = useRouter()

const addAssets = async data => {
  const res = await $api(`${baseUrl}/assets`, {
    method: "POST",
    body: data,
  })

  isSnackbarVisible.value = true
  message.value = res.message
  router.push({ name: 'apps-assets-list' })
  
}
</script>

<template>
  <section>
    <VSnackbar
      v-model="isSnackbarVisible"
      :timeout="2000"
      location="top end"
    >
      Data has been inserted successfully
    </VSnackbar>
    <VCard>
      <VCardText>
        <VForm @submit.prevent="addAssets(form)">
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
