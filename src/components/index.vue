<template>
  <div class="index">
    <div class="editbox">
      <div class="toolbar" >
        <span v-for="item in commponents" @click="clickToolbar(item)">{{item.name}}</span>
      </div>
      <div class="preview"
           @mousemove="draging"
           @mouseup="dragEnd">
        <div class="canvas-box" :style="{ width:canvas_width + 'px', height: canvas_height + 'px' }">
          <template v-for="attr in attrs" >
            <div class="canvas-item"
                 @mousedown="dragBegin($event,attr)"
                 :class="{current:currentAttr === attr}"
                 v-if="attr.name === 'Rect'" :style="{width:attr.width + 'px', height: attr.height + 'px',
                 left:attr.x + 'px', top:attr.y + 'px',
                 zIndex:attr.zIndex,
                 background:attr.background,border:'1px solid ' + attr.borderColor,borderRadius:attr.radius + 'px' }"></div>
            <div class="canvas-item"
                 @mousedown="dragBegin($event,attr)"
                 :class="{current:currentAttr === attr}"
                 v-if="attr.name === 'Image'" :style="{width:attr.width + 'px', height: attr.height + 'px',
                 left:attr.x + 'px', top:attr.y + 'px',
                 border:'1px solid ' + attr.borderColor,
                 zIndex:attr.zIndex,
                 background:'red',borderRadius:attr.circle ? '50%' : '0' }"></div>
            <div class="canvas-item"
                 @mousedown="dragBegin($event,attr)"
                 :class="{current:currentAttr === attr}"
                 v-if="attr.name === 'Text'" :style="{
                 left:attr.x + 'px', top:attr.y + 'px',
                 zIndex:attr.zIndex,
                 maxWidth:attr.maxWidth + 'px',fontSize:attr.fontSize,color:attr.color,height:attr.limitRow+'em',overflow:'hidden',textOverflow: 'ellipsis' }">{{attr.text}}</div>
          </template>
        </div>
      </div>
      <div class="attribute">
        <!--基础属性-->
        <div class="setItem">
          <h4>Base Attribute</h4>
          <el-form  label-width="100px" >
            <el-form-item label="canvas width">
              <el-input style="width: 200px" size="mini" v-model="canvas_width">
                <template slot="append">px</template>
              </el-input>
            </el-form-item>
            <el-form-item label="canvas height">
              <el-input style="width: 200px" size="mini" v-model="canvas_height">
                <template slot="append">px</template>
              </el-input>
            </el-form-item>
            <el-form-item label="auto height">
              <el-switch
                active-color="#13ce66"
                inactive-color="#ff4949">
              </el-switch>
            </el-form-item>
          </el-form>
        </div>

        <!--其他配置属性-->
        <!--Rect配置属性-->
        <template v-if="currentAttr">
          <div class="setItem" v-if="currentAttr.name === 'Rect'">
            <h4>Rect Attribute</h4>
            <el-form  label-width="100px" >
              <el-form-item label="z-index">
                <el-input style="width: 200px" size="mini" type="number" v-model="currentAttr.zIndex">
                </el-input>
              </el-form-item>
              <el-form-item label="x">
                <el-input style="width: 200px" size="mini" type="number" v-model="currentAttr.x">
                  <template slot="append">px</template>
                </el-input>
              </el-form-item>
              <el-form-item label="y">
                <el-input style="width: 200px" size="mini" type="number" v-model="currentAttr.y">
                  <template slot="append">px</template>
                </el-input>
              </el-form-item>
              <el-form-item label="width">
                <el-input style="width: 200px" size="mini" type="number" v-model="currentAttr.width">
                  <template slot="append">px</template>
                </el-input>
              </el-form-item>
              <el-form-item label="height">
                <el-input  style="width: 200px" size="mini" type="number" v-model="currentAttr.height">
                  <template slot="append">px</template>
                </el-input>
              </el-form-item>
              <el-form-item label="radius">
                <el-input  style="width: 200px" size="mini" type="number" v-model="currentAttr.radius">
                  <template slot="append">px</template>
                </el-input>
              </el-form-item>
              <el-form-item label="backgound">
                <el-color-picker v-model="currentAttr.background"></el-color-picker>
              </el-form-item>
              <el-form-item label="border color">
                <el-color-picker v-model="currentAttr.borderColor"></el-color-picker>
              </el-form-item>
            </el-form>
          </div>
          <!--Image配置属性-->
          <div class="setItem" v-if="currentAttr.name === 'Image'">
            <h4>Image Attribute</h4>
            <el-form  label-width="100px" >
              <el-form-item label="z-index">
                <el-input style="width: 200px" size="mini" type="number" v-model="currentAttr.zIndex">
                </el-input>
              </el-form-item>
              <el-form-item label="x">
                <el-input style="width: 200px" size="mini" type="number" v-model="currentAttr.x">
                  <template slot="append">px</template>
                </el-input>
              </el-form-item>
              <el-form-item label="y">
                <el-input style="width: 200px" size="mini" type="number" v-model="currentAttr.y">
                  <template slot="append">px</template>
                </el-input>
              </el-form-item>
              <el-form-item label="width">
                <el-input style="width: 200px" size="mini" type="number" v-model="currentAttr.width">
                  <template slot="append">px</template>
                </el-input>
              </el-form-item>
              <el-form-item label="height">
                <el-input  style="width: 200px" size="mini" type="number" v-model="currentAttr.height">
                  <template slot="append">px</template>
                </el-input>
              </el-form-item>
              <el-form-item label="background">
                <el-input placeholder="请输入内容" size="mini" v-model="currentAttr.background">
                  <template slot="prepend">url</template>
                </el-input>
              </el-form-item>
              <el-form-item label="border color">
                <el-color-picker v-model="currentAttr.borderColor"></el-color-picker>
              </el-form-item>
              <el-form-item label="circle">
                <el-switch
                  v-model="currentAttr.circle"
                  active-color="#13ce66"
                  inactive-color="#ff4949">
                </el-switch>
              </el-form-item>
            </el-form>
          </div>
          <!--Text配置属性-->

          <div class="setItem" v-if="currentAttr.name === 'Text'">
            <h4>Text Attribute</h4>
            <el-form  label-width="100px" >
              <el-form-item label="z-index">
                <el-input style="width: 200px" size="mini" type="number" v-model="currentAttr.zIndex">
                </el-input>
              </el-form-item>
              <el-form-item label="x">
                <el-input style="width: 200px" size="mini" type="number" v-model="currentAttr.x">
                  <template slot="append">px</template>
                </el-input>
              </el-form-item>
              <el-form-item label="y">
                <el-input style="width: 200px" size="mini" type="number" v-model="currentAttr.y">
                  <template slot="append">px</template>
                </el-input>
              </el-form-item>
              <el-form-item label="font size">
                <el-input style="width: 200px" size="mini" type="number" v-model="currentAttr.fontSize">
                  <template slot="append">px</template>
                </el-input>
              </el-form-item>
              <el-form-item label="max width">
                <el-input  style="width: 200px" size="mini" type="number" v-model="currentAttr.maxWidth">
                  <template slot="append">px</template>
                </el-input>
              </el-form-item>
              <el-form-item label="limit row">
                <el-input placeholder="请输入内容" size="mini" type="number" v-model="currentAttr.limitRow">
                </el-input>
              </el-form-item>
              <el-form-item label="content">
                <el-input type="textarea" autosize placeholder="请输入内容" v-model="currentAttr.text"></el-input>
              </el-form-item>
              <el-form-item label="color">
                <el-color-picker v-model="currentAttr.color"></el-color-picker>
              </el-form-item>
            </el-form>
          </div>

        </template>
        <div style="text-align: right;margin-top: 50px">
          <el-button type="primary" size="mini" @click="genCode">生成代码</el-button>
        </div>
      </div>
    </div>
    <div class="genCodebox">
      <el-input
        autosize
        type="textarea"
        placeholder="请输入内容"
        v-model="canvasCode">
      </el-input>
    </div>
  </div>
