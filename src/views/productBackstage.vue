<template>
  <div class="container">
    <div class="text-end mt-4">
      <button class="btn btn-primary" @click="openProductModal('new')">建立新的產品</button>
    </div>
    <table class="table mt-4">
      <thead>
        <tr>
          <th width="120">分類</th>
          <th>產品名稱</th>
          <th width="120">原價</th>
          <th width="120">售價</th>
          <th width="100">是否啟用</th>
          <th width="120">編輯</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="product in products" :key="product.id">
          <td>{{ product.category }}</td>
          <td>{{ product.title }}</td>
          <td class="text-end">{{ product['origin_price'] }}</td>
          <td class="text-end">{{ product.price }}</td>
          <td>
            <span class="text-success" v-if="product.is_enabled">啟用</span>
            <span v-else>未啟用</span>
          </td>
          <td>
            <div class="btn-group">
              <button
                type="button"
                class="btn btn-outline-primary btn-sm"
                @click="openProductModal('edit', product)"
              >
                編輯
              </button>
              <button
                type="button"
                class="btn btn-outline-danger btn-sm"
                @click="openProductModal('del', product)"
              >
                刪除
              </button>
            </div>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
  <!-- Modal -->
  <div
    id="productModal"
    ref="productModal"
    class="modal fade"
    tabindex="-1"
    aria-labelledby="productModalLabel"
    aria-hidden="true"
  >
    <div class="modal-dialog modal-xl">
      <div class="modal-content border-0">
        <div class="modal-header bg-dark text-white">
          <h5 id="productModalLabel" class="modal-title">
            <span>{{ state }}產品</span>
          </h5>
          <button
            type="button"
            class="btn-close"
            data-bs-dismiss="modal"
            aria-label="Close"
          ></button>
        </div>
        <div class="modal-body">
          <div class="row">
            <div class="col-sm-4">
              <div class="mb-2">
                <div class="mb-3">
                  <label for="imageUrl" class="form-label">輸入圖片網址</label>
                  <input
                    type="text"
                    class="form-control"
                    placeholder="請輸入圖片連結"
                    v-model="tempProduct.imageUrl"
                  />
                </div>
                <img class="img-fluid" :src="tempProduct.imageUrl" alt="" />
              </div>
              <h3 class="mb-3">多圖新增</h3>
              <template v-if="Array.isArray(tempProduct.imagesUrl)">
                <div v-for="(img, key) in tempProduct.imagesUrl" :key="key">
                  <label for="imageUrl" class="form-label">圖片{{ key + 1 }}網址</label>
                  <input
                    id="imageUrl"
                    type="text"
                    class="form-control mb-3"
                    placeholder="請輸入圖片連結"
                    v-model="tempProduct.imagesUrl[key]"
                  />
                  <img :src="img" alt="" class="img-fluid mb-3" />
                </div>
                <button
                  class="btn btn-outline-primary btn-sm d-block w-100"
                  v-if="
                    tempProduct.imagesUrl.length === 0 ||
                    tempProduct.imagesUrl[tempProduct.imagesUrl.length - 1]
                  "
                  @click="tempProduct.imagesUrl.push('')"
                >
                  新增圖片
                </button>
                <button
                  class="btn btn-outline-danger btn-sm d-block w-100"
                  v-else
                  @click="tempProduct.imagesUrl.pop()"
                >
                  刪除圖片
                </button>
              </template>

              <!-- <div v-if="!tempProduct.imageUrl">
                <button class="btn btn-outline-primary btn-sm d-block w-100">新增圖片</button>
              </div>
              <div v-else>
                v-else
                <button class="btn btn-outline-danger btn-sm d-block w-100">刪除圖片</button>
              </div> -->
            </div>
            <div class="col-sm-8">
              <div class="mb-3">
                <label for="title" class="form-label">標題</label>
                <input
                  id="title"
                  type="text"
                  class="form-control"
                  placeholder="請輸入標題"
                  v-model="tempProduct.title"
                />
              </div>

              <div class="row">
                <div class="mb-3 col-md-6">
                  <label for="category" class="form-label">分類</label>
                  <input
                    id="category"
                    type="text"
                    class="form-control"
                    placeholder="請輸入分類"
                    v-model="tempProduct.category"
                  />
                </div>
                <div class="mb-3 col-md-6">
                  <label for="unit" class="form-label">單位</label>
                  <input
                    id="unit"
                    type="text"
                    class="form-control"
                    placeholder="請輸入單位"
                    v-model="tempProduct.unit"
                  />
                </div>
              </div>

              <div class="row">
                <div class="mb-3 col-md-6">
                  <label for="origin_price" class="form-label">原價</label>
                  <input
                    id="origin_price"
                    type="number"
                    min="0"
                    class="form-control"
                    placeholder="請輸入原價"
                    v-model.number="tempProduct['origin_price']"
                  />
                </div>
                <div class="mb-3 col-md-6">
                  <label for="price" class="form-label">售價</label>
                  <input
                    id="price"
                    type="number"
                    min="0"
                    class="form-control"
                    placeholder="請輸入售價"
                    v-model.number="tempProduct.price"
                  />
                </div>
              </div>
              <hr />

              <div class="mb-3">
                <label for="description" class="form-label">產品描述</label>
                <textarea
                  id="description"
                  type="text"
                  class="form-control"
                  placeholder="請輸入產品描述"
                  v-model="tempProduct.description"
                >
                </textarea>
              </div>
              <div class="mb-3">
                <label for="content" class="form-label">說明內容</label>
                <textarea
                  id="content"
                  type="text"
                  class="form-control"
                  placeholder="請輸入說明內容"
                  v-model="tempProduct.content"
                >
                </textarea>
              </div>
              <div class="mb-3">
                <div class="form-check">
                  <input
                    id="is_enabled"
                    class="form-check-input"
                    type="checkbox"
                    :true-value="1"
                    :false-value="0"
                    v-model="tempProduct['is_enabled']"
                  />
                  <label class="form-check-label" for="is_enabled">是否啟用</label>
                </div>
              </div>
            </div>
          </div>
        </div>
        <div class="modal-footer">
          <button type="button" class="btn btn-outline-secondary" @click="noConfirmProduct()">
            取消
          </button>
          <button type="button" class="btn btn-primary" @click="confirmProduct(state)">確認</button>
        </div>
      </div>
    </div>
  </div>
  <div
    id="delProductModal"
    ref="delProductModal"
    class="modal fade"
    tabindex="-1"
    aria-labelledby="delProductModalLabel"
    aria-hidden="true"
  >
    <div class="modal-dialog">
      <div class="modal-content border-0">
        <div class="modal-header bg-danger text-white">
          <h5 id="delProductModalLabel" class="modal-title">
            <span>刪除產品</span>
          </h5>
          <button
            type="button"
            class="btn-close"
            data-bs-dismiss="modal"
            aria-label="Close"
          ></button>
        </div>
        <div class="modal-body">
          是否刪除{{ tempProduct.title }}
          <strong class="text-danger"></strong> 商品(刪除後將無法恢復)。
        </div>
        <div class="modal-footer">
          <button type="button" class="btn btn-outline-secondary" data-bs-dismiss="modal">
            取消
          </button>
          <button type="button" class="btn btn-danger" @click="confirmProductDel()">
            確認刪除
          </button>
        </div>
      </div>
    </div>
  </div>
  <!-- Modal -->
