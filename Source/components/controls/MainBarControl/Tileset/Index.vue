<template>
  <!-- box -->
  <div class="xbsj-template">
    <div
      class="xbsj-list"
      ref="container"
      @mousedown="startMove($event)"
      @mousemove="onMoving($event)"
      @mouseup="endMove($event)"
    >
      <div class="xbsj-list-item">
        <span class="xbsj-list-name">{{lang.source}}</span>
        <div class="xbsj-item-btnbox ml20">
          <div
            class="xbsj-item-btn onlinebutton"
            @click="modelOnline=!modelOnline"
            :class="{highlight:modelOnline}"
          ></div>
          <span class="xbsj-item-name">{{lang.online}}</span>
        </div>
        <div class="xbsj-item-btnbox">
          <div
            class="xbsj-item-btn localhostbutton"
            @click="modelLab=!modelLab"
            :class="{highlight:modelLab}"
          ></div>
          <span class="xbsj-item-name">{{lang.localhost}}</span>
        </div>
      </div>

      <div class="xbsj-list-item" v-if="customBtns.length>0">
        <span class="xbsj-list-name">{{lang.custom}}</span>
        <div class="xbsj-item-btnbox ml20" v-for="btn in customBtns">
          <div class="xbsj-item-btn">
            <button class="custombutton" :class="btn.cls()" @click="btn.click()"></button>
          </div>
          <span class="xbsj-item-name">{{btn.title()}}</span>
        </div>
      </div>

      <div class="xbsj-list-item">
        <span class="xbsj-list-name">{{lang.edit}}</span>
        <div class="xbsj-item-btnbox ml20">
          <div class="xbsj-item-btn">
            <button
              class="fenleititlesbutton"
              :class=" { highlight:classificationType === 'ClassificationType.CESIUM_3D_TILE' || classificationType === 'ClassificationType.BOTH'}"
              @click="titlesClick"
              :disabled="!enabled"
            ></button>
          </div>
          <span class="xbsj-item-name">{{lang.fenleimmodel}}</span>
        </div>

        <div class="xbsj-item-btnbox">
          <div class="xbsj-item-btn">
            <button
              class="fenleiterrainbutton"
              :class="{ highlight: classificationType === 'ClassificationType.TERRAIN' || classificationType === 'ClassificationType.BOTH' }"
              @click="terrainClick"
              :disabled="!enabled"
            ></button>
          </div>
          <span class="xbsj-item-name">{{lang.fenleiterrain}}</span>
        </div>
        <div class="xbsj-item-btnbox">
          <div class="xbsj-item-btn">
            <button class="stylebutton" :disabled="!enabled" @click="styleEditor()"></button>
          </div>
          <span class="xbsj-item-name">{{lang.style}}</span>
        </div>

        <div class="xbsj-item-btnbox">
          <div class="xbsj-item-btn">
            <button
              class="movebutton"
              :class="{highlight:positionEditing}"
              :disabled="!enabled"
              @click="toggleMove"
            ></button>
          </div>
          <span class="xbsj-item-name">{{lang.move}}</span>
        </div>

        <div class="xbsj-item-btnbox">
          <div class="xbsj-item-btn">
            <button
              class="rotatebutton"
              :class="{highlight:rotationEditing}"
              :disabled="!enabled"
              @click="toggleRotate"
            ></button>
          </div>
          <span class="xbsj-item-name">{{lang.rotate}}</span>
        </div>

        <div class="xbsj-item-btnbox">
          <div class="xbsj-item-btn">
            <button
              class="lefttopButton"
              :class="xbsjLeftTopView ? 'lefttopButtonActive' : ''"
              @click="xbsjLeftTopView = !xbsjLeftTopView;"
              :disabled="!enabled"
            ></button>
            <button
              class="righttopButton"
              :class="xbsjRightTopView ? 'righttopButtonActive' : ''"
              @click="xbsjRightTopView = !xbsjRightTopView"
              :disabled="!enabled"
            ></button>
            <button
              class="leftbottomButton"
              :class="xbsjLeftBottomView ? 'leftbottomButtonActive' : ''"
              @click="xbsjLeftBottomView=!xbsjLeftBottomView"
              :disabled="!enabled"
            ></button>
            <button
              class="rightbottomButton"
              :class="xbsjRightBottomView ? 'rightbottomButtonActive' : ''"
              @click="xbsjRightBottomView=!xbsjRightBottomView"
              :disabled="!enabled"
            ></button>
          </div>
          <span class="xbsj-item-name">{{lang.view}}</span>
        </div>
      </div>
      <div class="xbsj-list-item xbsj-list-lastitem">
        <span class="xbsj-list-name">{{lang.visible}}</span>
        <div class="xbsj-slide-group">
          <div class="xbsj-slide-top">
            <label class="xbsj-slide-label" @click="maximumScreenSpaceError=16">{{lang.accuracy}}</label>
            <div class="xbsj-slide-div">
              <XbsjSlider
                :min="-4"
                :max="8"
                :step="0.1"
                v-model="ssePower"
                :disabled="!enabled"
                :show-tip="showTip"
              ></XbsjSlider>
            </div>
            <span class="xbsj-slide-span">{{maximumScreenSpaceError|f_one}}</span>
          </div>
          <div class="xbsj-slide-bottom">
            <label class="xbsj-slide-label">{{lang.material}}</label>
            <div class="xbsj-slide-div">
              <XbsjSlider
                :min="0"
                :max="5.0"
                :step="0.02"
                v-model="luminanceAtZenith"
                :disabled="!enabled"
                :show-tip="showTip"
              ></XbsjSlider>
            </div>
            <span class="xbsj-slide-span">{{luminanceAtZenith}}</span>
          </div>
        </div>
        <div class="xbsj-slide-group">
          <div class="xbsj-slide-top">
            <label class="xbsj-slide-label">{{lang.scatter}}</label>
            <div class="xbsj-slide-div">
              <XbsjSlider
                :min="0"
                :max="1.0"
                :step="0.01"
                v-model="imageBasedLightingFactor[0]"
                :disabled="!enabled"
                :show-tip="showTip"
              ></XbsjSlider>
            </div>
            <span class="xbsj-slide-span">{{imageBasedLightingFactor[0]}}</span>
          </div>
          <div class="xbsj-slide-bottom">
            <label class="xbsj-slide-label">{{lang.mirror}}</label>
            <div class="xbsj-slide-div">
              <XbsjSlider
                :min="0"
                :max="1.0"
                :step="0.01"
                v-model="imageBasedLightingFactor[1]"
                :disabled="!enabled"
                :show-tip="showTip"
              ></XbsjSlider>
            </div>
            <span class="xbsj-slide-span">{{imageBasedLightingFactor[1]}}</span>
          </div>
        </div>
        <!--  先注释这两块的调整
        <div class="xbsj-slide-group">
          <div class="xbsj-slide-top">
            <label class="xbsj-slide-label">{{lang.direct}}</label>
            <div class="xbsj-slide-div">
              <XbsjSlider
                :min="0"
                :max="10.0"
                :step="0.1"
                v-model="lightColorOne"
                :disabled="!enabled"
                :show-tip="showTip"
              ></XbsjSlider>
            </div>
            <span class="xbsj-slide-span">{{lightColorOne}}</span>
          </div>
          <div class="xbsj-slide-bottom">
            <label class="xbsj-slide-label yapingzu">{{lang.yapingzu}}</label>
 
            <div class="xbsj-tileset-selectdiv" @click="selectClick">
              <span class="xbsj-tileset-selectText">{{getFlatingName(xbsjFlattenGuid)}}</span>
              <button class="xbsj-tileset-selectButton"></button>
            </div>
            <ul class="xbsj-tileset-selectOption" v-show="selectshow">
              <li v-for="f in flattings" :key="f.guid" :value="f.guid" @click="selectName(f.guid)">
                <span>{{ f.name }}</span>
              </li>
            </ul>
          </div>
        </div>
        -->
      </div>
    </div>
  </div>
