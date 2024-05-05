<script setup>
import girlUsingMobile from "@images/pages/girl-using-mobile.png"

const roles = ref([])

// 👉 Roles List
const isAddRoleDialogVisible = ref(false)
const isEditRoleDialogVisible = ref(false)
const isDeleteRoleDialogVisible = ref(false)
const editRoleValue = ref()
const editRoleId = ref()
const deleteRoleId = ref()
const deleteRoleName = ref()

onMounted(async () => {
  await getRoles()
})

const getRoles =async () => {
  const token = useCookie("accessToken").value
  try {
    const res = await $api(`${baseUrl}/roles`, {
      method: "GET",
      headers: {
        Authorization: `Bearer ${token}`,
      },
    })

    roles.value = res?.role
  } catch (err) {
    console.error(err)
  }
}

const editRoles=(item, index)=>{
  isEditRoleDialogVisible.value=true
  editRoleValue.value=item
  editRoleId.value=index
}

const deleteRoles=(index, value)=>{
  isDeleteRoleDialogVisible.value=true
  deleteRoleId.value=index
  deleteRoleName.value=value
}
</script>

<template>
  <VRow>
    <!-- 👉 Add New Role -->
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
              :src="girlUsingMobile"
            >
          </VCol>
          <VCol cols="7">
            <VCardText class="d-flex flex-column align-end justify-end gap-2 h-100">
              <VBtn @click="isAddRoleDialogVisible = true">
                Add Role
              </VBtn>
            </VCardText>
          </VCol>
        </VRow>
      </VCard>
    </VCol>
    <!-- 👉 Roles -->
    <VCol
      v-for="item in roles"
      :key="item.role"
      cols="12"
      sm="6"
      lg="4"
    >
      <VCard>
        <VCardText class="d-flex align-center pb-3">
          <span>Total {{ item.permissions.length }} permissions</span>
        </VCardText>
        <VCardText class="pb-5">
          <h4 class="text-h4">
            {{ item.name }}
          </h4>
        </VCardText>
      </VCard>
    </VCol>
    <!--
      <VCol
      v-for="item,index in roles"
      :key="index"
      cols="12"
      sm="6"
      lg="4"
      >
      <VCard>
      <VCardText class="pb-5">
      <h4 class="text-h4">
      {{ item }}
      </h4>
      <div class="d-flex align-center">
      <a
      href="javascript:void(0)"
      @click="editRoles(item,index)"
      >
      Edit Role
      </a>
      <VSpacer />
      <VBtn
      icon
      color="error"
      variant="text"
      size="x-small"
      @click="deleteRoles(index,item)"
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
    -->
  </VRow>
  <!-- Dialoges -->
  <AddRoleDialog
    v-model:is-dialog-visible="isAddRoleDialogVisible"
    :get-roles="getRoles"
  />
  <EditRoleDialog
    v-model:is-dialog-visible="isEditRoleDialogVisible"
    :edit-role-id="editRoleId"
    :edit-role-value="editRoleValue"
    :get-roles="getRoles"
  />
  <DeleteRoleDialog
    v-model:isDeleteRoleDialogVisible="isDeleteRoleDialogVisible"
    :delete-role-id="deleteRoleId"
    :delete-role-name="deleteRoleName"
    :get-roles="getRoles"
  />
</template>
