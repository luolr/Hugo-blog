+++
categories = ["微信小程序"]
date = "2018-03-10"
description = "来踩一踩小程序上侧滑删除的坑"
tags = ["微信小程序"]
title = "微信小程序的侧滑删除实现"
author = "罗椋仁"
+++

最近这段时间，我一直都在和微信小程序打交道，在开发中也遇到了各种各样的问题，有些有意思的问题我会记录下来，方便以后回顾。
比如最近接到一个需求，需要实现列表中的侧滑删除功能。这个功能在APP应用中十分常见，比如微信自身就有这个功能，不过在小程序上倒是很少见，主要是体验不好，原因后面也会讲到。
<!--more-->

### 实现思路

要实现侧滑删除的功能，说起来也并不复杂，只要用好`bindtouchstart`、`bindtouchmove`和`bindtouchend`这三个事件，在手指横向滑动的时候，记录起始的位置和移动的距离，通过为该列设置相应的style样式来实现该列的移动。

至于删除按钮的显示，我们可以通过绝对定位设置重叠的两层，内容展示放在上层，删除按钮放在下层。这样，当上层跟随手指一起移动时，就会露出下层的删除按钮，当然，不要忘了设置上层可移动的最大距离为按钮的宽度。

除此之外，我们还可以做一个小的优化，当上层滑动的距离大于按钮一半的宽度并松开手指时，自动让上层自动滑动到最左侧来露出完整的按钮，反之则上层回到原位。以下是实现的效果展示：

![demo](/img/wxapp.gif)

### 具体代码

由于我这个项目是基于WePy框架开发的，如果是基于原生的语法开发的小程序，需要修改部分代码才可使用。

	<style lang="less" scoped>
	  .list-box {
	    padding-bottom: 204rpx;
	  }
	  .header {
	    padding: 36rpx 30rpx 24rpx;
	    color: #888888;
	  }
	  .w6 {
	    width: 6%;
	    display: inline-block;
	  }
	  .w6:after {
	    font-family: 'iconfont';
	    content: '&#xe633;';
	    margin-right: 30rpx;
	    left: 0;
	    top: 0;
	    position: relative;
	    color: #09BB07;
	  }
	  .items {
	    background-color: #fff;
	    font-size: 32rpx;
	    font-weight: 400;

	    .item {
	      position: relative;
	      height: 100rpx;
	      line-height: 100rpx;
	      overflow: hidden;
	      border-bottom: solid 1rpx #E5E5E5;
	      background-color: #fff;
	      padding-left: 30rpx;

	      .inner {
	        position: absolute;
	        top: 0;
	      }

	      .inner.text {
	        width: 100%;
	        z-index: 5;
	        background-color: #fff;
	        transition: left 0.2s ease-in-out;
	      }

	      .inner.del {
	        width: 120rpx;
	        text-align: center;
	        color: #fff;
	        background-color: #e64340;
	        right: 0;
	        z-index: 4;
	        height: 100rpx;
	        line-height: 100rpx;
	      }
	    }
	    .item:last-of-type {
	      border-bottom: none;
	    }
	  }
	  .footer {
	    z-index: 10;
	    position: fixed;
	    bottom: 0;
	    left: 0;
	    right: 0;
	    background-color: #f5f5f5;
	    padding: 50rpx 30rpx;
	  }
	</style>
	<template>
	  <view class="container">
	  	<view class="list-box">
		    <view class="header">保存记录</view>
		    <view class="items">
		      <repeat for="{{saveList}}" item="item" index="index">
		        <view class="item">
		          <view class="inner text" bindtouchstart="touchStart({{index}})" bindtouchmove="touchMove({{index}}, 'save')" bindtouchend="touchEnd({{index}}, 'save')" style="{{item.style}}">
		            <view class="w6"></view>
		            <view class="w20 text-ellipsis">{{item.name}}</view>
		            <view class="w12 text-ellipsis">{{item.sex}}</view>
		            <view class="w32 text-ellipsis">{{item.phone}}</view>
		            <view class="w30 text-ellipsis">{{item.birthday}}</view>
		          </view>
		          <view class="inner del">删除</view>
		        </view>
		      </repeat>
		    </view>
		    <view class="header">家庭成员</view>
		    <view class="items">
		      <repeat for="{{familyList}}" item="item" index="index">
		        <view class="item">
		          <view class="inner text" bindtouchstart="touchStart({{index}})" bindtouchmove="touchMove({{index}}, 'family')" bindtouchend="touchEnd({{index}}, 'family')" style="{{item.style}}">
		            <view class="w6"></view>
		            <view class="w20 text-ellipsis">{{item.name}}</view>
		            <view class="w12 text-ellipsis">{{item.sex}}</view>
		            <view class="w32 text-ellipsis">{{item.phone}}</view>
		            <view class="w30 text-ellipsis">{{item.birthday}}</view>
		          </view>
		          <view class="inner del">删除</view>
		        </view>
		      </repeat>
		    </view>
	    </view>
	  </view>
	  <view class="footer">
	    <button class="btn-success">新增就诊人</button>
	  </view>
	</template>

	<script>
	  import wepy from 'wepy'

	  export default class Reservation extends wepy.page {
	    config = {
	      navigationBarTitleText: '选择就诊人'
	    };

	    data = {
	      delBtnWidth: 60,  // 删除按钮的宽度，单位是px
	      startX: '',
	      style: '',
	      saveList: [{
	        id: 3,
	        name: '灰原哀',
	        sexCode: 2,
	        sex: '女',
	        phone: '13040506070',
	        birthday: '1989-03-03'
	      }, {
	        id: 4,
	        name: '园子',
	        sexCode: 2,
	        sex: '女',
	        phone: '13040506070',
	        birthday: '1989-06-26'
	      }],
	      familyList: [{
	        id: 1,
	        name: '工藤新一',
	        sexCode: 1,
	        sex: '男',
	        phone: '13040506070',
	        birthday: '1989-02-23'
	      }, {
	        id: 2,
	        name: '毛利兰',
	        sexCode: 2,
	        sex: '女',
	        phone: '13040506070',
	        birthday: '1989-04-11'
	      }]
	    };

	    methods = {
	      touchStart: function(index) {
	        if (arguments[1].touches.length == 1) {
	          this.startX = arguments[1].touches[0].clientX;
	        }
	      },
	      touchMove: function(index, type) {
	        if (arguments[2].touches.length == 1) {
	          const moveX = arguments[2].touches[0].clientX;  // 手指移动结束后水平位置
	          const distant = this.startX - moveX;            // 触摸开始与结束，手指移动的距离
	          let style = '';

	          if (distant <= 0) {              // 如果移动距离小于等于0，文本层位置不变
	            style = 'left: 15px';
	          } else {                       // 移动距离大于0，文本层left值等于手指移动距离
	            style = `left: -${distant}px`;
	            if (distant >= this.delBtnWidth) {
	              style = `left: -${this.delBtnWidth}px`;
	            }
	          }

	          if (type === 'save') {
	            this.saveList[index]['style'] = style
	          } else {
	            this.familyList[index]['style'] = style
	          }
	        }
	      },
	      touchEnd: function(index, type) {
	        if (arguments[2].changedTouches.length == 1) {
	          const moveX = arguments[2].changedTouches[0].clientX;  // 手指移动结束后水平位置
	          const distant = this.startX - moveX;                   // 触摸开始与结束，手指移动的距离
	          const delBtnWidth = this.delBtnWidth;
	          let style = distant > delBtnWidth / 2 ? `left: -${delBtnWidth}px` : 'left: 15px';

	          if (type === 'save') {
	            this.saveList[index]['style'] = style
	          } else {
	            this.familyList[index]['style'] = style
	          }
	        }
	      }
	    };
	  }
	</script>

