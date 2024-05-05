<script setup>
const props = defineProps({
  isDeletePermissionDialogVisible: Boolean,
  deletePermissionId: Number,
  deletePermissionName: String,
  getPermission: Function,
})

const emit = defineEmits([
  'update:isDeletePermissionDialogVisible',
])

const onReset = () => {
  emit('update:isDeletePermissionDialogVisible', false)
}


const deletePermission= async()=>{
  const token = useCookie("accessToken").value
  try {
    const res = await $api(`${baseUrl}/permissions/${props.deletePermissionId}`, {
      method: "DELETE",
      headers: {
        Authorization: `Bearer ${token}`,
      },
    })


    if(res.status=="Success"){
 
      props.getPermission()
      onReset()
      
    }
    
  } catch (err) {
    console.error(err)
  }
}
</script>

<template>
  <VDialog
    :model-value="props.isDeletePermissionDialogVisible"
    width="500"
  >
    <!-- Dialog close btn -->
    <DialogCloseBtn @click="onReset" />

    <!-- Dialog Content -->
    <VCard>
      <VCardText>Do you want to delete <span class="text-danger">{{ deletePermissionName }}</span>  ?</VCardText>
   
      <VCardText class="d-flex justify-end">
        <VBtn
          color="error"
          @click="deletePermission(index)"
        >
          I accept
        </VBtn>
      </VCardText>
    </VCard>
  </VDialog>
</template>
