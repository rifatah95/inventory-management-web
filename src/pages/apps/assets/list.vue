<script setup>
import { onMounted, ref } from "vue"
import { VDataTable } from "vuetify/labs/VDataTable"

// 👉 Store
const searchQuery = ref("")

const isSnackbarVisible = ref(false)

const isDeleteUser = ref(false)
const deletedMessage = ref(null)

let company = ref()
let backup = ref()

// Data table options


// Headers
const headers = [
  {
    title: "Name",
    key: "name",
  },
  {
    title: "Details",
    key: "details",
  },
  {
    title: "Purchase Date",
    key: "purchase_date",
  },
  {
    title: "Price",
    key: "price",
  },
  {
    title: "Actions",
    key: "actions",
    sortable: false,
  },
]

onMounted(async () => {
  await fetchUsers()
})


const fetchUsers = async () => {
  try {
    const res = await $api(`${baseUrl}/assets`, {
      method: "GET",
    })

    company.value = res
    backup.value = res
  } catch (err) {
    console.error(err)
  }
}






const handleSearch = () => {
  const searchTerm = searchQuery.value.toLowerCase()
  if (searchTerm) {
    const res = backup.value.filter(
      user =>
        user.name.toLowerCase().includes(searchTerm) ||
        user.purchase_date.toLowerCase().includes(searchTerm),
    )

    company.value = res
  } else company.value = backup.value
}

const deleteService = async id =>{
  try {
    const res = await $api(`${baseUrl}/assets/${id}`, {
      method: "DELETE",
    })

    searchQuery.value = ''
    isDeleteUser.value=false
    isSnackbarVisible.value = true
    deletedMessage.value = res
    fetchUsers()
    


  } catch (err) {
    isSnackbarVisible.value = true
    deletedMessage.value = err?.message
    console.error(err)
  }
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
            @click="$router.push({name:'apps-assets'})"
          >
            Add New Assets
          </VBtn>
        </div>
      </VCardText>
      <VDivider />
      <!-- SECTION datatable -->
      <VDataTable
        :items="company"
        :headers="headers"
        class="text-no-wrap"
      >
        <!-- Actions -->
        <template #item.actions="{ item }">
          <IconBtn
            color="error"
            @click="()=>{
              isDeleteUser = true
              deleteUserInfo = item?.id;
            
            }"
          >
            <VIcon icon="tabler-trash" />
          </IconBtn>

          <IconBtn
            @click="
              $router.push({
                name: 'apps-assets-edit-id',
                params: { id: item.id }})
            "
          >
            <VIcon icon="tabler-edit" />
          </IconBtn>
        </template>
      </VDataTable>
      <!-- SECTION -->
    </VCard>
    <!-- Delete User -->
    <VDialog
      :model-value="isDeleteUser"
      width="500"
    >
      <!-- Dialog close btn -->
      <DialogCloseBtn @click="isDeleteUser=false" />

      <!-- Dialog Content -->
      <VCard>
        <VCardText>Do you want to delete <span class="text-danger">Assets</span>  ?</VCardText>
   
        <VCardText class="d-flex justify-end">
          <VBtn
            color="error"
            @click="deleteService(deleteUserInfo)"
          >
            I accept
          </VBtn>
        </VCardText>
      </VCard>
    </VDialog>
  </section>
</template>
