<script setup>
import { VDataTableVirtual } from "vuetify/labs/VDataTable"

defineProps({
  roles: Object,
  getRoles: Function,
})

const colors = ["info", "success", "warning", "primary", "error"]

const headers = [
  {
    title: "Role",
    key: "name",
  },
  {
    title: "Permissions",
    key: "permissions",
  },
  {
    title: "Actions",
    key: "actions",
    sortable: false,
  },
]

const isDeleteRoleDialogVisible =ref(false)
const deleteRoleId = ref(null)
const deleteRoleName = ref(null)


// Event
const deleteRoles=item=>{
  isDeleteRoleDialogVisible.value=true
  deleteRoleId.value=item?.id
  deleteRoleName.value=item?.name
}
</script>

<template>
  <section>
    <VCard>
      <VDivider />
      <!-- SECTION datatable -->
      <VDataTableVirtual
        :items="roles"
        :headers="headers"
        class="text-no-wrap"
      >
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
              :key="index"
              label
              :color="colors[index]"
              class="font-weight-medium"
            >
              {{ text?.name || "-" }}
            </VChip>
          </div>
        </template>
        <!-- Actions -->
        <template #item.actions="{ item }">
          <IconBtn @click="deleteRoles(item)">
            <VIcon icon="tabler-trash" />
          </IconBtn>
          <IconBtn @click="$router.push({name:`apps-roles-edit-id`,params:{id: item?.id}})">
            <VIcon icon="tabler-edit" />
          </IconBtn>
        </template>
      </VDataTableVirtual>
    </VCard>
    <DeleteRoleDialog
      v-model:isDeleteRoleDialogVisible="isDeleteRoleDialogVisible"
      :delete-role-id="deleteRoleId"
      :delete-role-name="deleteRoleName"
      :get-roles="getRoles"
    />
  </section>
</template>
