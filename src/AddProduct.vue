<template>
  <div id="add-product">
    <div class="header">
      <b-container>
        <h1>เพิ่มสินค้า</h1>
      </b-container>
    </div>
    <div class="wrapper-container">
      <b-container>
        <div class="product-detail">
          <!--- Start add product form --->
          <div class="add-product-form" v-if="!previewMode">
            <form @submit.prevent="onSubmit">
              <b-row>
                <b-col md="12">
                  <b-form-group>
                    <b-form-input
                      v-model="form.title"
                      placeholder="ชื่อสินค้า..."
                      class="product-title"
                      :state="isValidate('title')"
                    ></b-form-input>
                    <b-form-invalid-feedback
                      v-if="errors.title"
                      :state="isValidate('title')"
                    >{{ errors.title }}</b-form-invalid-feedback>
                  </b-form-group>

                  <div class="product-meta">
                    <b-row>
                      <b-col md="6">
                        <b-form-group id="input-group-1" label="หมวดหมู่:" label-for="category">
                          <b-form-select
                            v-model="form.category"
                            :options="categories"
                            id="category"
                            :state="isValidate('category')"
                          ></b-form-select>
                          <b-form-invalid-feedback
                            v-if="errors.category"
                            :state="isValidate('category')"
                          >{{ errors.category }}</b-form-invalid-feedback>
                        </b-form-group>
                      </b-col>
                      <b-col md="6">
                        <b-form-group id="input-group-1" label="จำนวน:" label-for="quantity">
                          <b-form-input
                            type="number"
                            v-model="form.quantity"
                            id="quantity"
                            min="1"
                            :state="isValidate('quantity')"
                          ></b-form-input>
                          <b-form-invalid-feedback
                            v-if="errors.quantity"
                            :state="isValidate('quantity')"
                          >{{ errors.quantity }}</b-form-invalid-feedback>
                        </b-form-group>
                      </b-col>
                    </b-row>
                    <b-row>
                      <b-col>
                        <b-form-group
                          id="input-group-1"
                          label="สิ่งที่อยากได้:"
                          label-for="wantItem"
                        >
                          <b-form-input
                            type="text"
                            v-model="form.wantItem"
                            id="wantItem"
                            :state="isValidate('wantItem')"
                          ></b-form-input>
                          <b-form-invalid-feedback
                            v-if="errors.wantItem"
                            :state="isValidate('wantItem')"
                          >{{ errors.wantItem }}</b-form-invalid-feedback>
                        </b-form-group>
                      </b-col>
                    </b-row>
                  </div>
                </b-col>
              </b-row>
              <b-row>
                <b-col md="12">
                  <h3>รายละเอียดของสินค้า</h3>
                  <b-form-group>
                    <b-form-invalid-feedback
                      v-if="errors.detail"
                      :state="isValidate('detail')"
                    >{{ errors.detail }}</b-form-invalid-feedback>
                    <vue-editor v-model="form.detail" :editorToolbar="editor.customToolbar"></vue-editor>
                  </b-form-group>
                </b-col>
              </b-row>
              <b-row>
                <b-col md="12">
                  <h3>รูปภาพสินค้า</h3>
                  <b-form-group>
                    <vue-dropzone
                      class="image-upload"
                      id="image-upload"
                      :options="imageUploadOption"
                      @vdropzone-success="imageUploadSuccess"
                      @vdropzone-removed-file="imageUploadRemove"
                    ></vue-dropzone>
                  </b-form-group>
                </b-col>
              </b-row>
              <b-row>
                <b-col md="12" class="text-right">
                  <span class="error" v-if="formError">กรุณากรอกรายละเอียดให้ครบ</span>
                  <b-button type="submit" variant="primary">เพิ่มสินค้า</b-button>
                </b-col>
              </b-row>
            </form>
          </div>
          <!--- end add product form --->
          <!--- Start add product preview form --->
          <div class="product-form-review" v-else>
            <div class="info">โปรดตรวจสอบความถูกต้องของข้อมูลด้านล่างนี้...</div>
            <product-overview
              :title="form.title"
              :category="form.category"
              :quantity="form.quantity"
              :detail="form.detail"
              :wantItem="form.wantItem"
              :images="media_images"
            ></product-overview>
            <!--- end add product preview form --->
            <!--- start add product preview control --->
            <b-row>
              <b-col cols="6">
                <b-button @click="onClickBack">กลับไปแก้ไข</b-button>
              </b-col>
              <b-col cols="6" class="text-right">
                <b-button @click="onConfirmSubmit" variant="primary">ยืนยัน</b-button>
              </b-col>
            </b-row>
          </div>
          <!--- end add product preview control --->
        </div>
      </b-container>
    </div>
  </div>
</template>

<script>
import { VueEditor } from 'vue2-editor'
import vue2Dropzone from 'vue2-dropzone'
import 'vue2-dropzone/dist/vue2Dropzone.min.css'
import { mapGetters } from 'vuex'

import ProductOverview from '@/components/ProductOverview.vue'

