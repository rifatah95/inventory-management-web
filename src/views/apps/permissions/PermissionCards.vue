<script setup>
import permissionImage from "@images/pages/pose-fs-9.png"

const permissions = ref([])

// 👉 Roles List
const isAddPermissionDialogVisible = ref(false)
const isEditPermissionDialogVisible = ref(false)
const isDeletePermissionDialogVisible = ref(false)
const editPermissionValue = ref()
const editPermissionId = ref()
const deletePermissionId = ref()
const deletePermissionName = ref()

onMounted(async () => {
  await getPermission()
})

const getPermission =async () => {
  const token = useCookie("accessToken").value
  try {
    const res = await $api(`${baseUrl}/permission_list`, {
      method: "GET",
      headers: {
        Authorization: `Bearer ${token}`,
      },
    })

    permissions.value = res?.permission
  } catch (err) {
    console.error(err)
  }
}

const editPermission=(item, index)=>{
  isEditPermissionDialogVisible.value=true
  editPermissionValue.value=item
  editPermissionId.value=index
}

const deletePermission=(index, value)=>{
  isDeletePermissionDialogVisible.value=true
  deletePermissionId.value=index
  deletePermissionName.value=value

}
</script>

<template>
  <VRow>
    <!-- 👉 Add New Permission -->
    <VCol
      cols="12"
      sm="6"
      lg="4"
    >
      <VCard :ripple="false">
        <VRow no-gutters>
          <VCol
            cols="5"
            class="d-flex flex-column justify-end align-center mt-5"
          >
            <img
              width="60"
              :src="permissionImage"
            >
          </VCol>
          <VCol cols="7">
            <VCardText class="d-flex flex-column align-end justify-end gap-2 h-100">
              <VBtn @click="isAddPermissionDialogVisible = true">
                Add Permission
              </VBtn>
            </VCardText>
          </VCol>
        </VRow>
      </VCard>
    </VCol>
    <!-- 👉 Permission -->
    <VCol
      v-for="item,index in permissions"
      :key="index"
      cols="12"
      sm="6"
      lg="4"
    >
      <VCard>
        <VCardText class="pb-5">
          <h4 class="text-h4">
            {{ item?.name }}
          </h4>
          <div class="d-flex align-center">
            <a
              href="javascript:void(0)"
              @click="editPermission(item?.name,item?.id)"
            >
              Edit Permission
            </a>
            <VSpacer />
            <VBtn
              icon
              color="error"
              variant="text"
              size="x-small"
              @click="deletePermission(item?.id,item?.name)"
            >
              <VIcon
                size="24"
                icon="tabler-trash"
              />
            </VBtn>
          </div>
        </VCardText>
      </VCard>
    </VCol>
  </VRow>
  <!-- Dialoges -->
  <AddPermissionDialog
    v-model:is-dialog-visible="isAddPermissionDialogVisible"
    :get-permission="getPermission"
  />
  <EditPermissionDialog
    v-model:is-dialog-visible="isEditPermissionDialogVisible"
    :edit-permission-id="editPermissionId"
    :edit-permission-value="editPermissionValue"
    :get-permission="getPermission"
  />
  <DeletePermissionDialog
    v-model:isDeletePermissionDialogVisible="isDeletePermissionDialogVisible"
    :delete-permission-id="deletePermissionId"
    :delete-permission-name="deletePermissionName"
    :get-permission="getPermission"
  />
</template>
