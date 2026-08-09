---
author: SplatterDash
desc: Creating and Using Shaders
lastUpdated: 2026-08-07T16:39:14.193Z
title: Advanced Topics - Shaders
---
<h1 id="what" sidebar="What Are Shaders?">What Are Shaders?</h1>

Shaders are scripts that modify how a graphic looks in-game. Think of shaders as post-processing effects in a video editor; while you're not modifying the file directly, you're changing how something looks in the project.

Shaders in Flixel - and by extension, shaders in Codename - are written in the OpenGL GLSL language. There are two types of shaders you can implement - `.frag` and `.vert`. The major difference between them is that `.frag` shaders modify the sprite through bitmaps/pixels, and `.vert` modifies the image through vertices/mathematics.

`.frag`s are called `fragment` shaders, and `.vert`s are called `vertex` shaders, as this is what most people call them.

This page will not cover all the details of *making* a shader, but you can find resources at the bottom of this page to do that. Instead, we'll talk about how to implement shaders into Codename, including importing shaders you find from places like [Shadertoy](https://www.shadertoy.com/), and we'll finish by making a basic shader while also talking about shader uniforms/variables.

<h2 id="cne-shaders" sidebar="Implementing CNE Shaders">Implementing CNE Shaders</h1>

Codename Engine comes pre-packaged with some shaders out of the box. Let's talk about how to implement those first!

For this example, we'll use [coloredVignette.frag](https://github.com/CodenameCrew/CodenameEngine/blob/main/assets/shaders/coloredVignette.frag) and apply it to the background sprite (`bg`) in [MainMenuState.hx](https://github.com/CodenameCrew/CodenameEngine/blob/main/source/funkin/menus/MainMenuState.hx). The line to implement this shader is simply:

<div style="display: flex; justify-content: center;">

```haxe
bg.shader = new CustomShader('coloredVignette');
```
</div>

It's really that simple to implement one of the custom Codename shaders. Note that you don't have to include the extension - Codename finds it for you. To see what other shaders are built into Codename, take a look at the [shaders folder](https://github.com/CodenameCrew/CodenameEngine/tree/main/assets/shaders) in source assets.

<h2 id="custom-shaders" sidebar="Custom Shaders">Implementing/Converting Custom Shaders</h1>

What about shaders outside of the source assets? For this example, we'll use this [Fast CRT Shader](https://www.shadertoy.com/view/WsVSzV) from kbjwes77 on Shadertoy, and we're going to put it on our main camera using our global script (`./data/global.hx`).

## ⚠️ WARNING ⚠️
Always check to ensure that you have permission to use a shader before implementing it in your mod! Just because something is on Shadertoy doesn't inherently mean it's free to use!

Create a new file in your `shaders` folder (`./data/shaders/crt.frag`). Paste the code from the shader into the file. Then, in `global.hx`, add the following code to the `onPostStateSwitch` function:
<div style="display: grid; justify-content: left;">

```haxe
function onPostStateSwitch() {
	FlxG.camera.addShader(new CustomShader("crt"));
}
```
</div>

Now when you run the mod... the game crashes. Why is that?

Well, to make a shader work specifically in Flixel, there's a few modifications we need to do to the original code. They can be broken down to five simple changes:
1. Add `#pragma header` to the top of the file.
2. Change `void mainImage(out vec4 fragColor,in vec2 fragCoord)` to simply `void main()`.
3. Change `vec2 uv = fragCoord/iResolution.xy;` to `vec2 uv = openfl_TextureCoordv`. In some cases however, you may have to do the following instead:
```glsl
vec2 fragCoord = openfl_TextureCoordv*openfl_TextureSize.xy;
vec2 uv = fragCoord/openfl_TextureSize.xy;
```
4. Change `texture(iChannel0,uv)` to `texture2D(bitmap, uv)` *(see appendix)*
5. Change `fragColor` to `gl_fragColor`.

Now when you run the game, you should see the shader working and applied as intended!

There is also a tool called [Shadertoy to Flixel](https://theleername.github.io/ShadertoyToFlixel/) that can convert *most* Shadertoy shaders, not every shader is a 1:1 convert!
Will save you some time though!

<h2 id="create-shaders" sidebar="Create Shaders/Shader Variables">Creating Custom Shaders & Shader Variables</h1>

For our final example, we're going to create a simple shader from scratch. Our goal is to turn Boyfriend blue - but we'll have a variable to determine how blue he is. That's our special shader!

Create another file in `./data/shaders/`. Name it whatever you'd like, but use the `.frag` extension!

If we follow our previous steps, our code should look like this:
```glsl
#pragma header
// Always include that at the top of your file!!

// Float variable to determine intensity.
float blue = 0.5;

// Main function.
void main() {
    // Get color. Since we're modifying a sprite, not the entire game or a camera,
    // we'll use flixel_texture2D instead of texture2D.
    vec4 col = flixel_texture2D(bitmap, openfl_TextureCoordv);

    // Outputting the color. Since we want blue, we'll multiply each color's RGBA value
    // by 1 - blue, except for blue, which we'll multiply by blue directly.
    gl_fragColor = vec4(col.r * (1 - blue), col.g * (1 - blue), col.b * blue, col.a * (1 - blue));
}
```

But how do we change `blue`? Well, that's where `uniform` variables come in on GLSL shaders. Let's change `float blue = 0.5;` to `uniform float blue;`. (It's important not to set a default on uniforms.)

<div style="display: grid; justify-content: left;">

Then, in our hx file:
```haxe
var shader = new CustomShader("shader_name_here");
shader.blue = 0.5;
boyfriend.shader = shader;
```
</div>

Now not only is Boyfriend blue, but we can control how blue he is in a regular HScript file!

<h1 id="appendix" sidebar="Appendix">Appendix</h1>

The following is some good information to know:
- When getting the texture of a sprite or singular object, use `flixel_texture2d` when getting the texture of a camera or `FlxGame`, use `texture2D`.
- Don't use `0.` or `.0` for floats, instead use `0.0` (append a trailing number).
- Avoid using switch cases - use if statements to ensure macOS compatibility.
- Use floats instead of integers when initiating vectors (ex. `vec2(1.0, 1.0)` instead of `vec2(1, 1)`).
- Avoid using or initializing variables that start with `gl_`, or that use the name `input` or `sample` - these can break shaders on some or all platforms.
- The `ivec` family (2/3/4), `bvec` family, `uvec` family, and `uint` are all unsupported on certain platforms, so avoid using them.
- Avoid using the modulo operator and instead use the mod function.
- Avoid using arrays as some platforms don't support them.
- Runtime-dependent variables, such as `openfl_TextureCoordv` and `openfl_TextureSize`, should be established *inside* your `void main()` function and not outside of it.

<h1 id="further" sidebar="Further Reading">Further Reading</h1>

To learn more about constructing shaders, this page from [LearnOpenGL](https://learnopengl.com/getting-started/shaders) is a good starting resource.