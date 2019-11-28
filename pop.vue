<template>
    <transition name="slide-fade">
     <div :class="['shade', showBlackGround ? 'shadeBlackground' : '']"   @click.self='close' v-if="showPop" v-fixed>
          <slot></slot>
     </div>
   </transition>
</template>
<style   scoped >
  .shade{
     position:fixed;
     top:0px;
     left: 0px;
     bottom: 0px;
     right:0px;
     z-index:9999999;
  }
  .shadeBlackground{
     background-color: rgba(0, 0, 0, 0.4);
  }
  .content{
     box-shadow: 0 2px 10px 0 rgba(0, 0, 0, 0.15);
     background:none;
     border: 1px red solid;
     height: 100%;
  }
.slide-fade-enter-active {
  transition: all .2s ease;
}
.slide-fade-leave-active {
  transition: all .2s cubic-bezier(1.0, 0.5, 0.8, 1.0);
}
.slide-fade-enter, .slide-fade-leave-to {
  transform: translateY(8px);
  opacity: 0;
}
</style>
<script>
export default {
  name: 'chopePop',
  props: {
    showPop: {  
      type: Boolean,
      default:false
    },
    showBlackGround: {  
      type: Boolean,
      default:true
    },
  },
  directives: {
    fixed: {
      // inserted 被绑定元素插入父节点时调用
      inserted () {
        let scrollTop = document.body.scrollTop || document.documentElement.scrollTop
        document.body.style.cssText += 'position:fixed;width:100%;top:-' + scrollTop + 'px;'
      },
      // unbind 指令与元素解绑时调用
      unbind () {
        let body = document.body
        body.style.position = ''
        let top = body.style.top
        document.body.scrollTop = document.documentElement.scrollTop = -parseInt(top)
        body.style.top = ''
      }
    }
  } ,
  data () {
    return {
      show:false
    }
  },
  watch: {
   showPop(val){
   
   }
  },
  computed: {
  },
  mounted () {
    document.body.appendChild(this.$el);
  },
  destroyed () {
    document.body.removeChild(this.$el);
  },
  methods: {
    close(){
        this.$emit('close');
    },
    stop(e){
      e.preventDefault();
      e.stopPropagation();
      return false;
    }
  }
}
</script>