</template>

<script>

  const TEXT = 'Text'
  const IMAGE = 'Image'
  const RECT = 'Rect'
  const RANGE = 10

    let commponents = [
      {
        name:IMAGE
      },
      {
        name:RECT
      },
      {
        name:TEXT
      }
    ]

    let isDrag = false
    let beginX = null
    let beginY = null
    let delX = null
    let delY = null
    let dragType = null




  export default {
        name: "index",
        data:function(){
          return {
            commponents:commponents,
            canvas_width:'375',
            canvas_height:'667',
            attrs:[],
            currentAttr:null,
            canvasCode:''
          }
        },
      methods:{
        clickToolbar(item){
          let attr

          if (item.name === TEXT){
            attr = {
              x:0,
              y:0,
              text:'这是文字',
              maxWidth:null,
              limitRow:null,
              color:'#333',
              fontSize:14,
              zIndex:1
            }
          } else {
            attr = {
              x:0,
              y:0,
              width:50,
              height:50,
              zIndex:1
            }
            if (item.name === IMAGE){
              attr['circle'] = false
              attr['url'] = null
            }else if (item.name === RECT) {
              attr['background'] = 'red'
              attr['borderColor'] = 'transparent'
              attr['radius'] = 0

            }
          }
          attr.name = item.name

          this.currentAttr = attr
          this.attrs.push(attr)


        },
        dragBegin(e,attr){
          console.log(e)
          this.currentAttr = attr
          beginX = e.clientX
          beginY = e.clientY
          isDrag = true


          if (this.currentAttr.width - e.offsetX < RANGE
            && this.currentAttr.height - e.offsetY < RANGE) {
            dragType = 'change'
          }else{
            dragType = 'move'
          }

        },
        draging(e,attr){
          if(isDrag){
            delX = e.clientX - beginX
            delY = e.clientY - beginY
            let width = parseFloat(this.currentAttr.width)
            let height = parseFloat(this.currentAttr.height)
            let x = parseFloat(this.currentAttr.x)
            let y = parseFloat(this.currentAttr.y)

            if(dragType === 'change'){
              this.currentAttr.width = width + delX
              this.currentAttr.height = height + delY
              if (this.currentAttr.width > this.canvas_width - this.currentAttr.x){
                this.currentAttr.width = this.canvas_width - this.currentAttr.x
              }
              if (this.currentAttr.height > this.canvas_height - this.currentAttr.y){
                this.currentAttr.height = this.canvas_height - this.currentAttr.y
              }

            }else if (dragType === 'move') {
              this.currentAttr.x = x + delX
              this.currentAttr.y = y + delY

              if (this.currentAttr.x < 0){
                this.currentAttr.x = 0
              }else if(this.currentAttr.x > this.canvas_width - this.currentAttr.width ){
                this.currentAttr.x = this.canvas_width - this.currentAttr.width
              }else if(this.currentAttr.y < 0 ){
                this.currentAttr.y = 0
              }else if(this.currentAttr.y > this.canvas_height- this.currentAttr.height ){
                this.currentAttr.y = this.canvas_height - this.currentAttr.height
              }

            }
            beginX = e.clientX
            beginY = e.clientY
          }



        },
        dragEnd(){
          isDrag = false
          console.log('结束')
        },
        genCode(){
          let new_attrs = this.attrs.slice()
          new_attrs.sort(function (a,b) {
            if (parseFloat(a.zIndex) < parseFloat(b.zIndex)){
              return -1
            }else if (parseFloat(a.zIndex) > parseFloat(b.zIndex)) {
              return 1
            }else {
              return 0
            }
          })


          let code = `

            <canvas canvas-id="canvas" style="width:${this.canvas_width}px; height: ${this.canvas_height}px;"></canvas>

            let context;
            const WIDTH = ${this.canvas_width}, HEIGHT = ${this.canvas_width};

            Page({
              data: {
              },
              onLoad(options) {
                context = wx.createCanvasContext('canvas');
                wx.chooseImage({
                  success: (res) => {
                    this.draw(res.tempFilePaths[0]);
                  }
                })

              },

              draw(url) {
          `

          for(let i = 0; i < new_attrs.length; i++){
            let new_attr = new_attrs[i]
            if (new_attr.name === RECT){
              new_attr.radius = parseFloat(new_attr.radius)
              if (new_attr.radius === 0){
                code += `
                  context.setFillStyle('${new_attr.background}')
                  context.fillRect(${new_attr.x},${new_attr.y},${new_attr.width},${new_attr.height});
                  context.setStrokeStyle('${new_attr.borderColor}')
                  context.strokeRect(${new_attr.x},${new_attr.y},${new_attr.width},${new_attr.height})
                `
              }else{
                code += `
                  context.beginPath();
                  context.moveTo(${new_attr.x + new_attr.radius},${new_attr.y})
                  context.lineTo(${new_attr.x + new_attr.width - new_attr.radius},${new_attr.y})
                  context.quadraticCurveTo(
                    ${new_attr.x + new_attr.width},
                    ${new_attr.y},
                    ${new_attr.x + new_attr.width},
                    ${new_attr.y + new_attr.radius}
                  )
                  context.lineTo(${new_attr.x + new_attr.width},${new_attr.y + new_attr.height - new_attr.radius})
                  context.quadraticCurveTo(
                    ${new_attr.x + new_attr.width},
                    ${new_attr.y + new_attr.height},
                    ${new_attr.x + new_attr.width - new_attr.radius},
                    ${new_attr.y + new_attr.height}
                  )
                  context.lineTo(${new_attr.x + new_attr.radius},${new_attr.y + new_attr.height})
                  context.quadraticCurveTo(
                    ${new_attr.x},
                    ${new_attr.y + new_attr.height},
                    ${new_attr.x},
                    ${new_attr.y + new_attr.height - new_attr.radius}
                  )
                  context.lineTo(${new_attr.x},${new_attr.y + new_attr.radius})
                  context.quadraticCurveTo(
                    ${new_attr.x},
                    ${new_attr.y},
                    ${new_attr.x + new_attr.radius},
                    ${new_attr.y}
                  )
                  context.closePath();

                  context.setFillStyle('${new_attr.background}')
                  context.fill();
                  context.setStrokeStyle('${new_attr.borderColor}')
                  context.stroke()
                `
              }
            }else if(new_attr.name === IMAGE){
              if (new_attr.circle){
                code += `
                  context.save()
                  context.beginPath()
                  context.arc(${new_attr.x + new_attr.width/2},${new_attr.y + new_attr.height/2},${new_attr.width/2} , 0, 2*Math.PI)
                  context.clip()
                  context.drawImage(url, ${new_attr.x}, ${new_attr.y}, ${new_attr.width}, ${new_attr.height})
                  context.restore()
                `
              } else{
                code += `
                  context.drawImage(url, ${new_attr.x}, ${new_attr.y}, ${new_attr.width}, ${new_attr.height})
                `
              }

            }else if(new_attr.name === TEXT) {
              if(new_attr.maxWidth || new_attr.limitRow){
                code += `
                  // const context = wx.createCanvasContext('myCanvas')
                  var text = '${new_attr.text}';//这是要绘制的文本
                  context.setFontSize(${new_attr.fontSize})
                  context.setFillStyle("${new_attr.color}")
                  let chr = text.split("");//这个方法是将一个字符串分割成字符串数组
                  let temp = "";
                  let row = [];
                  if(${new_attr.maxWidth}){
                    for (let a = 0; a < chr.length; a++) {
                      if (context.measureText(temp).width < ${new_attr.maxWidth}) {
                        temp += chr[a];
                      }
                      else {
                        a--; //这里添加了a-- 是为了防止字符丢失，效果图中有对比
                        row.push(temp);
                        temp = "";
                      }
                    }
                    row.push(temp);
                  }
                  if(${new_attr.limitRow}){
                    //如果数组长度大于2 则截取前两个
                    if (row.length > ${new_attr.limitRow}) {
                      let rowCut = row.slice(0,${new_attr.limitRow});
                      let rowPart = rowCut[${new_attr.limitRow} - 1];
                      console.log(rowPart.length)
                      let test = "";
                      let empty = [];
                      for (let a = 0; a < rowPart.length; a++) {
                        if (context.measureText(test).width < (${new_attr.maxWidth} - 30)) {
                          test += rowPart[a];
                        }
                        else {
                          break;
                        }
                      }
                      empty.push(test);
                      let group = empty[0] + "..."//这里只显示两行，超出的用...表示
                      rowCut.splice(1, 1, group);
                      row = rowCut;
                    }
                  }
                  for (var b = 0; b < row.length; b++) {
                    context.fillText(row[b], ${new_attr.x}, b*${new_attr.fontSize+5} + ${new_attr.y}, ${new_attr.maxWidth});
                  }
                `
              }else {
                code += `
                  context.fillText('${new_attr.text}', ${new_attr.x}, ${new_attr.y});
                `
              }
            }

          }

          code += `
               context.draw(false, () => {
                  wx.canvasToTempFilePath({

                    canvasId: 'canvas',
                    quality: 1,
                    success: (res) => {
                      console.log(res.tempFilePath)
                      this.setData({
                        image: res.tempFilePath
                      })
                      wx.saveImageToPhotosAlbum({
                        filePath: res.tempFilePath,
                      })
                    }
                  })
                })
              }
            })
          `

          this.canvasCode = code


        }
      },
      mounted(){

        // let canvasBox = document.getElementsByClassName('canvas-box')[0]
        // console.log(canvasBox)
        document.onkeydown = (event) => {
          console.log(event)
          if (! (event.target.tagName === 'INPUT' || event.target.tagName === 'TEXTAREA')){
            if(event.keyCode === 8) {
              var oIndex = this.attrs.indexOf(this.currentAttr)
              this.attrs.splice(oIndex,1)
            }
          }

      　}
      }
    }
