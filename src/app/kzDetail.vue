<template xmlns:v-clipboard="http://www.w3.org/1999/xhtml">
    <div class="index ">
        <div class="accountDetail_block container">
            <div class="headbox">
                <div class="title">
                    <div class="word">
                        <span class="word1 color2">{{$t('snapshot.SNAPSHOT')}}</span>
                        <span class="word2 color3">#{{pageData.hash}}</span>
                    </div>
                    <a href="javascript:;" class="copy"
                       v-clipboard:copy="pageData.hash"
                       v-clipboard:success="onCopy">
                        <img src="@/assets/img/copy.png" alt="">
                        <div class="copymsg" v-show="isCopy">
                            <span class="colorfff">{{$t('last.Copied')}}</span>
                        </div>
                    </a>
                </div>
                <div class="cont">
                    <div class="item">
                        <p class="conttitle color9"><span>{{$t('snapshot.Status')}}：</span></p>
                        <p class="text color3">{{$t('snapshot.Confirmed')}}</p>
                    </div>
                    <div class="item">
                        <p class="conttitle color9"><span>{{$t('snapshot.Time')}}：</span></p>
                        <p class="text color3">{{pageData.timestamp | fomatTime}}({{pageData.timestamp |
                            parseTime}})</p>
                    </div>
                    <div class="item">
                        <p class="conttitle color9"><span>{{$t('snapshot.From')}}：</span></p>
                        <p class="text color3">{{pageData.fromAddress}}</p>
                    </div>
                    <div class="item">
                        <p class="conttitle color9"><span>{{$t('snapshot.Version')}}</span></p>
                        <p class="text color3">
                            <span>{{pageData.snapVersion}}</span>
                        </p>
                    </div>
                    <div class="item">
                        <p class="conttitle color9"><span>{{$t('snapshot.Previous')}}：</span></p>
                        <p class="text color3">{{messageData.preHash}}</p>
                    </div>
                    <div class="item">
                        <p class="conttitle color9"><span>{{$t('snapshot.Current')}}：</span></p>
                        <p class="text color3">{{pageData.hash}}</p>
                    </div>
                    <div class="item">
                        <p class="conttitle color9"><span>{{$t('snapshot.SnapshotpPint')}}：</span></p>
                        <p class="text color3">{{snapshotPoint.msgMaxId}}</p>
                    </div>
                    <div class="item">
                        <p class="conttitle color9"><span>Merkle tree：</span></p>
                        <p class="text color3">{{snapshotPoint.msgHashTreeRoot}}</p>
                    </div>
                    <div class="item">
                        <p class="conttitle color9"><span>{{$t('snapshot.TotalFee')}}：</span></p>
                        <p class="text color3">{{snapshotPoint.totalFee/1e+18}} INVE</p>
                    </div>
                    <div class="item">
                        <p class="conttitle color9"><span>{{$t('snapshot.RewardRatio')}}：</span></p>
                        <p class="text color3">{{snapshotPoint.rewardRatio}}%</p>
                    </div>
                </div>
            </div>
        </div>
        <div class="accountBox container">
            <div class="title color2">
                <span>{{$t('snapshot.REWARD')}}</span>
            </div>
            <table class="table-hover" border="1">
                <tr class="tablehead color9">
                    <th>{{$t('snapshot.To')}}</th>
                    <th>{{$t('snapshot.Time')}}</th>
                    <th>{{$t('snapshot.Amount')}}</th>
                </tr>
                <tbody>
                <tr v-for="item in listData">
                    <td class="colorA8B">13 mins ago</td>
                    <td><a href="javascript:;" class="color306">{{item.name}}}</a></td>
                    <td>
                        {{item.value}} <span> atom</span>
                    </td>
                </tr>
                </tbody>
            </table>
        </div>
    </div>
