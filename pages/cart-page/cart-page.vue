<template>
    <view :class="theme_view">
        <!-- 购物车 -->
        <component-cart ref="cart"></component-cart>

        <!-- 快捷导航 -->
        <component-quick-nav :propIsBar="true"></component-quick-nav>
    </view>
</template>
<script>
    const app = getApp();
    import componentQuickNav from "../../components/quick-nav/quick-nav";
    import componentCart from "../../components/cart/cart";
    export default {
        data() {
            return {
                theme_view: app.globalData.get_theme_value_view(),
            };
        },
        components: {
            componentQuickNav,
            componentCart
        },

        onShow() {
            // 数据加载
            if((this.$refs.cart || null) != null) {
                this.$refs.cart.init();
            }
        },

        methods: {}
    };
</script>
<style>
</style>