</script>

<style lang="scss" scoped>
  .index {
    .editbox {
      display: flex;
      padding: 20px;
    }
    .toolbar {
      display:flex;
      flex-direction: column;
      margin-right: 20px;
      span {
        min-width: 60px;
        padding: 0 10px;
        height: 30px;
        line-height: 30px;
        margin: 5px 0;
        text-align: center;
        border:1px solid #ccc;
      }
    }
    .preview {
      border:1px solid #ccc;
      width: 200px;
      margin-right: 20px;
      flex-grow: 1;
      .canvas-box {
        border:1px solid #ccc;
        margin: 100px auto;
        position: relative;
        .canvas-item {
          position: absolute;
          -webkit-box-sizing: border-box;
          -moz-box-sizing: border-box;
          box-sizing: border-box;

          &.current {
            outline:1px solid deepskyblue;
          }
        }
      }
    }
    .attribute {
      padding: 20px;
      border:1px solid #ccc;
      width: 300px;
      .setItem {
        h4 {
          font-size: 16px;
          margin-bottom: 10px;
          text-align: left;
          font-weight: bold;
        }
        p {
          margin-bottom: 20px;
          text-align: left;
          span {
            font-size: 14px;
            color: #333;
            margin-right: 5px;
          }
        }
      }
    }
  }
</style>
