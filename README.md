# 这是一个mpvue的三级联动可定制数据的组件
引入到父组件即可使用
## 属性
@val 获取组件传递过来的对应城市的信息，有三级的code码和一二级的省市名称
districtCode：返显时候三级的code码 只需要传递三级的就会自动返显对应一二级的省市
idCode：数据的地址code码字段名称，这里默认为id
nameCode：数据的地址名称，和idCode一一对应默认为name
chiled：次级数据的字段名称默认为chiled
mulLinkAgeArray：是你自己定制的数据，如果字段和上面默认的三个属性是一致的那么直接放上去就可以使用了
##代码
<linkcode @val="父组件获取子组件的方法，三个参数" :districtCode="获取的三级code"  :mulLinkAgeArray="data" ></linkcode>

data的默认格式
[
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
                  }],
                  }
								]
							}
						]
