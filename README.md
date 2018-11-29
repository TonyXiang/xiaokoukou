# xiaokoukou备忘录

### axios post下载文件
```js
    exportInvoice(data) {
        return fetch({
            url: '/api/invoice/export',
            method: 'post',
            data: data,
            responseType: 'arraybuffer',
        })
    }

    Api.exportInvoice(params).then(res => {
        loading.close()
        const content = res
        const blob = new Blob([content])
        const fileName = 'group_invoice_list.zip'
        if ('download' in document.createElement('a')) { // 非IE下载
            const elink = document.createElement('a')
            elink.download = fileName
            elink.style.display = 'none'
            elink.href = URL.createObjectURL(blob)
            document.body.appendChild(elink)
            elink.click()
            URL.revokeObjectURL(elink.href) // 释放URL 对象
            document.body.removeChild(elink)
        } else { // IE10+下载
            navigator.msSaveBlob(blob, fileName)
        }
    }).catch(e => {
        loading.close()
        console.log(e)
    })
```

### addEventListener注意事项
多次绑定 `window.addEventListener('resize', () => {}）` 之后，安卓手机在触发 `resize` 事件的时候会卡死；

### 1px边框
```scss
.border-1px {
  &::after {
    content: '';
    position: absolute;
    height: 100%;
    width: 100%;
    top: 0;
    left: 0;
    border: 1px solid #e5e5e5;
    display: block;
    transform-origin: 0 0;
  }
}

@media (-webkit-device-pixel-ratio: 1.5), (-webkit-min-device-pixel-ratio: 1.5) {
  .border-1px {
    &::after {
      content: '';
      position: absolute;
      height: 300%;
      width: 300%;
      top: 0;
      left: 0;
      border: 2px solid #e5e5e5;
      display: block;
      transform: scale(0.333333, 0.33333);
      transform-origin: 0 0;
    }
  }
}

@media (-webkit-device-pixel-ratio: 2), (-webkit-min-device-pixel-ratio: 2) {
  .border-1px {
    &::after {
      content: '';
      position: absolute;
      height: 200%;
      width: 200%;
      top: 0;
      left: 0;
      border: 1px solid #e5e5e5;
      display: block;
      transform: scale(0.5, 0.5);
      transform-origin: 0 0;
    }
  }
}

@media (-webkit-device-pixel-ratio: 3), (-webkit-min-device-pixel-ratio: 3) {
  .border-1px {
    &::after {
      content: '';
      position: absolute;
      height: 300%;
      width: 300%;
      top: 0;
      left: 0;
      border: 2px solid #e5e5e5;
      display: block;
      transform: scale(0.333333, 0.333333);
      transform-origin: 0 0;
    }
  }
}
```

### iPhone X 适配
`viewport-fit=cover`
```scss
.page-footer{
  position: fixed;
  width: 100%;
  bottom: 0;
  left: 0;
  min-height: 100px;
  background: #f8f8f8;
  box-sizing: initial;
}
.page-btn{
  height: 72px;
  width: 92%;
  margin-top: 14px;
  margin-left: 4%;
  background: #508CEE;
  background-size: cover;
  border-radius: 8px;
  font-size: 28px;
  color: #fff;
  line-height: 72px;
  text-align: center;
}
.page-btn-empty {
  height: 14px;
}

.page-footer-empty {
  box-sizing: initial;
  min-height: 100px;
  width: 100%;
}

.has-padding {
  padding-bottom: constant(safe-area-inset-bottom);
  padding-bottom: env(safe-area-inset-bottom);
}
```
```html
    <div class="page-footer" :class="{'has-padding': hasPadding}">
      <div class="page-btn" @click="loadeData">底部按钮</div>
      <div class="page-btn-empty"></div>
    </div>
    <div class="page-footer-empty" :class="{'has-padding': hasPadding}"></div>
```
```js
  loadData() {
    this.add = !this.add
    this.$nextTick(function () {
      // DOM 现在更新了
      // `this` 绑定到当前实例
      this.resetPadding()
    })
  },
  resetPadding() {
    if (window.innerHeight >= document.body.clientHeight) {
      this.hasPadding = false
    } else {
      this.hasPadding = true
    }
  }
 ```
