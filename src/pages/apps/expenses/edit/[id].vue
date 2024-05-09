<script setup>
const form = ref({

  name: '',
  category_id: '',
  short_order: '',
  status: '',
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

let expenseHead = ref()
let backup = ref()




const fetchUsers = async () => {
  try {
    const res = await $api(`${baseUrl}/expense_heads`, {
      method: "GET",
    })

    
    expenseHead.value = res.map(permission => ({
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

const updateCategory = async data => {
  const res = await $api(`${baseUrl}/expenses/${route.params.id}`, {
    method: "PUT",
    body: data,
  })

  
  isSnackbarVisible.value = true
  message.value = res.message
  router.push({ name: 'apps-expenses-list' })
  
}

onMounted(async () => {

  await fetchUsers()

  const res = await $api(`${baseUrl}/expenses/${route.params.id}`, {
    method: "GET",
  })


  const data = res


  const categoryid = expenseHead.value.find(item => item?.value == data.expense_head_id)

  form.value = {
    expense_head_id: categoryid.value,
    amount: data?.amount,
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
                <!-- 👉 status -->
                <VCol
                  cols="12"
                  md="3"
                  class="d-flex align-items-center"
                >
                  <label
                    class="v-label text-body-2 text-high-emphasis"
                    for="status"
                  >Expense Head</label>
                </VCol>
                <VCol
                  cols="12"
                  md="9"
                >
                  <AppSelect
                    v-model="form.expense_head_id"
                    placeholder="Select category"
                    :items="expenseHead"
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
                    for="amount"
                  >Amount</label>
                </VCol>
                <VCol
                  cols="12"
                  md="9"
                >
                  <AppTextField
                    id="amount"
                    v-model="form.amount"
                    type="text"
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