### 事情还没完

在实现效果之后，本以为大功告成，却在真机上体验时出现了问题，当我的手指在列表上进行水平滑动时，很容易会引起垂直方向上的移动，导致一种结果，那就是在水平滑动时，整个页面经常会被上下拉扯一下，体验非常不好。毕竟，我们不能指望用户都是完美的水平移动。

那么怎么解决这个问题呢，毕竟这是由于小程序提供的垂直方向上的滚动效果导致的，原本是为下拉刷新等API服务的。可能也正是因为这个原因，所以小程序上的侧滑删除功能才不那么常见？

不过问题还是要解决，我首先考虑的是否能够禁掉小程序自带的页面滚动效果，查阅了下资料，还真有，可以通过在 **page.json** 中配置 **disableScroll: true** 来实现。不过，在我加上该项后，结果整个页面都滚不了了，经过调试才发现，好家伙，直接在根节点上加了一个 **overflow-y: hidden** 的样式，怪不得整个页面都滚不动了。好吧，这条路算是走不通了。

然后我想起来小程序里scroll-view可以控制页面的滚动与否，于是把原本的节点换成scroll-view，然后通过对滑动时的垂直方向的移动与否对`scroll-y`属性进行动态设置。可惜理想很丰满，现实却很骨感，scroll-view的性能实在是太差了，当节点过多，又涉及到大量的绝对定位布局时，在滚动时会出现非常明显的渲染问题，删除按钮会在滑动时显示出来，或者滑动不流畅，体验很差。好吧，这条路貌似也走不通。

### 结论

到了这个时候，我对于小程序上为什么很少出现侧滑删除效果，表示大大的理解。所以，如果产品是对体验要求非常严格的同学，建议不要去踩侧滑删除这个坑，目前我还没找到这个的最佳实践。如果列表项不多，不涉及到列表的滑动时，倒是可以考虑禁掉整个页面的滚动。

当然，我也会继续思考这个问题，如果有了较好的解决方案，我会继续更新的。


