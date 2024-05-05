<script setup>
import { paginationMeta } from "@api-utils/paginationMeta"
import { VDataTableServer } from "vuetify/labs/VDataTable"

// 👉 Store
const searchQuery = ref("")

const isSnackbarVisible = ref(false)
const message = ref()
const router = useRouter()

// Data table options
const itemsPerPage = ref(10)
const page = ref(1)


// Headers
const headers = [
  {
    title: "Name",
    key: "name",
  },
  {
    title: "Model",
    key: "model",
  },
  {
    title: "Price",
    key: "price",
  },
  {
    title: "Details",
    key: "details",
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


const { data: companyData } = await useApi(
  createUrl(`${baseUrl}/products`),
)



let company = ref()
let backup = ref()
company.value = companyData.value
backup.value = companyData.value

const totalcompany = computed(() => companyData.value.company?.length)

const handleSearch = () => {
  const searchTerm = searchQuery.value.toLowerCase()
  if (searchTerm) {
    const res = backup.value.filter(
      user =>
        user.name.toLowerCase().includes(searchTerm) ||
        user.address.toLowerCase().includes(searchTerm),
    )

    company.value = res
  } else company.value = backup.value
}

const deleteService = async id => {
  const confirmed = window.confirm("Are you sure you want to delete this type of service?")
  if (!confirmed) {
    return // Exit if user cancels deletion
  }

  const res = await $api(`${baseUrl}/products/${id}`, {
    method: "DELETE",
  })

  isSnackbarVisible.value = true
  message.value = res.message

  const { data: companyData } = await useApi(createUrl(`${baseUrl}/products`))

  company.value = companyData.value
  router.push({ name: 'apps-products-list' })
}
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
            @click="$router.push({name:'apps-products'})"
          >
            Add New Sub Products
          </VBtn>
        </div>
      </VCardText>
      <VDivider />
      <!-- SECTION datatable -->
      <VDataTableServer
        v-model:items-per-page="itemsPerPage"
        v-model:page="page"
        :items="company"
        :items-length="totalcompany"
        :headers="headers"
        class="text-no-wrap"
      >
        <!-- Actions -->
        <template #item.actions="{ item }">
          <IconBtn @click="deleteService(item.id)">
            <VIcon icon="tabler-trash" />
          </IconBtn>

          <IconBtn
            @click="
              $router.push({
                name: 'apps-products-edit-id',
                params: { id: item.id }})
            "
          >
            <VIcon icon="tabler-edit" />
          </IconBtn>
        </template>
        <!-- pagination -->
        <template #bottom>
          <VDivider />
          <div class="d-flex align-center justify-sm-space-between justify-center flex-wrap gap-3 pa-5 pt-3">
            <p class="text-sm text-disabled mb-0">
              {{ paginationMeta({ page, itemsPerPage }, totalcompany) }}
            </p>
            <VPagination
              v-model="page"
              :length="Math.ceil(totalcompany / itemsPerPage)"
              :total-visible="
                $vuetify.display.xs ? 1 : Math.ceil(totalcompany / itemsPerPage)
              "
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
  </section>
</template>
