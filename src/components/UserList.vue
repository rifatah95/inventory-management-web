<script setup>
import AddNewUserDrawer from "@/views/apps/user/list/AddNewUserDrawer.vue"
import { onMounted, ref } from "vue"
import { VDataTable } from "vuetify/labs/VDataTable"

// 👉 Store
const searchQuery = ref("")
const isDrawerOpen = ref(false)
const isSnackbarVisible = ref(false)
const isDeleteUser = ref(false)
const deletedMessage = ref(null)

const deleteUserInfo = ref({
  name: null,
  id: null,
})

const colors = ["info", "success", "warning", "primary", "error"]
const users = ref([])
const backup = ref([])
const roleList = ref([])

// Headers
const headers = [
  {
    title: "User",
    key: "user",
  },
  {
    title: "Email",
    key: "email",
  },
  {
    title: "Role",
    key: "roles",
  },
  {
    title: "Permissions",
    key: "permissions",
  },
  {
    title: "Status",
    key: "status",
  },
  {
    title: "Actions",
    key: "actions",
    sortable: false,
  },
]

onMounted(async () => {
  await fetchUsers()
  await getRoles()
})

const getRoles = async () => {
  try {
    const res = await $api(`${baseUrl}/roles`, {
      method: "GET",
    })

    roleList.value = res?.role?.map(role => role.name)
  } catch (err) {
    roleList.value = null
    console.error(err)
  }
}

const fetchUsers = async () => {
  try {
    const res = await $api(`${baseUrl}/user_list`, {
      method: "GET",
    })

    users.value = res?.users
    backup.value = res?.users
  } catch (err) {
    console.error(err)
  }
}

const deleteUser = async id =>{
  try {
    const res = await $api(`${baseUrl}/user_delete/${id}`, {
      method: "DELETE",
    })

    if(res.status== 'Success'){
      searchQuery.value = ''
      isDeleteUser.value=false
      isSnackbarVisible.value = true
      deletedMessage.value = res?.message
      fetchUsers()
    }


  } catch (err) {
    isSnackbarVisible.value = true
    deletedMessage.value = err?.message
    console.error(err)
  }
}

const handleSearch = () => {
  const searchTerm = searchQuery.value.toLowerCase()
  if (searchTerm) {
    const res = backup.value.filter(
      user =>
        user.name.toLowerCase().includes(searchTerm) ||
        user.email.toLowerCase().includes(searchTerm),
    )

    users.value = res
  } else users.value = backup.value
}
</script>

<template>
  <section>
    <VSnackbar
      v-model="isSnackbarVisible"
      :timeout="2000"
      location="top end"
    >
      {{ deletedMessage }}
    </VSnackbar>
    <VCard>
      <VCardText class="d-flex flex-wrap py-4 gap-4">
        <VSpacer />
        <div class="app-user-search-filter d-flex align-center flex-wrap gap-4">
          <!-- 👉 Search  -->
          <div style="inline-size: 10rem">
            <AppTextField
              v-model="searchQuery"
              placeholder="Search"
              density="compact"
              @input="handleSearch"
            />
          </div>
          <VBtn
            prepend-icon="tabler-plus"
            @click="isDrawerOpen = true"
          >
            Add New User
          </VBtn>
        </div>
      </VCardText>
      <VDivider />
      <!-- SECTION datatable -->
      <VDataTable
        :items="users"
        :headers="headers"
        class="text-no-wrap"
      >
        <!-- User -->
        <template #item.user="{ item }">
          <div class="d-flex align-center">
            <VAvatar
              size="34"
              :variant="!item.avatar ? 'tonal' : undefined"
              class="me-3"
            >
              <span class="text-capitalize">{{ avatarText(item.name) }}</span>
            </VAvatar>
            <div class="d-flex flex-column">
              <h6 class="text-base">
                <RouterLink
                  :to="{ name: 'apps-user-view-id', params: { id: item.id } }"
                  class="font-weight-medium text-link text-capitalize"
                >
                  {{ item.name }}
                </RouterLink>
              </h6>
            </div>
          </div>
        </template>
        <!-- roles -->
        <template #item.roles="{ item }">
          <p v-if="!item?.roles?.length">
            -
          </p>
          <div
            v-else
            class="d-flex gap-2"
          >
            <VChip
              v-for="(text, index) in item.roles"
              :key="text"
              label
              :color="colors[index]"
              class="font-weight-medium"
            >
              {{ text?.name || "-" }}
            </VChip>
          </div>
        </template>
        <!-- permissions -->
        <template #item.permissions="{ item }">
          <p v-if="!item?.permissions?.length">
            -
          </p>
          <div
            v-else
            class="d-flex gap-2"
          >
            <VChip
              v-for="(text, index) in item.permissions"
              :key="text"
              label
              :color="colors[index]"
              class="font-weight-medium"
            >
              {{ text?.name || "-" }}
            </VChip>
          </div>
        </template>
        <!-- Status -->
        <template #item.status="{ item }">
          <VChip
            :color="`${item?.status==1? 'primary':'warning'}`"
            variant="outlined"
          >
            {{ item?.status==1?'Active':'Inactive' }}
          </VChip>
        </template>
        <!-- Actions -->
        <template #item.actions="{ item }">
          <IconBtn
            color="error"
            @click="()=>{
              isDeleteUser = true
              deleteUserInfo.name = item?.email;
              deleteUserInfo.id = item?.id;
            
            }"
          >
            <VIcon icon="tabler-trash" />
          </IconBtn>
          <IconBtn
         
            @click="
              $router.push({
                name: 'apps-user-edit-id',
                params: { id: item.id },
                query: {
                  name: item?.name,
                  email: item?.email,
                  roles: item?.roles.map((role) => Object(role).name),
                },
              })
            "
          >
            <VIcon icon="tabler-edit" />
          </IconBtn>
        </template>
      </VDataTable>
      <!-- SECTION -->
    </VCard>
    <!-- 👉 Add New User -->
    <AddNewUserDrawer
      v-model:isDrawerOpen="isDrawerOpen"
      :role-list="roleList"
      :fetch-users="fetchUsers"
    />
    <!-- Delete User -->
    <VDialog
      :model-value="isDeleteUser"
      width="500"
    >
      <!-- Dialog close btn -->
      <DialogCloseBtn @click="isDeleteUser=false" />

      <!-- Dialog Content -->
      <VCard>
        <VCardText>Do you want to delete <span class="text-danger">{{ deleteUserInfo.name }}</span>  ?</VCardText>
   
        <VCardText class="d-flex justify-end">
          <VBtn
            color="error"
            @click="deleteUser(deleteUserInfo.id)"
          >
            I accept
          </VBtn>
        </VCardText>
      </VCard>
    </VDialog>
  </section>
</template>
