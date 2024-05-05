<script setup>
import PermissionCards from '@/views/apps/permissions/PermissionCards.vue'

</script>

<template>
  <VRow>
    <VCol cols="12">
      <h4 class="text-h4 mb-6">
        Permissions List
      </h4>
    </VCol>

    <!-- 👉 Roles Cards -->
    <VCol cols="12">
      <PermissionCards />
    </VCol>
  </VRow>
</template>
