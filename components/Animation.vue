<template>
	<div class="animation">
		{{page}}
	</div>
</template>


<script>
	import { mapState } from 'vuex';
	import * as THREE from 'three';
	import {TimelineMax} from 'gsap';
	var OrbitControls = require('three-orbit-controls')(THREE);

	import VTKLoader from '@/lib/vtkloader';

	let loader = new THREE.VTKLoader();

	export default{
		data(){
			return {
				state: [],
				material: {},
				geometry: {}
			}
		},
		computed: mapState(['page']),
		watch: {
			page: function(newValue,oldValue){
				let vm = this;
				console.log(newValue,oldValue);
				vm.geometry.attributes.target = vm.state[newValue];
				vm.geometry.needsUpdate = true;

				let tl = new TimelineMax({onComplete: function(){
					vm.material.uniforms.blend.value = 0;
					vm.geometry.attributes.source = vm.geometry.attributes.target;
					vm.geometry.needsUpdate = true;
				}});

				tl
				  .to(vm.material.uniforms.blend,0.5,{value:1},0);
				
			}
		},
		mounted(){
			var camera, pos, controls, scene, renderer, geometry, geometry1, material;
			let vm = this;


			function init() {
			  scene = new THREE.Scene();
			  scene.background = new THREE.Color(0x000000);

			  renderer = new THREE.WebGLRenderer();

			  let states = [];
			  loader.load('/female.vtk',function(mesh) {
			    console.log(mesh);



			    vm.geometry = new THREE.BufferGeometry();

			    let numVertices = mesh.vertices.length;

			    let source = new Float32Array(numVertices*3);
			    let target = new Float32Array(numVertices*3);
			    let cube = new Float32Array(numVertices*3);
			    let sphere = new Float32Array(numVertices*3);
			    let positions = new Float32Array(numVertices*3);





			    vm.geometry.addAttribute('position', new THREE.BufferAttribute(positions,3));
			    vm.geometry.addAttribute('target', new THREE.BufferAttribute(positions,3));
			    vm.geometry.attributes.position.copyVector3sArray(mesh.vertices);

			    let temp = new THREE.BufferAttribute(positions,3);
			    states.push(temp.copyVector3sArray(mesh.vertices));
			    vm.state['index'] = temp.copyVector3sArray(mesh.vertices);



			    for (var i = 0; i <= numVertices*3; i=i+3) {
			      cube[i] = Math.random()*2;
			      cube[i+1] = Math.random()*2 + 1;
			      cube[i+2] = Math.random()*2 -1;
			    }

			    vm.geometry.addAttribute('source', new THREE.BufferAttribute(cube,3));
			    states.push(new THREE.BufferAttribute(cube,3));

			    vm.state['about'] = new THREE.BufferAttribute(cube,3);
			    console.log(vm.state,'VM STATE');


			    let teta,z;
			    for (var i = 0; i < numVertices*3; i=i+3) {

			      teta = Math.random()*2*Math.PI;
			      z = 2*Math.random()*2 - 1;
			      sphere[i] = 2*Math.sqrt(1 - z*z)*Math.cos(teta) ;
			      sphere[i+1] = 2*Math.sqrt(1 - z*z)*Math.sin(teta) + 2;
			      sphere[i+2] = 2*z;
			    }
			
			    
			    vm.state['another'] = new THREE.BufferAttribute( sphere, 3 );


			    // console.log($nuxt.$route.name,'HAPPINESSS');

			    vm.geometry.attributes.source = vm.state[$nuxt.$route.name];



			    vm.material = new THREE.RawShaderMaterial( {
			      uniforms: {
			        dot: { type: 't', value: new THREE.TextureLoader().load('/reddot.png') },
			        blend: { type: 'f', value: 0 },
			        size: { type: 'f', value: 2.1 }
			      },
			      vertexShader: `precision highp float;

		attribute vec3 position;
		attribute vec3 source;
		attribute vec3 target;

		uniform mat4 modelViewMatrix;
		uniform mat4 projectionMatrix;

		uniform float size;
		uniform float blend;
		uniform sampler2D sourceTex;
		uniform sampler2D targetTex;
		uniform vec2 dimensions;

		varying vec3 vColor;
		void main() {

			

			vec3 result = source*(1. - blend) + blend*target;
			vColor = normalize(result);
			vec4 mvPosition = modelViewMatrix * vec4( result, 1. );
			gl_PointSize = size * ( 1. / - mvPosition.z );
			gl_Position = projectionMatrix * mvPosition;
		}`,
			      fragmentShader: `precision highp float;
		varying vec3 vColor;
		uniform sampler2D dot;
		void main() {
			vec4 color = texture2D(dot,gl_PointCoord);
			gl_FragColor = vec4(vColor,1.);
		}`,
			      alphaTest: 0.5,
			      transparent: true
			    });

			    let points = new THREE.Points(vm.geometry,vm.material);
			    scene.add(points);
			    points.position.y = -2;
			    points.rotation.y = -1.6;


			    



			  });



			  renderer.setPixelRatio(window.devicePixelRatio);
			  renderer.setSize(window.innerWidth, window.innerWidth);

			  var container = vm.$el;
			  container.appendChild(renderer.domElement);

			  camera = new THREE.PerspectiveCamera(
			    70,
			    window.innerWidth / window.innerHeight,
			    0.001, 500
			  );
			  camera.position.set( 0, 0, 4 );


			  controls = new OrbitControls(camera, renderer.domElement);

			  resize();
			    

			 
			}


			function resize() {
			  var w = window.innerWidth;
			  var h = window.innerHeight;
			  renderer.setSize( w, h );
			  camera.aspect = w / h;
			  camera.updateProjectionMatrix();
			}

			let time = 0;
			function animate() {
			  time++;
			  
			  requestAnimationFrame(animate);
			  render();
			}

			function render() {
			  renderer.render(scene, camera);
			}

			init();
			animate();
		}
	}
</script>

<style>
	.animation{
		position: fixed;
	}
</style>