<script setup>
const props = defineProps({
  isDialogVisible: Boolean,
  getPermission: Function,
})

const emit = defineEmits([
  'update:isDialogVisible',
])

const name = ref('')
const message = ref('')

const onReset = () => {
  emit('update:isDialogVisible', false)
  name.value = ''
  message.value=""
}

const createPermission = async data => {
  if(!data) return
  try {
    const res = await $api(`${baseUrl}/permissions`, {
      method: 'POST',
      body: {
        name: data,
      },
    })


    if(res?.status=="Success"){
      props.getPermission()
      onReset()
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
          Add Permission
        </VCardTitle>
        <VCardSubtitle>
          Add permission as per your requirements.
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
          <VBtn @click.prevent="createPermission(name)">
            Create
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
