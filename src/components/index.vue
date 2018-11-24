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
                 background:attr.background,border:'1px solid ' + attr.borderColor,borderRadius:attr.radius + 'px' }"></div>
            <div class="canvas-item"
                 @mousedown="dragBegin($event,attr)"
                 :class="{current:currentAttr === attr}"
                 v-if="attr.name === 'Image'" :style="{width:attr.width + 'px', height: attr.height + 'px',
                 left:attr.x + 'px', top:attr.y + 'px',
                 border:'1px solid ' + attr.borderColor,
                 background:'red',borderRadius:attr.radius ? '50%' : '0' }"></div>
            <div class="canvas-item"
                 @mousedown="dragBegin($event,attr)"
                 :class="{current:currentAttr === attr}"
                 v-if="attr.name === 'Text'" :style="{
                 left:attr.x + 'px', top:attr.y + 'px',
                 maxWidth:attr.maxWidth,fontSize:attr.fontSize,color:attr.color,height:attr.limitRow+'em',overflow:'hidden',textOverflow: 'ellipsis' }">{{attr.text}}</div>
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
            </el-form>
          </div>
          <!--Text配置属性-->

          <div class="setItem" v-if="currentAttr.name === 'Text'">
            <h4>Text Attribute</h4>
            <el-form  label-width="100px" >
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
                <el-input type="textarea" autosize placeholder="请输入内容">{{currentAttr.text}}</el-input>
              </el-form-item>
              <el-form-item label="color">
                <el-color-picker></el-color-picker>
              </el-form-item>
            </el-form>
          </div>

        </template>

      </div>
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
            currentAttr:null
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
              fontSize:14
            }
          } else {
            attr = {
              x:0,
              y:0,
              width:50,
              height:50
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
            border:1px solid deepskyblue;
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
