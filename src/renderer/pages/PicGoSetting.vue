<template>
  <div id="picgo-setting">
    <div class="view-title">
      PicGo设置 - <i class="el-icon-document" @click="goConfigPage"></i>
    </div>
    <el-row class="setting-list">
      <el-col :span="16" :offset="4">
        <el-row>
        <el-form
          label-width="160px"
          label-position="right"
          size="small"
        >
          <el-form-item
            label="打开配置文件"
          >
            <el-button type="primary" round size="mini" @click="openFile('data.json')">点击打开</el-button>
          </el-form-item>
          <el-form-item
            label="设置日志文件"
          >
            <el-button type="primary" round size="mini" @click="openLogSetting">点击设置</el-button>
          </el-form-item>
          <el-form-item
            label="修改快捷键"
          >
            <el-button type="primary" round size="mini" @click="goShortCutPage">点击设置</el-button>
          </el-form-item>
          <el-form-item
            label="自定义链接格式"
          >
            <el-button type="primary" round size="mini" @click="customLinkVisible = true">点击设置</el-button>
          </el-form-item>
          <el-form-item
            label="设置代理和镜像地址"
          >
            <el-button type="primary" round size="mini" @click="proxyVisible = true">点击设置</el-button>
          </el-form-item>
          <el-form-item
            label="设置Server"
          >
            <el-button type="primary" round size="mini" @click="serverVisible = true">点击设置</el-button>
          </el-form-item>
          <el-form-item
            label="检查更新"
          >
            <el-button type="primary" round size="mini" @click="checkUpdate">点击检查</el-button>
          </el-form-item>
          <el-form-item
            label="打开更新助手"
          >
            <el-switch
              v-model="form.updateHelper"
              active-text="开"
              inactive-text="关"
              @change="updateHelperChange"
            ></el-switch>
          </el-form-item>
          <el-form-item
            v-show="form.updateHelper"
            label="接受Beta版本更新"
          >
            <el-switch
              v-model="form.checkBetaUpdate"
              active-text="开"
              inactive-text="关"
              @change="checkBetaUpdateChange"
            ></el-switch>
          </el-form-item>
          <el-form-item
            label="开机自启"
          >
            <el-switch
              v-model="form.autoStart"
              active-text="开"
              inactive-text="关"
              @change="handleAutoStartChange"
            ></el-switch>
          </el-form-item>
          <el-form-item
            label="上传前重命名"
          >
            <el-switch
              v-model="form.rename"
              active-text="开"
              inactive-text="关"
              @change="handleRename"
            ></el-switch>
          </el-form-item>
          <el-form-item
            label="时间戳重命名"
          >
            <el-switch
              v-model="form.autoRename"
              active-text="开"
              inactive-text="关"
              @change="handleAutoRename"
            ></el-switch>
          </el-form-item>
          <el-form-item
            label="开启上传提示"
          >
            <el-switch
              v-model="form.uploadNotification"
              active-text="开"
              inactive-text="关"
              @change="handleUploadNotification"
            ></el-switch>
          </el-form-item>
          <el-form-item
            v-if="os !== 'darwin'"
            label="Mini窗口置顶"
          >
            <el-switch
              v-model="form.miniWindowOntop"
              active-text="开"
              inactive-text="关"
              @change="handleMiniWindowOntop"
            ></el-switch>
          </el-form-item>
          <el-form-item
            label="上传后自动复制URL"
          >
            <el-switch
              v-model="form.autoCopyUrl"
              active-text="开"
              inactive-text="关"
              @change="handleAutoCopyUrl"
            ></el-switch>
          </el-form-item>
          <el-form-item
            label="选择显示的图床"
          >
            <el-checkbox-group
              v-model="form.showPicBedList"
              @change="handleShowPicBedListChange"
            >
              <el-checkbox
                v-for="item in picBed"
                :label="item.name"
                :key="item.name"
              ></el-checkbox>
            </el-checkbox-group>
          </el-form-item>
        </el-form>
        </el-row>
      </el-col>
    </el-row>
    <el-dialog
      title="自定义链接格式"
      :visible.sync="customLinkVisible"
      :modal-append-to-body="false"
    >
      <el-form
        label-position="top"
        :model="customLink"
        ref="customLink"
        :rules="rules"
        size="small"
      >
        <el-form-item
          prop="value"
        >
          <div class="custom-title">
            用占位符 <b>$url</b> 来表示url的位置
          </div>
          <div class="custom-title">
            用占位符 <b>$fileName</b> 来表示文件名的位置
          </div>
          <el-input
            class="align-center"
            v-model="customLink.value"
            :autofocus="true"
          ></el-input>
        </el-form-item>
      </el-form>
      <div>
        如[$fileName]($url)
      </div>
      <span slot="footer">
        <el-button @click="cancelCustomLink" round>取消</el-button>
        <el-button type="primary" @click="confirmCustomLink" round>确定</el-button>
      </span>
    </el-dialog>
    <el-dialog
      title="设置代理和镜像地址"
      :visible.sync="proxyVisible"
      :modal-append-to-body="false"
      width="70%"
    >
      <el-form
        label-position="right"
        :model="customLink"
        ref="customLink"
        :rules="rules"
        label-width="120px"
      >
        <el-form-item
          label="上传代理"
        >
          <el-input
            v-model="proxy"
            :autofocus="true"
            placeholder="例如：http://127.0.0.1:1080"
          ></el-input>
        </el-form-item>
        <el-form-item
          label="插件安装代理"
        >
          <el-input
            v-model="npmProxy"
            :autofocus="true"
            placeholder="例如：http://127.0.0.1:1080"
          ></el-input>
        </el-form-item>
        <el-form-item
          label="插件镜像地址"
        >
          <el-input
            v-model="npmRegistry"
            :autofocus="true"
            placeholder="例如：https://registry.npm.taobao.org/"
          ></el-input>
        </el-form-item>
      </el-form>
      <span slot="footer">
        <el-button @click="cancelProxy" round>取消</el-button>
        <el-button type="primary" @click="confirmProxy" round>确定</el-button>
      </span>
    </el-dialog>
    <el-dialog
      title="检查更新"
      :visible.sync="checkUpdateVisible"
      :modal-append-to-body="false"
    >
      <div>
        当前版本：{{ version }}
      </div>
      <div>
        最新版本：{{ latestVersion ? latestVersion : '正在获取中...' }}
      </div>
      <div v-if="needUpdate">
        PicGo更新啦，请点击确定打开下载页面
      </div>
      <span slot="footer">
        <el-button @click="cancelCheckVersion" round>取消</el-button>
        <el-button type="primary" @click="confirmCheckVersion" round>确定</el-button>
      </span>
    </el-dialog>
    <el-dialog
      title="设置日志文件"
      :visible.sync="logFileVisible"
      :modal-append-to-body="false"
    >
      <el-form
        label-position="right"
        label-width="100px"
      >
        <el-form-item
          label="日志文件"
        >
          <el-button type="primary" round size="mini" @click="openFile('picgo.log')">点击打开</el-button>
        </el-form-item>
        <el-form-item
          label="日志记录等级"
        >
          <el-select
            v-model="form.logLevel"
            multiple
            collapse-tags
          >
            <el-option
              v-for="(value, key) of logLevel"
              :key="key"
              :label="value"
              :value="key"
              :disabled="handleLevelDisabled(key)"
            ></el-option>
          </el-select>
        </el-form-item>
      </el-form>
      <span slot="footer">
        <el-button @click="cancelLogLevelSetting" round>取消</el-button>
        <el-button type="primary" @click="confirmLogLevelSetting" round>确定</el-button>
      </span>
    </el-dialog>
    <el-dialog
      class="server-dialog"
      width="60%"
      title="设置PicGo-Server"
      :visible.sync="serverVisible"
      :modal-append-to-body="false"
    >
      <div class="notice-text">
        如果你不知道Server的作用，请阅读文档，或者不用修改配置。
      </div>
      <el-form
        label-position="right"
        label-width="120px"
      >
        <el-form-item
          label="是否开启Server"
        >
          <el-switch
            v-model="server.enable"
            active-text="开"
            inactive-text="关"
          ></el-switch>
        </el-form-item>
        <template v-if="server.enable">
          <el-form-item
            label="设置监听地址"
          >
            <el-input
              type="input"
              v-model="server.host"
              placeholder="推荐默认地址:127.0.0.1"
            ></el-input>
          </el-form-item>
          <el-form-item
            label="设置监听端口"
          >
            <el-input
              type="number"
              v-model="server.port"
              placeholder="推荐默认端口:36677"
            ></el-input>
          </el-form-item>
        </template>
      </el-form>
      <span slot="footer">
        <el-button @click="cancelServerSetting" round>取消</el-button>
        <el-button type="primary" @click="confirmServerSetting" round>确定</el-button>
      </span>
    </el-dialog>
  </div>
