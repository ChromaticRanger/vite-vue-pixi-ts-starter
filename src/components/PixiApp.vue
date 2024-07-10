<template>
    <div ref="pixiContainer"></div>
</template>

<script setup lang="ts"> 
import { ref, onMounted, onUnmounted } from 'vue';
import * as PIXI from 'pixi.js';

const pixiContainer = ref<HTMLDivElement | null>(null);
const pixiApp = ref<PIXI.Application | null>(null);

onMounted( async () => {
    console.log('PixiApp mounted');
    if ( pixiContainer.value ) {
        const app = new PIXI.Application();
        pixiApp.value = app;
        await app.init( {
            width: 300,
            height: 300,
            backgroundColor: 0xFFFFFF,
        } );
        pixiContainer.value.appendChild( app.canvas );

        const rec = new PIXI.Graphics();
        rec
            .roundRect( 0, 0, 32, 32, 10 )
            .fill( 0xE02F66 );
        rec.position.set( 150 - rec.width / 2, 150 - rec.height / 2 );
        let recSpeed: number = 300;

        app.stage.addChild( rec );

        app.ticker.add( ( ticker ) => {
            const delta = ticker.deltaTime / 100;
            rec.x += recSpeed * delta;

            if( rec.x > app.screen.width - rec.width ) {
                rec.x = app.screen.width - rec.width;
                recSpeed = -recSpeed;
            } else if( rec.x < 0 ) {
                rec.x = 0;
                recSpeed = -recSpeed;
            }
        } );

        // Load resources (sounds, textures, etc.)
        // Create PixiJS elements and add to the stage
        // Rest of your game/app logic
    } else {
        console.error('Pixi container not found');
    }
});

onUnmounted(() => {
    // Destroy PixiJS app and resources to prevent memory leaks
    if ( pixiApp.value ) {
        pixiApp.value.destroy( true, true );
    }
});
</script>