</template>

<script>
import languagejs from "./index_locale";

export default {
  data() {
    return {
      showTip: "never",
      popup: "",
      lang: {},
      enabled: false,
      ssePower: 4,
      maximumScreenSpaceError: 16,
      luminanceAtZenith: 0.5,
      imageBasedLightingFactor: [1.0, 1.0],
      // lightColorOne: 1,
      lightColor: [1, 1, 1],
      xbsjFlattenGuid: "",
      classificationType: "",
      xbsjLeftTopView: false,
      xbsjLeftBottomView: false,
      xbsjRightTopView: false,
      xbsjRightBottomView: false,
      modelLab: false,
      modelOnline: false,
      flattings: [],
      positionEditing: false,
      rotationEditing: false,
      xbsjUseOriginTransform: false,
      selectshow: false,
      langs: languagejs,
      customBtns: []
    };
  },
  created() {},
  mounted() {
    this.$nextTick(() => {
      this._unBinds = [];
      this._unBinds.push(
        XE.MVVM.watch(() => {
          const csn = this.$root.$earth.sceneTree.currentSelectedNode;
          if (csn && csn.czmObject && csn.czmObject instanceof XE.Obj.Tileset) {
            this.setTileset(csn.czmObject);
          } else {
            this.setTileset(undefined);
          }
        })
      );

      this._unBinds.push(
        XE.MVVM.watch(() => {
          //接受压平多边形组
          this.flattings = [
            {
              name: "",
              guid: ""
            }
          ];

          this.$root.$earth.flattenedPolygonCollection.forEach(f => {
            this.flattings.push({
              name: f.name,
              guid: f.guid
            });
          });
        })
      );
    });
  },
  beforeDestroy() {
    // _viewUnbinds需要清理 vtxf
    if (this._viewUnbinds) {
      this._viewUnbinds.forEach(u => u());
      this._viewUnbinds.length = 0;
    }

    if (this._unBinds) {
      this._unBinds.forEach(u => u());
      this._unBinds.length = 0;
    }
  },
  methods: {
    addCustomButton(item) {
      //给几个方法默认值，避免出错
      item.cls =
        item.cls ||
        function() {
          return "";
        };
      item.title =
        item.title ||
        function() {
          return "custom";
        };
      item.click = item.click || function() {};

      this.customBtns.push(item);
    },
    removeCustomButton(item) {
      let idx = this.customBtns.findIndex(it => it === item);
      if (idx >= 0) {
        this.customBtns.splice(idx, 1);
      }
    },
    toggleRotate() {
      this.rotationEditing = !this.rotationEditing;
      if (this.rotationEditing) {
        this.xbsjUseOriginTransform = false;
      }
    },

    toggleMove() {
      this.positionEditing = !this.positionEditing;
      if (this.positionEditing) {
        this.xbsjUseOriginTransform = false;
      }
    },
    getFlatingName(guid) {
      var flat = this.flattings.find(e => e.guid == guid);
      if (flat) return flat.name;
      else return "";
    },
    selectClick() {
      this.selectshow = !this.selectshow;
    },
    selectName(value) {
      this.xbsjFlattenGuid = value;
      this.selectshow = false;
    },
    styleEditor() {
      //显示样式编辑器
      this.$root.$earthUI.showPropertyWindow(this._tileset, {
        component: "TilesetStyleEditor"
      });
    },
    titlesClick() {
      if (this.classificationType == "ClassificationType.NONE")
        this.classificationType = "ClassificationType.CESIUM_3D_TILE";
      else if (this.classificationType == "ClassificationType.CESIUM_3D_TILE")
        this.classificationType = "ClassificationType.NONE";
      else if (this.classificationType == "ClassificationType.TERRAIN")
        this.classificationType = "ClassificationType.BOTH";
      else if (this.classificationType == "ClassificationType.BOTH")
        this.classificationType = "ClassificationType.TERRAIN";
    },
    terrainClick() {
      if (this.classificationType == "ClassificationType.NONE")
        this.classificationType = "ClassificationType.TERRAIN";
      else if (this.classificationType == "ClassificationType.TERRAIN")
        this.classificationType = "ClassificationType.NONE";
      else if (this.classificationType == "ClassificationType.CESIUM_3D_TILE")
        this.classificationType = "ClassificationType.BOTH";
      else if (this.classificationType == "ClassificationType.BOTH")
        this.classificationType = "ClassificationType.CESIUM_3D_TILE";
    },
    lefttopClick() {
      if (this._tileset) {
      }
    },
    righttopClick() {
      if (this._tileset) {
        this.righttopShow = !this.righttopShow;
      }
    },
    leftbottomClick() {
      if (this._tileset) {
        this.leftbottomShow = !this.leftbottomShow;
      }
    },
    rightbottomClick() {
      if (this._tileset) {
        this.rightbottomShow = !this.rightbottomShow;
      }
    },
    _bind(prp) {
      this._viewUnbinds.push(XE.MVVM.bind(this, prp, this._tileset, prp));
    },
    setTileset(tileset) {
      if (this._tileset !== tileset) {
        this._tileset = tileset;
        // vtxf add 视口绑定
        this._viewUnbinds = this._viewUnbinds || [];
        // 先清理之前的绑定
        this._viewUnbinds.forEach(u => u());
        this._viewUnbinds.length = 0;
        // 再增加新的绑定
        if (this._tileset) {
          this._bind("xbsjLeftTopView");
          this._bind("xbsjRightTopView");
          this._bind("xbsjLeftBottomView");
          this._bind("xbsjRightBottomView");

          this._bind("classificationType");
          this._bind("maximumScreenSpaceError");
          this._bind("luminanceAtZenith");
          this._bind("imageBasedLightingFactor");
          this._bind("lightColor");
          this._bind("xbsjFlattenGuid");

          this._bind("positionEditing");
          this._bind("xbsjUseOriginTransform");
          this._bind("rotationEditing");
        }
      }

      this.enabled = !!this._tileset;
    },
    getPopupComp() {
      if (this.$refs.hasOwnProperty(this.popup)) {
        return this.$refs[this.popup];
      } else {
        return undefined;
      }
    },
    showPopup(v) {
      let comp = this.getPopupComp();
      if (comp && typeof comp.show == "function") {
        comp.show(v);
      }
      return comp;
    },
    togglePopup(p, event) {
      //调用上一个组件的隐藏
      this.showPopup(false);

      this.popup = this.popup == p ? "" : p;

      //调用当前组件的显示
      let curComp = this.showPopup(true);

      if (this.popup != "" && event) {
        if (curComp) {
          try {
            //基于现在UI结构强行计算的
            let el = curComp.$el;
            el.style.left =
              event.target.offsetLeft +
              event.target.offsetParent.offsetLeft -
              40 +
              "px";
            el.style.top =
              event.target.offsetTop +
              event.target.offsetParent.offsetTop +
              44 +
              "px";
          } catch (ex) {
            console.log(ex);
          }
        }
      }
    },
    startMove(event) {
      //如果事件的目标不是本el 返回
      if (
        event.target.parentElement !== this.$refs.container &&
        event.target.parentElement.parentElement !== this.$refs.container
      ) {
        this.moving = false;
        return;
      }
      this.moving = true;
    },
    onMoving(event) {
      //获取鼠标和为开始位置的插值，滚动滚动条
      if (!this.moving) return;

      var dom = this.$refs.container;
      var wleft = dom.scrollLeft - event.movementX;
      if (wleft >= 0 && wleft <= dom.scrollWidth - dom.clientWidth) {
        dom.scrollLeft = wleft;
      }
    },
    endMove(envent) {
      this.moving = false;
    }
  },
  computed: {},
  watch: {
    ssePower(v) {
      this.maximumScreenSpaceError = Math.pow(2, v);
    },
    maximumScreenSpaceError(v) {
      this.ssePower = Math.log2(v);
    }
    // lightColorOne(v) {
    //   this.lightColor = [v, v, v];
    // },
    // lightColor(v) {
    //   this.lightColorOne = v[0];
    // }
  },
  filters: {
    f_one(v) {
      return v.toFixed(1);
    }
  }
};
</script>