</template>
<script lang="ts">
import keyDetect from '@/utils/key-binding'
import pkg from 'root/package.json'
import { IConfig } from 'picgo/dist/src/types/index'
import { PICGO_OPEN_FILE } from '#/events/constants'
import {
  ipcRenderer,
  remote
} from 'electron'
import { Component, Vue } from 'vue-property-decorator'
// import db from '#/datastore'
const releaseUrl = 'https://api.github.com/repos/Molunerfinn/PicGo/releases/latest'
const releaseUrlBackup = 'https://cdn.jsdelivr.net/gh/Molunerfinn/PicGo@latest/package.json'
const downloadUrl = 'https://github.com/Molunerfinn/PicGo/releases/latest'
const customLinkRule = (rule: string, value: string, callback: (arg0?: Error) => void) => {
  if (!/\$url/.test(value)) {
    return callback(new Error('必须含有$url'))
  } else {
    return callback()
  }
}

@Component({
  name: 'picgo-setting'
})
export default class extends Vue {
  form: ISettingForm = {
    updateHelper: false,
    showPicBedList: [],
    autoStart: false,
    rename: false,
    autoRename: false,
    uploadNotification: false,
    miniWindowOntop: false,
    logLevel: ['all'],
    autoCopyUrl: true,
    checkBetaUpdate: true
  }
  picBed: IPicBedType[] = []
  logFileVisible = false
  keyBindingVisible = false
  customLinkVisible = false
  checkUpdateVisible = false
  serverVisible = false
  proxyVisible = false
  customLink = {
    value: '$url'
  }
  shortKey: IShortKeyMap = {
    upload: ''
  }
  proxy = ''
  npmRegistry = ''
  npmProxy = ''
  rules = {
    value: [
      { validator: customLinkRule, trigger: 'blur' }
    ]
  }
  logLevel = {
    all: '全部-All',
    success: '成功-Success',
    error: '错误-Error',
    info: '普通-Info',
    warn: '提醒-Warn',
    none: '不记录日志-None'
  }
  server = {
    port: 36677,
    host: '127.0.0.1',
    enable: true
  }
  version = pkg.version
  latestVersion = ''
  os = ''

