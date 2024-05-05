<script setup>
const props =defineProps(["isDrawerOpen", "roleList", "fetchUsers"])

const emit = defineEmits(["update:isDrawerOpen"])
const isSnackbarVisible = ref(false)


const form = ref({
  name: "",
  email: "",
  password: "",
  roles: null,
})

// 👉 drawer close
const reset = () => {
  emit("update:isDrawerOpen", false)
  form.value={
    name: "",
    email: "",
    password: "",
    roles: "",
  }
}

const addNewUser = async data => {
  const res = await $api(`${baseUrl}/register`, {
    method: "POST",
    body: data,
  })

  if (res?.status == "success") {isSnackbarVisible.value = true
    reset()
    props.fetchUsers()}
}
</script>

<template>
  <VSnackbar
    v-model="isSnackbarVisible"
    :timeout="2000"
    location="top end"
  >
    New user created successfully
  </VSnackbar>
  <VNavigationDrawer
    temporary
    :width="400"
    location="end"
    class="scrollable-content"
    :model-value="isDrawerOpen"
  >
    <!-- 👉 Title -->
    <AppDrawerHeaderSection
      title="Add User"
      @cancel="reset"
    />

    <VCard flat>
      <VCardText>
        <!-- 👉 Form -->
        <VForm @submit.prevent="addNewUser(form)">
          <VRow>
            <!-- 👉 Full name -->
            <VCol cols="12">
              <AppTextField
                v-model="form.name"
                :rules="[requiredValidator]"
                label="Name"
                placeholder="Enter here.."
              />
            </VCol>

            <!-- 👉 Email -->
            <VCol cols="12">
              <AppTextField
                v-model="form.email"
                :rules="[requiredValidator, emailValidator]"
                label="Email"
                placeholder="example@demo.com"
              />
            </VCol>

            <!-- 👉 password -->
            <VCol cols="12">
              <AppTextField
                v-model="form.password"
                :rules="[requiredValidator]"
                label="Password"
                placeholder="············"
                type="password"
              />
            </VCol>
            <!-- 👉 roles -->
            <VCol cols="12">
              <AppSelect
                v-model="form.roles"
                label="Select Role"
                placeholder="Select Role"
                :rules="[requiredValidator]"
                :items="roleList"
              />
            </VCol>
            <!-- 👉 Submit and Cancel -->
            <VCol cols="12">
              <VBtn
                type="submit"
                class="me-3"
              >
                Submit
              </VBtn>
              <VBtn
                type="reset"
                variant="outlined"
                color="secondary"
                @click="reset"
              >
                Cancel
              </VBtn>
            </VCol>
          </VRow>
        </VForm>
      </VCardText>
    </VCard>
  </VNavigationDrawer>
</template>
