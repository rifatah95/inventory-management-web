<template>
  <div>
    <div class="d-flex flex-wrap justify-start justify-sm-space-between gap-y-4 gap-x-6 mb-6">
      <div class="d-flex flex-column justify-center">
        <h4 class="text-h4 font-weight-medium">
          Add a new purchase
        </h4>
      </div>
    </div>

    <VRow>
      <VCol md="9">
        <!-- 👉 Product Information -->
        <VCard class="mb-9">
          <VCardText>
            <VRow>
              <VCol cols="12">
                <AppTextField
                  v-model="searchTerm"
                  placeholder="Start typing item name or scan barcode."
                  @input="updateSearch"
                  @keydown.down.prevent="handleArrowDown"
                  @keydown.up.prevent="handleArrowUp"
                  @keydown.enter.prevent="addToCartFromKeyboard"
                />
                <ul
                  v-if="searchTerm.trim() !== ''"
                  class="product-list"
                >
                  <li
                    v-for="(product, index) in filteredProducts"
                    :key="product.id"
                    class="product-item"
                    :class="{ active: index === selectedProductIndex }"
                    tabindex="0"
                    @click="addToCart(product)"
                    @keydown.enter.prevent="addToCart(product)"
                  >
                    <div class="product-details">
                      <span class="product-name">{{ product.name }}</span>
                    </div>
                  </li>
                </ul>
              </VCol>
            </VRow>
          </VCardText>
        </VCard>
        <VCard class="mb-9">
          <VCardText>
            <VRow>
              <VCol cols="12">
                <table
                  id="product_info_table"
                  class="table table-bordered custom-table"
                >
                  <thead>
                    <tr>
                      <th class="text-start">
                        Name
                      </th>
                      <th class="text-end">
                        Price
                      </th>
                      <th class="text-end">
                        Quantity
                      </th>
                      <th class="text-end">
                        Total
                      </th>
                      <th>Action</th>
                    </tr>
                  </thead>
                  <tbody>
                    <tr
                      v-for="(item, index) in cart"
                      :key="item.id"
                    >
                      <td class="text-start">
                        <div class="d-flex align-center gap-x-2">
                          <img
                            :src="imageUrl"
                            alt="Image"
                            variant="tonal"
                            rounded
                            :style="{ height: imageHeight + 'px' }"
                          > 
                          <div class="d-flex flex-column">
                            <span class="text-body-1 font-weight-medium">{{ item.name }}</span>
                          </div>
                        </div>
                      </td>
                      <td class="text-end">
                        <span class="text-body-1 font-weight-medium">{{ item.price }}</span>
                      </td>
                      <td class="text-end">
                        <input
                          v-model="cart[index].quantity"
                          type="number"
                          min="1"
                          class="form-control"
                        >
                      </td>
                      <td class="text-end">
                        <span class="text-body-1 font-weight-medium">{{ item.price * cart[index].quantity }}</span>
                      </td>
                      <td>
                        <button
                          class="btn"
                          @click="removeFromCart(index)"
                        >
                          <VIcon icon="tabler-square-rounded-x-filled" />
                        </button>
                      </td>
                    </tr>
                  </tbody>
                </table>
              </VCol>
            </VRow>
          </VCardText>
        </VCard>
      </VCol>

      <VCol
        md="3"
        cols="12"
      >
        <!-- 👉 Pricing -->
        <VCard class="mb-6 subamount">
          <VCardText>
            <select class="form-select custom-select">
              <option>Select Supplier</option>
              <option
                v-for="i in customers"
                :value="i.id"
              >
                {{ i.name }}
              </option>
            </select>
          </VCardText>
        </VCard>
        <VCard class="mb-6 subamount">
          <VCardText>
            <AppTextField
              label="Sub Total"
              :value="calculateGrossAmount()"
              class="mb-3"
              disabled=""
            />
            <AppTextField
              v-model="vat"
              label="Total Vat"
              class="mb-3"
              @input="calculateVatAmount"
            />
            <AppTextField
              v-model="totalAmount"
              label="Total"
              class="mb-3"
              disabled=""
            />
            <AppTextField
              v-model="discount"
              label="Discount"
              class="mb-3"
              @input="calculateDueAmount"
            />
            <AppTextField
              v-model="paidAmount"
              label="Paid Amount"
              class="mb-3"
              @input="calculateDueAmount"
            />
            <AppTextField
              label="Amount Due"
              :value="dueAmount"
              class="mb-8"
              disabled=""
            />
            <VBtn
              block
              class="mt-4"
              @click="nextStep"
            >
              Submit
            </VBtn>
          </VCardText>
        </VCard>
      </VCol>
    </VRow>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  data() {
    return {
      searchTerm: "",
      products: [],
      customers: [],
      cart: [],
      discount: '',
      paidAmount: '',
      vat: '',
      dueAmount: 0,
      selectedProductIndex: -1, 
      imageUrl: 'https://www.shutterstock.com/image-illustration/big-capsule-small-capsules-medicines-260nw-132722336.jpg',
      imageHeight: 20,
    }
  },
  computed: {
    filteredProducts() {
      return this.products.filter(product =>
        product.name.toLowerCase().includes(this.searchTerm.toLowerCase()),
      )
    },
    grossAmount() {
      return this.calculateGrossAmount()
    },
  },
  watch: {
    discount() {
      this.calculateDueAmount()
    },
    paidAmount() {
      this.calculateDueAmount()
    },

    vat() {
      this.calculateVatAmount()
    },
  },
  async mounted() {
    try {
      await this.fetchProducts()
      await this.fetchCustomer()
    } catch (error) {
      console.error("Failed to fetch products:", error)
    }
  },
  methods: {
    async fetchProducts() {
      try {
        const token = useCookie('accessToken').value 

        const response = await axios.get(`${baseUrl}/products`, {
          headers: {
            Authorization: `Bearer ${token}`,
          },
        })

        this.products = response.data
      } catch (error) {
        console.error("Failed to fetch products:", error)
        throw error 
      }
    },
    async fetchCustomer() {
      try {
        const token = useCookie('accessToken').value 

        const response = await axios.get(`${baseUrl}/suppliers`, {
          headers: {
            Authorization: `Bearer ${token}`,
          },
        })

        this.customers = response.data
      } catch (error) {
        console.error("Failed to fetch products:", error)
        throw error 
      }
    },
    addToCart(product) {
      const existingItem = this.cart.find(item => item.id === product.id)
      if (existingItem) {
        existingItem.quantity++
      } else {
        this.cart.push({ ...product, quantity: 1 })
      }
      this.searchTerm = '' // Clear search term to hide search results
    },
    addToCartFromKeyboard() {
      if (this.selectedProductIndex !== -1) {
        this.addToCart(this.filteredProducts[this.selectedProductIndex])
      }
    },
    removeFromCart(index) {
      this.cart.splice(index, 1)
    },
    calculateGrossAmount() {
      return this.cart.reduce((total, item) => total + item.price * item.quantity, 0)
    },
    calculateDueAmount() {
      const grossAmount = parseFloat(this.totalAmount) || 0
      const discount = parseFloat(this.discount) || 0
      const paidAmount = parseFloat(this.paidAmount) || 0
        
      if (discount > grossAmount || paidAmount > grossAmount) {
        this.discount = ''
        this.paidAmount = ''
        this.dueAmount = 0
      } else {
        this.dueAmount = grossAmount - discount - paidAmount
      }
    },
    calculateVatAmount() {
      const grossAmount = this.calculateGrossAmount()
      const vat = parseFloat(this.vat) || 0

      this.totalAmount = grossAmount + vat
      
    },
    submitCart() {
      console.log("Cart submitted:", this.cart)
    },
    updateSearch(event) {
      this.searchTerm = event.target.value
    },
    handleArrowDown() {
      if (this.selectedProductIndex < this.filteredProducts.length - 1) {
        this.selectedProductIndex++
      }
    },
    handleArrowUp() {
      if (this.selectedProductIndex > 0) {
        this.selectedProductIndex--
      }
    },
  },
}
</script>

