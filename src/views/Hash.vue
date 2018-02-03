<template>
    <my-page title="哈希/散列" :page="page">
        <section class="input-box">
            <div class="form-item">
                <ui-select-field v-model="type" label="散列/哈希类型">
                    <ui-menu-item value="md5" title="MD5"/>
                    <ui-menu-item value="sha1" title="SHA1"/>
                    <ui-menu-item value="sha256" title="SHA256"/>
                    <ui-menu-item value="sha512" title="SHA512"/>
                </ui-select-field>
            </div>
            <div class="form-item">
                <ui-text-field v-model="text" label="加密内容/明文" multiLine :rows="3" />
            </div>
            <div class="btns">
                <ui-raised-button class="btn" label="加密" primary @click="compute" />
                <ui-raised-button class="btn" label="重新输入" @click="clear" />
            </div>

            <div class="result" v-if="result">
                <ui-paper class="card">
                    <h2 class="title">哈希值/摘要/加密结果</h2>
                    {{ result }}
                    <ui-icon-button class="btn-copy" icon="content_copy" title="复制" :data-clipboard-text="result" />
                </ui-paper>
                <!--<div class="form-item">-->
                    <!--<textarea class="form-control" v-model="result" rows="3" placeholder="哈希值/摘要/加密结果"></textarea>-->
                <!--</div>-->
                <div class="form-item">
                    <ui-checkbox class="checkbox" v-model="upper" label="结果字母大写"/>
                    <ui-select-field class="select" v-model="bit" v-if="type === 'md5'">
                        <ui-menu-item value="32" title="32位"/>
                        <ui-menu-item value="16" title="16位"/>
                    </ui-select-field>
                </div>
            </div>
        </section>
    </my-page>
</template>

<script>
    const CryptoJS = window.CryptoJS
    const Clipboard = window.Clipboard

    export default {
        data () {
            return {
                text: '',
                result: '',
                bit: '32',
                upper: false,
                type: 'md5',
                page: {
                    menu: [
                        {
                            type: 'icon',
                            icon: 'help',
                            to: '/hash/help',
                            title: '帮助'
                        }
                    ]
                }
            }
        },
        mounted() {
            this.init()
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
                this.text = '123'
                this.compute()
            },
            compute() {
                switch (this.type) {
                    case 'md5':
                        this.result = CryptoJS.MD5(this.text).toString()
                        console.log(this.bit)
                        if (this.bit === '16') {
                            this.result = this.result.substr(8, 16)
                        }
                        break
                    case 'sha1':
                        this.result = CryptoJS.SHA1(this.text).toString()
                        break
                    case 'sha256':
                        this.result = CryptoJS.SHA256(this.text).toString()
                        break
                    case 'sha512':
                        this.result = CryptoJS.SHA512(this.text).toString()
                        break
                }

                if (this.upper) {
                    this.result = this.result.toUpperCase()
                }
            },
            clear() {
                this.text = this.result = ''
            }
        },
        watch: {
            bit() {
                console.log('bit')
                this.compute()
            },
            upper() {
                this.compute()
            }
        }
    }
</script>

<style lang="scss" scoped>
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
    .select {
        width: 80px;
        margin-left: 8px;
    }
    /**/
    .input-box {

    }
    .input-box
    .input-box .form-control {
        margin-bottom: 16px;
    }
    .input-box .result {
        margin-bottom: 16px;
    }
    /**/
    .result-box .info {
        margin-bottom: 16px;
    }
    /**/
    .list-common {
        padding-left: 16px;
    }
    .list-common li {
        list-style: disc;
    }
</style>
