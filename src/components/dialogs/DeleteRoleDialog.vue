<script setup>
const props = defineProps({
  isDeleteRoleDialogVisible: Boolean,
  deleteRoleId: Number,
  deleteRoleName: String,
  getRoles: Function,
})

const emit = defineEmits([
  'update:isDeleteRoleDialogVisible',
])

const onReset = () => {
  emit('update:isDeleteRoleDialogVisible', false)
}


const deleteRole= async()=>{
  const token = useCookie("accessToken").value
  try {
    const res = await $api(`${baseUrl}/roles/${props.deleteRoleId}`, {
      method: "DELETE",
      headers: {
        Authorization: `Bearer ${token}`,
      },
    })


    if(res.status=="Success"){
 
      props.getRoles()
      onReset()
      
    }
    
  } catch (err) {
    console.error(err)
  }
}
</script>

<template>
  <VDialog
    :model-value="props.isDeleteRoleDialogVisible"
    width="500"
  >
    <!-- Dialog close btn -->
    <DialogCloseBtn @click="onReset" />

    <!-- Dialog Content -->
    <VCard>
      <VCardText>Do you want to delete <span class="text-danger">{{ deleteRoleName }}</span>  ?</VCardText>
   
      <VCardText class="d-flex justify-end">
        <VBtn
          color="error"
          @click="deleteRole(index)"
        >
          I accept
        </VBtn>
      </VCardText>
    </VCard>
  </VDialog>
</template>