export default {
  components: {
    VueEditor,
    ProductOverview,
    vueDropzone: vue2Dropzone
  },
  data () {
    return {
      form: {
        title: '',
        category: null,
        quantity: 1,
        wantItem: '',
        detail: '',
        images: []
      },
      media_images: [],
      images_uuid: [],
      categories: [],
      formError: false,
      editor: {
        customToolbar: [
          ['bold', 'italic', 'underline', 'strike', 'link'],
          [{ header: 1 }, { header: 2 }],
          [{ list: 'ordered' }, { list: 'bullet' }],
          ['blockquote', 'align', 'direction', 'code-block'],
          ['image', 'video']
        ]
      },
      imageUploadOption: {
        url: process.env.VUE_APP_API_ROOT + '/upload/product/',
        acceptedFiles: 'image/*',
        thumbnailMethod: 'contain',
        addRemoveLinks: true,
        thumbnailHeight: '300',
        thumbnailWidth: null,
        dictDefaultMessage: 'คลิกหรือลากไฟล์มาที่นี่เพื่ออัพโหลดรูปภาพ',
        dictCancelUpload: 'หยุดอัพโหลด',
        dictCancelUploadConfirmation:
          'คุณแน่ใจแล้วใช่หรือไม่ที่จะหยุดอัพโหลดรูปภาพนี้',
        dictRemoveFile: 'ลบรูปภาพ'
      },
      previewMode: false,
      errors: {}
    }
  },
  created () {
    this.getCategories()
    this.imageUploadOption['headers'] = {
      Authorization: `Bearer ${this.getUserToken}`
    }
  },
  methods: {
    onSubmit () {
      if (this.form.title === '') {
        this.errors['title'] = 'กรุณากรอกข้อมูล'
      }
      if (this.category == null) {
        this.errors['category'] = 'กรุณากรอกข้อมูล'
      }
      if (this.wantItem == null) {
        this.errors['wantItem'] = 'กรุณากรอกข้อมูล'
      }
      if (this.detail == null) {
        this.errors['detail'] = 'กรุณากรอกข้อมูล'
      }
      if (
        this.form.title !== '' &&
        this.category !== null &&
        this.wantItem !== null &&
        this.detail !== null
      ) {
        this.previewMode = true
        this.formError = false
      } else {
        this.previewMode = false
        this.formError = true
      }
    },
    isValidate (field) {
      if (Object.keys(this.errors).length !== 0) {
        if (this.errors[field]) {
          return false
        } else {
          return true
        }
      }
      return null
    },
    onClickBack () {
      this.previewMode = false
    },
    onConfirmSubmit () {
      this.submitProduct()
      // this.$router.push("/");
    },
    async getCategories () {
      try {
        let response = await this.$axios.get('/category/')
        let categories = [
          {
            text: 'เลือกหมวดหมู่สินค้า',
            value: null
          }
        ]

        response.data.results.forEach(category => {
          categories.push({
            text: category.name,
            value: category.id
          })
        })

        this.categories = categories
      } catch (error) {
        console.log(error)
      }
    },
    async submitProduct () {
      try {
        let response = await this.$axios.post('/product/', {
          name: this.form.title,
          category_id: this.form.category,
          detail: this.form.detail,
          quantity: this.form.quantity,
          want_product: this.form.wantItem,
          images_id: this.form.images
        })

        console.log(response.data)
        this.$router.push({
          name: 'product',
          params: { id: response.data.id }
        })
      } catch (error) {
        console.log(error.response)
      }
    },
    imageUploadSuccess (file, response) {
      this.form.images.push(response.id)
      this.images_uuid.push({
        file_uid: file.upload.uuid,
        id: response.id
      })
      this.media_images.push({
        picture_path: response.picture_path
      })
      console.log(response)
    },
    imageUploadRemove (file, error, xhr) {
      if (!this.previewMode) {
        let imageUUIDindex = this.images_uuid.findIndex(
          x => x.file_uid === file.upload.uuid
        )
        let imageUploadId = this.form.images.indexOf(
          this.images_uuid[imageUUIDindex].id
        )
        this.form.images.splice(imageUploadId, 1)
        this.images_uuid.splice(imageUUIDindex, 1)
        console.log(file)
      }
    }
  },
  computed: {
    ...mapGetters(['getUserToken'])
  }
}
</script>

<style lang="scss" scoped>
@import "@/assets/custom.scss";

.add-product-form {
  border: 2px dashed $primary-light-color;
  padding: 20px;
}

.add-product-form .product-title {
  font-size: 2em;
  color: $primary-color;
  font-weight: 500;
}

.add-product-form .product-title::placeholder {
  color: $primary-light-color;
}

.add-product-form h3 {
  font-size: 1.6em;
  color: $primary-color;
}

.product-form-review .info {
  background: $primary-light-color2;
  padding: 10px;
  font-weight: 600;
  margin-bottom: 15px;
}

.image-upload {
  font-family: "Athiti", sans-serif;
  font-weight: 600;
  font-size: 1.1em;
}
</style>

<style lang="scss">
@import "@/assets/custom.scss";

.image-upload .dz-preview {
  box-shadow: 0px 4px 9px rgba($color: #000000, $alpha: 0.6);
  border-radius: 5px;
  overflow: hidden;
}

.image-upload > .dz-preview .dz-details {
  background-color: rgba($color: $primary-light-color, $alpha: 0.6);
}
</style>
