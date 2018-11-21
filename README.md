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
.bx-uploader-item--border {
  &::after {
    content: '';
    position: absolute;
    height: 100%;
    width: 100%;
    top: 0;
    left: 0;
    border: 1px solid var(--uploader-border-color);
    display: block;
    transform-origin: 0 0;
  }
}

@media (-webkit-device-pixel-ratio: 1.5), (-webkit-min-device-pixel-ratio: 1.5) {
  .bx-uploader-item--border {
    &::after {
      content: '';
      position: absolute;
      height: 300%;
      width: 300%;
      top: 0;
      left: 0;
      border: 2px solid var(--uploader-border-color);
      display: block;
      transform: scale(0.333333, 0.33333);
      transform-origin: 0 0;
    }
  }
}

@media (-webkit-device-pixel-ratio: 2), (-webkit-min-device-pixel-ratio: 2) {
  .bx-uploader-item--border {
    &::after {
      content: '';
      position: absolute;
      height: 200%;
      width: 200%;
      top: 0;
      left: 0;
      border: 1px solid var(--uploader-border-color);
      display: block;
      transform: scale(0.5, 0.5);
      transform-origin: 0 0;
    }
  }
}

@media (-webkit-device-pixel-ratio: 3), (-webkit-min-device-pixel-ratio: 3) {
  .bx-uploader-item--border {
    &::after {
      content: '';
      position: absolute;
      height: 300%;
      width: 300%;
      top: 0;
      left: 0;
      border: 2px solid var(--uploader-border-color);
      display: block;
      transform: scale(0.333333, 0.333333);
      transform-origin: 0 0;
    }
  }
}
```
