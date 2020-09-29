# xiaokoukou备忘录

### puppeteer
```js
const browser = await puppeteer.launch({
  args: ['--no-sandbox'],
  headless: true,
  // executablePath:
  //   "/Applications/Google Chrome.app/Contents/MacOS/Google Chrome",
})
const page = await browser.newPage()
page.on('dialog', async dialog => {
  await dialog.dismiss()
})
await page.setViewport({
  width: 1800,
  height: 1200,
})

// 设置 navigator.userAgent，防止被识别为无头浏览器
await page.setUserAgent(
  'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/83.0.4103.106 Safari/537.36'
)

// 设置 navigator.webdriver，防防止被识别为程序控制的浏览器
const preloadFile = fs.readFileSync('./src/preload.js', 'utf8')
await page.evaluateOnNewDocument(preloadFile)
this.browser = browser
this.page = page

// 设置下载行为，指定下载目录
await this.page._client.send('Page.setDownloadBehavior', { behavior: 'allow', downloadPath: './downloads' })
```

src/preload.js
```js
Object.defineProperty(navigator, 'webdriver', {
  get: function () {
    return undefined
  }
})

Object.defineProperty(window, 'open', {
  get: function () {
    return function (url) {
      window.location.href = url
    }
  }
})

```


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
 
### js获取图片旋转角度
[exif-js](https://www.npmjs.com/package/exif-js)是一个第三方的库，可以获取图片的信息，包括图片旋转角度等；如果只是为了获取图片角度，而引入整个包的话，代码体积过大；如果使用下面的方法来获取图片旋转角度的话，会大大减少代码体积；

```js
function getOrientation(file) {
  return new Promise((resolve, reject) => {
    try {
      const reader = new FileReader()
      reader.onload = function(e) {
        const view = new DataView(e.target.result)
        if (view.getUint16(0, false) !== 0xffd8) {
          resolve(-2) // 不是jpeg
        }
        const length = view.byteLength
        let offset = 2
        while (offset < length) {
          if (view.getUint16(offset + 2, false) <= 8) {
            resolve(-1) // 不包含旋转信息
          }
          const marker = view.getUint16(offset, false)
          offset += 2
          if (marker === 0xffe1) {
            offset += 2
            if (view.getUint32(offset, false) !== 0x45786966) {
              resolve(-1) // 不包含旋转信息
            }
            const little = view.getUint16((offset += 6), false) === 0x4949
            offset += view.getUint32(offset + 4, little)
            const tags = view.getUint16(offset, little)
            offset += 2
            for (let i = 0; i < tags; i += 1) {
              if (view.getUint16(offset + i * 12, little) === 0x0112) {
                resolve(view.getUint16(offset + i * 12 + 8, little))
              }
            }
          } else if ((marker & 0xff00) !== 0xff00) {
            break
          } else {
            offset += view.getUint16(offset, false)
          }
        }
        resolve(-1) // 不包含旋转信息
      }
      reader.readAsArrayBuffer(file)
    } catch (e) {
      reject(e)
    }
  })
}
```
参考自: [accessing-jpeg-exif-rotation-data-in-javascript-on-the-client-side](https://stackoverflow.com/questions/7584794/accessing-jpeg-exif-rotation-data-in-javascript-on-the-client-side)
