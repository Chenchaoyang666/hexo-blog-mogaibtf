---
title: el-upload中drag属性源码解析
top_img: '/CDN/wallpaper/cover_6.jpg'
cover: '/CDN/wallpaper/cover_6.jpg'
tags:
  - Vue
  - JavaScrip
categories:
  - 前端
abbrlink: 45972
---
## drag开启时，源码解析
```js
//主要是对文件拖动进div后进行解析
onDrop(e) {
        if (this.disabled || !this.uploader) return;
        //接受上传的文件类型（thumbnail-mode 模式下此参数无效），此处判断该文件是都符合能上传的类型
        const accept = this.uploader.accept;
        this.dragover = false;
        if (!accept) {
          this.$emit('file', e.dataTransfer.files);
          return;
        }
        this.$emit('file', [].slice.call(e.dataTransfer.files).filter(file => {
          const { type, name } = file;
          //获取文件名后缀，与设置的文件类型进行对比
          const extension = name.indexOf('.') > -1
            ? `.${ name.split('.').pop() }`
            : '';
          const baseType = type.replace(/\/.*$/, '');
          return accept.split(',')
            .map(type => type.trim())
            .filter(type => type)
            .some(acceptedType => {
              if (/\..+$/.test(acceptedType)) {
                //文件名后缀与设置的文件类型进行对比
                return extension === acceptedType;
              }
              if (/\/\*$/.test(acceptedType)) {
                return baseType === acceptedType.replace(/\/\*$/, '');
              }
              if (/^[^\/]+\/[^\/]+$/.test(acceptedType)) {
                return type === acceptedType;
              }
              return false;
            });
        }));
      }
    }
```
## 为什么用了drag属性后不触发on-change事件
```js
//在upload-dragger.vue执行完onDrop函数，将file传入on-file={uploadFiles}，触发uploadFiles
return (
      //判断是否允许拖拽，允许的话显示upload-dragger组件，不允许就显示所有插槽中的节点
      <div {...data} tabindex="0" >
        {
          drag
            ? <upload-dragger disabled={disabled} on-file={uploadFiles}>{this.$slots.default}</upload-dragger>
            : this.$slots.default
        }
        <input class="el-upload__input" type="file" ref="input" name={name} on-change={handleChange} multiple={multiple} accept={accept}></input>
      </div>
//这里的正常流程应该是拖动文件格式正确返回一个非空数组[xxx]，然后执行onstart，在onstart中执行onchange事件；
//如果拖动文件格式错误返回数组为空[]，那么会直接return。
uploadFiles(files) {
      //文件超出个数限制时，调用onExceed钩子函数
      if (this.limit && this.fileList.length + files.length > this.limit) {
        this.onExceed && this.onExceed(files, this.fileList);
        return;
      }
      //将files转成数组
      let postFiles = Array.prototype.slice.call(files);
      if (!this.multiple) { postFiles = postFiles.slice(0, 1); }

      if (postFiles.length === 0) { return; } //这行代码就是为什么不继续执行on-change的原因

      postFiles.forEach(rawFile => {
        this.onStart(rawFile); //调用on-start方法
        //选取文件后调用upload方法立即进行上传文件
        if (this.autoUpload) this.upload(rawFile);
      });
    },
```