<style>
.text-end .v-text-field__input {
  text-align: end;
}

.subamount input[type="text"]{ 
  font-weight: bold;
  text-align: end;
}
</style>



<style lang="scss" scoped>
/* Add your custom styles here */

.product-list {
  list-style-type: none;
  padding-inline-start: 0;
}

input[type="number"] {
  text-align: end;
}

.product-item {
  cursor: pointer;
}

.active {
  background-color: lightgray;
}

.inventory-card{
  .v-radio-group,
  .v-checkbox {
    .v-selection-control {
      align-items: start !important;

      .v-selection-control__wrapper{
        margin-block-start: -0.375rem !important;
      }
    }

    .v-label.custom-input {
      border: none !important;
    }
  }

  .v-tabs.v-tabs-pill {
    .v-slide-group-item--active.v-tab--selected.text-primary {
      h6{
        color: #fff !important
      }
    }
  }

}

.ProseMirror{
  p{
    margin-block-end: 0;
  }

  padding: 0.5rem;
  outline: none;

  p.is-editor-empty:first-child::before {
    block-size: 0;
    color: #adb5bd;
    content: attr(data-placeholder);
    float: inline-start;
    pointer-events: none;
  }
}

.custom-table {
  border-collapse: collapse;
  inline-size: 100%;
}

