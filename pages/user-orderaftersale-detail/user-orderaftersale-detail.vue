<template>
    <view :class="theme_view">
        <view v-if="order_data != null" class="padding-horizontal-main padding-top">
            <!-- 商品 -->
            <view class="goods padding-main border-radius-main bg-white spacing-mb">
                <view class="goods-item oh">
                    <navigator :url="order_data.items.goods_url" hover-class="none">
                        <image class="goods-image fl radius" :src="order_data.items.images" mode="aspectFill"></image>
                        <view class="goods-base">
                            <view class="multi-text">{{ order_data.items.title }}</view>
                            <view v-if="order_data.items.spec != null" class="margin-top-sm">
                                <block v-for="(sv, si) in order_data.items.spec" :key="si">
                                    <text v-if="si > 0" class="cr-grey padding-left-xs padding-right-xs">;</text>
                                    <text class="cr-grey">{{ sv.value }}</text>
                                </block>
                            </view>
                            <view class="margin-top-sm">
                                <text class="fw-b">{{ order_data.currency_data.currency_symbol }}{{ order_data.items.price }}</text>
                                <text class="margin-left-sm">x{{ order_data.items.buy_number }}</text>
                            </view>
                        </view>
                    </navigator>
                </view>
            </view>

            <!-- 客服信息 -->
            <view v-if="(plugins_intellectstools_data || null) != null" class="bg-white padding-main border-radius-main spacing-mb">
                <view v-if="(plugins_intellectstools_data.service_msg || null) != null" class="cr-red margin-bottom">{{ plugins_intellectstools_data.service_msg }}</view>
                <button type="default" size="mini" class="bg-main br-main cr-white text-size-sm padding-left-xxxl padding-right-xxxl round" @tap="plugins_intellectstools_service_event">
                    <view class="dis-inline-block va-m margin-right-sm lh-0">
                        <uni-icons type="chatbubble" size="34rpx" color="#fff"></uni-icons>
                    </view>
                    <text>联系客服</text>
                </button>
                <view v-if="plugins_intellectstools_service_status" class="plugins-intellectstools-service pa border-radius-main oh bg-white br">
                    <view v-if="(plugins_intellectstools_data.chat || null) != null" class="item padding-main br-t single-text">
                        <text class="va-m">客服：</text>
                        <view class="dis-inline-block chat-info cp" @tap="chat_event">
                            <image class="dis-inline-block va-m" :src="plugins_intellectstools_data.chat.icon" mode="scaleToFill"></image>
                            <text class="margin-left-sm va-m cr-blue" :data-value="plugins_intellectstools_data.chat.chat_url">{{ plugins_intellectstools_data.chat.name }}</text>
                        </view>
                    </view>
                    <view v-if="(plugins_intellectstools_data.service_qq || null) != null" class="item padding-main br-t single-text">
                        <text>Q Q：</text>
                        <text class="cp" @tap="text_event" data-event="copy" :data-value="plugins_intellectstools_data.service_qq">{{ plugins_intellectstools_data.service_qq }}</text>
                    </view>
                    <view v-if="(plugins_intellectstools_data.service_tel || null) != null" class="item padding-main br-t single-text">
                        <text>电话：</text>
                        <text class="cp" @tap="text_event" data-event="tel" :data-value="plugins_intellectstools_data.service_tel">{{ plugins_intellectstools_data.service_tel }}</text>
                    </view>
                    <view v-if="(plugins_intellectstools_data.service_weixin || null) != null || (plugins_intellectstools_data.service_line || null) != null" class="oh qrcode tc br-t">
                        <view v-if="(plugins_intellectstools_data.service_weixin || null) != null" class="item padding-bottom-lg dis-inline-block">
                            <image class="radius cp" :src="plugins_intellectstools_data.service_weixin" mode="scaleToFill" @tap="image_show_event" :data-value="plugins_intellectstools_data.service_weixin"></image>
                            <view>长按微信咨询</view>
                        </view>
                        <view v-if="(plugins_intellectstools_data.service_line || null) != null" class="item padding-bottom-lg dis-inline-block">
                            <image class="radius cp" :src="plugins_intellectstools_data.service_line" mode="scaleToFill" @tap="image_show_event" :data-value="plugins_intellectstools_data.service_line"></image>
                            <view>长按line咨询</view>
                        </view>
                    </view>
                </view>
            </view>

            <!-- 基础信息 -->
            <view v-if="new_aftersale_data != null">
                <!-- 提示/退货 -->
                <view v-if="new_aftersale_data.status <= 2" class="msg-tips padding-main border-radius-main spacing-mb">
                    <text class="msg-text">{{ new_aftersale_data.tips_msg.title }}</text>
                    <text class="msg-a" @tap="show_aftersale_event">查看 >></text>
                    <view v-if="new_aftersale_data.status == 1 && new_aftersale_data.type == 1 && return_goods_address != null" class="margin-top-sm oh">
                        <button class="bg-green cr-white round dis-block fl" type="default" size="mini" @tap="delivery_submit_event">立即退货</button>
                    </view>
                </view>

                <!-- 退货地址 -->
                <view v-if="new_aftersale_data.status == 1 && new_aftersale_data.type == 1 && return_goods_address != null" class="return-address msg-tips msg-tips-warning padding-main border-radius-main spacing-mb">
                    <text class="cr-base fw-b">退货地址：</text>
                    <view class="cr-blue" @tap="text_event" data-event="copy" :data-value="return_goods_address.name + ' ' + return_goods_address.tel + ' ' + return_goods_address.address">
                        <view>
                            <text class="margin-right-xxxl">{{ return_goods_address.name }}</text>
                            <text>{{ return_goods_address.tel }}</text>
                        </view>
                        <view>{{ return_goods_address.address }}</view>
                    </view>
                </view>

                <!-- 提示 -->
                <view v-if="new_aftersale_data.status >= 3" :class="'msg-tips padding-main border-radius-main spacing-mb ' + (new_aftersale_data.status == 3 ? 'msg-tips-success' : new_aftersale_data.status == 4 ? 'msg-tips-danger' : 'msg-tips-warning')">
                    <text class="msg-text">{{ new_aftersale_data.tips_msg.title }}</text>
                    <text class="msg-a margin-left-sm" @tap="show_aftersale_event">查看 >></text>
                </view>

                <!-- 详情 -->
                <view v-if="new_aftersale_data.status != 5">
                    <!-- 申请信息 -->
                    <view class="panel-item padding-main border-radius-main bg-white spacing-mb">
                        <view class="br-b padding-bottom-main fw-b text-size">申请信息</view>
                        <view class="panel-content oh">
                            <view v-for="(item, index) in panel_base_data_list" :key="index" class="item br-b oh padding-vertical-main">
                                <view class="title fl padding-right-main cr-grey">{{ item.name }}</view>
                                <view class="content fl br-l padding-left-main">{{ new_aftersale_data[item.field] || "" }}</view>
                            </view>
                        </view>
                    </view>

                    <!-- 快递信息 -->
                    <view v-if="new_aftersale_data.status > 1 && new_aftersale_data.type == 1" class="panel-item padding-main border-radius-main bg-white spacing-mb">
                        <view class="br-b padding-bottom-main fw-b text-size">快递信息</view>
                        <view class="panel-content oh">
                            <view v-for="(item, index) in panel_express_data_list" :key="index" class="item br-b oh padding-vertical-main">
                                <view class="title fl padding-right-main cr-grey">{{ item.name }}</view>
                                <view class="content fl br-l padding-left-main">{{ new_aftersale_data[item.field] || "" }}</view>
                            </view>
                        </view>
                    </view>

                    <!-- 凭证 -->
                    <view v-if="(new_aftersale_data.images || null) != null && new_aftersale_data.images.length > 0" class="panel-item padding-main border-radius-main bg-white spacing-mb">
                        <view class="br-b padding-bottom-main fw-b text-size">凭证</view>
                        <view class="panel-content-images oh">
                            <view v-for="(item, index) in new_aftersale_data.images" :key="index" class="fl item padding-sm">
                                <image :src="item" mode="aspectFill" @tap="images_view_event" :data-index="index" class="dis-block radius"></image>
                            </view>
                        </view>
                    </view>
                </view>
            </view>

            <!-- 没有售后数据/售后数据为已取消则可以重新申请售后 -->
            <view v-if="new_aftersale_data == null || new_aftersale_data.status == 5">
                <!-- 类型选择 -->
                <view v-if="aftersale_type_list.length > 0" class="choose-type padding-main border-radius-main bg-white oh spacing-mb">
                    <block v-for="(item, index) in aftersale_type_list" :key="index">
                        <view :class="'choose-item radius padding-main br ' + (index == 0 ? 'fl' : 'fr') + ' ' + (form_type == item.value ? 'br-main' : '')" :data-value="item.value" @tap="form_type_event">
                            <view class="fw-b margin-bottom-xs">{{ item.name }}</view>
                            <view class="cr-grey">{{ item.desc }}</view>
                        </view>
                    </block>
                </view>

                <!-- 表单 -->
                <view v-if="form_type != null" class="form-container oh spacing-mb">
                    <view class="form-gorup">
                        <view class="form-gorup-title">退款原因<text class="form-group-tips-must">*</text></view>
                        <picker @change="form_reason_event" :value="form_reason_index" :range="reason_data_list">
                            <view :class="'picker ' + (form_reason_index == null ? 'cr-grey' : 'cr-base') + ' arrow-right'">
                                {{ form_reason_index == null ? "请选择原因" : reason_data_list[form_reason_index] }}
                            </view>
                        </picker>
                    </view>

                    <view v-if="form_type == 1" class="form-gorup">
                        <view class="form-gorup-title"
                            >商品件数<text class="form-group-tips">数量不能大于{{ returned_data.returned_quantity }}</text></view
                        >
                        <slider @change="form_number_event" min="0" :max="returned_data.returned_quantity" step="1" :value="returned_data.returned_quantity" show-value></slider>
                    </view>

                    <view class="form-gorup">
                        <view class="form-gorup-title"
                            >退款金额<text class="form-group-tips">不能大于{{ returned_data.refund_price }}元</text></view
                        >
                        <input type="digit" @input="form_price_event" placeholder-class="cr-grey" class="cr-base" placeholder="请输入退款金额" :value="form_price" />
                    </view>

                    <view class="form-gorup">
                        <view class="form-gorup-title">退款说明</view>
                        <textarea @input="form_msg_event" placeholder-class="cr-grey" class="cr-base" placeholder="退款说明最多200个字符" maxlength="200" :auto-height="true" :value="form_msg"></textarea>
                    </view>

                    <view class="form-gorup form-container-upload oh">
                        <view class="form-gorup-title">上传凭证<text class="form-group-tips">最多上传3张图片</text></view>
                        <view class="form-upload-data oh">
                            <block v-if="form_images_list.length > 0">
                                <view v-for="(item, index) in form_images_list" :key="index" class="item fl">
                                    <text class="delete-icon" @tap="upload_delete_event" :data-index="index">x</text>
                                    <image :src="item" @tap="upload_show_event" :data-index="index" mode="aspectFill"></image>
                                </view>
                            </block>
                            <image v-if="(form_images_list || null) == null || form_images_list.length < 3" class="item fl upload-icon" :src="common_static_url + 'upload-icon.png'" mode="aspectFill" @tap="file_upload_event"></image>
                        </view>
                    </view>
                    <view class="form-gorup form-gorup-submit">
                        <button class="bg-main br-main cr-white round text-size" type="default" @tap="form_submit_event" hover-class="none" :disabled="form_button_disabled">提交</button>
                    </view>
                </view>
            </view>
        </view>
        <view v-else>
            <!-- 提示信息 -->
            <component-no-data :propStatus="data_list_loding_status" :propMsg="data_list_loding_msg"></component-no-data>
        </view>

        <block v-if="new_aftersale_data != null && new_aftersale_data.status != 5">
            <!-- 结尾 -->
            <component-bottom-line :propStatus="data_bottom_line_status"></component-bottom-line>
        </block>

        <!-- 退货弹层 -->
        <component-popup :propShow="popup_delivery_status" propPosition="bottom" @onclose="popup_delivery_close_event">
            <view class="delivery-popup bg-base padding-horizontal-main padding-top-main">
                <view class="fr oh">
                    <view class="fr" @tap.stop="popup_delivery_close_event">
                        <iconfont name="icon-huiyuan-guanbi" size="28rpx" color="#999"></iconfont>
                    </view>
                </view>
                <view class="margin-top-xxxl padding-top-xxl">
                    <view class="form-container">
                        <view class="form-gorup">
                            <view class="form-gorup-title">快递名称<text class="form-group-tips-must">*</text></view>
                            <input type="text" @input="form_express_name_event" placeholder-class="cr-grey" class="cr-base" placeholder="请输入快递名称" :value="form_express_name" />
                        </view>
                        <view class="form-gorup">
                            <view class="form-gorup-title">快递单号<text class="form-group-tips-must">*</text></view>
                            <input type="text" @input="form_express_number_event" placeholder-class="cr-grey" class="cr-base" placeholder="请输入快递单号" :value="form_express_number" />
                        </view>
                        <view class="form-gorup form-gorup-submit">
                            <button class="bg-main br-main cr-white round text-size" type="default" @tap="form_delivery_submit_event" hover-class="none" :disabled="form_button_disabled">提交</button>
                        </view>
                    </view>
                </view>
            </view>
        </component-popup>
    </view>
