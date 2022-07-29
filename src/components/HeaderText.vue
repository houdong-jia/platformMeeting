<template>
  <!--
    根目录规范(必须不能为空)：
    idm-ctrl：控件类型 drag_container：容器，drag_container_inlieblock：行内容器，idm_module：非容器的组件
    id：使用moduleObject.id，如果id不使用这个将会被idm-ctrl-id属性替换
    idm-ctrl-id：组件的id，这个必须不能为空
  -->
  <div
    idm-ctrl="idm_module"
    :id="moduleObject.id"
    :idm-ctrl-id="moduleObject.id"
    class="container"
  >
    <a-button
      :id="moduleObject.id + 'Btn1'"
      class="btn btn-left"
      @click="buttonClickHandle1"
      :loading="isLoading"
      :type="propData.Btn1buttonType"
      v-if="propData.Btn1defaultStatus != 'hidden'"
      :disabled="propData.Btn1defaultStatus == 'disabled'"
      :size="propData.Btn1size || 'default'"
    >
      <svg
        class="button-svg-icon LeftIcon"
        v-if="propData.Btn1icon && propData.Btn1icon.length > 0"
        aria-hidden="true"
      >
        <use :xlink:href="`#${propData.Btn1icon[0]}`"></use></svg
      >{{ propData.Btn1label }}
    </a-button>
    <div
      class="center-text"
      @click="textClickHandle"
      v-if="propData.defaultStatus != 'hidden'"
      :id="moduleObject.id + 'CenterText'"
    >
      {{ propData.fontContent }}
    </div>

    <a-button
      :id="moduleObject.id + 'Btn2'"
      class="btn btn-right"
      @click="buttonClickHandle2"
      :loading="isLoading"
      :type="propData.Btn2buttonType"
      v-if="propData.Btn2defaultStatus != 'hidden'"
      :disabled="propData.Btn2defaultStatus == 'disabled'"
      :size="propData.Btn2size || 'default'"
    >
      <span>{{ propData.Btn2label }}</span>
      <svg
        class="button-svg-icon RightIcon"
        v-if="propData.Btn2icon && propData.Btn2icon.length > 0"
        aria-hidden="true"
      >
        <use :xlink:href="`#${propData.Btn2icon[0]}`"></use>
      </svg>
    </a-button>
  </div>
</template>

