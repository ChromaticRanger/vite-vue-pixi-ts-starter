<template>
    <div ref="pixiContainer"></div>
</template>

<script setup lang="ts"> 
import { ref, onMounted, onUnmounted, defineProps } from 'vue';
import * as PIXI from 'pixi.js';

const props = defineProps<{
    width: number,
    height: number,
    backgroundColor: PIXI.ColorSource
}>();

const pixiContainer = ref<HTMLDivElement | null>(null);
const pixiApp = ref<PIXI.Application | null>(null);

const initialisePixi = async (): Promise<void> => {
    const app = new PIXI.Application();
    pixiApp.value = app;
    
    await app.init( {
        width: props.width,
        height: props.height,
        backgroundColor: props.backgroundColor,
    } );
    
    if (pixiContainer.value) {
        pixiContainer.value.appendChild(pixiApp.value.canvas);
        await PIXI.Assets.load([ "scene/background.png" ]);
    } else {
        console.error('Pixi container not found');
    }
}

/**
 * Draw a rectangle that moves back and forth horizontally
 */
// const drawGraphics = () => {
//     const rec = new PIXI.Graphics();
//     rec.roundRect( 0, 0, 32, 32, 10 ).fill( 0xE02F66 );
//     rec.position.set( 150 - rec.width / 2, 150 - rec.height / 2 );
//     let recSpeed: number = 300;
// 
//     pixiApp.value!.stage.addChild( rec );
// 
//     pixiApp.value!.ticker.add( ( ticker ) => {
//         const delta = ticker.deltaTime / 100;
//         rec.x += recSpeed * delta;
// 
//         if( rec.x > pixiApp.value!.screen.width - rec.width ) {
//             rec.x = pixiApp.value!.screen.width - rec.width;
//             recSpeed = -recSpeed;
//         } else if( rec.x < 0 ) {
//             rec.x = 0;
//             recSpeed = -recSpeed;
//         }
//     } );
// }

onMounted( async () => {
    console.log('PixiApp mounted 5');
    await initialisePixi();
    // drawGraphics();
    const background = PIXI.Sprite.from( "scene/background.png" );
    pixiApp.value!.stage.addChild(background);
    pixiApp.value!.stage.scale.x = pixiApp.value!.canvas.width / background.width;
    pixiApp.value!.stage.scale.y = pixiApp.value!.canvas.height / background.height;
});

onUnmounted(() => {
    // Destroy PixiJS app and resources to prevent memory leaks
    if ( pixiApp.value ) {
        pixiApp.value.destroy( true, true );
    }
});
</script>