</template>
<script>
const app = getApp();
import componentPopup from "../../components/popup/popup";
import componentNoData from "../../components/no-data/no-data";
import componentBottomLine from "../../components/bottom-line/bottom-line";

var common_static_url = app.globalData.get_static_url("common");
export default {
    data() {
        return {
            theme_view: app.globalData.get_theme_value_view(),
            common_static_url: common_static_url,
            params: null,
            data_list_loding_status: 1,
            data_list_loding_msg: "",
            data_bottom_line_status: false,
            popup_delivery_status: false,
            // 接口数据
            editor_path_type: "",
            order_data: null,
            new_aftersale_data: null,
            step_data: null,
            returned_data: null,
            return_only_money_reason: [],
            return_money_goods_reason: [],
            aftersale_type_list: [],
            reason_data_list: [],
            return_goods_address: null,
            // 售后基础信息
            panel_base_data_list: [
                {
                    name: "退款类型",
                    field: "type_text",
                },
                {
                    name: "当前状态",
                    field: "status_text",
                },
                {
                    name: "申请原因",
                    field: "reason",
                },
                {
                    name: "退货数量",
                    field: "number",
                },
                {
                    name: "退款金额",
                    field: "price",
                },
                {
                    name: "退款说明",
                    field: "msg",
                },
                {
                    name: "退款方式",
                    field: "refundment_text",
                },
                {
                    name: "拒绝原因",
                    field: "refuse_reason",
                },
                {
                    name: "申请时间",
                    field: "apply_time",
                },
                {
                    name: "确认时间",
                    field: "confirm_time",
                },
                {
                    name: "退货时间",
                    field: "delivery_time",
                },
                {
                    name: "审核时间",
                    field: "audit_time",
                },
                {
                    name: "取消时间",
                    field: "cancel_time",
                },
                {
                    name: "添加时间",
                    field: "add_time",
                },
                {
                    name: "更新时间",
                    field: "upd_time",
                },
            ],
            // 快递信息
            panel_express_data_list: [
                {
                    name: "快递名称",
                    field: "express_name",
                },
                {
                    name: "快递单号",
                    field: "express_number",
                },
                {
                    name: "退货时间",
                    field: "delivery_time",
                },
            ],
            // 表单数据
            form_button_disabled: false,
            form_type: null,
            form_reason_index: null,
            form_price: "",
            form_msg: "",
            form_number: 0,
            form_images_list: [],
            form_express_name: "",
            form_express_number: "",
            // 智能工具插件、客服信息展示
            plugins_intellectstools_data: null,
            plugins_intellectstools_service_status: false,
        };
    },

    components: {
        componentPopup,
        componentNoData,
        componentBottomLine,
    },
    props: {},

    onLoad(params) {
        this.setData({
            params: params,
            popup_delivery_status: (params.is_delivery_popup || 0) == 1,
        });
    },

    onShow() {
        // 数据加载
        this.init();

        // 分享菜单处理
        app.globalData.page_share_handle();
    },

    // 下拉刷新
    onPullDownRefresh() {
        this.init();
    },

    methods: {
        // 获取数据
        init() {
            var self = this;
            uni.showLoading({
                title: "加载中...",
            });
            this.setData({
                data_list_loding_status: 1,
            });
            uni.request({
                url: app.globalData.get_request_url("aftersale", "orderaftersale"),
                method: "POST",
                data: {
                    oid: this.params.oid,
                    did: this.params.did,
                },
                dataType: "json",
                success: (res) => {
                    uni.hideLoading();
                    uni.stopPullDownRefresh();
                    if (res.data.code == 0) {
                        var data = res.data.data;
                        self.setData({
                            data_list_loding_status: 3,
                            data_bottom_line_status: true,
                            data_list_loding_msg: "",
                            editor_path_type: data.editor_path_type || "",
                            order_data: data.order_data || null,
                            new_aftersale_data: (data.new_aftersale_data || null) == null || data.new_aftersale_data.length <= 0 ? null : data.new_aftersale_data,
                            step_data: data.step_data || null,
                            returned_data: data.returned_data || null,
                            return_only_money_reason: data.return_only_money_reason || [],
                            return_money_goods_reason: data.return_money_goods_reason || [],
                            aftersale_type_list: data.aftersale_type_list || [],
                            return_goods_address: data.return_goods_address || null,
                            form_price: data.returned_data || null != null ? data.returned_data.refund_price : 0,
                            plugins_intellectstools_data: data.plugins_intellectstools_data || null,
                        });
                    } else {
                        self.setData({
                            data_list_loding_status: 0,
                            data_bottom_line_status: false,
                            data_list_loding_msg: res.data.msg,
                        });
                        if (app.globalData.is_login_check(res.data, self, "init")) {
                            app.globalData.showToast(res.data.msg);
                        }
                    }
                },
                fail: () => {
                    uni.hideLoading();
                    uni.stopPullDownRefresh();
                    self.setData({
                        data_list_loding_status: 2,
                        data_bottom_line_status: false,
                        data_list_loding_msg: "网络开小差了哦~",
                    });
                    app.globalData.showToast("网络开小差了哦~");
                },
            });
        },

        // 类型选择
        form_type_event(e) {
            var value = e.currentTarget.dataset.value;
            this.setData({
                form_type: value,
                form_reason_index: this.form_type == value ? this.form_reason_index : null,
                reason_data_list: value == 0 ? this.return_only_money_reason : this.return_money_goods_reason,
                form_number: value == 0 ? 0 : this.returned_data.returned_quantity,
            });
        },

        // 原因选择
        form_reason_event(e) {
            this.setData({
                form_reason_index: e.detail.value,
            });
        },

        // 商品件数
        form_number_event(e) {
            this.setData({
                form_number: e.detail.value,
            });
        },

        // 退款金额
        form_price_event(e) {
            this.setData({
                form_price: e.detail.value,
            });
        },

        // 退款说明
        form_msg_event(e) {
            this.setData({
                form_msg: e.detail.value,
            });
        },

        // 快递名称
        form_express_name_event(e) {
            this.setData({
                form_express_name: e.detail.value,
            });
        },

        // 快递单号
        form_express_number_event(e) {
            this.setData({
                form_express_number: e.detail.value,
            });
        },

        // 上传图片预览
        upload_show_event(e) {
            uni.previewImage({
                current: this.form_images_list[e.currentTarget.dataset.index],
                urls: this.form_images_list,
            });
        },

        // 图片删除
        upload_delete_event(e) {
            var self = this;
            uni.showModal({
                title: "温馨提示",
                content: "删除后不可恢复、继续吗？",
                success(res) {
                    if (res.confirm) {
                        var list = self.form_images_list;
                        list.splice(e.currentTarget.dataset.index, 1);
                        self.setData({
                            form_images_list: list,
                        });
                    }
                },
            });
        },

        // 文件上传
        file_upload_event(e) {
            var self = this;
            uni.chooseImage({
                count: 3,
                success(res) {
                    var success = 0;
                    var fail = 0;
                    var length = res.tempFilePaths.length;
                    var count = 0;
                    self.upload_one_by_one(res.tempFilePaths, success, fail, count, length);
                },
            });
        },

        // 采用递归的方式上传多张
        upload_one_by_one(img_paths, success, fail, count, length) {
            var self = this;
            if (self.form_images_list.length < 3) {
                uni.uploadFile({
                    url: app.globalData.get_request_url("index", "ueditor"),
                    filePath: img_paths[count],
                    name: "upfile",
                    formData: {
                        action: "uploadimage",
                        path_type: self.editor_path_type,
                    },
                    success: function (res) {
                        success++;
                        if (res.statusCode == 200) {
                            var data = typeof res.data == "object" ? res.data : JSON.parse(res.data);
                            if (data.code == 0 && (data.data.url || null) != null) {
                                var list = self.form_images_list;
                                list.push(data.data.url);
                                self.setData({
                                    form_images_list: list,
                                });
                            } else {
                                app.globalData.showToast(data.msg);
                            }
                        }
                    },
                    fail: function (e) {
                        fail++;
                    },
                    complete: function (e) {
                        count++;

                        // 下一张
                        if (count >= length) {
                            // 上传完毕，作一下提示
                            //app.showToast('上传成功' + success +'张', 'success');
                        } else {
                            // 递归调用，上传下一张
                            self.upload_one_by_one(img_paths, success, fail, count, length);
                        }
                    },
                });
            }
        },

        // 售后表单提交
        form_submit_event(e) {
            // 表单数据
            var form_data = {
                order_id: this.params.oid,
                order_detail_id: this.params.did,
                type: this.form_type,
                reason: this.reason_data_list[this.form_reason_index],
                number: this.form_type == 0 ? 0 : this.form_number,
                price: this.form_price,
                msg: this.form_msg,
                images: this.form_images_list.length > 0 ? JSON.stringify(this.form_images_list) : "",
            };

            // 防止金额大于计算的金额
            if (form_data["price"] > this.returned_data["refund_price"]) {
                form_data["price"] = this.returned_data["refund_price"];
            }

            // 防止数量大于计算的数量
            if (form_data["number"] > this.returned_data["returned_quantity"]) {
                form_data["number"] = this.returned_data["returned_quantity"];
            }

            // 数据校验
            var validation = [
                {
                    fields: "type",
                    msg: "请选择操作类型",
                    is_can_zero: 1,
                },
                {
                    fields: "reason",
                    msg: "请选择原因",
                },
            ];
            if (form_data["type"] == 1) {
                validation.push({
                    fields: "number",
                    msg: "请选择退货数量",
                });
            }

            // 校验参数并提交
            if (app.globalData.fields_check(form_data, validation)) {
                var self = this;
                uni.showLoading({
                    title: "处理中...",
                });
                self.setData({
                    form_button_disabled: true,
                });
                uni.request({
                    url: app.globalData.get_request_url("create", "orderaftersale"),
                    method: "POST",
                    data: form_data,
                    dataType: "json",
                    success: (res) => {
                        uni.hideLoading();
                        if (res.data.code == 0) {
                            app.globalData.showToast(res.data.msg, "success");
                            setTimeout(function () {
                                self.setData({
                                    form_button_disabled: false,
                                });
                                self.init();
                            }, 1000);
                        } else {
                            self.setData({
                                form_button_disabled: false,
                            });
                            app.globalData.showToast(res.data.msg);
                        }
                    },
                    fail: () => {
                        uni.hideLoading();
                        self.setData({
                            form_button_disabled: false,
                        });
                        app.globalData.showToast("网络开小差了哦~");
                    },
                });
            }
        },

        // 退货开启弹层
        delivery_submit_event(e) {
            this.setData({
                popup_delivery_status: true,
            });
        },

        // 退货弹层关闭
        popup_delivery_close_event(e) {
            this.setData({
                popup_delivery_status: false,
            });
        },

        // 退货表单
        form_delivery_submit_event(e) {
            // 表单数据
            var form_data = {
                id: this.new_aftersale_data.id,
                express_name: this.form_express_name,
                express_number: this.form_express_number,
            };

            // 数据校验
            var validation = [
                {
                    fields: "express_name",
                    msg: "请填写快递名称",
                },
                {
                    fields: "express_number",
                    msg: "请填写快递单号",
                },
            ];

            // 校验参数并提交
            if (app.globalData.fields_check(form_data, validation)) {
                var self = this;
                uni.showLoading({
                    title: "处理中...",
                });
                self.setData({
                    form_button_disabled: true,
                });
                uni.request({
                    url: app.globalData.get_request_url("delivery", "orderaftersale"),
                    method: "POST",
                    data: form_data,
                    dataType: "json",
                    success: (res) => {
                        uni.hideLoading();
                        self.setData({
                            popup_delivery_status: false,
                        });
                        if (res.data.code == 0) {
                            app.globalData.showToast(res.data.msg, "success");
                            setTimeout(function () {
                                self.setData({
                                    form_button_disabled: false,
                                });
                                self.init();
                            }, 1000);
                        } else {
                            self.setData({
                                form_button_disabled: false,
                            });
                            app.globalData.showToast(res.data.msg);
                        }
                    },
                    fail: () => {
                        uni.hideLoading();
                        self.setData({
                            form_button_disabled: false,
                        });
                        app.globalData.showToast("网络开小差了哦~");
                    },
                });
            }
        },

        // 凭证图片预览
        images_view_event(e) {
            uni.previewImage({
                current: this.new_aftersale_data.images[e.currentTarget.dataset.index],
                urls: this.new_aftersale_data.images,
            });
        },

        // 查看售后数据
        show_aftersale_event(e) {
            uni.navigateTo({
                url: "/pages/user-orderaftersale/user-orderaftersale?keywords=" + this.new_aftersale_data.order_no,
            });
        },

        // 客服事件
        plugins_intellectstools_service_event(e) {
            this.setData({
                plugins_intellectstools_service_status: !this.plugins_intellectstools_service_status,
            });
        },

        // 图片预览
        image_show_event(e) {
            app.globalData.image_show_event(e);
        },

        // 进入客服系统
        chat_event() {
            app.globalData.chat_entry_handle(this.plugins_intellectstools_data.chat.chat_url);
        },

        // 文本事件
        text_event(e) {
            app.globalData.text_event_handle(e);
        },
    },
};
</script>
<style>
@import "./user-orderaftersale-detail.css";
</style>
