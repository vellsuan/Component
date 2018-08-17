<template>
  <div class="page">
    <div :class="{'pickerMask':isShowMask}"></div>
    <div class="default" @click="showPickerView">
      <span class="paleft" v-if="columuOne.length">{{columuOne[one]}}</span><span class="paleft" v-if="columnTwo.length" >{{columnTwo[two]}}</span><span class="paleft" v-if="columnThree.length">{{columnThree[three]}}</span>
    </div>
    <div class="weui-picker" :class="{'weui_picker_view_show':pickerShow}">
      <div class="weui-picker__hd">
        <div href="javascript:;" class="weui-picker__action" @click="pickerCancel">取消</div>
        <div href="javascript:;" class="weui-picker__action" @click="pickerConfirm">确定</div>
      </div>
      <picker-view indicator-style="height: 40px;" :value="pickerValue" class="weui_picker_view" @change="pickerChange">
        <picker-view-column>
          <div class="picker-item" v-for="item in columuOne" v-if="columuOne.length" :key="index">{{item}}</div>
        </picker-view-column>
        <picker-view-column>
          <div class="picker-item" v-for="item in columnTwo"  v-if="columnTwo.length" :key="index">{{item}}</div>
        </picker-view-column>
        <picker-view-column>
          <div class="picker-item" v-for="item in columnThree" v-if="columnThree.length" :key="index">{{item}}</div>
        </picker-view-column>
      </picker-view>
    </div>
  </div>
</template>

<script>
  export default {
    props: {
      show: {type: Boolean, default: false}, // 是否出现
      listdata: {type: Array, default: []},
      regions: {type: String, default: '北京'},
      districtCode: {type: String, default: '001001001001'},
      idCode: {type: String, default: 'id'},
      nameCode: {type: String, default: 'name'},
      child: {type: String, default: 'child'},
      mulLinkAgeArray: {
        type: Array,
        default: [
          {
            'name': '北京',
            'id': '001001',
            'child': [
              {
                'name': '北京市',
                'id': '001001001',
                'child': [
                  {
                    'name': '东城区',
                    'id': '001001001001'
                  }, {
                    'name': '西城区',
                    'id': '001001001002'
                  }, {
                    'name': '朝阳区',
                    'id': '001001001005'
                  }, {
                    'name': '丰台区',
                    'id': '001001001006'
                  }, {
                    'name': '石景山区',
                    'id': '001001001007'
                  }, {
                    'name': '海淀区',
                    'id': '001001001008'
                  }, {
                    'name': '门头沟区',
                    'id': '001001001009'
                  }, {
                    'name': '房山区',
                    'id': '001001001010'
                  }, {
                    'name': '通州区',
                    'id': '001001001011'
                  }, {
                    'name': '顺义区',
                    'id': '001001001012'
                  }, {
                    'name': '昌平区',
                    'id': '001001001013'
                  }, {
                    'name': '大兴区',
                    'id': '001001001014'
                  }, {
                    'name': '怀柔区',
                    'id': '001001001015'
                  }, {
                    'name': '平谷区',
                    'id': '001001001016'
                  }, {
                    'name': '密云县',
                    'id': '001001001017'
                  }, {
                    'name': '延庆县',
                    'id': '001001001018'
                  }
                ]
              }
            ]
          }
        ]
      }
    },
    data () {
      return {
        pickerShow: false,
        isShowMask: false,
        pickerValue: [0, 0, 0],
        columuOne: [],
        columnTwo: [],
        columnThree: [],
        one: 0,
        two: 0,
        three: 0,
        cancelList: [],
        columnTwoReality: []
      }
    },
    mounted () {
      this._initPicker()
    },
    methods: {
      pickerChange: function (e) {
        let _this = this
        let value = e.mp.detail.value
        // picker用于存储下标的数组
        this.three = value[2]
        // picker下标2值
        // 二级chied是否存在
        let code = ''
        let twoCode = this.mulLinkAgeArray[_this.pickerValue[0]][_this.child][_this.pickerValue[1]][_this.child].length
        if (twoCode) {
          code = this.mulLinkAgeArray[_this.pickerValue[0]][_this.child][_this.pickerValue[1]][_this.child][_this.pickerValue[2]][_this.idCode]
        } else {
          code = this.mulLinkAgeArray[_this.pickerValue[0]][_this.child][_this.pickerValue[1]][_this.idCode]
        }
        // 取出三级的id号就是code码
        this.$emit('val', code, this.columuOne[0], this.columnTwo[0])
        // 传递给父组件
        if (value[0] !== this.pickerValue[0]) {
          let columnTwoNew = _this.mulLinkAgeArray[value[0]][_this.child]
          //  从一级中获取二级
          value[1] = 0
          // 初始化二级为第一位
          console.log(columnTwoNew)
          _this.columnTwo = []
          // 二级name数组初始化
          _this.columnTwoReality = []
          // code 初始化
          for (let i = 0; i < columnTwoNew.length; i++) {
            // 便利数组
            _this.columnTwo.push(columnTwoNew[i][this.nameCode])
            // 插入name
            _this.columnTwoReality.push(columnTwoNew[i])
            // 插入code
          }
          let two = _this.columnTwoReality[value[1]][_this.child]
          //  从二级中获取三级
          _this.columnThree = []
          // 初始化三级
          for (let j = 0; j < two.length; j++) {
            _this.columnThree.push(two[j][this.nameCode])
          }
          // _this.pickerValue[1] = e.mp.detail.value[1]
          //  对应当前所选项
          _this.pickerValue[0] = value[0]
        }
        // 滚动第一列给第二列赋值
        if (value[1] !== this.pickerValue[1]) {
          value[2] = 0
          if (_this.columnTwoReality[value[1]][_this.child].length) {
            let two = _this.columnTwoReality[value[1]][_this.child]
            _this.columnThree = []
            for (let i = 0; i < two.length; i++) {
              _this.columnThree.push(two[i][this.nameCode])
            }
            //  对应当前所选项
            _this.pickerValue[1] = value[1]
            // 滚动第二列给第三列赋值
          }
        }
        // 滚动第二列给第三列赋值
        if (value[2] !== this.pickerValue[2]) {
          //  对应当前所选项
          _this.pickerValue[2] = value[2]
        }
      },
      pickerConfirm () {
        this.isShowMask = false
        this.pickerShow = false
      },
      pickerCancel () {
        this.isShowMask = false
        this.pickerShow = false
        this._initPicker()
      },
      showPickerView () {
        this.isShowMask = true
        this.pickerShow = true
      },
      _initPicker: function () {
        let _this = this
        let mulLinkAgeArray = this.mulLinkAgeArray
        mulLinkAgeArray.forEach(function (item, index) {
          // 通过这个方法获取pickerValue里的值
          item[_this.child].forEach(function (itemo, indexo) {
            if (itemo[_this.child].length) {
              // 三级存在否 存在调三级不存在用上一级
              itemo[_this.child].forEach(function (itemt, indext) {
                if (itemt[_this.idCode] === _this.districtCode) {
                  _this.one = index
                  _this.two = indexo
                  _this.three = indext
                  _this.pickerValue = [index, indexo, indext]
                }
              })
            } else {
              if (itemo[_this.idCode] === _this.districtCode) {
                _this.one = index
                _this.two = indexo
                _this.pickerValue = [index, indexo, 0]
              }
            }
          })
        })
        _this.columuOne = []
        _this.columnTwo = []
        _this.columnThree = []
        for (let i = 0; i < mulLinkAgeArray.length; i++) {
          _this.columuOne.push(mulLinkAgeArray[i][this.nameCode])
        }
        // 渲染第二列
        let columnTwoArray = mulLinkAgeArray[_this.pickerValue[0]][_this.child]
        for (let i = 0; i < columnTwoArray.length; i++) {
          _this.columnTwo.push(columnTwoArray[i][this.nameCode])
        }
        // 渲染第三列
        let columnTherrArray = mulLinkAgeArray[_this.pickerValue[0]][_this.child][_this.pickerValue[1]][_this.child]
        for (let i = 0; i < columnTherrArray.length; i++) {
          _this.columnThree.push(columnTherrArray[i][this.nameCode])
        }
        /*  let i = -1
         for (let item of mulLinkAgeArray[_this.pickerValue[0]].child[_this.pickerValue[1]].child) {
           i++
           if (item.id === this.districtCode) {
             this.three = i
             console.log(this.three)
           }
         }  */
      }
    }
  }
