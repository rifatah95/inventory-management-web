<script setup>
const props = defineProps({
  isDialogVisible: Boolean,
  getRoles: Function,
})

const emit = defineEmits([
  'update:isDialogVisible',
])

const roleData = ref({
  name: '',
  permission: [],
})

const permissionList = ref()
const message = ref()

const onReset = () => {
  emit('update:isDialogVisible', false)
  message.value = ''
  roleData.value = {
    name: '',
    permission: [],
  }
}


onMounted(async() => {
  const data = await $api(`${baseUrl}/permission_list`, {
    method: "GET",
  })

  permissionList.value = data.permission.map(permission => permission.name)
})

const createRole =async data=>{
  if(!data?.name || !data?.permission) return
  try {
    const res = await $api(`${baseUrl}/roles`, {
      method: 'POST',
      body: {
        name: data.name,
        permission: data.permission,
      },
    })

    
    if(res?.status=="Success"){
      props.getRoles()
      onReset()
    }
    if(res?.status=="error"){
      message.value = res?.message?.name?.at(0)
    }
  } catch (err) {
    console.error(err)
  }
}
</script>

<template>
  <VDialog
    :width="$vuetify.display.smAndDown ? 'auto' : 900"
    :model-value="props.isDialogVisible"
    @update:model-value="onReset"
  >
    <!-- 👉 Dialog close btn -->
    <DialogCloseBtn @click="onReset" />
    <VCard class="pa-sm-8 pa-5">
      <!-- 👉 Title -->
      <VCardItem class="text-center">
        <VCardTitle class="text-h3 mb-3">
          Add Role
        </VCardTitle>
        <p class="text-base mb-0">
          Set Role Permissions
        </p>
      </VCardItem>
      <VCardText class="mt-6">
        <!-- 👉 Form -->
        <!-- 👉 Role name -->
        <AppTextField
          v-model="roleData.name"
          class="mb-3"
          label="Role Name"
          placeholder="Enter Role Name"
          :rules="[requiredValidator]"
        />
        <AppSelect
          v-model="roleData.permission"
          class="mb-3"
          label="Select Permission"
          placeholder="Select Permission"
          :rules="[requiredValidator]"
          :items="permissionList"
          multiple
        />
        <div>
          <VBtn
            block
            type="submit"
            @click.prevent="createRole(roleData)"
          >
            Create Role
          </VBtn>
        </div>
        <VAlert
          v-if="message"
          color="error"
          class="mt-3"
        >
          {{ message }}
        </VAlert>
      </VCardText>
    </VCard>
  </VDialog>
</template>
