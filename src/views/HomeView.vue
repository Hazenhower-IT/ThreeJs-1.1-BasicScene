<template>
  <div id="canvasContainer" style="width:400px; height:350px; padding:20px;">
  <canvas ref="canvasRef"></canvas>
  </div>
</template>

<script setup>
import { ref, onMounted} from "vue"
import * as THREE from "three"



let canvasRef = ref();



// Per iniziare con threejs abbiamo bisogno di 4 elementi, una scena, una camera, un renderer, e degli oggetti da mostrare
//la Scena e la camera vengono inizializzate nel setup mentre il renderer nel onMounted

// La scena è come un container al cui interno posizioniamo i nostri oggetti, luci, modelli ecc.
let scene = new THREE.Scene()

// il renderer ci serve per dire a threeJs di renderizzare questa scena
let renderer

/* 
  All'interno della scena reindirizzeremo i nostri oggetti che possono essere di diversi tipi:
  - Geometrie primitive (cubi, sfere, ecc)
  - Modelli importati
  - Particles
  - Luci
  - Etc

  cominciamo con un semplice cubo rosso
*/

//Per fare cio, abbiamo bisogno di creare una Mesh, che è una combinazione di una geometria (la forma) + un materiale (il colore, ed altre proprietà ad esempio la reazione alla luce,ecc)
// Iniziamo con una BoxGeometry e un MeshBasicMaterial
const boxGeometry = new THREE.BoxGeometry(1,1,1)
const boxMaterial = new THREE.MeshBasicMaterial({color: 0xff0000}) 
//ci sono molti modi per passare il colore al materiale, puoi anche usare l'esadecimale (es: "#ff0000") o come stringa ("red")
// O possiamo addirittura creare i colori e assegnargli ad una variabile per poi riutilizzarli in questo modo es: const color = new THREE.Color(0xff0000) 
// N.B. il MeshBasicMaterial è l'unico che non ha bisogno di luci per essere visibile

//Uniamo geometria e materiale per formare la mesh
const box = new THREE.Mesh(boxGeometry,boxMaterial)

//aggiungiamo la mesh alla scena
scene.add(box)

// LA CAMERA
// 1) La camera ci serve per avere appunto il "punto di vista" sulla scena
//    Il renderer è come se facesse una foto alla nostra scena, ma da dove la sta facendo questa foto? 
//    modificando la posizione della camera, ed i suoi parametri noi cambiamo questo punto di vista. 
// 2) La camera non è un oggetto visibile come la geometria creata poco fa.
// 3) Puoi avere piu camere sulla scena ma quando facciamo il render, questo proviene da una camera,
//    è pero possibile switchare da una camera all'altra quando vuoi, anche se sconsigliato 
//    visto che possiamo spostare la nostra camera con javascript.
//4) Ci sono diversi tipi di Camera, per iniziare useremo la piu semplice, la PerspectiveCamera
//   che in sostanza funziona così: se un oggetto è lontano si vedra piccolo, se un oggetto è vicino si vedra piu grande
let camera = new THREE.PerspectiveCamera(
  75, //vertical field of view
  window.innerWidth / window.innerHeight, //aspect ratio
  /*Optional
  0.1, //near plane
  100 //far plane
  */
);
camera.position.y = 1;
camera.position.z = 3;
camera.position.x= -1;
scene.add(camera);


onMounted(() => {
  // Il renderer renderizza la scena dal punto di vista della telecamera
  // e ne scrive i risultati sul canvas
  // Un canvas è un elemento html in cui abbiamo la possibilità di disegnare cose,
  // e threeJs usa WebGL per disegnare il render all'interno di questo canvas
  renderer = new THREE.WebGLRenderer({
    canvas: canvasRef.value
  });

 
  const canvasContainer = document.getElementById("canvasContainer");
  renderer.setSize(canvasContainer.clientWidth, canvasContainer.clientHeight);
  renderer.setPixelRatio(window.devicePixelRatio);
  renderer.render(scene, camera);

});

</script>

<style>
canvas {
  width: 100%;
  height: 100%;
  display: block;
}
</style>