</script>

<style scoped>
  page {
    margin-top: 100px;
    position: relative;
  }
  .weui-picker {
    position: fixed;
    bottom: 0;
    left: 0;
    width: 100%;
    transition: all 0.3s ease;
    transform: translateY(100%);
    z-index: 3000;
  }
  .weui_picker_view {
    position: relative;
    bottom: 0;
    left: 0;
    width: 100%;
    height: 238px;
    background-color: rgba(255, 255, 255, 1);
  }
  .weui-picker__hd {
    display: flex;
    padding: 9px 15px;
    background-color: #fff;
    position: relative;
    text-align: center;
    font-size: 17px;
  }
  .weui-picker__action {
    display: block;
    flex: 1;
    color: #1aad19;
    cursor: pointer;
  }
  .weui-picker__action:first-child {
    text-align: left;
    color: #888;
  }
  .weui-picker__action:last-child {
    text-align: right;
  }
  .weui-picker__hd:after {
    content: " ";
    position: absolute;
    left: 0;
    bottom: 0;
    right: 0;
    height: 1px;
    border-bottom: 1px solid #e5e5e5;
    color: #e5e5e5;
    transform-origin: 0 100%;
    transform: scaleY(0.5);
  }
  .weui_picker_view_show {
    transform: translateY(0);
  }
  .picker-item {
    text-align: center;
    line-height: 40px;
  }
  .pickerMask {
    position: fixed;
    z-index: 1000;
    top: 0;
    right: 0;
    left: 0;
    bottom: 0;
    background: rgba(0, 0, 0, 0.6);
  }
  .default{
    width: 100%;
    height: 100%;
    background: #ffffff;
    text-align:right;
  }
  .paleft{
    padding-left: 20px;
    color: #787878;
  }
</style>