<style scoped>
.onlinebutton {
  background: url(../../../../images/online.png) no-repeat;
  background-size: contain;
  cursor: pointer;
}
.onlinebutton.highlight,
.onlinebutton:hover {
  background: url(../../../../images/online_on.png) no-repeat;
  background-size: contain;
  cursor: pointer;
}
.localhostbutton {
  background: url(../../../../images/localhost.png) no-repeat;
  background-size: contain;
  cursor: pointer;
}
.localhostbutton.highlight,
.localhostbutton:hover {
  background: url(../../../../images/localhost_on.png) no-repeat;
  background-size: contain;
  cursor: pointer;
}

.fenleititlesbutton {
  background: url(../../../../images/fenlei-model.png) no-repeat;
  background-size: contain;
  cursor: pointer;
}

.fenleititlesbutton.highlight,
.fenleititlesbutton:hover {
  background: url(../../../../images/fenlei-model_on.png) no-repeat;
  background-size: contain;
  cursor: pointer;
}
.fenleiterrainbutton {
  background: url(../../../../images/fenlei-terrain.png) no-repeat;
  background-size: contain;
  cursor: pointer;
}

.fenleiterrainbutton.highlight,
.fenleiterrainbutton:hover {
  background: url(../../../../images/fenlei-terrain_on.png) no-repeat;
  background-size: contain;
  cursor: pointer;
}
.stylebutton {
  background: url(../../../../images/style.png) no-repeat;
  background-size: contain;
  cursor: pointer;
}
.stylebutton:hover {
  background: url(../../../../images/style_on.png) no-repeat;
  background-size: contain;
  cursor: pointer;
}
.movebutton {
  background: url(../../../../images/move.png) no-repeat;
  background-size: contain;
  cursor: pointer;
}
.movebutton.highlight,
.movebutton:hover {
  background: url(../../../../images/move_on.png) no-repeat;
  background-size: contain;
  cursor: pointer;
}

