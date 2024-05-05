<script setup>
import { baseUrl } from '@/composables/useApi'
import AddNewUserDrawer from '@/views/apps/user/list/AddNewUserDrawer.vue'
import { paginationMeta } from '@api-utils/paginationMeta'
import { VDataTableServer } from 'vuetify/labs/VDataTable'

// 👉 Store
const searchQuery = ref('')


// Data table options
const itemsPerPage = ref(10)
const page = ref(1)
const sortBy = ref()
const orderBy = ref()

const updateOptions = options => {
  page.value = options.page
  sortBy.value = options.sortBy[0]?.key
  orderBy.value = options.sortBy[0]?.order
}


// Headers
const headers = [
  {
    title: 'User',
    key: 'user',
  },
  {
    title: 'Email',
    key: 'email',
  },
  {
    title: 'Role',
    key: 'role_name',
  },
  {
    title: 'Actions',
    key: 'actions',
    sortable: false,
  },
]

const {
  data: usersData,
  execute: fetchUsers,
} = await useApi(createUrl(`${baseUrl}/user_list`))

let users = ref()
let backup = ref()
users.value = usersData.value.users
backup.value = usersData.value.users

const totalUsers = computed(() => usersData.value.users?.length)
const isAddNewUserDrawerVisible = ref(false)

const handleSearch = () => {
  const searchTerm = searchQuery.value.toLowerCase()
  if(searchTerm){
    const res = backup.value.filter(user => user.name.toLowerCase().includes(searchTerm) || user.email.toLowerCase().includes(searchTerm))

    users.value = res
  }else
    users.value = backup.value
}

const addNewUser = async userData => {
  await $api('/apps/users', {
    method: 'POST',
    body: userData,
  })

  // refetch User
  fetchUsers()
}

const deleteUser = async id => {
  await $api(`/apps/users/${ id }`, { method: 'DELETE' })

  // refetch User
  fetchUsers()
}
</script>

<template>
  <section>
    <VCard>
      <VCardText class="d-flex flex-wrap py-4 gap-4">
        <VSpacer />
        <div class="app-user-search-filter d-flex align-center flex-wrap gap-4">
          <!-- 👉 Search  -->
          <div style="inline-size: 10rem;">
            <AppTextField
              v-model="searchQuery"
              placeholder="Search"
              density="compact"
              @input="handleSearch"
            />
          </div>
      
          <!-- 👉 Add user button -->
          <!--
            <VBtn
            prepend-icon="tabler-plus"
            @click="isAddNewUserDrawerVisible = true"
            >
            Add New User
            </VBtn> 
          -->
        </div>
      </VCardText>
      <VDivider />
      <!-- SECTION datatable -->
      <VDataTableServer
        v-model:items-per-page="itemsPerPage"
        v-model:page="page"
        :items="users"
        :items-length="totalUsers"
        :headers="headers"
        class="text-no-wrap"
        @update:options="updateOptions"
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
        <!-- Actions -->
        <template #item.actions="{ item }">
          <IconBtn @click="deleteUser(item.id)">
            <VIcon icon="tabler-trash" />
          </IconBtn>
          <IconBtn>
            <VIcon icon="tabler-edit" />
          </IconBtn>
          <VBtn
            icon
            variant="text"
            size="small"
            color="medium-emphasis"
          >
            <VIcon
              size="24"
              icon="tabler-dots-vertical"
            />
            <VMenu activator="parent">
              <VList>
                <VListItem :to="{ name: 'apps-user-view-id', params: { id: item.id } }">
                  <template #prepend>
                    <VIcon icon="tabler-eye" />
                  </template>
                  <VListItemTitle>View</VListItemTitle>
                </VListItem>
                <VListItem link>
                  <template #prepend>
                    <VIcon icon="tabler-pencil" />
                  </template>
                  <VListItemTitle>Edit</VListItemTitle>
                </VListItem>
                <VListItem @click="deleteUser(item.id)">
                  <template #prepend>
                    <VIcon icon="tabler-trash" />
                  </template>
                  <VListItemTitle>Delete</VListItemTitle>
                </VListItem>
              </VList>
            </VMenu>
          </VBtn>
        </template>
        <!-- pagination -->
        <template #bottom>
          <VDivider />
          <div class="d-flex align-center justify-sm-space-between justify-center flex-wrap gap-3 pa-5 pt-3">
            <p class="text-sm text-disabled mb-0">
              {{ paginationMeta({ page, itemsPerPage }, totalUsers) }}
            </p>
            <VPagination
              v-model="page"
              :length="Math.ceil(totalUsers / itemsPerPage)"
              :total-visible="$vuetify.display.xs ? 1 : Math.ceil(totalUsers / itemsPerPage)"
            >
              <template #prev="slotProps">
                <VBtn
                  variant="tonal"
                  color="default"
                  v-bind="slotProps"
                  :icon="false"
                >
                  Previous
                </VBtn>
              </template>
              <template #next="slotProps">
                <VBtn
                  variant="tonal"
                  color="default"
                  v-bind="slotProps"
                  :icon="false"
                >
                  Next
                </VBtn>
              </template>
            </VPagination>
          </div>
        </template>
      </VDataTableServer>
      <!-- SECTION -->
    </VCard>
    <!-- 👉 Add New User -->
    <AddNewUserDrawer
      v-model:isDrawerOpen="isAddNewUserDrawerVisible"
      @user-data="addNewUser"
    />
  </section>
</template>
