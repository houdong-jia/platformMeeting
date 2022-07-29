<template>
    <!--
    根目录规范(必须不能为空)：
    idm-ctrl：控件类型 drag_container：容器，drag_container_inlieblock：行内容器，idm_module：非容器的组件
    id：使用moduleObject.id，如果id不使用这个将会被idm-ctrl-id属性替换
    idm-ctrl-id：组件的id，这个必须不能为空
  -->
    <div idm-ctrl="idm_module" :id="moduleObject.id" :idm-ctrl-id="moduleObject.id" :idm-refresh-container="activeTab" class="idm_itabs_box">
        <!--
      组件内部容器
      增加class="drag_container" 代表内部可存放组件容器 必选
      增加idm-ctrl-inner 代表内部容器组件（可定义单独的属性，只支持定义一类的属性,一个组件内只包含一种） 可选
      idm-ctrl-id：组件的id，这个必须不能为空
      idm-container-index  组件的内部容器索引，不重复唯一且不变，必选，建议从1开始
    -->
        <van-tabs v-model="active" :animated="propData.animated!==false?true:false" :size="propData.size||'default'" :tabPosition="propData.tabPosition||'top'" :type="propData.type||'line'" :tabBarGutter="propData.tabBarGutter==0?0:(propData.tabBarGutter||null)" :tabBarStyle="getTabStyleObject()" @change="changeCallback" @nextClick="nextClickCallback" @prevClick="prevClickCallback" @tabClick="tabClickCallback">
            <van-tab title="标签 1">内容 1</van-tab>
        </van-tabs>
        <!-- <layout-a-tabs :activeKey="activeTab" :animated="propData.animated!==false?true:false" :size="propData.size||'default'" :tabPosition="propData.tabPosition||'top'" :type="propData.type||'line'" :tabBarGutter="propData.tabBarGutter==0?0:(propData.tabBarGutter||null)" :tabBarStyle="getTabStyleObject()" @change="changeCallback" @nextClick="nextClickCallback" @prevClick="prevClickCallback" @tabClick="tabClickCallback">
            <layout-a-tab-pane forceRender v-for="item in tabList" :key="item.key">
                <div class="drag_container" v-if="item.opened" :class="`idm-tab-inner-${item.key}`" idm-ctrl-inner :idm-ctrl-id="moduleObject.id" :idm-container-index="item.key">

                </div>
                <span slot="tab">
                    <div v-if="item.tabSlotFunction&&item.tabSlotFunction.length>0" v-html="getTabCustomRender(item)"></div>
                    <template v-else>{{ item.tab }}</template>
                </span>
            </layout-a-tab-pane>
            <div v-if="propData.tabBarExtraContentFunction&&propData.tabBarExtraContentFunction.length>0" :slot="`tabBarExtraContent${propData.tabBarExtraContentFunction&&propData.tabBarExtraContentFunction.length>0?'':'close'}`">
                <div v-if="propData.tabBarExtraContentFunction&&propData.tabBarExtraContentFunction.length>0" v-html="getTabBarExtraContentCustomRender()"></div>
            </div>
        </layout-a-tabs> -->
    </div>
</template>

