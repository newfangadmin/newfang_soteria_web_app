<template>
  <el-row class="didContainer" v-loading="floading" element-loading-background="rgba(240, 240, 240, 0.95)" element-loading-text="Decrypting Keystore...">
    <el-col :span="12" :offset="6">
      <div class="" v-if="!loggedin">
        <div class="headerText">Enter an ethereum keystore to get started</div>
        <el-button type="primary" class="getKeystoreBtn" @click="chooseKeystore()">Load Keystore</el-button>
        <input type="file" ref="fileSelect" style="display: none" v-on:change="handleKeystoreSelect()">
      </div>
      <div v-else>
        <div class="headerText">Enter a file DID to verify permission and download</div>
        <el-input placeholder="Enter File DID" v-model="did" class="didInput"></el-input>
        <div class="errorContainer">{{ errorMsg }}</div>
        <div class="btnContainer">
          <el-button class="secondaryBtn">Verify Permission</el-button>
          <el-button type="primary" icon="el-icon-download" class="downloadBtn">Download File</el-button>
        </div>
      </div>
    </el-col>
  </el-row>
</template>

<script>
import { ethers } from 'ethers'
const Newfang = window.newfang.default
console.log(Newfang)

export default {
  name: 'did',
  props: {
  },
  data() {
    return {
      did: '',
      errorMsg: '',
      keystore: '',
      floading: false,
      loggedin: false
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
        this.floading = true
        this.checkPassword(value)
      }).catch(() => {
      })
    },

    async checkPassword(pass) {
      let wallet = null
      try {
        wallet = await ethers.Wallet.fromEncryptedJson(this.keystore, pass)
        console.log(wallet)
        this.floading = false
        localStorage.setItem('pvt_key', wallet.privateKey)
        localStorage.setItem('addr', wallet.address)
        this.loggedin = true
        this.$root.$emit("loggedin")
      }
      catch(error) {
        this.floading = false
        this.$message.error('Invalid password or keystore. Try again')
        this.getPassword()
      }
    }
  },
  mounted() {
    if(localStorage.getItem('pvt_key')) {
      this.loggedin = true
    }
    this.$root.$on("loggedout", () => {
      this.keystore = ''
      this.loggedin = false
    })
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

.didContainer {
  padding-top: 30px;
  height: calc(100vh - 50px);
}

.headerText {
  font-size: 40px;
  font-weight: 200;
  padding: 30px;
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

.didInput {
  height: 48px;
  font-size: 12px;
}
</style>
