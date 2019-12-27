<template>
  <el-row class="didContainer">
    <el-col :span="12" :offset="6">
      <el-button type="primary" class="getKeystoreBtn" @click="chooseKeystore()">Get Keystore</el-button>
      <input type="file" ref="fileSelect" style="display: none" v-on:change="handleKeystoreSelect()">
      <el-input placeholder="Enter File DID" v-model="did"></el-input>
      <div class="errorContainer">{{ errorMsg }}</div>
      <div class="btnContainer">
        <el-button class="secondaryBtn">Verify</el-button>
        <el-button type="primary" icon="el-icon-download" class="downloadBtn">Download</el-button>
      </div>
    </el-col>
  </el-row>
</template>

<script>
import { ethers } from 'ethers'

export default {
  name: 'did',
  props: {
  },
  data() {
    return {
      did: '',
      errorMsg: '',
      keystore: ''
    }
  },
  methods: {
    chooseKeystore() {
      this.$refs.fileSelect.click()
    },

    handleKeystoreSelect () {
      const file = this.$refs.fileSelect.files[0]
      if (file !== undefined) {
        var reader = new FileReader();
        reader.readAsText(file, "utf-8")
        const self = this
        reader.onload = function (evt) {
          self.keystore = evt.target.result
          self.getPassword()
        }
      }
    },

    getPassword () {
      this.$prompt('Please enter your password', 'Enter Password', {
        confirmButtonText: 'Confirm',
        cancelButtonText: 'Cancel',
        inputType: 'password'
      }).then(({ value }) => {
        this.loading = true
        this.checkPassword(value)
      }).catch(() => {
      })
    },

    async checkPassword(pass) {
      let wallet = await ethers.Wallet.fromEncryptedJson(this.keystore, pass)
      // eslint-disable-next-line no-console
      console.log("w", wallet)
    }
  },
  mounted() {

  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

.didContainer {
  margin: 30px;
}

.errorContainer {
  font-size: 10px;
  font-weight: 600;
  color: rgb(219, 65, 65);
  text-align: left;
  padding-left: 2px;
  height: 20px;
  line-height: 20px;
}

.btnContainer {
  text-align: right;
}
</style>
