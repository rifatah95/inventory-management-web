<script setup>
const form = ref({

  name: '',
  model: '',
  details: '',
  price: '',
  short_order: '',
  subcategory_id: '',
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

let categoryList = ref()
let backup = ref()




const fetchUsers = async () => {
  try {
    const res = await $api(`${baseUrl}/subcategories`, {
      method: "GET",
    })

    
    categoryList.value = res.map(permission => ({
      title: permission.name,
      value: permission.id,
    }))
    
    backup.value = res
  } catch (err) {
    console.error(err)
  }
}



const isSnackbarVisible = ref(false)
const message = ref()
const router = useRouter()
const route = useRoute()

const updateProduct = async data => {
  const res = await $api(`${baseUrl}/products/${route.params.id}`, {
    method: "PUT",
    body: data,
  })

  
  isSnackbarVisible.value = true
  message.value = res.message
  router.push({ name: 'apps-products-list' })
  
}

onMounted(async () => {

  await fetchUsers()

  const res = await $api(`${baseUrl}/products/${route.params.id}`, {
    method: "GET",
  })


  const data = res
  const categoryid = categoryList.value.find(item => item?.value == data.subcategory_id)

  form.value = {
    name: data?.name,
    model: data?.model,
    details: data?.details,
    price: data?.price,
    short_order: data?.short_order,
    subcategory_id: categoryid.value,
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
        <VForm @submit.prevent="updateProduct(form)">
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
                  >Product Name</label>
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
                <!-- 👉 category_id -->
                <VCol
                  cols="12"
                  md="3"
                  class="d-flex align-items-center"
                >
                  <label
                    class="v-label text-body-2 text-high-emphasis"
                    for="category_id"
                  >Model</label>
                </VCol>
                <VCol
                  cols="12"
                  md="9"
                >
                  <AppTextField
                    id="category_id"
                    v-model="form.model"
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
                    for="category_id"
                  >Details</label>
                </VCol>
                <VCol
                  cols="12"
                  md="9"
                >
                  <AppTextField
                    id="category_id"
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
                    for="category_id"
                  >Price</label>
                </VCol>
                <VCol
                  cols="12"
                  md="9"
                >
                  <AppTextField
                    id="category_id"
                    v-model="form.price"
                    type="number"
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
                    for="short_order"
                  >Short Order</label>
                </VCol>
                <VCol
                  cols="12"
                  md="9"
                >
                  <AppTextField
                    id="short_order"
                    v-model="form.short_order"
                    type="number"
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
                    for="subcategory_id"
                  >SubCategories</label>
                </VCol>
                <VCol
                  cols="12"
                  md="9"
                >
                  <AppSelect
                    v-model="form.subcategory_id"
                    placeholder="Select category"
                    :items="categoryList"
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
                    v-model="form.status"
                    placeholder="Select Status"
                    :items="statusList"
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