  get needUpdate () {
    if (this.latestVersion) {
      return this.compareVersion2Update(this.version, this.latestVersion)
    } else {
      return false
    }
  }
  created () {
    this.os = process.platform
    ipcRenderer.send('getPicBeds')
    ipcRenderer.on('getPicBeds', this.getPicBeds)
    this.initData()
  }
  async initData () {
    const config = (await this.getConfig<IConfig>())!
    if (config !== undefined) {
      const settings = config.settings || {}
      const picBed = config.picBed
      this.form.updateHelper = settings.showUpdateTip || false
      this.form.autoStart = settings.autoStart || false
      this.form.rename = settings.rename || false
      this.form.autoRename = settings.autoRename || false
      this.form.uploadNotification = settings.uploadNotification || false
      this.form.miniWindowOntop = settings.miniWindowOntop || false
      this.form.logLevel = this.initLogLevel(settings.logLevel || [])
      this.form.autoCopyUrl = settings.autoCopy === undefined ? true : settings.autoCopy
      this.form.checkBetaUpdate = settings.checkBetaUpdate === undefined ? true : settings.checkBetaUpdate

      this.customLink.value = settings.customLink || '$url'
      this.shortKey.upload = settings.shortKey.upload
      this.proxy = picBed.proxy || ''
      this.npmRegistry = settings.registry || ''
      this.npmProxy = settings.proxy || ''
      this.server = settings.server || {
        port: 36677,
        host: '127.0.0.1',
        enable: true
      }
    }
  }

