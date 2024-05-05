<script setup>
const props = defineProps({
  alldata: {
    type: Object,
    required: true,
  },
})
</script>

<template>
  <VRow>
    <VCol cols="12">
      <VCard v-if="props.alldata">
        <VCardText class="text-center pt-15">
          <!-- 👉 Avatar -->
          <VAvatar
            rounded
            :size="100"
            :color="!props.alldata.avatar ? 'primary' : undefined"
            :variant="!props.alldata.avatar ? 'tonal' : undefined"
          >
            <span class="text-5xl font-weight-medium">
              {{ avatarText(props.alldata.name) }}
            </span>
          </VAvatar>

          <!-- 👉 User fullName -->
          <h6 class="text-h4 mt-4">
            {{ props.alldata.name }}
          </h6>

          <!-- 👉 Role chip -->
          <VChip
            v-for="role,index in props.alldata.roles"
            :key="index"
            label
            
            size="small"
            class="text-capitalize mt-3 me-2"
          >
            {{ role?.name }}
          </VChip>
        </VCardText>

       

        <VDivider />

        <!-- 👉 Details -->
        <VCardText>
          <p class="text-sm text-uppercase text-disabled">
            Details
          </p>

          <!-- 👉 User Details list -->
          <VList class="card-list mt-2">
            <VListItem>
              <VListItemTitle>
                <h6 class="text-h6">
                  Name:
                  <span class="text-body-1">
                    {{ props.alldata.name }}
                  </span>
                </h6>
              </VListItemTitle>
            </VListItem>

            <VListItem v-if="props.alldata.email">
              <VListItemTitle>
                <h6 class="text-h6">
                  Email:
                  <span class="text-body-1">{{ props.alldata.email }}</span>
                </h6>
              </VListItemTitle>
            </VListItem>

          

            <VListItem v-if="props.alldata.roles">
              <VListItemTitle>
                <h6 class="text-h6">
                  Role:
                  <span
                    v-for="role,index in props.alldata.roles"
                    :key="index"
                    class="text-capitalize text-body-1 me-1"
                  >{{ role?.name }},</span>
                </h6>
              </VListItemTitle>
            </VListItem>
            <VListItem v-if="props.alldata.permissions?.length>1">
              <VListItemTitle>
                <h6 class="text-h6">
                  Permissions:
                  <span
                    v-for="permission,index in props.alldata.permissions"
                    :key="index"
                    class="text-capitalize text-primary text-body-1 me-1"
                  >{{ permission?.name }},</span>
                </h6>
              </VListItemTitle>
            </VListItem>
          </VList>
        </VCardText>
      </VCard>
    </VCol>
    <!-- !SECTION -->
  </VRow>
</template>

<style lang="scss" scoped>
.card-list {
  --v-card-list-gap: 0.75rem;
}

.text-capitalize {
  text-transform: capitalize !important;
}
</style>
