<template>
    <div ref="sceneEl" class="d-flex m-auto overflow-hidden scene"></div>
</template>

<script>
export default {
    name: "MainScene"
};
</script>

<script setup>
import { onBeforeUnmount, onMounted, ref } from "vue";
import * as PIXI from "pixi.js";

const sceneEl = ref(null);
let app = null;

onMounted(async () => {
    app = new PIXI.Application();
    globalThis.__PIXI_APP__ = app;

    await app.init({
        width: window.innerWidth, // Set initial width
        height: window.innerHeight, // Set initial height
        backgroundColor: 0x1099bb,
        resizeTo: window
    });

    // Append the PixiJS canvas to the container
    if (sceneEl.value) {
        sceneEl.value.appendChild(app.canvas);
    }

    resize();
    window.addEventListener("resize", resize);

    // load the PNG asynchronously
    await PIXI.Assets.init({ manifest: "src/assets/manifest.json" });
    // 2734/1427
    console.log(app.screen.height, app.screen.width, app.screen);

    const style = new PIXI.TextStyle({
        align: "center",
        fontFamily: "Arial",
        fontSize: (app.screen.width / app.screen.height) * 22,
        fontStyle: "italic",
        fontWeight: "bold",
        fill: "#ffffff",
        stroke: { color: "#4a1850", width: 5, join: "round" },
        dropShadow: {
            color: "#000000",
            blur: 4,
            angle: Math.PI / 6,
            distance: 6
        },
        wordWrap: true,
        wordWrapWidth: 340
    });

    const homeScene = new PIXI.Container();

    app.stage.addChild(homeScene);

    await PIXI.Assets.loadBundle("home-screen");
    const bgHomeTexture = PIXI.Assets.get("background");
    const charHomeImage = PIXI.Assets.get("character");
    const signHomeImage = PIXI.Assets.get("signboard");

    const bgHomeSprite = new PIXI.Sprite(bgHomeTexture);
    const charHomeSprite = new PIXI.Sprite(charHomeImage);
    const signHomeSprite = new PIXI.Sprite(signHomeImage);

    // Set the background sprite to cover the entire scene
    bgHomeSprite.width = app.screen.width;
    bgHomeSprite.height = app.screen.height;
    charHomeSprite.width = app.screen.width / 5.3;
    charHomeSprite.height = app.screen.height / 3.3;
    charHomeSprite.x = app.screen.width / 2.44 - charHomeSprite.width / 2;
    charHomeSprite.y = app.screen.height / 1.93 - charHomeSprite.height / 2;
    charHomeSprite.angle = 4;
    signHomeSprite.width = app.screen.width / 5.3;
    signHomeSprite.height = app.screen.height / 3.3;
    signHomeSprite.x = app.screen.width - signHomeSprite.width;

    homeScene.addChild(bgHomeSprite);
    homeScene.addChild(charHomeSprite);
    homeScene.addChild(signHomeSprite);

    const titleHome = new PIXI.Text({
        text: "Lord of The <Code>",
        style
    });

    titleHome.x = app.screen.width - signHomeSprite.width / 1.275;
    titleHome.y = signHomeSprite.height / 1.625;
    titleHome.angle = -9;

    homeScene.addChild(titleHome);

    // // Add a variable to count up the seconds our demo has been running
    // let elapsed = 0.0
    // // Tell our application's ticker to run a new callback every frame, passing
    // // in the amount of time that has passed since the last tick
    // app.ticker.add(ticker => {
    //   // Add the time to our total elapsed time
    //   elapsed += ticker.deltaTime
    //   // Update the bgHome's X position based on the cosine of our elapsed time.  We divide
    //   // by 50 to slow the animation down a bit...
    //   bgHome.x = 100.0 + Math.cos(elapsed / 50.0) * 100.0
    // })
});

// Cleanup the PixiJS application before the component is unmounted
onBeforeUnmount(() => {
    if (app) {
        app.destroy(true, { children: true });
    }
    window.removeEventListener("resize", resize); // Clean up event listener
});

function resize() {
    if (!app) return;

    // Get the new width and height
    const width = window.innerWidth;
    const height = window.innerHeight;

    // Resize the PixiJS renderer to fit the new window size
    app.renderer.resize(width, height);

    if (app.stage.children.length > 0) {
        const bgHomeSprite = app.stage.children[0].children[0]; // Get background sprite
        bgHomeSprite.width = width;
        bgHomeSprite.height = height;

        // Adjust character position based on new size (if needed)
        const charHomeSprite = app.stage.children[0].children[1]; // Get character sprite
        charHomeSprite.x = width / 2.44 - charHomeSprite.width / 2;
        charHomeSprite.y = height / 1.93 - charHomeSprite.height / 2;
        charHomeSprite.width = width / 5.3;
        charHomeSprite.height = height / 3.3;

        const signHomeSprite = app.stage.children[0].children[2]; // Get character sprite
        signHomeSprite.width = app.screen.width / 5.3;
        signHomeSprite.height = app.screen.height / 3.3;
        signHomeSprite.x = app.screen.width - signHomeSprite.width;

        const titleHome = app.stage.children[0].children[3];
        titleHome.x = app.screen.width - signHomeSprite.width / 1.275;
        titleHome.y = signHomeSprite.height / 1.625;
    }
}
</script>

<style lang="scss" scoped>
.scene {
    width: 100%;
    height: 100%;
    display: flex;
    align-items: center;
    justify-content: center;
    overflow: hidden;
}
</style>
