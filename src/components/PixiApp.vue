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

onMounted( async () => {
    await initialisePixi();
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