<script>
export default {
  name: "HeaderText",
  data() {
    return {
      errorMessage: "",
      thisValue: "",
      moduleObject: {},
      propData: this.$root.propData.compositeAttr || {
        fontContent: "Hello Word",
        Btn1label: "按钮1",
        Btn2label: "按钮2",
        Btn1icon: ["idm-icon-shortcutscriptapp"],
      },
      isLoading: false,
      lastReceiveMessage: null,
    };
  },
  props: {},
  created() {
    this.moduleObject = this.$root.moduleObject;
    // console.log(this.moduleObject)
    this.convertAttrToStyleObject();
  },
  mounted() {
    //赋值给window提供跨页面调用
    this.$nextTick(function (params) {
      //单独组件不能使用这种方式
      // window[this.moduleObject.packageid] = this;
    });
  },
  destroyed() {},
  methods: {
    /**
     * 提供父级组件调用的刷新prop数据组件
     */
    propDataWatchHandle(propData) {
      this.propData = propData.compositeAttr || {};
      this.convertAttrToStyleObject();
    },
    /**
     * 把属性转换成样式对象
     */
    convertAttrToStyleObject() {
      var styleObject = {};
      if (this.propData.bgSize && this.propData.bgSize == "custom") {
        styleObject["background-size"] =
          (this.propData.bgSizeWidth
            ? this.propData.bgSizeWidth.inputVal +
              this.propData.bgSizeWidth.selectVal
            : "auto") +
          " " +
          (this.propData.bgSizeHeight
            ? this.propData.bgSizeHeight.inputVal +
              this.propData.bgSizeHeight.selectVal
            : "auto");
      } else if (this.propData.bgSize) {
        styleObject["background-size"] = this.propData.bgSize;
      }
      if (this.propData.positionX && this.propData.positionX.inputVal) {
        styleObject["background-position-x"] =
          this.propData.positionX.inputVal + this.propData.positionX.selectVal;
      }
      if (this.propData.positionY && this.propData.positionY.inputVal) {
        styleObject["background-position-y"] =
          this.propData.positionY.inputVal + this.propData.positionY.selectVal;
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
            case "HeaderBgColor":
              if (element && element.hex8) {
                window.IDM.setStyleToPageHead(this.moduleObject.id, {
                  "background-color": element.hex8,
                });
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
              styleObject[
                "background-image"
              ] = `url(${window.IDM.url.getWebPath(element)})`;
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
                styleObject["border-top-width"] =
                  element.border.top.width + element.border.top.widthUnit;
                styleObject["border-top-style"] = element.border.top.style;
                if (element.border.top.colors.hex8) {
                  styleObject["border-top-color"] =
                    element.border.top.colors.hex8;
                }
              }
              if (element.border.right.width > 0) {
                styleObject["border-right-width"] =
                  element.border.right.width + element.border.right.widthUnit;
                styleObject["border-right-style"] = element.border.right.style;
                if (element.border.right.colors.hex8) {
                  styleObject["border-right-color"] =
                    element.border.right.colors.hex8;
                }
              }
              if (element.border.bottom.width > 0) {
                styleObject["border-bottom-width"] =
                  element.border.bottom.width + element.border.bottom.widthUnit;
                styleObject["border-bottom-style"] =
                  element.border.bottom.style;
                if (element.border.bottom.colors.hex8) {
                  styleObject["border-bottom-color"] =
                    element.border.bottom.colors.hex8;
                }
              }
              if (element.border.left.width > 0) {
                styleObject["border-left-width"] =
                  element.border.left.width + element.border.left.widthUnit;
                styleObject["border-left-style"] = element.border.left.style;
                if (element.border.left.colors.hex8) {
                  styleObject["border-left-color"] =
                    element.border.left.colors.hex8;
                }
              }

              styleObject["border-top-left-radius"] =
                element.radius.leftTop.radius +
                element.radius.leftTop.radiusUnit;
              styleObject["border-top-right-radius"] =
                element.radius.rightTop.radius +
                element.radius.rightTop.radiusUnit;
              styleObject["border-bottom-left-radius"] =
                element.radius.leftBottom.radius +
                element.radius.leftBottom.radiusUnit;
              styleObject["border-bottom-right-radius"] =
                element.radius.rightBottom.radius +
                element.radius.rightBottom.radiusUnit;
              break;
            case "font":
              styleObject["font-family"] = element.fontFamily;
              if (element.fontColors.hex8) {
                styleObject["color"] = element.fontColors.hex8;
              }
              styleObject["font-weight"] =
                element.fontWeight && element.fontWeight.split(" ")[0];
              styleObject["font-style"] = element.fontStyle;
              styleObject["font-size"] =
                element.fontSize + element.fontSizeUnit;
              styleObject["line-height"] =
                element.fontLineHeight +
                (element.fontLineHeightUnit == "-"
                  ? ""
                  : element.fontLineHeightUnit);
              styleObject["text-align"] = element.fontTextAlign;
              styleObject["text-decoration"] = element.fontDecoration;
              break;
          }
        }
      }
      window.IDM.setStyleToPageHead(
        this.moduleObject.id + "CenterText",
        styleObject
      );
      this.initData();
      //默认值
      this.convertAttrToButtonBaseStyle();
      //样式设置下面四种状态:
      if (this.propData.Btn1buttonType == "customBtn1") {
        //默认
        this.convertAttrToButtonDefaultStyle();
        //hover
        this.convertAttrToButtonHoverStyle();
        //focus
        this.convertAttrToButtonFocusStyle();
        //active
        this.convertAttrToButtonActiveStyle();
      }

      if (this.propData.Btn2buttonType == "customBtn2") {
        //默认
        this.convertAttrToButtonDefaultStyle();
        //hover
        this.convertAttrToButtonHoverStyle();
        //focus
        this.convertAttrToButtonFocusStyle();
        //active
        this.convertAttrToButtonActiveStyle();
      }
      //图标、图标颜色
      this.convertAttrToButtonIconStyle();
    },
    /**
     * 通用的url参数对象
     * 所有地址的url参数转换
     */
    commonParam() {
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
     * 重新加载
     */
    reload() {
      //请求数据源
      this.initData();
    },
    /**
     * 加载动态数据
     */
    initData() {
      let that = this;
      //所有地址的url参数转换
      var params = that.commonParam();
      switch (this.propData.dataSourceType) {
        case "customInterface":
          this.propData.customInterfaceUrl &&
            window.IDM.http
              .get(this.propData.customInterfaceUrl, params)
              .then((res) => {
                //res.data
                that.$set(
                  that.propData,
                  "fontContent",
                  that.getExpressData(
                    "resultData",
                    that.propData.dataFiled,
                    res.data
                  )
                );
                // that.propData.fontContent = ;
              })
              .catch(function (error) {});
          break;
        case "pageCommonInterface":
          //使用通用接口直接跳过，在setContextValue执行
          break;
        case "customFunction":
          if (
            this.propData.customFunction &&
            this.propData.customFunction.length > 0
          ) {
            var resValue = "";
            try {
              resValue =
                window[this.propData.customFunction[0].name] &&
                window[this.propData.customFunction[0].name].call(this, {
                  ...params,
                  ...this.propData.customFunction[0].param,
                  moduleObject: this.moduleObject,
                });
            } catch (error) {}
            that.propData.fontContent = resValue;
          }
          break;
      }
    },
    /**
     * 通用的获取表达式匹配后的结果
     */
    getExpressData(dataName, dataFiled, resultData) {
      //给defaultValue设置dataFiled的值
      var _defaultVal = undefined;
      if (dataFiled) {
        var filedExp = dataFiled;
        filedExp = dataName + (filedExp.startsWiths("[") ? "" : ".") + filedExp;
        var dataObject = { IDM: window.IDM };
        dataObject[dataName] = resultData;
        _defaultVal = window.IDM.express.replace.call(
          this,
          "@[" + filedExp + "]",
          dataObject
        );
      }
      //对结果进行再次函数自定义
      if (
        this.propData.customFunction &&
        this.propData.customFunction.length > 0
      ) {
        var params = this.commonParam();
        var resValue = "";
        try {
          resValue =
            window[this.propData.customFunction[0].name] &&
            window[this.propData.customFunction[0].name].call(this, {
              ...params,
              ...this.propData.customFunction[0].param,
              moduleObject: this.moduleObject,
              expressData: _defaultVal,
              interfaceData: resultData,
            });
        } catch (error) {}
        _defaultVal = resValue;
      }

      return _defaultVal;
    },
    /**
     * 文本点击事件
     */
    textClickHandle() {
      let that = this;
      if (this.moduleObject.env == "develop") {
        //开发模式下不执行此事件
        return;
      }
      //获取所有的URL参数、页面ID（pageId）、以及所有组件的返回值（用范围值去调用IDM提供的方法取出所有的组件值）
      let urlObject = window.IDM.url.queryObject(),
        pageId =
          window.IDM.broadcast && window.IDM.broadcast.pageModule
            ? window.IDM.broadcast.pageModule.id
            : "";
      //自定义函数
      /**
       * [
       * {name:"",param:{}}
       * ]
       */
      var clickFunction = this.propData.clickFunction;
      clickFunction &&
        clickFunction.forEach((item) => {
          window[item.name] &&
            window[item.name].call(this, {
              urlData: urlObject,
              pageId,
              customParam: item.param,
              _this: this,
            });
        });
    },
    showThisModuleHandle() {
      this.propData.defaultStatus = "default";
    },
    hideThisModuleHandle() {
      this.propData.defaultStatus = "hidden";
    },

    //按钮1的方法
    /**
     * 把属性转换成按钮的样式设置(active)
     */
    convertAttrToButtonActiveStyle() {
      var styleObject = {};
      if (
        this.propData.Btn1bgSizeActive &&
        this.propData.Btn1bgSizeActive == "customBtn1"
      ) {
        styleObject["background-size"] =
          (this.propData.Btn1bgSizeWidthActive
            ? this.propData.Btn1bgSizeWidthActive.inputVal +
              this.propData.Btn1bgSizeWidthActive.selectVal
            : "auto") +
          " " +
          (this.propData.Btn1bgSizeHeightActive
            ? this.propData.Btn1bgSizeHeightActive.inputVal +
              this.propData.Btn1bgSizeHeightActive.selectVal
            : "auto");
      } else if (this.propData.Btn1bgSizeActive) {
        styleObject["background-size"] = this.propData.Btn1bgSizeActive;
      }
      if (
        this.propData.Btn1positionXActive &&
        this.propData.Btn1positionXActive.inputVal
      ) {
        styleObject["background-position-x"] =
          this.propData.Btn1positionXActive.inputVal +
          this.propData.Btn1positionXActive.selectVal;
      }
      if (
        this.propData.Btn1positionYActive &&
        this.propData.Btn1positionYActive.inputVal
      ) {
        styleObject["background-position-y"] =
          this.propData.Btn1positionYActive.inputVal +
          this.propData.Btn1positionYActive.selectVal;
      }
      for (const key in this.propData) {
        if (this.propData.hasOwnProperty.call(this.propData, key)) {
          const element = this.propData[key];
          if (!element && element !== false && element != 0) {
            continue;
          }
          switch (key) {
            case "Btn1bgColorActive":
              if (element && element.hex8) {
                styleObject["background-color"] = IDM.hex8ToRgbaString(
                  element.hex8
                );
              }
              break;
            case "Btn1bgImgUrlActive":
              styleObject[
                "background-image"
              ] = `url(${window.IDM.url.getWebPath(element)})`;
              break;
            case "Btn1positionXActive":
              //背景横向偏移

              break;
            case "Btn1positionYActive":
              //背景纵向偏移

              break;
            case "Btn1bgRepeatActive":
              //平铺模式
              styleObject["background-repeat"] = element;
              break;
            case "Btn1bgAttachmentActive":
              //背景模式
              styleObject["background-attachment"] = element;
              break;
            case "Btn1borderActive":
              if (element.border.top.width > 0) {
                styleObject["border-top-width"] =
                  element.border.top.width + element.border.top.widthUnit;
                styleObject["border-top-style"] = element.border.top.style;
                if (element.border.top.colors.hex8) {
                  styleObject["border-top-color"] = IDM.hex8ToRgbaString(
                    element.border.top.colors.hex8
                  );
                }
              }
              if (element.border.right.width > 0) {
                styleObject["border-right-width"] =
                  element.border.right.width + element.border.right.widthUnit;
                styleObject["border-right-style"] = element.border.right.style;
                if (element.border.right.colors.hex8) {
                  styleObject["border-right-color"] = IDM.hex8ToRgbaString(
                    element.border.right.colors.hex8
                  );
                }
              }
              if (element.border.bottom.width > 0) {
                styleObject["border-bottom-width"] =
                  element.border.bottom.width + element.border.bottom.widthUnit;
                styleObject["border-bottom-style"] =
                  element.border.bottom.style;
                if (element.border.bottom.colors.hex8) {
                  styleObject["border-bottom-color"] = IDM.hex8ToRgbaString(
                    element.border.bottom.colors.hex8
                  );
                }
              }
              if (element.border.left.width > 0) {
                styleObject["border-left-width"] =
                  element.border.left.width + element.border.left.widthUnit;
                styleObject["border-left-style"] = element.border.left.style;
                if (element.border.left.colors.hex8) {
                  styleObject["border-left-color"] = IDM.hex8ToRgbaString(
                    element.border.left.colors.hex8
                  );
                }
              }

              styleObject["border-top-left-radius"] =
                element.radius.leftTop.radius +
                element.radius.leftTop.radiusUnit;
              styleObject["border-top-right-radius"] =
                element.radius.rightTop.radius +
                element.radius.rightTop.radiusUnit;
              styleObject["border-bottom-left-radius"] =
                element.radius.leftBottom.radius +
                element.radius.leftBottom.radiusUnit;
              styleObject["border-bottom-right-radius"] =
                element.radius.rightBottom.radius +
                element.radius.rightBottom.radiusUnit;
              break;
            case "Btn1fontActive":
              styleObject["font-family"] = element.fontFamily;
              if (element.fontColors.hex8) {
                styleObject["color"] = IDM.hex8ToRgbaString(
                  element.fontColors.hex8
                );
              }
              styleObject["font-weight"] =
                element.fontWeight && element.fontWeight.split(" ")[0];
              styleObject["font-style"] = element.fontStyle;
              styleObject["font-size"] =
                element.fontSize + element.fontSizeUnit;
              styleObject["line-height"] =
                element.fontLineHeight +
                (element.fontLineHeightUnit == "-"
                  ? ""
                  : element.fontLineHeightUnit);
              styleObject["text-align"] = element.fontTextAlign;
              styleObject["text-decoration"] = element.fontDecoration;
              if (element.fontSize && element.fontSizeUnit) {
                window.IDM.setStyleToPageHead(
                  this.moduleObject.id + "Btn1:active .button-svg-icon",
                  {
                    "font-size": element.fontSize + element.fontSizeUnit,
                    "max-height": element.fontSize + element.fontSizeUnit,
                    width: element.fontSize + element.fontSizeUnit,
                  }
                );
              }
              break;
          }
        }
      }
      window.IDM.setStyleToPageHead(
        this.moduleObject.id + "Btn1:active",
        styleObject
      );

      styleObject = {};
      if (
        this.propData.Btn2bgSizeActive &&
        this.propData.Btn2bgSizeActive == "customBtn2"
      ) {
        styleObject["background-size"] =
          (this.propData.Btn2bgSizeWidthActive
            ? this.propData.Btn2bgSizeWidthActive.inputVal +
              this.propData.Btn2bgSizeWidthActive.selectVal
            : "auto") +
          " " +
          (this.propData.Btn2bgSizeHeightActive
            ? this.propData.Btn2bgSizeHeightActive.inputVal +
              this.propData.Btn2bgSizeHeightActive.selectVal
            : "auto");
      } else if (this.propData.Btn2bgSizeActive) {
        styleObject["background-size"] = this.propData.Btn2bgSizeActive;
      }
      if (
        this.propData.Btn2positionXActive &&
        this.propData.Btn2positionXActive.inputVal
      ) {
        styleObject["background-position-x"] =
          this.propData.Btn2positionXActive.inputVal +
          this.propData.Btn2positionXActive.selectVal;
      }
      if (
        this.propData.Btn2positionYActive &&
        this.propData.Btn2positionYActive.inputVal
      ) {
        styleObject["background-position-y"] =
          this.propData.Btn2positionYActive.inputVal +
          this.propData.Btn2positionYActive.selectVal;
      }
      for (const key in this.propData) {
        if (this.propData.hasOwnProperty.call(this.propData, key)) {
          const element = this.propData[key];
          if (!element && element !== false && element != 0) {
            continue;
          }
          switch (key) {
            case "Btn2bgColorActive":
              if (element && element.hex8) {
                styleObject["background-color"] = IDM.hex8ToRgbaString(
                  element.hex8
                );
              }
              break;
            case "Btn2bgImgUrlActive":
              styleObject[
                "background-image"
              ] = `url(${window.IDM.url.getWebPath(element)})`;
              break;
            case "Btn2positionXActive":
              //背景横向偏移

              break;
            case "Btn2positionYActive":
              //背景纵向偏移

              break;
            case "Btn2bgRepeatActive":
              //平铺模式
              styleObject["background-repeat"] = element;
              break;
            case "Btn2bgAttachmentActive":
              //背景模式
              styleObject["background-attachment"] = element;
              break;
            case "Btn2borderActive":
              if (element.border.top.width > 0) {
                styleObject["border-top-width"] =
                  element.border.top.width + element.border.top.widthUnit;
                styleObject["border-top-style"] = element.border.top.style;
                if (element.border.top.colors.hex8) {
                  styleObject["border-top-color"] = IDM.hex8ToRgbaString(
                    element.border.top.colors.hex8
                  );
                }
              }
              if (element.border.right.width > 0) {
                styleObject["border-right-width"] =
                  element.border.right.width + element.border.right.widthUnit;
                styleObject["border-right-style"] = element.border.right.style;
                if (element.border.right.colors.hex8) {
                  styleObject["border-right-color"] = IDM.hex8ToRgbaString(
                    element.border.right.colors.hex8
                  );
                }
              }
              if (element.border.bottom.width > 0) {
                styleObject["border-bottom-width"] =
                  element.border.bottom.width + element.border.bottom.widthUnit;
                styleObject["border-bottom-style"] =
                  element.border.bottom.style;
                if (element.border.bottom.colors.hex8) {
                  styleObject["border-bottom-color"] = IDM.hex8ToRgbaString(
                    element.border.bottom.colors.hex8
                  );
                }
              }
              if (element.border.left.width > 0) {
                styleObject["border-left-width"] =
                  element.border.left.width + element.border.left.widthUnit;
                styleObject["border-left-style"] = element.border.left.style;
                if (element.border.left.colors.hex8) {
                  styleObject["border-left-color"] = IDM.hex8ToRgbaString(
                    element.border.left.colors.hex8
                  );
                }
              }

              styleObject["border-top-left-radius"] =
                element.radius.leftTop.radius +
                element.radius.leftTop.radiusUnit;
              styleObject["border-top-right-radius"] =
                element.radius.rightTop.radius +
                element.radius.rightTop.radiusUnit;
              styleObject["border-bottom-left-radius"] =
                element.radius.leftBottom.radius +
                element.radius.leftBottom.radiusUnit;
              styleObject["border-bottom-right-radius"] =
                element.radius.rightBottom.radius +
                element.radius.rightBottom.radiusUnit;
              break;
            case "Btn2fontActive":
              styleObject["font-family"] = element.fontFamily;
              if (element.fontColors.hex8) {
                styleObject["color"] = IDM.hex8ToRgbaString(
                  element.fontColors.hex8
                );
              }
              styleObject["font-weight"] =
                element.fontWeight && element.fontWeight.split(" ")[0];
              styleObject["font-style"] = element.fontStyle;
              styleObject["font-size"] =
                element.fontSize + element.fontSizeUnit;
              styleObject["line-height"] =
                element.fontLineHeight +
                (element.fontLineHeightUnit == "-"
                  ? ""
                  : element.fontLineHeightUnit);
              styleObject["text-align"] = element.fontTextAlign;
              styleObject["text-decoration"] = element.fontDecoration;
              if (element.fontSize && element.fontSizeUnit) {
                window.IDM.setStyleToPageHead(
                  this.moduleObject.id + "Btn2:active .button-svg-icon",
                  {
                    "font-size": element.fontSize + element.fontSizeUnit,
                    "max-height": element.fontSize + element.fontSizeUnit,
                    width: element.fontSize + element.fontSizeUnit,
                  }
                );
              }
              break;
          }
        }
      }
      window.IDM.setStyleToPageHead(
        this.moduleObject.id + "Btn2:active",
        styleObject
      );
    },
    /**
     * 把属性转换成按钮的样式设置(hover)
     */
    convertAttrToButtonHoverStyle() {
      var styleObject = {};
      if (
        this.propData.Btn1bgSizeHover &&
        this.propData.Btn1bgSizeHover == "customBtn1"
      ) {
        styleObject["background-size"] =
          (this.propData.Btn1bgSizeWidthHover
            ? this.propData.Btn1bgSizeWidthHover.inputVal +
              this.propData.Btn1bgSizeWidthHover.selectVal
            : "auto") +
          " " +
          (this.propData.Btn1bgSizeHeightHover
            ? this.propData.Btn1bgSizeHeightHover.inputVal +
              this.propData.Btn1bgSizeHeightHover.selectVal
            : "auto");
      } else if (this.propData.Btn1bgSizeHover) {
        styleObject["background-size"] = this.propData.Btn1bgSizeHover;
      }
      if (
        this.propData.Btn1positionXHover &&
        this.propData.Btn1positionXHover.inputVal
      ) {
        styleObject["background-position-x"] =
          this.propData.Btn1positionXHover.inputVal +
          this.propData.Btn1positionXHover.selectVal;
      }
      if (
        this.propData.Btn1positionYHover &&
        this.propData.Btn1positionYHover.inputVal
      ) {
        styleObject["background-position-y"] =
          this.propData.Btn1positionYHover.inputVal +
          this.propData.Btn1positionYHover.selectVal;
      }
      for (const key in this.propData) {
        if (this.propData.hasOwnProperty.call(this.propData, key)) {
          const element = this.propData[key];
          if (!element && element !== false && element != 0) {
            continue;
          }
          switch (key) {
            case "Btn1bgColorHover":
              if (element && element.hex8) {
                styleObject["background-color"] = IDM.hex8ToRgbaString(
                  element.hex8
                );
              }
              break;
            case "Btn1bgImgUrlHover":
              styleObject[
                "background-image"
              ] = `url(${window.IDM.url.getWebPath(element)})`;
              break;
            case "Btn1positionXHover":
              //背景横向偏移

              break;
            case "Btn1positionYHover":
              //背景纵向偏移

              break;
            case "Btn1bgRepeatHover":
              //平铺模式
              styleObject["background-repeat"] = element;
              break;
            case "Btn1bgAttachmentHover":
              //背景模式
              styleObject["background-attachment"] = element;
              break;
            case "Btn1borderHover":
              if (element.border.top.width > 0) {
                styleObject["border-top-width"] =
                  element.border.top.width + element.border.top.widthUnit;
                styleObject["border-top-style"] = element.border.top.style;
                if (element.border.top.colors.hex8) {
                  styleObject["border-top-color"] = IDM.hex8ToRgbaString(
                    element.border.top.colors.hex8
                  );
                }
              }
              if (element.border.right.width > 0) {
                styleObject["border-right-width"] =
                  element.border.right.width + element.border.right.widthUnit;
                styleObject["border-right-style"] = element.border.right.style;
                if (element.border.right.colors.hex8) {
                  styleObject["border-right-color"] = IDM.hex8ToRgbaString(
                    element.border.right.colors.hex8
                  );
                }
              }
              if (element.border.bottom.width > 0) {
                styleObject["border-bottom-width"] =
                  element.border.bottom.width + element.border.bottom.widthUnit;
                styleObject["border-bottom-style"] =
                  element.border.bottom.style;
                if (element.border.bottom.colors.hex8) {
                  styleObject["border-bottom-color"] = IDM.hex8ToRgbaString(
                    element.border.bottom.colors.hex8
                  );
                }
              }
              if (element.border.left.width > 0) {
                styleObject["border-left-width"] =
                  element.border.left.width + element.border.left.widthUnit;
                styleObject["border-left-style"] = element.border.left.style;
                if (element.border.left.colors.hex8) {
                  styleObject["border-left-color"] = IDM.hex8ToRgbaString(
                    element.border.left.colors.hex8
                  );
                }
              }

              styleObject["border-top-left-radius"] =
                element.radius.leftTop.radius +
                element.radius.leftTop.radiusUnit;
              styleObject["border-top-right-radius"] =
                element.radius.rightTop.radius +
                element.radius.rightTop.radiusUnit;
              styleObject["border-bottom-left-radius"] =
                element.radius.leftBottom.radius +
                element.radius.leftBottom.radiusUnit;
              styleObject["border-bottom-right-radius"] =
                element.radius.rightBottom.radius +
                element.radius.rightBottom.radiusUnit;
              break;
            case "Btn1fontHover":
              styleObject["font-family"] = element.fontFamily;
              if (element.fontColors.hex8) {
                styleObject["color"] = IDM.hex8ToRgbaString(
                  element.fontColors.hex8
                );
              }
              styleObject["font-weight"] =
                element.fontWeight && element.fontWeight.split(" ")[0];
              styleObject["font-style"] = element.fontStyle;
              styleObject["font-size"] =
                element.fontSize + element.fontSizeUnit;
              styleObject["line-height"] =
                element.fontLineHeight +
                (element.fontLineHeightUnit == "-"
                  ? ""
                  : element.fontLineHeightUnit);
              styleObject["text-align"] = element.fontTextAlign;
              styleObject["text-decoration"] = element.fontDecoration;
              if (element.fontSize && element.fontSizeUnit) {
                window.IDM.setStyleToPageHead(
                  this.moduleObject.id + "Btn1:hover .button-svg-icon",
                  {
                    "font-size": element.fontSize + element.fontSizeUnit,
                    "max-height": element.fontSize + element.fontSizeUnit,
                    width: element.fontSize + element.fontSizeUnit,
                  }
                );
              }
              break;
          }
        }
      }
      window.IDM.setStyleToPageHead(
        this.moduleObject.id + "Btn1:hover",
        styleObject
      );

      styleObject = {};
      if (
        this.propData.Btn2bgSizeHover &&
        this.propData.Btn2bgSizeHover == "customBtn2"
      ) {
        styleObject["background-size"] =
          (this.propData.Btn2bgSizeWidthHover
            ? this.propData.Btn2bgSizeWidthHover.inputVal +
              this.propData.Btn2bgSizeWidthHover.selectVal
            : "auto") +
          " " +
          (this.propData.Btn2bgSizeHeightHover
            ? this.propData.Btn2bgSizeHeightHover.inputVal +
              this.propData.Btn2bgSizeHeightHover.selectVal
            : "auto");
      } else if (this.propData.Btn2bgSizeHover) {
        styleObject["background-size"] = this.propData.Btn2bgSizeHover;
      }
      if (
        this.propData.Btn2positionXHover &&
        this.propData.Btn2positionXHover.inputVal
      ) {
        styleObject["background-position-x"] =
          this.propData.Btn2positionXHover.inputVal +
          this.propData.Btn2positionXHover.selectVal;
      }
      if (
        this.propData.Btn2positionYHover &&
        this.propData.Btn2positionYHover.inputVal
      ) {
        styleObject["background-position-y"] =
          this.propData.Btn2positionYHover.inputVal +
          this.propData.Btn2positionYHover.selectVal;
      }
      for (const key in this.propData) {
        if (this.propData.hasOwnProperty.call(this.propData, key)) {
          const element = this.propData[key];
          if (!element && element !== false && element != 0) {
            continue;
          }
          switch (key) {
            case "Btn2bgColorHover":
              if (element && element.hex8) {
                styleObject["background-color"] = IDM.hex8ToRgbaString(
                  element.hex8
                );
              }
              break;
            case "Btn2bgImgUrlHover":
              styleObject[
                "background-image"
              ] = `url(${window.IDM.url.getWebPath(element)})`;
              break;
            case "Btn2positionXHover":
              //背景横向偏移

              break;
            case "Btn2positionYHover":
              //背景纵向偏移

              break;
            case "Btn2bgRepeatHover":
              //平铺模式
              styleObject["background-repeat"] = element;
              break;
            case "Btn2bgAttachmentHover":
              //背景模式
              styleObject["background-attachment"] = element;
              break;
            case "Btn2borderHover":
              if (element.border.top.width > 0) {
                styleObject["border-top-width"] =
                  element.border.top.width + element.border.top.widthUnit;
                styleObject["border-top-style"] = element.border.top.style;
                if (element.border.top.colors.hex8) {
                  styleObject["border-top-color"] = IDM.hex8ToRgbaString(
                    element.border.top.colors.hex8
                  );
                }
              }
              if (element.border.right.width > 0) {
                styleObject["border-right-width"] =
                  element.border.right.width + element.border.right.widthUnit;
                styleObject["border-right-style"] = element.border.right.style;
                if (element.border.right.colors.hex8) {
                  styleObject["border-right-color"] = IDM.hex8ToRgbaString(
                    element.border.right.colors.hex8
                  );
                }
              }
              if (element.border.bottom.width > 0) {
                styleObject["border-bottom-width"] =
                  element.border.bottom.width + element.border.bottom.widthUnit;
                styleObject["border-bottom-style"] =
                  element.border.bottom.style;
                if (element.border.bottom.colors.hex8) {
                  styleObject["border-bottom-color"] = IDM.hex8ToRgbaString(
                    element.border.bottom.colors.hex8
                  );
                }
              }
              if (element.border.left.width > 0) {
                styleObject["border-left-width"] =
                  element.border.left.width + element.border.left.widthUnit;
                styleObject["border-left-style"] = element.border.left.style;
                if (element.border.left.colors.hex8) {
                  styleObject["border-left-color"] = IDM.hex8ToRgbaString(
                    element.border.left.colors.hex8
                  );
                }
              }

              styleObject["border-top-left-radius"] =
                element.radius.leftTop.radius +
                element.radius.leftTop.radiusUnit;
              styleObject["border-top-right-radius"] =
                element.radius.rightTop.radius +
                element.radius.rightTop.radiusUnit;
              styleObject["border-bottom-left-radius"] =
                element.radius.leftBottom.radius +
                element.radius.leftBottom.radiusUnit;
              styleObject["border-bottom-right-radius"] =
                element.radius.rightBottom.radius +
                element.radius.rightBottom.radiusUnit;
              break;
            case "Btn2fontHover":
              styleObject["font-family"] = element.fontFamily;
              if (element.fontColors.hex8) {
                styleObject["color"] = IDM.hex8ToRgbaString(
                  element.fontColors.hex8
                );
              }
              styleObject["font-weight"] =
                element.fontWeight && element.fontWeight.split(" ")[0];
              styleObject["font-style"] = element.fontStyle;
              styleObject["font-size"] =
                element.fontSize + element.fontSizeUnit;
              styleObject["line-height"] =
                element.fontLineHeight +
                (element.fontLineHeightUnit == "-"
                  ? ""
                  : element.fontLineHeightUnit);
              styleObject["text-align"] = element.fontTextAlign;
              styleObject["text-decoration"] = element.fontDecoration;
              if (element.fontSize && element.fontSizeUnit) {
                window.IDM.setStyleToPageHead(
                  this.moduleObject.id + "Btn2:hover .button-svg-icon",
                  {
                    "font-size": element.fontSize + element.fontSizeUnit,
                    "max-height": element.fontSize + element.fontSizeUnit,
                    width: element.fontSize + element.fontSizeUnit,
                  }
                );
              }
              break;
          }
        }
      }
      window.IDM.setStyleToPageHead(
        this.moduleObject.id + "Btn2:hover",
        styleObject
      );
    },
    /**
     * 把属性转换成按钮的样式设置(默认)
     */
    convertAttrToButtonDefaultStyle() {
      var styleObject = {};
      if (
        this.propData.Btn1bgSizeDefault &&
        this.propData.Btn1bgSizeDefault == "customBtn1"
      ) {
        styleObject["background-size"] =
          (this.propData.Btn1bgSizeWidthDefault
            ? this.propData.Btn1bgSizeWidthDefault.inputVal +
              this.propData.Btn1bgSizeWidthDefault.selectVal
            : "auto") +
          " " +
          (this.propData.Btn1bgSizeHeightDefault
            ? this.propData.Btn1bgSizeHeightDefault.inputVal +
              this.propData.Btn1bgSizeHeightDefault.selectVal
            : "auto");
      } else if (this.propData.Btn1bgSizeDefault) {
        styleObject["background-size"] = this.propData.Btn1bgSizeDefault;
      }
      if (
        this.propData.Btn1positionXDefault &&
        this.propData.Btn1positionXDefault.inputVal
      ) {
        styleObject["background-position-x"] =
          this.propData.Btn1positionXDefault.inputVal +
          this.propData.Btn1positionXDefault.selectVal;
      }
      if (
        this.propData.Btn1positionYDefault &&
        this.propData.Btn1positionYDefault.inputVal
      ) {
        styleObject["background-position-y"] =
          this.propData.Btn1positionYDefault.inputVal +
          this.propData.Btn1positionYDefault.selectVal;
      }
      for (const key in this.propData) {
        if (this.propData.hasOwnProperty.call(this.propData, key)) {
          const element = this.propData[key];
          if (!element && element !== false && element != 0) {
            continue;
          }
          switch (key) {
            case "Btn1bgColorDefault":
              if (element && element.hex8) {
                styleObject["background-color"] = IDM.hex8ToRgbaString(
                  element.hex8
                );
              }
              break;
            case "Btn1bgImgUrlDefault":
              styleObject[
                "background-image"
              ] = `url(${window.IDM.url.getWebPath(element)})`;
              break;
            case "Btn1positionXDefault":
              //背景横向偏移

              break;
            case "Btn1positionYDefault":
              //背景纵向偏移

              break;
            case "Btn1bgRepeatDefault":
              //平铺模式
              styleObject["background-repeat"] = element;
              break;
            case "Btn1bgAttachmentDefault":
              //背景模式
              styleObject["background-attachment"] = element;
              break;
            case "Btn1borderDefault":
              if (element.border.top.width > 0) {
                styleObject["border-top-width"] =
                  element.border.top.width + element.border.top.widthUnit;
                styleObject["border-top-style"] = element.border.top.style;
                if (element.border.top.colors.hex8) {
                  styleObject["border-top-color"] = IDM.hex8ToRgbaString(
                    element.border.top.colors.hex8
                  );
                }
              }
              if (element.border.right.width > 0) {
                styleObject["border-right-width"] =
                  element.border.right.width + element.border.right.widthUnit;
                styleObject["border-right-style"] = element.border.right.style;
                if (element.border.right.colors.hex8) {
                  styleObject["border-right-color"] = IDM.hex8ToRgbaString(
                    element.border.right.colors.hex8
                  );
                }
              }
              if (element.border.bottom.width > 0) {
                styleObject["border-bottom-width"] =
                  element.border.bottom.width + element.border.bottom.widthUnit;
                styleObject["border-bottom-style"] =
                  element.border.bottom.style;
                if (element.border.bottom.colors.hex8) {
                  styleObject["border-bottom-color"] = IDM.hex8ToRgbaString(
                    element.border.bottom.colors.hex8
                  );
                }
              }
              if (element.border.left.width > 0) {
                styleObject["border-left-width"] =
                  element.border.left.width + element.border.left.widthUnit;
                styleObject["border-left-style"] = element.border.left.style;
                if (element.border.left.colors.hex8) {
                  styleObject["border-left-color"] = IDM.hex8ToRgbaString(
                    element.border.left.colors.hex8
                  );
                }
              }

              styleObject["border-top-left-radius"] =
                element.radius.leftTop.radius +
                element.radius.leftTop.radiusUnit;
              styleObject["border-top-right-radius"] =
                element.radius.rightTop.radius +
                element.radius.rightTop.radiusUnit;
              styleObject["border-bottom-left-radius"] =
                element.radius.leftBottom.radius +
                element.radius.leftBottom.radiusUnit;
              styleObject["border-bottom-right-radius"] =
                element.radius.rightBottom.radius +
                element.radius.rightBottom.radiusUnit;
              break;
            case "Btn1fontDefault":
              styleObject["font-family"] = element.fontFamily;
              if (element.fontColors.hex8) {
                styleObject["color"] = IDM.hex8ToRgbaString(
                  element.fontColors.hex8
                );
              }
              styleObject["font-weight"] =
                element.fontWeight && element.fontWeight.split(" ")[0];
              styleObject["font-style"] = element.fontStyle;
              styleObject["font-size"] =
                element.fontSize + element.fontSizeUnit;
              styleObject["line-height"] =
                element.fontLineHeight +
                (element.fontLineHeightUnit == "-"
                  ? ""
                  : element.fontLineHeightUnit);
              styleObject["text-align"] = element.fontTextAlign;
              styleObject["text-decoration"] = element.fontDecoration;
              if (element.fontSize && element.fontSizeUnit) {
                window.IDM.setStyleToPageHead(
                  this.moduleObject.id + "Btn1 .button-svg-icon",
                  {
                    "font-size": element.fontSize + element.fontSizeUnit,
                    "max-height": element.fontSize + element.fontSizeUnit,
                    width: element.fontSize + element.fontSizeUnit,
                  }
                );
              }
              break;
          }
        }
      }
      window.IDM.setStyleToPageHead(
        this.moduleObject.id + "Btn1,.emptyclassname",
        styleObject
      );

      styleObject = {};
      if (
        this.propData.Btn2bgSizeDefault &&
        this.propData.Btn2bgSizeDefault == "customBtn2"
      ) {
        styleObject["background-size"] =
          (this.propData.Btn2bgSizeWidthDefault
            ? this.propData.Btn2bgSizeWidthDefault.inputVal +
              this.propData.Btn2bgSizeWidthDefault.selectVal
            : "auto") +
          " " +
          (this.propData.Btn2bgSizeHeightDefault
            ? this.propData.Btn2bgSizeHeightDefault.inputVal +
              this.propData.Btn2bgSizeHeightDefault.selectVal
            : "auto");
      } else if (this.propData.Btn2bgSizeDefault) {
        styleObject["background-size"] = this.propData.Btn2bgSizeDefault;
      }
      if (
        this.propData.Btn2positionXDefault &&
        this.propData.Btn2positionXDefault.inputVal
      ) {
        styleObject["background-position-x"] =
          this.propData.Btn2positionXDefault.inputVal +
          this.propData.Btn2positionXDefault.selectVal;
      }
      if (
        this.propData.Btn2positionYDefault &&
        this.propData.Btn2positionYDefault.inputVal
      ) {
        styleObject["background-position-y"] =
          this.propData.Btn2positionYDefault.inputVal +
          this.propData.Btn2positionYDefault.selectVal;
      }
      for (const key in this.propData) {
        if (this.propData.hasOwnProperty.call(this.propData, key)) {
          const element = this.propData[key];
          if (!element && element !== false && element != 0) {
            continue;
          }
          switch (key) {
            case "Btn2bgColorDefault":
              if (element && element.hex8) {
                styleObject["background-color"] = IDM.hex8ToRgbaString(
                  element.hex8
                );
              }
              break;
            case "Btn2bgImgUrlDefault":
              styleObject[
                "background-image"
              ] = `url(${window.IDM.url.getWebPath(element)})`;
              break;
            case "Btn2positionXDefault":
              //背景横向偏移

              break;
            case "Btn2positionYDefault":
              //背景纵向偏移

              break;
            case "Btn2bgRepeatDefault":
              //平铺模式
              styleObject["background-repeat"] = element;
              break;
            case "Btn2bgAttachmentDefault":
              //背景模式
              styleObject["background-attachment"] = element;
              break;
            case "Btn2borderDefault":
              if (element.border.top.width > 0) {
                styleObject["border-top-width"] =
                  element.border.top.width + element.border.top.widthUnit;
                styleObject["border-top-style"] = element.border.top.style;
                if (element.border.top.colors.hex8) {
                  styleObject["border-top-color"] = IDM.hex8ToRgbaString(
                    element.border.top.colors.hex8
                  );
                }
              }
              if (element.border.right.width > 0) {
                styleObject["border-right-width"] =
                  element.border.right.width + element.border.right.widthUnit;
                styleObject["border-right-style"] = element.border.right.style;
                if (element.border.right.colors.hex8) {
                  styleObject["border-right-color"] = IDM.hex8ToRgbaString(
                    element.border.right.colors.hex8
                  );
                }
              }
              if (element.border.bottom.width > 0) {
                styleObject["border-bottom-width"] =
                  element.border.bottom.width + element.border.bottom.widthUnit;
                styleObject["border-bottom-style"] =
                  element.border.bottom.style;
                if (element.border.bottom.colors.hex8) {
                  styleObject["border-bottom-color"] = IDM.hex8ToRgbaString(
                    element.border.bottom.colors.hex8
                  );
                }
              }
              if (element.border.left.width > 0) {
                styleObject["border-left-width"] =
                  element.border.left.width + element.border.left.widthUnit;
                styleObject["border-left-style"] = element.border.left.style;
                if (element.border.left.colors.hex8) {
                  styleObject["border-left-color"] = IDM.hex8ToRgbaString(
                    element.border.left.colors.hex8
                  );
                }
              }

              styleObject["border-top-left-radius"] =
                element.radius.leftTop.radius +
                element.radius.leftTop.radiusUnit;
              styleObject["border-top-right-radius"] =
                element.radius.rightTop.radius +
                element.radius.rightTop.radiusUnit;
              styleObject["border-bottom-left-radius"] =
                element.radius.leftBottom.radius +
                element.radius.leftBottom.radiusUnit;
              styleObject["border-bottom-right-radius"] =
                element.radius.rightBottom.radius +
                element.radius.rightBottom.radiusUnit;
              break;
            case "Btn2fontDefault":
              styleObject["font-family"] = element.fontFamily;
              if (element.fontColors.hex8) {
                styleObject["color"] = IDM.hex8ToRgbaString(
                  element.fontColors.hex8
                );
              }
              styleObject["font-weight"] =
                element.fontWeight && element.fontWeight.split(" ")[0];
              styleObject["font-style"] = element.fontStyle;
              styleObject["font-size"] =
                element.fontSize + element.fontSizeUnit;
              styleObject["line-height"] =
                element.fontLineHeight +
                (element.fontLineHeightUnit == "-"
                  ? ""
                  : element.fontLineHeightUnit);
              styleObject["text-align"] = element.fontTextAlign;
              styleObject["text-decoration"] = element.fontDecoration;
              if (element.fontSize && element.fontSizeUnit) {
                window.IDM.setStyleToPageHead(
                  this.moduleObject.id + "Btn2 .button-svg-icon",
                  {
                    "font-size": element.fontSize + element.fontSizeUnit,
                    "max-height": element.fontSize + element.fontSizeUnit,
                    width: element.fontSize + element.fontSizeUnit,
                  }
                );
              }
              break;
          }
        }
      }
      window.IDM.setStyleToPageHead(
        this.moduleObject.id + "Btn2,.emptyclassname",
        styleObject
      );
    },
    /**
     * 把属性转换成按钮的样式设置(图标)
     */
    convertAttrToButtonIconStyle() {
      var styleObject = {};
      for (const key in this.propData) {
        if (this.propData.hasOwnProperty.call(this.propData, key)) {
          const element = this.propData[key];
          if (!element && element !== false && element != 0) {
            continue;
          }
          switch (key) {
            case "Btn1iconColor":
              if (element && element.hex8) {
                styleObject["color"] = IDM.hex8ToRgbaString(element.hex8);
              }
              break;
            case "Btn1iconBox":
              if (element && element.inputVal) {
                window.IDM.setStyleToPageHead(
                  this.moduleObject.id + "Btn1 svg",
                  {
                    height: element.inputVal + element.selectVal,
                    width: element.inputVal + element.selectVal,
                    "max-height": element.inputVal + element.selectVal,
                  }
                );
              }
              styleObject["height"] = element.inputVal + element.selectVal;
              styleObject["width"] = element.inputVal + element.selectVal;
              styleObject["max-height"] = element.inputVal + element.selectVal;
              break;
          }
        }
      }
      window.IDM.setStyleToPageHead(
        this.moduleObject.id + "Btn1 .button-svg-icon",
        styleObject
      );

      styleObject = {};
      for (const key in this.propData) {
        if (this.propData.hasOwnProperty.call(this.propData, key)) {
          const element = this.propData[key];
          if (!element && element !== false && element != 0) {
            continue;
          }
          switch (key) {
            case "Btn2iconColor":
              if (element && element.hex8) {
                styleObject["color"] = IDM.hex8ToRgbaString(element.hex8);
              }
              break;
            case "Btn2iconBox":
              if (element && element.inputVal) {
                window.IDM.setStyleToPageHead(
                  this.moduleObject.id + "Btn1 svg",
                  {
                    height: element.inputVal + element.selectVal,
                    width: element.inputVal + element.selectVal,
                    "max-height": element.inputVal + element.selectVal,
                  }
                );
              }
              styleObject["height"] = element.inputVal + element.selectVal;
              styleObject["width"] = element.inputVal + element.selectVal;
              styleObject["max-height"] = element.inputVal + element.selectVal;
              break;
          }
        }
      }
      window.IDM.setStyleToPageHead(
        this.moduleObject.id + "Btn2 .button-svg-icon",
        styleObject
      );
    },
    /**
     * 把属性转换成按钮的样式设置(基础)
     */
    convertAttrToButtonBaseStyle() {
      var styleObject = {};
      for (const key in this.propData) {
        if (this.propData.hasOwnProperty.call(this.propData, key)) {
          const element = this.propData[key];
          if (!element && element !== false && element != 0) {
            continue;
          }
          switch (key) {
            case "Btn1width":
              if (element != "auto") {
                styleObject["width"] = element;
              }
              break;
            case "Btn1height":
              if (element != "auto") {
                styleObject["height"] = element;
              }
              break;
            case "Btn1box":
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
          }
        }
      }
      window.IDM.setStyleToPageHead(this.moduleObject.id + "Btn1", styleObject);

      styleObject = {};
      let styleObjectForText ={}
      for (const key in this.propData) {
        if (this.propData.hasOwnProperty.call(this.propData, key)) {
          const element = this.propData[key];
          if (!element && element !== false && element != 0) {
            continue;
          }
          switch (key) {
            case "Btn2width":
              if (element != "auto") {
                styleObject["width"] = element;
              }
              break;
            case "Btn2height":
              if (element != "auto") {
                styleObject["height"] = element;
              }
              break;
            case "Btn2box":
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
            case "Btn2font":
              styleObjectForText["font-family"] = element.fontFamily;
              if (element.fontColors.hex8) {
                styleObjectForText["color"] = IDM.hex8ToRgbaString(
                  element.fontColors.hex8
                );
              }
              styleObjectForText["font-weight"] =
                element.fontWeight && element.fontWeight.split(" ")[0];
              styleObjectForText["font-style"] = element.fontStyle;
              styleObjectForText["font-size"] =
                element.fontSize + element.fontSizeUnit;
              styleObjectForText["line-height"] =
                element.fontLineHeight +
                (element.fontLineHeightUnit == "-"
                  ? ""
                  : element.fontLineHeightUnit);
              styleObjectForText["text-align"] = element.fontTextAlign;
              styleObjectForText["text-decoration"] = element.fontDecoration;
              if (element.fontSize && element.fontSizeUnit) {
                window.IDM.setStyleToPageHead(
                  this.moduleObject.id + "Btn2 div",
                  {
                    "font-size": element.fontSize + element.fontSizeUnit,
                    "max-height": element.fontSize + element.fontSizeUnit,
                    width: element.fontSize + element.fontSizeUnit,
                  }
                );
              }
              break;
            case "Btn2TextBox":
              if (element.marginTopVal) {
                styleObjectForText["margin-top"] = `${element.marginTopVal}`;
              }
              if (element.marginRightVal) {
                styleObjectForText["margin-right"] = `${element.marginRightVal}`;
              }
              if (element.marginBottomVal) {
                styleObjectForText["margin-bottom"] = `${element.marginBottomVal}`;
              }
              if (element.marginLeftVal) {
                styleObjectForText["margin-left"] = `${element.marginLeftVal}`;
              }
              if (element.paddingTopVal) {
                styleObjectForText["padding-top"] = `${element.paddingTopVal}`;
              }
              if (element.paddingRightVal) {
                styleObjectForText["padding-right"] = `${element.paddingRightVal}`;
              }
              if (element.paddingBottomVal) {
                styleObjectForText["padding-bottom"] = `${element.paddingBottomVal}`;
              }
              if (element.paddingLeftVal) {
                styleObjectForText["padding-left"] = `${element.paddingLeftVal}`;
              }
              break;
          }
        }
      }
      window.IDM.setStyleToPageHead(this.moduleObject.id + "Btn2", styleObject);
      window.IDM.setStyleToPageHead(this.moduleObject.id + "Btn2 span", styleObjectForText);
    },
    /**
     * 内部点击事件
     */
    buttonClickHandle1(e) {
      let that = this;
      if (this.moduleObject.env == "develop") {
        //开发模式下不执行此事件
        return;
      }
      if (that.propData.Btn1openLoading) {
        that.isLoading = true;
      }
      //获取所有的URL参数、页面ID（pageId）、以及所有组件的返回值（用范围值去调用IDM提供的方法取出所有的组件值）
      let urlObject = window.IDM.url.queryObject(),
        pageId =
          window.IDM.broadcast && window.IDM.broadcast.pageModule
            ? window.IDM.broadcast.pageModule.id
            : "";
      //自定义函数
      /**
       * [
       * {name:"",param:{}}
       * ]
       */
      var Btn1clickFunction = this.propData.Btn1clickFunction;
      Btn1clickFunction &&
        Btn1clickFunction.forEach((item) => {
          window[item.name] &&
            window[item.name].call(this, {
              routerId: this.moduleObject.routerId,
              urlData: urlObject,
              pageId,
              customParam: item.param,
              _this: this,
            });
        });
      if (!Btn1clickFunction || (Btn1clickFunction && Btn1clickFunction.length == 0)) {
        that.isLoading = false;
      }

      if (
        this.propData.Btn1linkagePageModule &&
        this.propData.Btn1linkagePageModule.length > 0
      ) {
        var moduleIdArray = [];
        this.propData.Btn1linkagePageModule.forEach((item) => {
          moduleIdArray.push(item.moduleId);
        });
        this.sendBroadcastMessage({
          type: "linkageClick",
          message: null,
          rangeModule: moduleIdArray,
          messageKey: this.propData.Btn1messageKey,
        });
      }
    },

    /**
     * 把属性转换成按钮的样式设置(focus)
     */
    convertAttrToButtonFocusStyle() {
      var styleObject = {};
      if (
        this.propData.Btn2bgSizeFocus &&
        this.propData.Btn2bgSizeFocus == "customBtn2"
      ) {
        styleObject["background-size"] =
          (this.propData.Btn2bgSizeWidthFocus
            ? this.propData.Btn2bgSizeWidthFocus.inputVal +
              this.propData.Btn2bgSizeWidthFocus.selectVal
            : "auto") +
          " " +
          (this.propData.Btn2bgSizeHeightFocus
            ? this.propData.Btn2bgSizeHeightFocus.inputVal +
              this.propData.Btn2bgSizeHeightFocus.selectVal
            : "auto");
      } else if (this.propData.Btn2bgSizeFocus) {
        styleObject["background-size"] = this.propData.Btn2bgSizeFocus;
      }
      if (
        this.propData.Btn2positionXFocus &&
        this.propData.Btn2positionXFocus.inputVal
      ) {
        styleObject["background-position-x"] =
          this.propData.Btn2positionXFocus.inputVal +
          this.propData.Btn2positionXFocus.selectVal;
      }
      if (
        this.propData.Btn2positionYFocus &&
        this.propData.Btn2positionYFocus.inputVal
      ) {
        styleObject["background-position-y"] =
          this.propData.Btn2positionYFocus.inputVal +
          this.propData.Btn2positionYFocus.selectVal;
      }
      for (const key in this.propData) {
        if (this.propData.hasOwnProperty.call(this.propData, key)) {
          const element = this.propData[key];
          if (!element && element !== false && element != 0) {
            continue;
          }
          switch (key) {
            case "Btn2bgColorFocus":
              if (element && element.hex8) {
                styleObject["background-color"] = IDM.hex8ToRgbaString(
                  element.hex8
                );
              }
              break;
            case "Btn2bgImgUrlFocus":
              styleObject[
                "background-image"
              ] = `url(${window.IDM.url.getWebPath(element)})`;
              break;
            case "Btn2positionXFocus":
              //背景横向偏移

              break;
            case "Btn2positionYFocus":
              //背景纵向偏移

              break;
            case "Btn2bgRepeatFocus":
              //平铺模式
              styleObject["background-repeat"] = element;
              break;
            case "Btn2bgAttachmentFocus":
              //背景模式
              styleObject["background-attachment"] = element;
              break;
            case "Btn2borderFocus":
              if (element.border.top.width > 0) {
                styleObject["border-top-width"] =
                  element.border.top.width + element.border.top.widthUnit;
                styleObject["border-top-style"] = element.border.top.style;
                if (element.border.top.colors.hex8) {
                  styleObject["border-top-color"] = IDM.hex8ToRgbaString(
                    element.border.top.colors.hex8
                  );
                }
              }
              if (element.border.right.width > 0) {
                styleObject["border-right-width"] =
                  element.border.right.width + element.border.right.widthUnit;
                styleObject["border-right-style"] = element.border.right.style;
                if (element.border.right.colors.hex8) {
                  styleObject["border-right-color"] = IDM.hex8ToRgbaString(
                    element.border.right.colors.hex8
                  );
                }
              }
              if (element.border.bottom.width > 0) {
                styleObject["border-bottom-width"] =
                  element.border.bottom.width + element.border.bottom.widthUnit;
                styleObject["border-bottom-style"] =
                  element.border.bottom.style;
                if (element.border.bottom.colors.hex8) {
                  styleObject["border-bottom-color"] = IDM.hex8ToRgbaString(
                    element.border.bottom.colors.hex8
                  );
                }
              }
              if (element.border.left.width > 0) {
                styleObject["border-left-width"] =
                  element.border.left.width + element.border.left.widthUnit;
                styleObject["border-left-style"] = element.border.left.style;
                if (element.border.left.colors.hex8) {
                  styleObject["border-left-color"] = IDM.hex8ToRgbaString(
                    element.border.left.colors.hex8
                  );
                }
              }

              styleObject["border-top-left-radius"] =
                element.radius.leftTop.radius +
                element.radius.leftTop.radiusUnit;
              styleObject["border-top-right-radius"] =
                element.radius.rightTop.radius +
                element.radius.rightTop.radiusUnit;
              styleObject["border-bottom-left-radius"] =
                element.radius.leftBottom.radius +
                element.radius.leftBottom.radiusUnit;
              styleObject["border-bottom-right-radius"] =
                element.radius.rightBottom.radius +
                element.radius.rightBottom.radiusUnit;
              break;
            case "Btn2fontFocus":
              styleObject["font-family"] = element.fontFamily;
              if (element.fontColors.hex8) {
                styleObject["color"] = IDM.hex8ToRgbaString(
                  element.fontColors.hex8
                );
              }
              styleObject["font-weight"] =
                element.fontWeight && element.fontWeight.split(" ")[0];
              styleObject["font-style"] = element.fontStyle;
              styleObject["font-size"] =
                element.fontSize + element.fontSizeUnit;
              styleObject["line-height"] =
                element.fontLineHeight +
                (element.fontLineHeightUnit == "-"
                  ? ""
                  : element.fontLineHeightUnit);
              styleObject["text-align"] = element.fontTextAlign;
              styleObject["text-decoration"] = element.fontDecoration;
              if (element.fontSize && element.fontSizeUnit) {
                window.IDM.setStyleToPageHead(
                  this.moduleObject.id + "Btn1:focus .button-svg-icon",
                  {
                    "font-size": element.fontSize + element.fontSizeUnit,
                    "max-height": element.fontSize + element.fontSizeUnit,
                    width: element.fontSize + element.fontSizeUnit,
                  }
                );
              }
              break;
          }
        }
      }
      window.IDM.setStyleToPageHead(
        this.moduleObject.id + "Btn1:focus",
        styleObject
      );

      styleObject = {};
      if (
        this.propData.Btn1bgSizeFocus &&
        this.propData.Btn1bgSizeFocus == "customBtn1"
      ) {
        styleObject["background-size"] =
          (this.propData.Btn1bgSizeWidthFocus
            ? this.propData.Btn1bgSizeWidthFocus.inputVal +
              this.propData.Btn1bgSizeWidthFocus.selectVal
            : "auto") +
          " " +
          (this.propData.Btn1bgSizeHeightFocus
            ? this.propData.Btn1bgSizeHeightFocus.inputVal +
              this.propData.Btn1bgSizeHeightFocus.selectVal
            : "auto");
      } else if (this.propData.Btn1bgSizeFocus) {
        styleObject["background-size"] = this.propData.Btn1bgSizeFocus;
      }
      if (
        this.propData.Btn1positionXFocus &&
        this.propData.Btn1positionXFocus.inputVal
      ) {
        styleObject["background-position-x"] =
          this.propData.Btn1positionXFocus.inputVal +
          this.propData.Btn1positionXFocus.selectVal;
      }
      if (
        this.propData.Btn1positionYFocus &&
        this.propData.Btn1positionYFocus.inputVal
      ) {
        styleObject["background-position-y"] =
          this.propData.Btn1positionYFocus.inputVal +
          this.propData.Btn1positionYFocus.selectVal;
      }
      for (const key in this.propData) {
        if (this.propData.hasOwnProperty.call(this.propData, key)) {
          const element = this.propData[key];
          if (!element && element !== false && element != 0) {
            continue;
          }
          switch (key) {
            case "Btn1bgColorFocus":
              if (element && element.hex8) {
                styleObject["background-color"] = IDM.hex8ToRgbaString(
                  element.hex8
                );
              }
              break;
            case "Btn1bgImgUrlFocus":
              styleObject[
                "background-image"
              ] = `url(${window.IDM.url.getWebPath(element)})`;
              break;
            case "Btn1positionXFocus":
              //背景横向偏移

              break;
            case "Btn1positionYFocus":
              //背景纵向偏移

              break;
            case "Btn1bgRepeatFocus":
              //平铺模式
              styleObject["background-repeat"] = element;
              break;
            case "Btn1bgAttachmentFocus":
              //背景模式
              styleObject["background-attachment"] = element;
              break;
            case "Btn1borderFocus":
              if (element.border.top.width > 0) {
                styleObject["border-top-width"] =
                  element.border.top.width + element.border.top.widthUnit;
                styleObject["border-top-style"] = element.border.top.style;
                if (element.border.top.colors.hex8) {
                  styleObject["border-top-color"] = IDM.hex8ToRgbaString(
                    element.border.top.colors.hex8
                  );
                }
              }
              if (element.border.right.width > 0) {
                styleObject["border-right-width"] =
                  element.border.right.width + element.border.right.widthUnit;
                styleObject["border-right-style"] = element.border.right.style;
                if (element.border.right.colors.hex8) {
                  styleObject["border-right-color"] = IDM.hex8ToRgbaString(
                    element.border.right.colors.hex8
                  );
                }
              }
              if (element.border.bottom.width > 0) {
                styleObject["border-bottom-width"] =
                  element.border.bottom.width + element.border.bottom.widthUnit;
                styleObject["border-bottom-style"] =
                  element.border.bottom.style;
                if (element.border.bottom.colors.hex8) {
                  styleObject["border-bottom-color"] = IDM.hex8ToRgbaString(
                    element.border.bottom.colors.hex8
                  );
                }
              }
              if (element.border.left.width > 0) {
                styleObject["border-left-width"] =
                  element.border.left.width + element.border.left.widthUnit;
                styleObject["border-left-style"] = element.border.left.style;
                if (element.border.left.colors.hex8) {
                  styleObject["border-left-color"] = IDM.hex8ToRgbaString(
                    element.border.left.colors.hex8
                  );
                }
              }

              styleObject["border-top-left-radius"] =
                element.radius.leftTop.radius +
                element.radius.leftTop.radiusUnit;
              styleObject["border-top-right-radius"] =
                element.radius.rightTop.radius +
                element.radius.rightTop.radiusUnit;
              styleObject["border-bottom-left-radius"] =
                element.radius.leftBottom.radius +
                element.radius.leftBottom.radiusUnit;
              styleObject["border-bottom-right-radius"] =
                element.radius.rightBottom.radius +
                element.radius.rightBottom.radiusUnit;
              break;
            case "Btn1fontFocus":
              styleObject["font-family"] = element.fontFamily;
              if (element.fontColors.hex8) {
                styleObject["color"] = IDM.hex8ToRgbaString(
                  element.fontColors.hex8
                );
              }
              styleObject["font-weight"] =
                element.fontWeight && element.fontWeight.split(" ")[0];
              styleObject["font-style"] = element.fontStyle;
              styleObject["font-size"] =
                element.fontSize + element.fontSizeUnit;
              styleObject["line-height"] =
                element.fontLineHeight +
                (element.fontLineHeightUnit == "-"
                  ? ""
                  : element.fontLineHeightUnit);
              styleObject["text-align"] = element.fontTextAlign;
              styleObject["text-decoration"] = element.fontDecoration;
              if (element.fontSize && element.fontSizeUnit) {
                window.IDM.setStyleToPageHead(
                  this.moduleObject.id + "Btn2:focus .button-svg-icon",
                  {
                    "font-size": element.fontSize + element.fontSizeUnit,
                    "max-height": element.fontSize + element.fontSizeUnit,
                    width: element.fontSize + element.fontSizeUnit,
                  }
                );
              }
              break;
          }
        }
      }
      window.IDM.setStyleToPageHead(
        this.moduleObject.id + "Btn2:focus",
        styleObject
      );
    },

    /**
     * 内部点击事件
     */
    buttonClickHandle2(e) {
      let that = this;
      if (this.moduleObject.env == "develop") {
        //开发模式下不执行此事件
        return;
      }
      if (that.propData.Btn2openLoading) {
        that.isLoading = true;
      }
      //获取所有的URL参数、页面ID（pageId）、以及所有组件的返回值（用范围值去调用IDM提供的方法取出所有的组件值）
      let urlObject = window.IDM.url.queryObject(),
        pageId =
          window.IDM.broadcast && window.IDM.broadcast.pageModule
            ? window.IDM.broadcast.pageModule.id
            : "";
      //自定义函数
      /**
       * [
       * {name:"",param:{}}
       * ]
       */
      var Btn2clickFunction = this.propData.Btn2clickFunction;
      Btn2clickFunction &&
        Btn2clickFunction.forEach((item) => {
          window[item.name] &&
            window[item.name].call(this, {
              routerId: this.moduleObject.routerId,
              urlData: urlObject,
              pageId,
              customParam: item.param,
              _this: this,
            });
        });
      if (!Btn2clickFunction || (Btn2clickFunction && Btn2clickFunction.length == 0)) {
        that.isLoading = false;
      }

      if (
        this.propData.Btn2linkagePageModule &&
        this.propData.Btn2linkagePageModule.length > 0
      ) {
        var moduleIdArray = [];
        this.propData.Btn2linkagePageModule.forEach((item) => {
          moduleIdArray.push(item.moduleId);
        });
        this.sendBroadcastMessage({
          type: "linkageClick",
          message: null,
          rangeModule: moduleIdArray,
          messageKey: this.propData.Btn2messageKey,
        });
      }
    },

    /**
     * 组件通信：接收消息的方法
     * @param {
     *  type:"发送消息的时候定义的类型，这里可以自己用来要具体做什么，统一规定的type：linkageResult（组件联动传结果值）、linkageDemand（组件联动传需求值）、linkageReload（联动组件重新加载）
     * 、linkageOpenDialog（打开弹窗）、linkageCloseDialog（关闭弹窗）、linkageShowModule（显示组件）、linkageHideModule（隐藏组件）、linkageResetDefaultValue（重置默认值）"
     *  message:{发送的时候传输的消息对象数据}
     *  messageKey:"消息数据的key值，代表数据类型是什么，常用于表单交互上，比如通过这个key判断是什么数据"
     *  isAcross:如果为true则代表发送来源是其他页面的组件，默认为false
     * } object
     */
    receiveBroadcastMessage(object) {
      console.log("组件收到消息", object);
      if (object.type && object.type == "linkageShowModule") {
        this.showThisModuleHandle();
      } else if (object.type && object.type == "linkageHideModule") {
        this.hideThisModuleHandle();
      }
    },
    /**
     * 组件通信：发送消息的方法
     * @param {
     *  type:"自己定义的，统一规定的type：linkageResult（组件联动传结果值）、linkageDemand（组件联动传需求值）、linkageReload（联动组件重新加载）
     * 、linkageOpenDialog（打开弹窗）、linkageCloseDialog（关闭弹窗）、linkageShowModule（显示组件）、linkageHideModule（隐藏组件）、linkageResetDefaultValue（重置默认值）",
     *  message:{实际的消息对象},
     *  rangeModule:"为空发送给全部，根据配置的属性中设定的值（值的内容是组件的packageid数组），不取子表的，比如直接 this.$root.propData.compositeAttr["attrKey"]（注意attrKey是属性中定义的bindKey）,这里的格式为：['1','2']"",
     *  className:"指定的组件类型，比如只给待办组件发送，然后再去过滤上面的值"
     *  globalSend:如果为true则全站发送消息，注意全站rangeModule是无效的，只有className才有效，默认为false
     * } object
     */
    sendBroadcastMessage(object) {
      window.IDM.broadcast && window.IDM.broadcast.send(object);
    },
    /**
     * 交互功能：设置组件的上下文内容值
     * @param {
     *  type:"定义的类型，已知类型：pageCommonInterface（页面统一接口返回值）、"
     *  key:"数据key标识，页面每个接口设置的数据集名称，方便识别获取自己需要的数据"
     *  data:"数据集，内容为：字符串 or 数组 or 对象"
     * }
     */
    setContextValue(object) {
      console.log("统一接口设置的值", object);
      if (object.type != "pageCommonInterface") {
        return;
      }
      //这里使用的是子表，所以要循环匹配所有子表的属性然后再去设置修改默认值
      if (object.key == this.propData.dataName) {
        // this.propData.fontContent = this.getExpressData(this.propData.dataName,this.propData.dataFiled,object.data);
        this.$set(
          this.propData,
          "fontContent",
          this.getExpressData(
            this.propData.dataName,
            this.propData.dataFiled,
            object.data
          )
        );
      }
    },
  },
};
</script>

<style scoped>
.container {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
}
.center-text {
  text-align: center;
  justify-content: center;
}
.btn {
  text-align: center;
}

.button-svg-icon {
  font-size: 14px;
  max-height: 14px;
  width: 14px;
  margin-right: 8px;
  fill: currentColor;
  vertical-align: middle;
}
</style>