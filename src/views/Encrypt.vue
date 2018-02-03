<template>
    <my-page title="加密解密" :page="page">
        <div id="input-box">
            <div class="form-item">
                <ui-select-field v-model="type" label="加密类型">
                    <ui-menu-item value="aes" title="AES"/>
                    <ui-menu-item value="des" title="DES"/>
                    <ui-menu-item value="rc4" title="RC4"/>
                    <ui-menu-item value="rabbit" title="rabbit"/>
                    <ui-menu-item value="tripleDES" title="TripleDES"/>
                </ui-select-field>
            </div>
            <div class="form-item">
                <ui-text-field v-model="code" label="未加密文本/加密内容" multiLine :rows="3" />
            </div>
            <div class="form-item">
                <ui-text-field v-model="password" label="秘钥" />
            </div>
            <div class="form-item">
                <div class="btns">
                    <ui-raised-button class="btn" label="加密" primary @click="encode" />
                    <ui-raised-button class="btn" label="解密" secondary @click="decode" />
                </div>
            </div>
            <ui-paper class="card" v-if="result">
                <h2 class="title">加密/解密结果</h2>
                <div>{{ result }}</div>
                <ui-icon-button class="btn-copy" icon="content_copy" title="复制" :data-clipboard-text="result"/>
            </ui-paper>
        </div>
    </my-page>
</template>

<script>
    const CryptoJS = window.CryptoJS
    const Clipboard = window.Clipboard

    export default {
        data () {
            return {
                type: 'aes',
                password: '',
                code: '',
                result: '',
                page: {
                    menu: [
                        {
                            type: 'icon',
                            icon: 'help',
                            to: '/encrypt/help',
                            title: '帮助'
                        }
                    ]
                }
            }
        },
        mounted() {
            this.init()
//            this.debug()
        },
        destroyed() {
            this.clipboard.destroy()
        },
        methods: {
            init() {
                this.clipboard = new Clipboard('.btn-copy')
                this.clipboard.on('success', function (e) {
                    console.info('Action:', e.action)
                    console.info('Text:', e.text)
                    console.info('Trigger:', e.trigger)

                    e.clearSelection()
                })
                this.clipboard.on('error', function(e) {
                    console.error('Action:', e.action)
                    console.error('Trigger:', e.trigger)
                })
            },
            debug() {
                this.code = '123'
                this.password = '123456'
                this.encode()
            },
            encode: function () {
                var text = this.code
                if (text === '') {
                    alert('请填写要编码的内容')
                } else {
                    switch (this.type) {
                        case 'aes':
                            this.result = CryptoJS.AES.encrypt(this.code, this.password).toString()
                            console.log(this.result)
                            break
                        case 'des':
                            this.result = CryptoJS.DES.encrypt(this.code, this.password).toString()
                            break
                        case 'rc4':
                            this.result = CryptoJS.RC4.encrypt(this.code, this.password).toString()
                            break
                        case 'rabbit':
                            this.result = CryptoJS.Rabbit.encrypt(this.code, this.password).toString()
                            break
                        case 'tripleDES':
                            console.log('trip')
                            this.result = CryptoJS.TripleDES.encrypt(this.code, this.password).toString()
                            break
                    }
                }
            },
            decode: function () {
                var text = this.code
                if (text === '') {
                    alert('请填写要解码的内容')
                } else {
                    switch (this.type) {
                        case 'aes':
                            this.result = CryptoJS.AES.decrypt(this.code, this.password).toString(CryptoJS.enc.Utf8)
                            break
                        case 'des':
                            this.result = CryptoJS.DES.decrypt(this.code, this.password).toString(CryptoJS.enc.Utf8)
                            break
                        case 'rc4':
                            this.result = CryptoJS.RC4.decrypt(this.code, this.password).toString(CryptoJS.enc.Utf8)
                            break
                        case 'rabbit':
                            this.result = CryptoJS.Rabbit.decrypt(this.code, this.password).toString(CryptoJS.enc.Utf8)
                            break
                        case 'tripleDES':
                            this.result = CryptoJS.TripleDES.decrypt(this.code, this.password).toString(CryptoJS.enc.Utf8)
                            break
                    }
                }
            }
        }
    }
</script>

<style lang="scss" scoped>
    select {
        margin-bottom: 16px;
    }
    .btns {
        margin-bottom: 16px;
        .btn {
            margin-right: 8px;
        }
    }
    .card {
        position: relative;
        max-width: 320px;
        padding: 16px;
        margin-bottom: 16px;
        word-wrap: break-word;
        .title {
            font-size: 20px;
            margin-bottom: 16px;
        }
        .btn-copy {
            position: absolute;
            top: 8px;
            right: 8px;
        }
    }
</style>
