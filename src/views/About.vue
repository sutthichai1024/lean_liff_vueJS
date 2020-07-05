<template>
  <!-- pure js style  -->
  <!-- <div class="about">
  <h1>This is an about page</h1>
  <p id="os"><b>OS:</b> </p>
  <p id="language"><b>Language:</b> </p>
  <p id="version"><b>Version:</b> </p>
  <p id="isInClient"><b>isInClient:</b> </p>
  <p id="accessToken"><b>AccessToken:</b> </p>
  <img id="pictureUrl">
  <p id="userId"><b>userId:</b> </p>
  <p id="displayName"><b>displayName:</b> </p>
  <p id="statusMessage"><b>statusMessage:</b> </p>
  <p id="decodedIDToken"><b>email:</b> </p>
  <p id="type"><b>type:</b> </p>
  <p id="viewType"><b>viewType:</b> </p>
  <p id="utouId"><b>utouId:</b> </p>
  <p id="roomId"><b>roomId:</b> </p>
  <p id="groupId"><b>groupId:</b> </p>
  <p id="friendship"><b>isFriendship:</b> </p>
  <p id="scanCode"><b>Code:</b> </p>
  <p id="isLoggedIn"><b>isLoggedIn:</b> </p>
  <p id="universalLink1"><b>Universal Link:</b> </p>
  <p id="universalLink2"><b>Universal Link with Query params:</b> </p>
  <p><a href="liff/path/?param=9">Link to Path</a></p>
    <button @click='getUserProfile'>get Data </button>
  </div> -->

  <!-- vue style -->
  <div>
    <h1>This is an about page</h1>
    <p id="os"><b>OS: {{ os }}</b> </p>
    <p id="language"><b>Language: {{ language }}</b> </p>
    <p id="version"><b>Version: {{ version }}</b> </p>
    <p id="isInClient"><b>isInClient: {{ isInClient }}</b> </p>
    <p id="accessToken"><b>AccessToken: {{ accessToken }}</b> </p>
    <img id="pictureUrl" :src='pictureUrl'>
    <p id="userId"><b>userId: {{ userId }}</b> </p>
    <p id="displayName"><b>displayName: {{ displayName }}</b> </p>
    <p id="statusMessage"><b>statusMessage: {{ statusMessage }}</b> </p>
    <p id="decodedIDToken"><b>email: {{ decodedIDToken }}</b> </p>
    <p id="type"><b>type: {{ type }}</b> </p>
    <p id="viewType"><b>viewType: {{ viewType }}</b> </p>
    <p id="utouId"><b>utouId: {{ utouId }}</b> </p>
    <p id="roomId"><b>roomId: {{ roomId }}</b> </p>
    <p id="groupId"><b>groupId: {{ groupId }}</b> </p>
    <p id="friendship"><b>isFriendship: {{ friendship }}</b> </p>
    <p id="scanCode"><b>Code: {{ scanCode }}</b> </p>
    <p id="isLoggedIn"><b>isLoggedIn: {{ isLoggedIn }}</b> </p>
    <p id="universalLink1"><b>Universal Link: {{ universalLink1 }}</b> </p>
    <p id="universalLink2"><b>Universal Link with Query params: {{ universalLink2 }}</b> </p>
    <p><a href="liff/path/?param=9">Link to Path</a></p>

      <button id="btnMsg" @click="sendMsg" v-if="isInClient">Send Message</button>
      <button id="btnShare" @click="shareMsg">Share Target Picker</button>
      <button @click="openWindow">Open Window</button>
      <button id="btnScanCode" @click="scanCode" v-if="isInClient">Scan Code</button>
      <button id="btnLogOut" @click="logOut" v-else>Log Out</button>
      <button id="btnClose" @click="closed" v-if="isInClient">Close</button>

  </div>
</template>
<script>
export default {
  data: () => ({
    os: '',
    language: '',
    version: '',
    isInClient: '',
    accessToken: '',
    pictureUrl: '',
    userId: '',
    displayName: '',
    statusMessage: '',
    decodedIDToken: '',
    type: '',
    viewType: '',
    utouId: '',
    roomId: '',
    groupId: '',
    friendship: '',
    scanCode: '',
    isLoggedIn: '',
    universalLink1: '',
    universalLink2: ''
  }),
  mounted () {
    this.$liff.ready.then(() => {
      this.isLoggedIn = this.$liff.isLoggedIn()
      if (this.$liff.isInClient()) {
        this.getEnvironment()
        this.getUserProfile()
        this.getContext()
        this.getFriendship()
        this.createUniversalLink()
      } else {
        if (this.$liff.isLoggedIn()) {
          this.getEnvironment()
          this.getUserProfile()
          this.getContext()
          this.getFriendship()
        } else {
          this.loginLine()
        }
      }
    })
  },
  methods: {
    async getUserProfile () {
      const profile = await this.$liff.getProfile()
      this.pictureUrl = profile.pictureUrl
      this.userId = profile.userId
      this.statusMessage = profile.statusMessage
      this.displayName = profile.displayName
      this.decodedIDToken = this.$liff.getDecodedIDToken().email
    },
    getEnvironment () {
      this.os = this.$liff.getOS()
      this.language = this.$liff.getLanguage()
      this.version = this.$liff.getVersion()
      this.accessToken = this.$liff.getAccessToken()
      this.isInClient = this.$liff.isInClient()
    },
    getContext () {
      this.type = this.$liff.getContext().type
      this.viewType = this.$liff.getContext().viewType
      this.utouId = this.$liff.getContext().utouId
      this.roomId = this.$liff.getContext().roomId
      this.groupId = this.$liff.getContext().groupId
    },
    async sendMsg () {
      if (this.$liff.getContext().type !== 'none' && this.$liff.getContext().type !== 'external') {
        await this.$liff.sendMessages([
          {
            'type': 'sticker',  // eslint-disable-line
            'stickerId': 1,   // eslint-disable-line
            'packageId': 1  // eslint-disable-line
          }
        ])
        alert('Message sent ')
      }
    },
    async shareMsg () {
      await this.$liff.shareTargetPicker([
        {
          type: 'text',
          text: 'this message was sent by ShareTargetPicker'
        }
      ])
    },
    openWindow () {
      this.$liff.openWindow({
        url: 'https://discord.com/',
        external: true
      })
    },
    async getScanCode () {
      const result = await this.$liff.scanCode()
      this.scanCode = result.value
    },
    logOut () {
      this.$liff.logout()
      window.location.reload()
    },
    closed () {
      this.$liff.closeWindow()
    },
    async getFriendship () {
      const friend = await this.$liff.getFriendship()
      this.friendship = friend.friendFlag
      if (!this.friendship) {
        if (confirm('คุณยังไม่ได้เพิ่ม bot เป็นเพื่อน จะเพิ่มเลยไหม ?')) {
          window.location = 'https://line.me/R/ti/p/@857gqbzn'
        }
      }
    },
    createUniversalLink () {
      const link1 = this.$liff.permanentLink.createUrl()
      this.universalLink1 = link1

      this.$liff.permanentLink.setExtraQueryParams('param=5&id=123')
      const link2 = this.$liff.permanentLink.createUrl()
      this.universalLink2 = link2
    },
    loginLine () {
      this.$liff.login({ redirectUri: window.location.href })
    }
  }
}

</script>