</template>
<script>
    export default {
        data: function () {
            return {
                listData: {},
                pageData: {},
                messageData: {},
                snapshotPoint: {},
                isCopy: false,
                isShowing: null
            }
        },
        methods: {
            onCopy(e) {
                this.isCopy = true;
                clearTimeout(this.isShowing);
                this.isShowing = setTimeout(() => {
                    this.isCopy = false;
                }, 1500)
            },
            getData(hash) {
                let formdata = new FormData();
                formdata.append("hash", hash);
                this.axios.post('messagesinfo', formdata).then((res) => {


                    let messageData = JSON.parse(res.data.data.message);
                    let listData = [];

                    this.pageData = res.data.data;
                    this.messageData = messageData;
                    this.snapshotPoint = messageData.snapshotPoint;


                    Object.keys(messageData.snapshotPoint.contributions).forEach(function (key) {
                        // console.log(key);
                        let obj = {name: key, value: messageData.snapshotPoint.contributions[key]}
                        listData.push(obj)
                    });
                    this.listData = listData;


                    if (!res.data.data) {
                        this.total = 1;
                    } else {
                        this.total = 1;
                    }
                })
            }
        },
        created: function () {
            // console.log(this.$route.params.id);
        },
        mounted: function () {
            // console.log(this.$route.query.hash);
            this.getData(this.$route.query.hash)
        },
        beforeRouteLeave(to, from, next) {
            next()
        }
    }
</script>
<style scoped lang="scss">
    $width-mobile: 768px; // 移动端适配宽度
    .accountDetail_block {
        font-size: 20px;
        margin-top: 20px;
        padding: 25px 36px 40px;
        background-color: #fff;

        .title {
            padding-bottom: 1em;

            .word {
                display: inline-block;
                max-width: 90%;
                overflow: hidden;
                text-overflow: ellipsis;
                white-space: nowrap;

                .word1 {
                    font-size: 16px;
                }

                .word2 {
                    font-size: 14px;
                }
            }

            .copy {
                display: inline-block;
                position: relative;

                img {
                    vertical-align: text-top;
                }

                .copymsg {
                    position: absolute;
                    font-size: 12px;
                    text-align: center;
                    width: 112px;
                    padding: 11px 0;
                    background-color: rgba(0, 0, 0, .6);
                    border-radius: 4px;
                    left: -45px;

                    &:before {
                        content: ' ';
                        position: absolute;
                        width: 0;
                        height: 0;
                        top: -14px;
                        left: 45px;
                        border-top: 7px solid transparent;
                        border-bottom: 7px solid rgba(0, 0, 0, .6);
                        border-right: 7px solid transparent;
                        border-left: 7px solid transparent;

                    }
                }
            }
        }

        .cont {
            border-top: 1px solid #F0F5FF;
            padding-top: 1.45em;

            .item {
                vertical-align: top;

                & * {
                    display: inline-block;
                }

                .conttitle {
                    width: 95px;
                    font-size: 14px;
                    text-align: left;
                }

                .text {
                    word-break: break-all;
                    font-size: 12px;
                    text-align: left;
                }
            }
        }

        @media screen and (max-width: $width-mobile) {
            & {
                padding: 25px 20px 40px;
            }
            .cont {
                .item {
                    .text {
                        width: 62%;
                    }
                }
            }
        }
    }

    .accountBox {
        padding: 15px 36px 40px;
        margin-top: 20px;
        background-color: #fff;
        font-size: 20px;

        .title {
            font-size: 16px;
            /*padding-top: 15px;*/
            padding-bottom: 14px;
            /*padding-left: 23px;*/
        }

        table {
            border-collapse: collapse;
            border: solid #fff;
            border-width: 0px 0px 0px 0px;
            width: 100%;
            table-layout: fixed;

            .tablehead {
                font-size: 14px;
                height: 36px;
            }

            tr {
                border-bottom: 1px solid #F0F5FF;
                border-top: 1px solid #F0F5FF;
            }

            td,
            th {
                /*text-align: center;*/
                overflow: hidden;
                text-overflow: ellipsis;
                white-space: nowrap;
                word-break: break-all;
            }

            th:nth-child(1), td:nth-child(1) {
                max-width: 100px;
                width: 15%;
                text-align: left;
            }

            td {
                font-size: 12px;
                height: 56px;

                a:hover {
                    color: #005AFF;
                }
            }
        }

        @media screen and (max-width: $width-mobile) {
            & {
                padding: 15px 20px 40px;
            }
        }
    }
</style>
