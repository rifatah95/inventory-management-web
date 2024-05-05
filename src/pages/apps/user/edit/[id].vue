<script setup>
import UserBioPanel from '@/views/apps/user/view/UserBioPanel.vue'

const route = useRoute()
const router = useRouter()
const userData =ref()

const form = ref({
  name: '',
  email: '',
  roles: [],
  status: '',
})

const roleList = ref()

const statusList = ref([
  {
    title: 'Active',
    value: 1,
  },
  {
    title: 'Inactive',
    value: 0,
  },
  
])

const message = ref()
const isSnackbarTopStartVisible = ref(false)


// Event
const getUserByID = async() => {
  const res = await $api(`${baseUrl}/user_edit/${route.params.id}`, {
    method: "GET",
  })

  if(res?.status=="success"){
    userData.value=res?.user_info
    form.value={
      name: userData.value.name,
      email: userData.value.email,

      status: userData.value.status,
      roles: userData.value?.roles?.map(roles => roles.name),
    }
  }
}

const getRoleList = async()=>{
  const res = await $api(`${baseUrl}/roles`, {
    method: "GET",
  })

  if(res?.status=="Success")
    roleList.value =  res?.role.map(role => role.name)
}

onMounted(async() => {
  await getRoleList()
  await getUserByID()
})


const editUser = async () => {
  const res = await $api(`${baseUrl}/user_update/${route.params.id}?name=${form.value.name}&email=${form.value.email}&status=${form.value.status}&roles[]=${form.value.roles.join('&roles[]=')}`, {
    method: "PUT",
  })

  if(res?.status=='success'){
    message.value = res.message
    isSnackbarTopStartVisible.value=true
    router.push('/apps/user/list')
  }
}
</script>

<template>
  <VRow>
    <VCol
      cols="12"
      md="5"
      lg="4"
    >
      <UserBioPanel :alldata="userData" />
    </VCol>
    <VCol
      cols="12"
      md="7"
      lg="8"
    >
      <VCard>
        <VCardItem>
          <VCardTitle>Update User</VCardTitle>
          <VForm @submit.prevent="editUser">
            <VRow>
              <!-- Name -->
              <VCol cols="12">
                <AppTextField
                  v-model="form.name"
                  :rules="[requiredValidator]"
                  autofocus
                  label="Name"
                  placeholder="Name"
                />
              </VCol>
              <!-- email -->
              <VCol cols="12">
                <AppTextField
                  v-model="form.email"
                  :rules="[requiredValidator, emailValidator]"
                  label="Email"
                  type="email"
                  placeholder="demo@example.com"
                />
              </VCol>
              <!-- status -->
              <VCol cols="12">
                <AppSelect
                  v-model="form.status"
                  label="Select Status"
                  placeholder="Select Status"
                  :rules="[requiredValidator]"
                  :items="statusList"
                />
              </VCol>
              <!-- Roles -->
              <VCol cols="12">
                <AppSelect
                  v-model="form.roles"
                  label="Select Role"
                  placeholder="Select Role"
                  :rules="[requiredValidator]"
                  :items="roleList"
                  multiple
                />
              </VCol>
              <VCol
                cols="12"
                class="text-center text-danger"
              />
              <VCol
                cols="12"
                class="text-center text-base"
              >
                <VBtn
                  type="submit"
                  class="me-3"
                >
                  Submit
                </VBtn>
                <VBtn
                  type="reset"
                  variant="outlined"
                  color="secondary"
                  @click="$router.go(-1)"
                >
                  Cancel
                </VBtn>
              </VCol>
            </VRow>
          </VForm>
        </VCardItem>
      </VCard>
    </VCol>
    <VSnackbar
      v-model="isSnackbarTopStartVisible"
      location="top end"
    >
      {{ message }}
    </VSnackbar>
  </VRow>
</template>