.custom-table th,
.custom-table td {
  padding: 10px;
  text-align:center;
}

.custom-table th {
  background-color: #f5f5f5;
  font-weight: bold;
}

.custom-table tbody tr:nth-child(even) {
  background-color: #f9f9f9;
}

.custom-table tbody tr:hover {
  background-color: #f0f0f0;
}

.form-control {
  inline-size: 80px; /* Adjust the width as needed */
}

.btn {
  border-radius: 3px;
  cursor: pointer;
  font-size: 14px;
  padding-block: 5px;
  padding-inline: 10px;
}

.btn-danger {
  border-color: #dc3545;
  background-color: #dc3545;
  color: #fff;
}

.btn-danger:hover {
  border-color: #bd2130;
  background-color: #c82333;
}

.product-list {
  padding: 0;
  margin: 0;
  list-style: none;
}

.product-item {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 15px;
  border-block-end: 1px solid #eee;
  transition: background-color 0.3s;
}

.product-item:last-child {
  border-block-end: none;
}

.product-item:hover {
  background-color: #f9f9f9;
}

.product-details {
  flex: 1;
}

.product-name {
  font-weight: bold;
}

.product-price {
  color: #555;
}

.add-to-cart-button {
  border: none;
  border-radius: 5px;
  background-color: #007bff;
  color: #fff;
  cursor: pointer;
  font-size: 14px;
  padding-block: 10px;
  padding-inline: 20px;
  transition: background-color 0.3s;
}

.add-to-cart-button:hover {
  background-color: #0056b3;
}



.submit-button {
  display: inline-block;
  border: none;
  border-radius: 8px;
  background-color: #4CAF50; /* Green */
  color: white;
  cursor: pointer;
  font-size: 16px;
  margin-block: 4px;
  margin-block-start: 20px;
  margin-inline: 2px;
  padding-block: 10px;
  padding-inline: 50px;
  text-align: center;
  text-decoration: none;
  transition: background-color 0.3s ease;
}

.submit-button:hover {
  background-color: #45a049; /* Darker Green */
}

.custom-select {
  display: inline-block;
  border: 1px solid #ced4da;
  border-radius: .25rem;
  appearance: none;
  background: #fff url("data:image/svg+xml,%3csvg xmlns='http://www.w3.org/2000/svg' width='4' height='5' viewBox='0 0 4 5'%3e%3cpath fill='%23343a40' d='M2 0L0 2h4zm0 5L0 3h4z'/%3e%3c/svg%3e") right .75rem center/8px 10px no-repeat;
  block-size: calc(1.5em + .75rem + 2px);
  color: rgb(118, 118, 118);
  font-size: 1rem;
  font-weight: 400;
  inline-size: 100%;
  line-height: 1.5;
  padding-block: .375rem;
  padding-inline: .75rem 1.75rem;
  vertical-align: middle;
}

.v-text-field .v-field--no-label input {
  text-align: end !important;
}
</style>
