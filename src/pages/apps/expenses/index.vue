<script setup>
const form = ref({
  expense_head_id: '',
  amount: '',
})

console.log(form)

const expenseheadsList = ref([])
const isSnackbarVisible = ref(false)
const message = ref()
const router = useRouter()

const addCategory = async data => {
  const res = await $api(`${baseUrl}/expenses`, {
    method: "POST",
    body: data,
  })

  isSnackbarVisible.value = true
  message.value = res.message
  router.push({ name: 'apps-expenses-list' })
  
}

onMounted(async () => {

  const categoryDataResponse = await $api(`${baseUrl}/expense_heads`, {
    method: "GET",
  })

  expenseheadsList.value = categoryDataResponse.map(service => ({ name: service.name, id: service.id }))

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
        <VForm @submit.prevent="addCategory(form)">
          <VRow>
            <VCol cols="12">
              <VRow no-gutters>
                <!-- 👉 Category_id -->
                <VCol
                  cols="12"
                  md="3"
                  class="d-flex align-items-center"
                >
                  <label
                    class="v-label mb-1 text-body-2 text-high-emphasis"
                    for="app-select-Select"
                  >Expense Head</label>
                </VCol>
                <VCol
                  cols="12"
                  md="9"
                >
                  <select
                    v-model="form.expense_head_id"
                    class="form-select custom-select"
                  >
                    <option
                      v-for="i in expenseheadsList"
                      :value="i.id"
                    >
                      {{ i.name }}
                    </option>
                  </select>
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
                    type="number"
                    placeholder="Amount"
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

<style scoped>
.custom-select {
  display: inline-block;
  border: 1px solid #ced4da;
  border-radius: .25rem;
  appearance: none;
  background: #fff url("data:image/svg+xml,%3csvg xmlns='http://www.w3.org/2000/svg' width='4' height='5' viewBox='0 0 4 5'%3e%3cpath fill='%23343a40' d='M2 0L0 2h4zm0 5L0 3h4z'/%3e%3c/svg%3e") right .75rem center/8px 10px no-repeat;
  block-size: calc(1.5em + .75rem + 2px);
  color: rgb(118, 118, 118);
  font-size: 1rem;
  font-weight: 400;
  inline-size: 100%;
  line-height: 1.5;
  padding-block: .375rem;
  padding-inline: .75rem 1.75rem;
  vertical-align: middle;
}
</style>