.rotatebutton {
  background: url(../../../../images/rotate.png) no-repeat;
  background-size: contain;
  cursor: pointer;
}
.rotatebutton.highlight,
.rotatebutton:hover {
  background: url(../../../../images/rotate_on.png) no-repeat;
  background-size: contain;
  cursor: pointer;
}

.rotatebutton:disabled {
  background: url(../../../../images/rotate_disabled.png) no-repeat;
  background-size: contain;
  cursor: not-allowed;
}

.viewbutton {
  background: url(../../../../images/view-model.png) no-repeat;
  background-size: contain;
  cursor: pointer;
}
.fenleititlesbutton:disabled {
  background: url(../../../../images/fenlei-titles_disabled.png) no-repeat;
  background-size: contain;
  cursor: not-allowed;
}
.fenleiterrainbutton:disabled {
  background: url(../../../../images/fenlei-terrain_disabled.png) no-repeat;
  background-size: contain;
  cursor: not-allowed;
}
.stylebutton:disabled {
  background: url(../../../../images/style_disabled.png) no-repeat;
  background-size: contain;
  cursor: not-allowed;
}
.movebutton:disabled {
  background: url(../../../../images/move_disabled.png) no-repeat;
  background-size: contain;
  cursor: not-allowed;
}

.fenleititlesbutton,
.fenleiterrainbutton,
.stylebutton,
.movebutton,
.rotatebutton,
.custombutton {
  width: 100%;
  height: 100%;
  border: none;
  outline: none;
}
.lefttopButton {
  position: absolute;
  width: 17px;
  height: 17px;
  background: rgba(71, 71, 71, 1);
  cursor: pointer;
  border: none;
  outline: none;
}
.lefttopButton:disabled {
  background: url(../../../../images/view_disabled.png) no-repeat;
  background-size: contain;
  cursor: not-allowed;
}
.lefttopButtonActive {
  background-color: rgba(31, 255, 255, 1);
  cursor: pointer;
}
.righttopButton {
  width: 17px;
  height: 17px;
  background: rgba(71, 71, 71, 1);
  cursor: pointer;
  border: none;
  outline: none;
  position: absolute;
  left: 11px;
}
.righttopButton:disabled {
  background: url(../../../../images/view_disabled.png) no-repeat;
  background-size: contain;
  cursor: not-allowed;
}
.righttopButtonActive {
  background-color: rgba(31, 255, 255, 1);
  cursor: pointer;
}
.leftbottomButton {
  width: 17px;
  height: 17px;
  background: rgba(71, 71, 71, 1);
  cursor: pointer;
  border: none;
  outline: none;
  position: absolute;
  top: 38px;
  left: -7px;
}
.leftbottomButton:disabled {
  background: url(../../../../images/view_disabled.png) no-repeat;
  background-size: contain;
  cursor: not-allowed;
}
.leftbottomButtonActive {
  background-color: rgba(31, 255, 255, 1);
  cursor: pointer;
}
.rightbottomButton {
  width: 17px;
  height: 17px;
  background: rgba(71, 71, 71, 1);
  cursor: pointer;
  border: none;
  outline: none;
  position: absolute;
  top: 38px;
  left: 11px;
}
.rightbottomButton:disabled {
  background: url(../../../../images/view_disabled.png) no-repeat;
  background-size: contain;
  cursor: not-allowed;
}
.rightbottomButtonActive {
  background-color: rgba(31, 255, 255, 1);
  cursor: pointer;
}
.xbsj-template .xbsj-rangespan {
  left: 158px;
  position: relative;
  bottom: 19px;
}
.flatselect {
  width: 123px;
  height: 20px;
  display: block;
  /*清除默认样式*/
  appearance: none;
  -moz-appearance: none;
  -webkit-appearance: none;
  background: url(../../../../images/titles-select.png) no-repeat;
  /* background-attachment: fixed; */
  background-position: 95%;
  color: #ffffff;
  padding-left: 10px;
  outline: none;
  border: none;
  background-color: rgba(0, 0, 0, 0.5);
}
/*清除ie下面的默认样式*/
select::-ms-expand {
  display: none;
}
.yapingzu {
  margin-right: 10px;
}
.flatselect .flatoption {
  background-color: rgba(138, 138, 138, 1);
}

