<template>
    <my-page title="文件哈希值/MD5" :page="page">
        <ui-raised-button class="btn" primary label="选择文件">
            <input class="ui-file-button" type="file" @change="filechange($event)">
        </ui-raised-button>
        <ui-linear-progress class="progress" mode="determinate" :value="progress" v-if="doing"/>
        <ui-paper class="card" v-if="result">
            <h2 class="title">MD5</h2>
            {{ result }}
            <ui-icon-button class="btn-copy" icon="content_copy" title="复制" :data-clipboard-text="result" />
        </ui-paper>
    </my-page>
</template>

<script>
    /* eslint-disable */
    export default {
        data () {
            return {
                text: '',
                result: '',
                bit: 16,
                upper: false,

                doing: false,
                progress: 0,

                page: {
                    menu: [
                        {
                            type: 'icon',
                            icon: 'help',
                            to: '/hash/file/help',
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
            filechange(event) {
                var _this = this;
                this.result = null
                this.doing = true
                this.progress = 0
                this.startTime = new Date().getTime()
                var files = event.target.files;
                if(!files){
                    alert("<p>At least one selected file is invalid - do not select any folders.</p><p>Please reselect and try again.</p>");
                    return;
                }

                var file = files[0];

                var chunkSize = 204800;
                var pos = 0;

                var md5Instance = CryptoJS.algo.MD5.create();

                var reader = new FileReader();
                function progressiveReadNext(){
                    var end = Math.min(pos + chunkSize, file.size);

                    reader.onload = function(e) {
                        pos = end;
                        md5Instance.update(CryptoJS.enc.Latin1.parse(e.target.result));
                        var present = (parseFloat(pos)/parseFloat(file.size))*10000/100;
                        _this.progress = Math.round(present)
                        if (pos < file.size) {
                            progressiveReadNext();
                        } else {
                            var md5Value = md5Instance.finalize();
                            _this.result = md5Value.toString();
                            _this.doing = false
                            console.log(new Date().getTime() - _this.startTime)
                        }
                    }

                    if (file.slice) {
                        var blob = file.slice(pos, end);
                    } else if (file.webkitSlice) {
                        var blob = file.webkitSlice(pos, end);
                    }else if(File.prototype.mozSlice){
                        var blob = file.mozSlice(pos, end);
                    }
                    reader.readAsBinaryString(blob);
                }
                progressiveReadNext();
            },
            compute: function () {
                this.result = hex_md5(this.text);
                console.log(this.bit)
                if (parseInt(this.bit) === 16) {
                    this.result = this.result.substr(8, 16);
                }
                if (this.upper) {
                    this.result = this.result.toUpperCase();
                }
            },
            clear: function () {
                this.text = this.result = '';
            }
        }
    }
</script>

<style lang="scss" scoped>
    .btn {
        margin-bottom: 16px;
    }
    .progress {
        max-width: 320px;
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
    .ui-file-button {
        position: absolute;
        left: 0;
        right: 0;
        top: 0;
        bottom: 0;
        opacity: 0;
    }
</style>
