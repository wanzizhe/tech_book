# CodeReview记录

#### 记录一下各位老师帮我codeReview的反馈，警示我一些代码上的问题，避免以后再犯。

| **日期** | **反馈** | **cr url** |
| :--- | :--- | :--- |
| 20190719 | 鹏程哥：无 | [http://172.22.1.88/fe/sophon-components/commit/d416ccd6a23315cc10de688cccf06b65c59c7d2a](http://172.22.1.88/fe/sophon-components/commit/d416ccd6a23315cc10de688cccf06b65c59c7d2a) |
| 20190722 | 鹏程哥：滚动列表组件滚动不是很流畅，并且要进一步思考数据更新应该怎么处理 | [http://172.22.1.88/fe/sophon-components/commit/1daba02290f017fda2f4fc6cf6b0b5dec630e6ae](http://172.22.1.88/fe/sophon-components/commit/1daba02290f017fda2f4fc6cf6b0b5dec630e6ae) |
| 20190724 | 晓鹏哥：活动图组件应该考虑多种情况，释放参数供用户传值，如果用户不传值要根据数据条数算出相应的样式展示 | [http://172.22.1.88/fe/sophon-components/commit/a6fd88c88fe9ae1322723fd34feb730d97ddde33](http://172.22.1.88/fe/sophon-components/commit/a6fd88c88fe9ae1322723fd34feb730d97ddde33) |
| 20190731 | 红梅姐：1、代码格式调整一下 2、//活动图背景有传入值下面的代码：对传入的参数依赖性太强，可以做一些兼容：这样的写法是基于option.color和option.background有相同数量的值吧 3、确定的数字：110、40 可以定义到顶部作为常量，写清备注，数字表示的啥 4、let label = option.label; let data = option.data; let color = option.color; 改成 let {label, data, color} = option;的写法 5、data .map多个空格？6、相同变量的方法是否可以合并？7、renderNumber和renderPoint方法top: 'calc(' + (index * 0.12 + 0.06 - index * 0.06)*100 + '%' + ' ' + '-' + ' ' + 9 + 'px)',  这样的计算代码 没法优化的话，可以做些备注吧，后面的人基本不敢改动这个代码。。number.length = 1，2，3，6更多了等都需要做下处理呢，看看计算方法能不能统一  8、render方法中的style放在class里面呢 9、大片注释的代码数据可以删掉了 10、加油让你的代码更优雅| [http://172.22.1.88/fe/sophon-components/commit/7587d051ad943d9e773404e774193e9876808fb0](http://172.22.1.88/fe/sophon-components/commit/7587d051ad943d9e773404e774193e9876808fb0) |