<script setup>
const form = ref({

  name: '',
  status: '',
})

const isSnackbarVisible = ref(false)
const message = ref()
const router = useRouter()
const route = useRoute()

const updateCategory = async data => {
  const res = await $api(`${baseUrl}/categories/${route.params.id}`, {
    method: "PUT",
    body: data,
  })

  if (res?.status == "success") {
    isSnackbarVisible.value = true
    message.value = res.message
    router.push({ name: 'apps-category-list' })
  }
}

onMounted(async() => {
  const res = await $api(`${baseUrl}/categories/${route.params.id}/edit`, {
    method: "GET",
  })

  const data = res?.result


  form.value = {
    name: data?.name,
    status: data?.status,
    
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
        <VForm @submit.prevent="updateCategory(form)">
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
                    for="category_id"
                  >Type Of Services Name</label>
                </VCol>
                <VCol
                  cols="12"
                  md="9"
                >
                  <AppTextField
                    id="category_id"
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
                <!-- 👉 status -->
                <VCol
                  cols="12"
                  md="3"
                  class="d-flex align-items-center"
                >
                  <label
                    class="v-label text-body-2 text-high-emphasis"
                    for="status"
                  >Status</label>
                </VCol>
                <VCol
                  cols="12"
                  md="9"
                >
                  <AppSelect
                      :model-value="itemsPerPage"
                      v-model="form.status"
                      :items="[
                        { value: 1, title: 'Active' },
                        { value: 0, title: 'Inactive' },
                      ]"
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
