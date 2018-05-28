# xiaokoukou
# 备忘录

### axios post下载文件
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
