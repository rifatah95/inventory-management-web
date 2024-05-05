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
                <!-- 👉 User_id -->
                <VCol cols="12">
                  <label
                    class="v-label mb-1 text-body-2 text-high-emphasis"
                    for="app-select-Select"
                  >Select User</label>
                  <select
                    v-model="form.user_id"
                    class="form-select custom-select"
                  >
                    <option
                      v-for="i in userList"
                      :value="i.id"
                    >
                      {{ i.fullName }}
                    </option>
                  </select>
                </VCol>
              </VRow>
            </VCol>
            <VCol cols="12">
              <VRow no-gutters>
                <!-- 👉 Company_id -->
                <VCol cols="12">
                  <label
                    class="v-label mb-1 text-body-2 text-high-emphasis"
                    for="app-select-Select"
                  >Select Company</label>
                  <select
                    v-model="form.company_id"
                    class="form-select custom-select"
                  >
                    <option
                      v-for="i in companyList"
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
                <!-- 👉 Category_id -->
                <VCol cols="12">
                  <label
                    class="v-label mb-1 text-body-2 text-high-emphasis"
                    for="app-select-Select"
                  >Select Type Of Services</label>
                  <select
                    v-model="form.category_id"
                    class="form-select custom-select"
                  >
                    <option
                      v-for="i in serviceList"
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
                <!-- 👉 Task_id -->
                <VCol cols="12">
                  <label
                    class="v-label mb-1 text-body-2 text-high-emphasis"
                    for="app-select-Select"
                  >Select Task</label>
                  <select
                    v-model="form.task_id"
                    class="form-select custom-select"
                  >
                    <option
                      v-for="i in taskList"
                      :value="i.id"
                    >
                      {{ i.name }}
                    </option>
                  </select>
                </VCol>
              </VRow>
            </VCol>
            <!-- 👉 Submit and Reset button -->
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

<script setup>
import { onMounted, ref } from 'vue'
import { useRouter } from 'vue-router'

const router = useRouter()

const form = ref({
  user_id: '',
  company_id: '',
  category_id: '',
  task_id: '',
})

const userList = ref([])
const companyList = ref([])
const serviceList = ref([])
const taskList = ref([])
const isSnackbarVisible = ref(false)
const message = ref()

const addCategory = async data => {
  const res = await $api(`${baseUrl}/task_assign_company`, {
    method: "POST",
    body: data,
  })

  if (res?.status == "success") {
    isSnackbarVisible.value = true
    message.value = res.message
    router.push({ name: 'apps-tabc-list' })
  }
}

onMounted(async () => {
  const userDataResponse = await $api(`${baseUrl}/user_list`, {
    method: "GET",
  })

  userList.value = userDataResponse.users.map(user => ({ fullName: user.full_name, id: user.id }))
  console.log(userList.value.id)

  const companyDataResponse = await $api(`${baseUrl}/get_company_masters`, {
    method: "GET",
  })

  companyList.value = companyDataResponse.result.map(company => ({ name: company.name, id: company.id }))

  const serviceDataResponse = await $api(`${baseUrl}/service_all`, {
    method: "GET",
  })

  serviceList.value = serviceDataResponse.service_result.map(service => ({ name: service.name, id: service.id }))

  const taskDataResponse = await $api(`${baseUrl}/task_all`, {
    method: "GET",
  })

  taskList.value = taskDataResponse.task_result.map(task => ({ name: task.name, id: task.id }))
})
</script>

<style scoped>
.custom-select {
  display: inline-block;
  border: 1px solid #ced4da;
  border-radius: .25rem;
  appearance: none;
  background: #fff url("data:image/svg+xml,%3csvg xmlns='http://www.w3.org/2000/svg' width='4' height='5' viewBox='0 0 4 5'%3e%3cpath fill='%23343a40' d='M2 0L0 2h4zm0 5L0 3h4z'/%3e%3c/svg%3e") right .75rem center/8px 10px no-repeat;
  block-size: calc(1.5em + .75rem + 2px);
  color: #495057;
  font-size: 1rem;
  font-weight: 400;
  inline-size: 100%;
  line-height: 1.5;
  padding-block: .375rem;
  padding-inline: .75rem 1.75rem;
  vertical-align: middle;
}
</style>
