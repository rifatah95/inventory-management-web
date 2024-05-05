<script setup>
import UserBioPanel from '@/views/apps/user/view/UserBioPanel.vue'

const roleData = ref({})
const permissionList = ref([])
const route = useRoute()
const router = useRouter()
const message = ref()

const form = ref({
  name: '',
  permission: [],
})


// Event
const getRoleByID = async() => {
  const res = await $api(`${baseUrl}/roles/${route.params.id}/edit`, {
    method: "GET",
  })

  if(res?.status=="Success"){
    roleData.value=res?.role

    const permissions = res?.role?.permissions.map(permission => permission.name)

    form.value ={
      name: res?.role?.name,
      permission: permissions,
    }
  }
}

const getPermissionsList = async() => {
  const res = await $api(`${baseUrl}/permissions`, {
    method: "GET",
  })

  if(res?.status=="Success")
    permissionList.value = res?.permissions.map(permission => permission.name)
}

const updateRole =async data => {
  if(!data?.name || !data?.permission) return

  const res = await $api(`${baseUrl}/roles/${route.params.id}?name=${data.name}&permission[]=${data.permission.join('&permission[]=')}`, {
    method: "PUT",
  })

  if(res?.status=="Success"){
    router.push({ name: 'apps-roles' })
  }
  if(res?.status=="error"){
    message.value = res?.message?.name?.at(0)
  }
}

onMounted(async() => {
  await getPermissionsList()
  await getRoleByID()
})
</script>

<template>
  <VRow>
    <VCol
      cols="12"
      md="5"
      lg="4"
    >
      <UserBioPanel :alldata="roleData" />
    </VCol>
    <VCol
      cols="12"
      md="7"
      lg="8"
    >
      <VCard>
        <VCardItem>
          <VCardTitle>Update Role</VCardTitle>
          <VForm
            class="mt-3"
            @submit.prevent="updateRole(form)"
          >
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
              <!-- permission -->
              <VCol cols="12">
                <AppSelect
                  v-model="form.permission"
                  label="Select Permissions"
                  placeholder="Select Permissions"
                  :rules="[requiredValidator]"
                  :items="permissionList"
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
                  @click="router.push({ name: 'apps-roles' })"
                >
                  Cancel
                </VBtn>
              </VCol>
            </VRow>
          </VForm>
          <VAlert
            v-if="message"
            color="error"
            class="mt-3"
          >
            {{ message }}
          </VAlert>
        </VCardItem>
      </VCard>
    </VCol>
  </VRow>
</template>
