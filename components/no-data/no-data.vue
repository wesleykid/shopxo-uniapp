<template>
    <view :class="theme_view">
        <!-- 1 加载中 -->
        <view v-if="propStatus == 1" class="no-data-box no-data-loading loading-animation">
            <text>{{ title }}</text>
        </view>

        <!-- 2 处理错误 -->
        <view v-else-if="propStatus == 2" class="no-data-box">
            <image :src="static_dir + 'error.png'" mode="widthFix"></image>
            <view class="no-data-tips">{{ propMsg || '处理错误' }}</view>
            <view class="margin-top-xxxl tc">
                <button type="default" size="mini" class="bg-grey-e br-grey cr-base round" @tap="back_event">返回</button>
            </view>
        </view>

        <!-- 0 默认没有数据 -->
        <view v-else-if="propStatus == 0" class="no-data-box">
            <image :src="propUrl ? propUrl : static_dir + 'empty.png'" mode="widthFix"></image>
            <view class="no-data-tips">{{ propMsg || '没有相关数据' }}</view>
        </view>
    </view>
</template>
<script>
    const app = getApp();
    export default {
        data() {
            return {
                theme_view: app.globalData.get_theme_value_view(),
                static_dir: '/static/images/common/',
                title: app.globalData.get_application_title(),
            };
        },
        components: {},
        props: {
            propStatus: {
                type: [Number, String],
                default: 0,
            },
            propMsg: {
                type: String,
                default: '没有相关数据',
            },
            propUrl: {
                type: String,
                default: '',
            },
        },
        methods: {
            // 返回事件
            back_event(e) {
                app.globalData.page_back_prev_event();
            }
        },
    };
</script>
<style>
    .no-data-box {
        padding: 15% 0;
        text-align: center;
    }

    .no-data-box image {
        width: 160rpx;
        margin-bottom: 30rpx;
    }

    .no-data-box .no-data-tips {
        font-size: 24rpx;
        color: #999;
    }

    .no-data-loading text {
        color: #999;
    }

    .loading-animation {
        background: #e7e7e7 -webkit-linear-gradient(left, #c6c6c6 0%, #c6c6c6 90%) no-repeat 0 0;
        background-size: 20% 100%;
        -webkit-background-clip: text;
        -webkit-text-fill-color: transparent;
        font-size: 60rpx;
        font-weight: bold;
        padding-top: 20%;
    }

    .loading-animation {
        -webkit-animation: loading-animation 2s linear infinite;
        animation: loading-animation 2s linear infinite;
    }

    @-webkit-keyframes loading-animation {
        0% {
            background-position: 0 0;
        }

        100% {
            background-position: 100% 100%;
        }
    }

    @keyframes loading-animation {
        0% {
            background-position: 0 0;
        }

        100% {
            background-position: 100% 100%;
        }
    }
</style>
