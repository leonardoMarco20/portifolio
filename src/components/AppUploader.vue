<template>
  <div ref="preview" class="app-uploader cursor-pointer relative-position" :style="uploaderStyles" @click="uploadClick">
    <div v-if="showPreview" class="app-uploader__preview">
      <app-avatar  :img="previewImage" size="80px" />
      <div class="absolute-center app-uploader__preview__icon-box bg-primary text-primary" />
      <div class="absolute-center app-uploader__preview__icon flex items-center justify-center text-bolder">
        <q-icon color="white" name="add_a_photo" size="24px" />
        <div class="text-bold text-caption text-white">Alterar</div>
      </div>
    </div>
    <app-avatar v-else color="white" font-size="52px" icon="add_a_photo" size="80px" text-color="primary" />  
  </div>
  <q-uploader ref="uploader" auto-upload class="hidden z-max" :factory="uploadFactory" hide-upload-btn />
</template>

<script>
import { storage } from 'boot/firebase'
import {mapActions, mapGetters} from 'vuex'
import AppAvatar from 'src/components/AppAvatar.vue'

export default {
  name: 'AppUploader',

  components: { AppAvatar },

  data() {
    return {
      uploadedImage: '',
      name: 'Léo'
    }
  },

  props: {
    image: {
      type: String,
      default: ''
    },

    theme: {
      type: String,
      default: ''
    }
  },

  computed: {
    ...mapGetters('users', ['loggedUser']),

    hasImage () {
      return !!this.image
    },
    
    showPreview () {
      return !!this.uploadedImage || this.hasImage
    },

    previewImage () {
      return this.uploadedImage || this.image
    },

    uploaderStyles () {
      return [
        `border-color: ${this.theme.value ||  "#27e8a7"}`
      ]
    }
  },

  methods: {
    uploadFactory ([File]) {
      const storageRef = !!this.loggedUser?.uid 
        ? storage.ref(`profile-photos-${this.loggedUser.uid}`)
        : storage.ref(`profile-photos-register`)

      
      storageRef.child('profile-image').put(File).then(snapshot =>{
        storageRef.child('profile-image').getDownloadURL().then(url =>{
          this.uploadedImage = url
          this.$emit('uploaded-image', this.uploadedImage)
        })
      })
    },

    uploadClick () {
      const theFuckingQuasarUploaderBtn = this.$refs.
        uploader.$el.children[0].children[0].children[1].children[1].children[1]
      
      theFuckingQuasarUploaderBtn.click()
    }
  }
}
</script>

<style lang="scss">
  .app-uploader {
    border-radius: 50%;
    border: solid 6px;
    
    &__preview {
      &:hover {
        .app-uploader__preview__icon-box {
          opacity: .7;
          transition: .5s;
        }

        .app-uploader__preview__icon {
          opacity: .9;
          transition: .5s;
        }
      }

      &__icon-box {
        border-radius: 50%;
        width: 90px;
        height: 90px;
        opacity: 0;

      }
      
      &__icon {
        opacity: 0;
      }
    }  
  }
</style>