<script>
export default {
    name: 'ITabs',
    data () {
        return {
            moduleObject: {},
            propData: this.$root.propData.compositeAttr || {},
            innerAttr: this.$root.propData.innerAttr || [],
            active: "2",
            tabList: []
        }
    },
    props: {
    },
    created () {
        //this.moduleObject = this.$root.moduleObject
        // console.log(this.moduleObject)
        //this.initAttrToModule();
    },
    mounted () {
    },
    destroyed () { },
    methods: {
        /**
         * 切换面板的回调
         */
        changeCallback (key) {
            let that = this;
            this.activeTab = key;
            this.tabList.forEach(item => {
                if (this.activeTab == item.key) {
                    if (!item.opened) {
                        item.opened = true;
                        that.$nextTick(function (params) {
                            //去加载内部组件
                            that.moduleObject.moduleReload && that.moduleObject.moduleReload(that.moduleObject.packageid, item.key);
                        })
                    }
                    // item.opened = true;
                }
            })

            this.changeEventFunHandle("changeFunction");
        },
        /**
         * next 按钮被点击的回调	
         */
        nextClickCallback () {
            this.changeEventFunHandle("nextClickFunction");
        },
        /**
         * prev 按钮被点击的回调	
         */
        prevClickCallback () {
            this.changeEventFunHandle("prevClickFunction");
        },
        /**
         * tab 被点击的回调	
         */
        tabClickCallback () {
            this.changeEventFunHandle("tabClickFunction");
        },
        /**
         * 通用自定义函数
         */
        changeEventFunHandle (name) {
            let that = this;
            var customHandle = that.propData[name];
            customHandle &&
                customHandle.forEach((item) => {
                    window[item.name] &&
                        window[item.name].call(this, {
                            ...that.commonParam(),
                            customParam: item.param,
                            _this: that,
                            activeKey: that.activeTab,
                            allKey: that.tabList
                        });
                });
        },
        /**
         * 通用的url参数对象
         * 所有地址的url参数转换
         */
        commonParam () {
            let urlObject = IDM.url.queryObject();
            var params = {
                pageId:
                    window.IDM.broadcast && window.IDM.broadcast.pageModule
                        ? window.IDM.broadcast.pageModule.id
                        : "",
                urlData: JSON.stringify(urlObject),
            };
            return params;
        },
        /**
         * 获取扩展的结果
         */
        getTabBarExtraContentCustomRender () {
            return window[this.propData.tabBarExtraContentFunction[0].name] &&
                window[this.propData.tabBarExtraContentFunction[0].name].call(this, {
                    customParam: this.propData.tabBarExtraContentFunction[0].param,
                    tabList: this.tabList
                })
        },
        /**
         * 获取tab bar样式对象的结果
         */
        getTabStyleObject () {
            if (this.propData.tabBarStyleFunction && this.propData.tabBarStyleFunction.length > 0) {
                return window[this.propData.tabBarStyleFunction[0].name] &&
                    window[this.propData.tabBarStyleFunction[0].name].call(this, {
                        customParam: this.propData.tabBarStyleFunction[0].param,
                        tabList: this.tabList
                    })
            } else {
                return {}
            }
        },
        /**
         * 获取tab自定义的数据
         */
        getTabCustomRender (item) {
            if (item.tabSlotFunction && item.tabSlotFunction.length > 0) {
                if (!window[item.tabSlotFunction[0].name]) {
                    return this.moduleObject.env == 'develop' ? "请把动作面板打开" : "";
                }
                return window[item.tabSlotFunction[0].name] &&
                    window[item.tabSlotFunction[0].name].call(this, {
                        customParam: item.tabSlotFunction[0].param,
                        tab: item
                    })
            } else {
                return "方法未找到";
            }
        },
        /**
         * 提供父级组件调用的刷新prop数据组件
         */
        propDataWatchHandle (propData) {
            this.propData = propData.compositeAttr || {};
            this.innerAttr = propData.innerAttr || [];
            this.initAttrToModule();
            console.log("组件内属性发生变化，变化后====》", this.propData);
        },
        /**
         * 把属性转换成样式对象
         */
        convertAttrToStyleObject () {
            var styleObject = {};
            if (this.propData.bgSize && this.propData.bgSize == "custom") {
                styleObject["background-size"] = (this.propData.bgSizeWidth ? this.propData.bgSizeWidth.inputVal + this.propData.bgSizeWidth.selectVal : "auto") + " " + (this.propData.bgSizeHeight ? this.propData.bgSizeHeight.inputVal + this.propData.bgSizeHeight.selectVal : "auto")
            } else if (this.propData.bgSize) {
                styleObject["background-size"] = this.propData.bgSize;
            }
            if (this.propData.positionX && this.propData.positionX.inputVal) {
                styleObject["background-position-x"] = this.propData.positionX.inputVal + this.propData.positionX.selectVal;
            }
            if (this.propData.positionY && this.propData.positionY.inputVal) {
                styleObject["background-position-y"] = this.propData.positionY.inputVal + this.propData.positionY.selectVal;
            }
            for (const key in this.propData) {
                if (this.propData.hasOwnProperty.call(this.propData, key)) {
                    const element = this.propData[key];
                    if (!element && element !== false && element != 0) {
                        continue;
                    }
                    switch (key) {
                        case "width":
                        case "height":
                            styleObject[key] = element;
                            break;
                        case "bgColor":
                            if (element && element.hex8) {
                                styleObject["background-color"] = element.hex8;
                            }
                            break;
                        case "box":
                            if (element.marginTopVal) {
                                styleObject["margin-top"] = `${element.marginTopVal}`;
                            }
                            if (element.marginRightVal) {
                                styleObject["margin-right"] = `${element.marginRightVal}`;
                            }
                            if (element.marginBottomVal) {
                                styleObject["margin-bottom"] = `${element.marginBottomVal}`;
                            }
                            if (element.marginLeftVal) {
                                styleObject["margin-left"] = `${element.marginLeftVal}`;
                            }
                            if (element.paddingTopVal) {
                                styleObject["padding-top"] = `${element.paddingTopVal}`;
                            }
                            if (element.paddingRightVal) {
                                styleObject["padding-right"] = `${element.paddingRightVal}`;
                            }
                            if (element.paddingBottomVal) {
                                styleObject["padding-bottom"] = `${element.paddingBottomVal}`;
                            }
                            if (element.paddingLeftVal) {
                                styleObject["padding-left"] = `${element.paddingLeftVal}`;
                            }
                            break;
                        case "bgImgUrl":
                            styleObject["background-image"] = `url(${IDM.url.getWebPath(element)})`;
                            break;
                        case "positionX":
                            //背景横向偏移

                            break;
                        case "positionY":
                            //背景纵向偏移

                            break;
                        case "bgRepeat":
                            //平铺模式
                            styleObject["background-repeat"] = element;
                            break;
                        case "bgAttachment":
                            //背景模式
                            styleObject["background-attachment"] = element;
                            break;
                        case "border":
                            if (element.border.top.width > 0) {
                                styleObject["border-top-width"] = element.border.top.width + element.border.top.widthUnit;
                                styleObject["border-top-style"] = element.border.top.style;
                                if (element.border.top.colors.hex8) {
                                    styleObject["border-top-color"] = element.border.top.colors.hex8;
                                }
                            }
                            if (element.border.right.width > 0) {
                                styleObject["border-right-width"] = element.border.right.width + element.border.right.widthUnit;
                                styleObject["border-right-style"] = element.border.right.style;
                                if (element.border.right.colors.hex8) {
                                    styleObject["border-right-color"] = element.border.right.colors.hex8;
                                }
                            }
                            if (element.border.bottom.width > 0) {
                                styleObject["border-bottom-width"] = element.border.bottom.width + element.border.bottom.widthUnit;
                                styleObject["border-bottom-style"] = element.border.bottom.style;
                                if (element.border.bottom.colors.hex8) {
                                    styleObject["border-bottom-color"] = element.border.bottom.colors.hex8;
                                }
                            }
                            if (element.border.left.width > 0) {
                                styleObject["border-left-width"] = element.border.left.width + element.border.left.widthUnit;
                                styleObject["border-left-style"] = element.border.left.style;
                                if (element.border.left.colors.hex8) {
                                    styleObject["border-left-color"] = element.border.left.colors.hex8;
                                }
                            }

                            styleObject["border-top-left-radius"] = element.radius.leftTop.radius + element.radius.leftTop.radiusUnit;
                            styleObject["border-top-right-radius"] = element.radius.rightTop.radius + element.radius.rightTop.radiusUnit;
                            styleObject["border-bottom-left-radius"] = element.radius.leftBottom.radius + element.radius.leftBottom.radiusUnit;
                            styleObject["border-bottom-right-radius"] = element.radius.rightBottom.radius + element.radius.rightBottom.radiusUnit;
                            break;
                        case "font":
                            styleObject["font-family"] = element.fontFamily;
                            if (element.fontColors.hex8) {
                                styleObject["color"] = element.fontColors.hex8;
                            }
                            styleObject["font-weight"] = element.fontWeight && element.fontWeight.split(" ")[0];
                            styleObject["font-style"] = element.fontStyle;
                            styleObject["font-size"] = element.fontSize + element.fontSizeUnit;
                            styleObject["line-height"] = element.fontLineHeight + (element.fontLineHeightUnit == "-" ? "" : element.fontLineHeightUnit);
                            styleObject["text-align"] = element.fontTextAlign;
                            styleObject["text-decoration"] = element.fontDecoration;
                            break;
                    }
                }
            }
            IDM.setStyleToPageHead(this.moduleObject.id + "", styleObject);
        },
        /**
         * 把属性参数转换成内部容器样式对象
         */
        convertInnerAttrToStyleObject (propData, index) {
            var styleObject = {};
            if (propData.bgSize && propData.bgSize == "custom") {
                styleObject["background-size"] = (propData.bgSizeWidth ? propData.bgSizeWidth.inputVal + propData.bgSizeWidth.selectVal : "auto") + " " + (propData.bgSizeHeight ? propData.bgSizeHeight.inputVal + propData.bgSizeHeight.selectVal : "auto")
            } else if (propData.bgSize) {
                styleObject["background-size"] = propData.bgSize;
            }
            if (propData.positionX && propData.positionX.inputVal) {
                styleObject["background-position-x"] = propData.positionX.inputVal + propData.positionX.selectVal;
            }
            if (propData.positionY && propData.positionY.inputVal) {
                styleObject["background-position-y"] = propData.positionY.inputVal + propData.positionY.selectVal;
            }
            for (const key in propData) {
                if (propData.hasOwnProperty.call(propData, key)) {
                    const element = propData[key];
                    if (!element && element !== false && element != 0) {
                        continue;
                    }
                    switch (key) {
                        case "width":
                        case "height":
                            styleObject[key] = element;
                            break;
                        case "bgColor":
                            if (element.hex8) {
                                styleObject["background-color"] = element.hex8;
                            }
                            break;
                        case "box":
                            if (element.marginTopVal) {
                                styleObject["margin-top"] = `${element.marginTopVal}`;
                            }
                            if (element.marginRightVal) {
                                styleObject["margin-right"] = `${element.marginRightVal}`;
                            }
                            if (element.marginBottomVal) {
                                styleObject["margin-bottom"] = `${element.marginBottomVal}`;
                            }
                            if (element.marginLeftVal) {
                                styleObject["margin-left"] = `${element.marginLeftVal}`;
                            }
                            if (element.paddingTopVal) {
                                styleObject["padding-top"] = `${element.paddingTopVal}`;
                            }
                            if (element.paddingRightVal) {
                                styleObject["padding-right"] = `${element.paddingRightVal}`;
                            }
                            if (element.paddingBottomVal) {
                                styleObject["padding-bottom"] = `${element.paddingBottomVal}`;
                            }
                            if (element.paddingLeftVal) {
                                styleObject["padding-left"] = `${element.paddingLeftVal}`;
                            }
                            break;
                        case "bgImgUrl":
                            styleObject["background-image"] = `url(${IDM.url.getWebPath(element)})`;
                            break;
                        case "positionX":
                            //背景横向偏移

                            break;
                        case "positionY":
                            //背景纵向偏移

                            break;
                        case "bgRepeat":
                            //平铺模式
                            styleObject["background-repeat"] = element;
                            break;
                        case "bgAttachment":
                            //背景模式
                            styleObject["background-attachment"] = element;
                            break;
                        case "border":
                            if (element.border.top.width > 0) {
                                styleObject["border-top-width"] = element.border.top.width + element.border.top.widthUnit;
                                styleObject["border-top-style"] = element.border.top.style;
                                if (element.border.top.colors.hex8) {
                                    styleObject["border-top-color"] = element.border.top.colors.hex8;
                                }
                            }
                            if (element.border.right.width > 0) {
                                styleObject["border-right-width"] = element.border.right.width + element.border.right.widthUnit;
                                styleObject["border-right-style"] = element.border.right.style;
                                if (element.border.right.colors.hex8) {
                                    styleObject["border-right-color"] = element.border.right.colors.hex8;
                                }
                            }
                            if (element.border.bottom.width > 0) {
                                styleObject["border-bottom-width"] = element.border.bottom.width + element.border.bottom.widthUnit;
                                styleObject["border-bottom-style"] = element.border.bottom.style;
                                if (element.border.bottom.colors.hex8) {
                                    styleObject["border-bottom-color"] = element.border.bottom.colors.hex8;
                                }
                            }
                            if (element.border.left.width > 0) {
                                styleObject["border-left-width"] = element.border.left.width + element.border.left.widthUnit;
                                styleObject["border-left-style"] = element.border.left.style;
                                if (element.border.left.colors.hex8) {
                                    styleObject["border-left-color"] = element.border.left.colors.hex8;
                                }
                            }

                            styleObject["border-top-left-radius"] = element.radius.leftTop.radius + element.radius.leftTop.radiusUnit;
                            styleObject["border-top-right-radius"] = element.radius.rightTop.radius + element.radius.rightTop.radiusUnit;
                            styleObject["border-bottom-left-radius"] = element.radius.leftBottom.radius + element.radius.leftBottom.radiusUnit;
                            styleObject["border-bottom-right-radius"] = element.radius.rightBottom.radius + element.radius.rightBottom.radiusUnit;
                            break;
                        case "font":
                            styleObject["font-family"] = element.fontFamily;
                            if (element.fontColors.hex8) {
                                styleObject["color"] = element.fontColors.hex8;
                            }
                            styleObject["font-weight"] = element.fontWeight && element.fontWeight.split(" ")[0];
                            styleObject["font-style"] = element.fontStyle;
                            styleObject["font-size"] = element.fontSize + element.fontSizeUnit;
                            styleObject["line-height"] = element.fontLineHeight + (element.fontLineHeightUnit == "-" ? "" : element.fontLineHeightUnit);
                            styleObject["text-align"] = element.fontTextAlign;
                            styleObject["text-decoration"] = element.fontDecoration;
                            break;
                        case "layout":
                            styleObject["display"] = element.display;
                            if (element.display && element.display == "flex") {
                                if (element.direction) {
                                    styleObject["flex-direction"] = element.direction;
                                }
                                if (element.direction) {
                                    styleObject["align-items"] = element.align;
                                }
                                if (element.direction) {
                                    styleObject["justify-content"] = element.justify;
                                }
                            }
                            break;
                    }
                }
            }
            IDM.setStyleToPageHead(this.moduleObject.id + ` .drag_container[idm-ctrl-id="${this.moduleObject.id}"][idm-container-index="${index}"]`, styleObject);
        },
        /**
         * 加载基本属性
         */
        initBaseAttrToModule () {
            if (this.propData.TabPaneList && this.propData.TabPaneList.length > 0) {
                if (this.moduleObject.env != 'develop') {
                    this.propData.TabPaneList.forEach(tabItem => {
                        if (tabItem.defaultActiveKey) {
                            this.activeTab = tabItem.key;
                        }
                    })
                    if (!this.activeTab) {
                        this.activeTab = this.propData.TabPaneList[0].key
                    }
                }
                //设置初始化状态打开状态
                this.propData.TabPaneList.forEach((tabItem, index) => {
                    tabItem.opened = this.moduleObject.env == 'develop' ? true : this.activeTab == tabItem.key;
                    if (!tabItem.forceRender) {
                        tabItem.opened = true;
                    }
                })

                this.tabList = this.propData.TabPaneList;
            } else {
                this.tabList = [];
                if (this.moduleObject.env == undefined || this.moduleObject.env == 'develop') {
                    //开发模式下不执行此事件
                    this.tabList = [{
                        key: "123",
                        tab: "页签demo",
                        defaultActiveKey: true,
                        forceRender: false
                    }, {
                        key: "1233",
                        tab: "页签demo1",
                        defaultActiveKey: true,
                        forceRender: false
                    }];
                    if (!this.activeTab) {
                        this.activeTab = this.tabList[0].key
                    }
                }
            }
        },
        /**
         * 加载属性到组件中
         */
        initAttrToModule () {
            this.convertAttrToStyleObject();
            if (this.innerAttr && this.innerAttr.length > 0) {
                this.innerAttr.forEach(element => {
                    this.convertInnerAttrToStyleObject(element.dataAttr, element.containerIndex);
                });
            }
            this.initBaseAttrToModule();
        }
    }
}
</script>
<style lang="scss">
.idm_itabs_box {
    .ant-tabs-bar {
        margin: 0 0 0 0;
    }
}
</style>