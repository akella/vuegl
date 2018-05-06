<template>
  <section class="container">
    <h1 class="js-split">Homepage</h1>
  </section>
</template>

<script>
import {TimelineMax} from 'gsap';
import Splitter from 'split-html-to-chars';

export default {
  mounted(){
    let els = this.$el.querySelectorAll(".js-split");
    [].forEach.call(els, function(el) {
        el.outerHTML = Splitter(el.outerHTML, '<span class="letter">$</span>');
    });
  },
  transition : {
      name: 'test',
      mode: 'out-in',
      css: false,
      enter: function(el,done){
        let tl = new TimelineMax({onComplete: done});
        let els = el.querySelectorAll(".letter");

        tl.set(els,{y:20,opacity:0});
        tl.staggerTo(els,0.5,{y:0,opacity:1,ease: Elastic.easeOut.config(1, 0.3)},0.05)

        // tl.fromTo(el,0.3,{y:-100,opacity:0,rotation:30},{y:0,opacity:1,rotation: 0})
      },
      leave: function(el,done){
        let tl = new TimelineMax({onComplete: done});
        tl.fromTo(el,0.3,{y:0,opacity:1},{y:100,opacity:0})
      }
    }
}
</script>
