<script setup>
const props = defineProps({
  isDialogVisible: Boolean,
  editPermissionId: Number,
  editPermissionValue: String,
  getPermission: Function,
})

const emit = defineEmits([
  'update:isDialogVisible',
])

const message = ref('')

const onReset = () => {
  emit('update:isDialogVisible', false)
  message.value=""
}

const name = ref()

watch(() => props.editPermissionValue, newValue => name.value = newValue)

const updatePermission = async () => {

  try {
    const res = await $api(`${baseUrl}/permissions/${props.editPermissionId}?name=${name.value}`, {
      method: 'PUT',
      body: {
        name: name.value,
      },
    })


    if(res?.status=="Success"){
      props.getPermission()
      onReset()
    }
    if(res?.status=="error"){
      message.value = res?.message?.name?.at(0)
     
    }
  } catch (err) {
    console.log(err)
    message.value=err
  }
}
</script>

<template>
  <VDialog
    :width="$vuetify.display.smAndDown ? 'auto' : 600"
    :model-value="props.isDialogVisible"
    @update:model-value="onReset"
  >
    <!-- 👉 dialog close btn -->
    <DialogCloseBtn @click="onReset" />
    <VCard class="pa-sm-8 pa-5">
      <!-- 👉 Title -->
      <VCardItem class="text-center">
        <VCardTitle class="text-h5">
          Edit Permission
        </VCardTitle>
        <VCardSubtitle>
          Update permission as per your requirements.
        </VCardSubtitle>
      </VCardItem>
      <VCardText class="mt-1">
        <!-- 👉 Form -->
        <!-- 👉 Permission name -->
        <div class="d-flex align-end gap-3 mb-3">
          <AppTextField
            v-model="name"
            density="compact"
            label="Permission Name"
            placeholder="Enter Permission Name"
          />
          <VBtn @click.prevent="updatePermission(name)">
            Update
          </VBtn>
        </div>
        <VAlert
          v-if="message"
          color="error"
        >
          {{ message }}
        </VAlert>
      </VCardText>
    </VCard>
  </VDialog>
</template>

<style lang="scss">
.permission-table {
  td {
    border-block-end: 1px solid rgba(var(--v-border-color), var(--v-border-opacity));
    padding-block: 0.5rem;
    padding-inline: 0;
  }
}
</style>