</template>

<script>
import * as bootstrap from 'bootstrap'
const { VITE_API, VITE_PATH } = import.meta.env
let productModalUse = null
let delProductModalUse = null

export default {
  data() {
    return {
      state: '新增',
      tempProduct: {
        imagesUrl: []
      },
      products: []
    }
  },
  methods: {
    checkAdmin() {
      this.axios
        .post(`${VITE_API}/api/user/check`)
        .then((res) => {
          this.getProducts()
        })
        .catch((err) => {
          alert(err.response.data.message)
          this.$router.push('/')
        })
    },
    getProducts() {
      this.axios
        .get(`${VITE_API}/api/${VITE_PATH}/admin/products`)
        .then((res) => {
          this.products = res.data.products
        })
        .catch((err) => {
          alert(err.response.data.message)
        })
    },
    openProductModal(state, product) {
      if (state === 'new') {
        this.state = '新增'
        this.tempProduct = {
          imagesUrl: []
        }
        productModalUse.show()
      } else if (state === 'edit') {
        this.state = '編輯'
        this.tempProduct = { ...product }
        productModalUse.show()
      } else if (state === 'del') {
        this.tempProduct = product
        delProductModalUse.show()
      }
    },
    confirmProduct(state) {
      if (state === '新增') {
        this.axios
          .post(`${VITE_API}/api/${VITE_PATH}/admin/product`, { data: this.tempProduct })
          .then((res) => {
            productModalUse.hide()
            this.getProducts()
          })
          .catch((err) => {
            alert(err.response.data.message)
          })
      } else if (state === '編輯') {
        this.axios
          .put(`${VITE_API}/api/${VITE_PATH}/admin/product/${this.tempProduct.id}`, {
            data: this.tempProduct
          })
          .then((res) => {
            productModalUse.hide()
            this.getProducts()
          })
          .catch((err) => {
            alert(err.response.data.message)
          })
      }
    },
    confirmProductDel() {
      this.axios
        .delete(`${VITE_API}/api/${VITE_PATH}/admin/product/${this.tempProduct.id}`)
        .then((res) => {
          delProductModalUse.hide()
          this.getProducts()
        })
        .catch((err) => {
          alert(err.response.data.message)
        })
    },
    noConfirmProduct() {
      this.getProducts()
      productModalUse.hide()
    }
  },
  mounted() {
    const token = document.cookie.replace(/(?:(?:^|.*;\s*)hexschool\s*=\s*([^;]*).*$)|^.*$/, '$1')
    this.axios.defaults.headers.common['Authorization'] = token

    productModalUse = new bootstrap.Modal(document.querySelector('#productModal'), {
      keyboard: false,
      backdrop: 'static'
    })
    delProductModalUse = new bootstrap.Modal(document.querySelector('#delProductModal'), {
      keyboard: false,
      backdrop: 'static'
    })

    this.checkAdmin()
  }
}
</script>
