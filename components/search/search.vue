<template>
    <view :class="theme_view">
        <view class="search-content pr">
            <view class="search-icon dis-inline-block pa" @tap="search_icon_event">
                <iconfont :name="propIcon" :color="propIconColor" size="24rpx"></iconfont>
            </view>
            <input
                type="text"
                confirm-type="search"
                :class="'round wh-auto dis-block '+propClass"
                :placeholder="propPlaceholder"
                :placeholder-class="propPlaceholderClass"
                :value="propDefaultValue"
                @input="search_input_value_event"
                @confirm="search_submit_confirm_event"
                @focus="search_input_focus_event"
                @blur="search_input_blur_event"
                :style="'color:' + propTextColor + ';background:' + propBgColor + ';' + ((propBrColor || null) != null ? 'border:1px solid ' + propBrColor + ';' : '')"
            />
            <button v-if="propIsBtn" class="search-btn pa bg-main" size="mini" type="default" @tap="search_submit_confirm_event">搜索</button>
        </view>
    </view>
</template>
<script>
    const app = getApp();
    export default {
        data() {
            return {
                theme_view: app.globalData.get_theme_value_view(),
                input_value: '',
            };
        },
        components: {},
        props: {
            propUrl: {
                type: String,
                default: '/pages/goods-search/goods-search',
            },
            propFormName: {
                type: String,
                default: 'keywords',
            },
            propPlaceholder: {
                type: String,
                default: '其实搜索很简单 ^_^!',
            },
            propDefaultValue: {
                type: String,
                default: '',
            },
            propPlaceholderClass: {
                type: String,
                default: 'cr-grey-c',
            },
            propClass: {
                type: String,
                default: '',
            },
            propTextColor: {
                type: String,
                default: '#666',
            },
            propBgColor: {
                type: String,
                default: '#fff',
            },
            propBrColor: {
                type: String,
                default: '',
            },
            propIsRequired: {
                type: Boolean,
                default: true,
            },
            propIsOnEvent: {
                type: Boolean,
                default: false,
            },
            propIsOnFocusEvent: {
                type: Boolean,
                default: false,
            },
            propIsOnBlurEvent: {
                type: Boolean,
                default: false,
            },
            propIsOnInputEvent: {
                type: Boolean,
                default: false,
            },
            propIcon: {
                type: String,
                default: 'icon-index-search',
            },
            propIconColor: {
                type: String,
                default: '#ccc',
            },
            propIsIconOnEvent: {
                type: Boolean,
                default: false,
            },
            propIsBtn: {
                type: Boolean,
                default: false,
            },
        },
        // 属性值改变监听
        watch: {
            // 默认值
            propDefaultValue(value, old_value) {
                this.setData({
                    input_value: value,
                });
            }
        },
        // 页面被展示
        created: function () {
            this.setData({
                input_value: this.propDefaultValue
            });
        },
        methods: {
            // 搜索输入事件
            search_input_value_event(e) {
                this.setData({
                    input_value: e.detail.value,
                });
                // 是否回调事件
                if (this.propIsOnInputEvent) {
                    this.$emit('oninput', e.detail.value);
                }
            },

            // 搜索失去焦点事件
            search_input_blur_event(e) {
                this.setData({
                    input_value: e.detail.value,
                });
                // 是否回调事件
                if (this.propIsOnBlurEvent) {
                    this.$emit('onblur', e.detail.value);
                }
            },

            // 搜索获取焦点事件
            search_input_focus_event(e) {
                this.setData({
                    input_value: e.detail.value,
                });
                // 是否回调事件
                if (this.propIsOnFocusEvent) {
                    this.$emit('onfocus', e.detail.value);
                }
            },

            // 搜索确认事件
            search_submit_confirm_event(e) {
                // 是否验证必须要传值
                if (this.propIsRequired && this.input_value === '') {
                    app.globalData.showToast('请输入搜索关键字');
                    return false;
                }

                // 是否回调事件
                if (this.propIsOnEvent) {
                    this.$emit('onsearch', this.input_value);
                } else {
                    // 进入搜索页面
                    uni.navigateTo({
                        url: this.propUrl + '?' + this.propFormName + '=' + this.input_value,
                    });
                }
            },

            // icon事件
            search_icon_event() {
                // 是否回调事件
                if (this.propIsIconOnEvent) {
                    this.$emit('onicon', {});
                }
            },
        },
    };
</script>
<style>
    .search-content .search-icon {
        z-index: 1;
        left: 0;
        top: 0;
        padding: 14rpx 20rpx 0 20rpx;
        line-height: 28rpx;
        height: 42rpx;
    }

    .search-content input {
        font-size: 24rpx;
        padding: 0 32rpx 0 64rpx;
        box-sizing: border-box;
        height: 56rpx;
        line-height: 56rpx;
    }

    .search-content .search-btn {
        width: 106rpx;
        height: 46rpx;
        line-height: 46rpx;
        font-size: 28rpx;
        border-radius: 30rpx;
        padding: 0;
        color: #fff;
        right: 6rpx;
        top: 50%;
        transform: translateY(-50%);
        z-index: 2;
    }
</style>
