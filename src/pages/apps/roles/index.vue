<script setup>
import girlUsingMobile from "@images/pages/girl-using-mobile.png"

const roles = ref([])

// 👉 Roles List
const isAddRoleDialogVisible = ref(false)


onMounted(async () => {
  await getRoles()
})

const getRoles =async () => {
  try {
    const res = await $api(`${baseUrl}/roles`, {
      method: "GET",
    })

    roles.value = res?.role
  } catch (err) {
    console.error(err)
  }
}
</script>

<template>
  <VRow>
    <VCol cols="12">
      <h4 class="text-h4 mb-6">
        Roles List
      </h4>
      <p class="mb-0">
        A role provided access to predefined menus and features so that depending on assigned role an administrator can have access to what he need
      </p>
    </VCol>
    <!-- 👉 Roles Cards -->
    <VCol cols="12">
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
      </VRow>
    </VCol>
    <!-- 👉 Role List  -->
    <VCol cols="12">
      <RoleList
        :roles="roles"
        :get-roles="getRoles"
      />
    </VCol>
  </VRow>
  <!-- Dialoges -->
  <AddRoleDialog
    v-model:is-dialog-visible="isAddRoleDialogVisible"
    :get-roles="getRoles"
  />
</template>
