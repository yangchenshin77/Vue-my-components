# my-vue-components

## iView
基於 [iView](https://www.iviewui.com/) 的 [Vue](https://github.com/vuejs/vue) 組件

* my-table(表格)

    自動在表格結尾增加「編輯」欄位，包含「編輯」及「刪除」兩個按鈕。下方增加分頁組件。

* my-modal(對話框)

    開啟對話框的按鈕及對話框。

* my-upload(上傳組件)

    使用 [iView](https://www.iviewui.com/) 的 [Upload 組件](https://www.iviewui.com/components/upload) 及 [Axios](https://github.com/axios/axios) 異步上傳。

* my-upload-img(上傳圖片組件)

    使用 [iView](https://www.iviewui.com/) 的 [Upload 組件](https://www.iviewui.com/components/upload) 及 [Axios](https://github.com/axios/axios) 異步上傳。
    用 [Cropper.js](https://github.com/fengyuanchen/cropperjs) 實作裁切圖片，並產生縮圖(Canvas)。

* my-upload-img-cropper(裁切圖片組件)

    用 [Cropper.js](https://github.com/fengyuanchen/cropperjs) 實作裁切圖片，並產生縮圖(Canvas)。

* empty(圖片預設縮圖)

* ic-form(iview-custom 表單)

    將 [iView](https://www.iviewui.com/) 的表單封裝，使用 `form` 屬性及 `data` 即可完成一個表單。
    
    * form (Object): 表單的每個欄位的值
    
    ```js
    form: {
        name: ''
    }
    ```
    
    * data (Object): 表單的每個欄位的設定

    ```js
    data: {
        name: {
            label: 'Name',
            type: 'text'
        }
    }
    ```
    
* md-editor(Markdown 編輯器)

    將 [iView editor](http://editor.iviewui.com/) 組件封裝，預設使用 [highlight.js](https://highlightjs.org/) 語法高亮