  initLogLevel (logLevel: string | string[]) {
    if (!Array.isArray(logLevel)) {
      if (logLevel && logLevel.length > 0) {
        logLevel = [logLevel]
      } else {
        logLevel = ['all']
      }
    }
    return logLevel
  }

  getPicBeds (event: Event, picBeds: IPicBedType[]) {
    this.picBed = picBeds
    this.form.showPicBedList = this.picBed.map(item => {
      if (item.visible) {
        return item.name
      }
    }) as string[]
  }
  openFile (file: string) {
    ipcRenderer.send(PICGO_OPEN_FILE, file)
  }
  openLogSetting () {
    this.logFileVisible = true
  }
  keyDetect (type: string, event: KeyboardEvent) {
    this.shortKey[type] = keyDetect(event).join('+')
  }
  async cancelCustomLink () {
    this.customLinkVisible = false
    this.customLink.value = await this.getConfig<string>('settings.customLink') || '$url'
  }
  confirmCustomLink () {
    // @ts-ignore
    this.$refs.customLink.validate((valid: boolean) => {
      if (valid) {
        this.saveConfig('settings.customLink', this.customLink.value)
        this.customLinkVisible = false
        ipcRenderer.send('updateCustomLink')
      } else {
        return false
      }
    })
  }
  async cancelProxy () {
    this.proxyVisible = false
    this.proxy = await this.getConfig<string>('picBed.proxy') || ''
  }
  confirmProxy () {
    this.proxyVisible = false
    this.saveConfig({
      'picBed.proxy': this.proxy,
      'settings.proxy': this.npmProxy,
      'settings.registry': this.npmRegistry
    })
    const successNotification = new Notification('设置代理', {
      body: '设置成功'
    })
    successNotification.onclick = () => {
      return true
    }
  }
  updateHelperChange (val: boolean) {
    this.saveConfig('settings.showUpdateTip', val)
  }
  checkBetaUpdateChange (val: boolean) {
    this.saveConfig('settings.checkBetaUpdate', val)
  }
  handleShowPicBedListChange (val: string[]) {
    const list = this.picBed.map(item => {
      if (!val.includes(item.name)) {
        item.visible = false
      } else {
        item.visible = true
      }
      return item
    })
    this.saveConfig({
      'picBed.list': list
    })
    ipcRenderer.send('getPicBeds')
  }
  handleAutoStartChange (val: boolean) {
    this.saveConfig('settings.autoStart', val)
    ipcRenderer.send('autoStart', val)
  }
  handleRename (val: boolean) {
    this.saveConfig({
      'settings.rename': val
    })
  }
  handleAutoRename (val: boolean) {
    this.saveConfig({
      'settings.autoRename': val
    })
  }
  compareVersion2Update (current: string, latest: string) {
    const currentVersion = current.split('.').map(item => parseInt(item))
    const latestVersion = latest.split('.').map(item => parseInt(item))

    for (let i = 0; i < 3; i++) {
      if (currentVersion[i] < latestVersion[i]) {
        return true
      }
      if (currentVersion[i] > latestVersion[i]) {
        return false
      }
    }
    return false
  }
  checkUpdate () {
    this.checkUpdateVisible = true
    this.$http.get(releaseUrl)
      .then(res => {
        this.latestVersion = res.data.name
      }).catch(async () => {
        this.$http.get(releaseUrlBackup)
          .then(res => {
            this.latestVersion = res.data.version
          }).catch(() => {
            this.latestVersion = '网络错误暂时无法获取'
          })
      })
  }
  confirmCheckVersion () {
    if (this.needUpdate) {
      remote.shell.openExternal(downloadUrl)
    }
    this.checkUpdateVisible = false
  }
  cancelCheckVersion () {
    this.checkUpdateVisible = false
  }
  handleUploadNotification (val: boolean) {
    this.saveConfig({
      'settings.uploadNotification': val
    })
  }
  handleMiniWindowOntop (val: boolean) {
    this.saveConfig('settings.miniWindowOntop', val)
    this.$message.info('需要重启生效')
  }
  handleAutoCopyUrl (val: boolean) {
    this.saveConfig('settings.autoCopy', val)
    const successNotification = new Notification('设置自动复制链接', {
      body: '设置成功'
    })
    successNotification.onclick = () => {
      return true
    }
  }
  confirmLogLevelSetting () {
    if (this.form.logLevel.length === 0) {
      return this.$message.error('请选择日志记录等级')
    }
    this.saveConfig({
      'settings.logLevel': this.form.logLevel
    })
    const successNotification = new Notification('设置日志', {
      body: '设置成功'
    })
    successNotification.onclick = () => {
      return true
    }
    this.logFileVisible = false
  }
  async cancelLogLevelSetting () {
    this.logFileVisible = false
    let logLevel = await this.getConfig<string | string[]>('settings.logLevel')
    if (!Array.isArray(logLevel)) {
      if (logLevel && logLevel.length > 0) {
        logLevel = [logLevel]
      } else {
        logLevel = ['all']
      }
    }
    this.form.logLevel = logLevel
  }
  confirmServerSetting () {
    // @ts-ignore
    this.server.port = parseInt(this.server.port, 10)
    this.saveConfig({
      'settings.server': this.server
    })
    const successNotification = new Notification('设置PicGo-Server', {
      body: '设置成功'
    })
    successNotification.onclick = () => {
      return true
    }
    this.serverVisible = false
    ipcRenderer.send('updateServer')
  }
  async cancelServerSetting () {
    this.serverVisible = false
    this.server = await this.getConfig('settings.server') || {
      port: 36677,
      host: '127.0.0.1',
      enable: true
    }
  }
  handleLevelDisabled (val: string) {
    let currentLevel = val
    let flagLevel
    let result = this.form.logLevel.some(item => {
      if (item === 'all' || item === 'none') {
        flagLevel = item
      }
      return (item === 'all' || item === 'none')
    })
    if (result) {
      if (currentLevel !== flagLevel) {
        return true
      }
    } else if (this.form.logLevel.length > 0) {
      if (val === 'all' || val === 'none') {
        return true
      }
    }
    return false
  }
  goConfigPage () {
    remote.shell.openExternal('https://picgo.github.io/PicGo-Doc/zh/guide/config.html#picgo设置')
  }
  goShortCutPage () {
    this.$router.push('shortKey')
  }
  beforeDestroy () {
    ipcRenderer.removeListener('getPicBeds', this.getPicBeds)
  }
}
</script>
<style lang='stylus'>
.el-message
  left 60%
.view-title
  .el-icon-document
    cursor pointer
    transition color .2s ease-in-out
    &:hover
      color #49B1F5
#picgo-setting
  .sub-title
    font-size 14px
  .setting-list
    height 360px
    box-sizing border-box
    overflow-y auto
    overflow-x hidden
  .setting-list
    .el-form
      label
        line-height 32px
        padding-bottom 0
        color #eee
      .el-button-group
        width 100%
        .el-button
          width 50%
      .el-input__inner
        border-radius 19px
      .el-radio-group
        margin-left 25px
      .el-switch__label
        color #eee
        &.is-active
          color #409EFF
      .el-icon-question
        font-size 20px
        float right
        margin-top 9px
        color #eee
        cursor pointer
        transition .2s color ease-in-out
        &:hover
          color #409EFF
      .el-checkbox-group
        label
          margin-right 30px
          width 100px
      .el-checkbox+.el-checkbox
        margin-right 30px
        margin-left 0
      .confirm-button
        width 100%
  .server-dialog
    .notice-text
      color: #49B1F5
    .el-dialog__body
      padding-top: 0
    .el-form-item
      margin-bottom: 10px
</style>