.xbsj-tileset-selectdiv {
  display: inline-block;
  width: 131px;
  background: rgba(0, 0, 0, 0.5);
  height: 22px;
  position: relative;
  text-align: left;
  line-height: 22px;
  cursor: pointer;
  padding-left: 9px;
  left: -2px;
  top: -2px;
  border-radius: 3px;
  float: left;
}
.xbsj-tileset-selectText {
  font-size: 12px;
}
.xbsj-tileset-selectButton {
  width: 12px;
  height: 10px;
  border: none;
  background: url(../../../../images/titles-select.png) no-repeat;
  background-size: contain;
  cursor: pointer;
  float: right;
  margin-right: 6px;
  margin-top: 7px;
  outline: none;
}
.xbsj-tileset-selectOption {
  width: 140px;
  height: 46px;
  margin-left: 10px;
  background: rgba(138, 138, 138, 1);
  border-radius: 0px 0px 4px 4px;
  list-style: none;
  margin-top: 21px;
  margin-left: 56px;
  overflow: auto;
  z-index: 1;
  position: fixed;
}

.xbsj-tileset-selectOption li {
  width: 100%;
  height: 20px;
  font-size: 14px;
  color: rgba(221, 221, 221, 1);
  line-height: 20px;
  cursor: pointer;
}

.xbsj-tileset-selectOption li span {
  display: inline-block;
  height: 20px;
  position: relative;
  left: 11px;
}

.xbsj-tileset-selectOption li:hover {
  background: rgba(107, 107, 107, 1);
}
</style>

