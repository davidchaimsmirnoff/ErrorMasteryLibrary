
Beginner-Friendly Project Ladder (C++ → Blur Overlay)
Project 1: Hello World (console) ✅ 

Learn: how to write, compile, and run C++ code.

Code:

#include <iostream>
int main() {
    std::cout << "Hello, world!\n";
    return 0;
}


✅ Done when: it prints "Hello, world!" in a terminal.



--------------------------------------------------------------------------------

Project 2: Basic Windows App (blank window) ✅

Learn: how to create a Win32 window (no graphics yet).

Code: just WinMain, WndProc, CreateWindowEx, message loop.
✅ Done when: A blank resizable window pops up.

--------------------------------------------------------------------------------

Project 3: Transparent overlay window✅

Learn: Win32 extended window styles.

Goal: Fullscreen, borderless, always-on-top window.

Add WS_EX_LAYERED | WS_EX_TRANSPARENT → click-through toggle with a key.
✅ Done when: Your window covers the screen, but you can still click apps underneath.

--------------------------------------------------------------------------------


Project 4: Drawing a color (D3D11 basics) ✅

Learn: initialize Direct3D 11.

Goal: Create a device/swapchain, clear the screen blue each frame.
✅ Done when: Your overlay is just a solid color (no flicker).

--------------------------------------------------------------------------------

Project 5: Fullscreen triangle

Learn: how shaders work.

Goal: Draw a triangle that covers the window, color it with a pixel shader.
 Done when: You see a red (or any color) screen from your shader.

Project 6: Textures

Learn: load a bitmap and show it.

Goal: Draw a fullscreen triangle, sample the texture in the pixel shader.
 Done when: The image fills your overlay window.

Project 7: Mask buffer (brush + eraser)

Learn: handle mouse input.

Goal: Keep a 2D byte array (mask), paint circles on mouse move. Upload to GPU texture.
 Done when: You can visualize mask (white = painted, black = empty).

Project 8: Screen capture

Learn: Desktop Duplication API.

Goal: Capture screen frames into a texture.
 Done when: Your overlay shows the live desktop feed.

Project 9: Composite shader

Learn: mix two images.

Goal: Blur the captured desktop texture, then mix original & blurred based on mask alpha.
 Done when: Painting mask causes blur in painted areas only.

Project 10: Separable blur

Learn: efficiency.

Goal: Implement horizontal+vertical passes for faster blur.
 Done when: Blur radius looks nice at 60fps.

Project 11: Performance tweaks

Goal: Use half/quarter res mask, only upload dirty tiles.
 Done when: Large brush strokes don’t lag.

Project 12: Multi-monitor support

Learn: Enumerate DXGI outputs.

Goal: Run one overlay per display, each capturing its own monitor.
 Done when: Both monitors can be blurred independently.

Project 13 (Optional): Per-app hook

Learn: MinHook or Detours.

Goal: Hook IDXGISwapChain::Present inside an app you own, blur inside only that window.
 Done when: One app is blurred, rest of desktop is not.

Project 14 (Optional): macOS backend

Learn: Objective-C++ basics, Metal shaders.

Goal: Replicate overlay on macOS with ScreenCaptureKit + Metal blur.
 Done when: Painting blur works on macOS after granting Screen Recording permission.

Philosophy

Each project is small, self-contained, and teaches exactly one new thing:

First, plain C++ syntax.

Then, Windows basics (window, overlay).

Then, graphics basics (D3D11 + shaders).

Then, specific features (mask, capture, blur).


Here’s the clean, numbered ladder of small apps to build on the way to the full desktop blur tool:

Minimal overlay window
— Fullscreen, borderless, always-on-top; toggle click-through (edit vs pass-through).

D3D11 init & render loop
— Create device/swapchain; clear screen each frame; handle resize.

Fullscreen triangle & texture sampling
— Draw a full-screen triangle; sample a test texture in a pixel shader.

Brush/Eraser mask (R8) + GPU upload
— Paint into a CPU R8 mask; upload to a GPU texture every frame.

Desktop capture (Desktop Duplication)
— Show the live desktop feed inside your overlay (AcquireNextFrame → ReleaseFrame).

Masked blur composite (baseline)
— Simple 9-tap blur; output = lerp(original, blurred, mask).

Separable blur (performance)
— Horizontal then vertical passes (Gaussian/box) with ping-pong render targets.

Mask at half/quarter res + dirty tiles
— Keep mask lower-res; bilinear upscale in shader; upload only changed tiles.

UX tools & hotkeys
— Brush size/strength, blur radius, clear/save/load mask; rectangle stickers (move/resize).

Multi-monitor support
— One overlay window + capture stream per display; independent blur regions.

Robustness & color correctness
— Handle device-lost, DPI/display changes, sleep/wake; sRGB/HDR sanity toggles.

(Optional) Per-app Present hook build
— Hook IDXGISwapChain::Present/ResizeBuffers for a target app you own; share the mask.

(Optional) macOS backend
— ScreenCaptureKit capture + Metal (MPS Gaussian blur) + borderless topmost window.

If you want, I’ll package 1–3 as a starter repo so you can begin at step 4 (mask painting)

By the time you reach Project 9, you’ll already have the core blur overlay working 🎉.
Projects 10–14 are about making it fast, robust, and cross-platform.


Thought for 5s

Love it—here’s a step-by-step project plan from zero to a working, fast desktop blur filter. Each step is a small, shippable project with clear goals and “you’re done when…” checks. Do them in order; you can skip the optional ones later.

Roadmap (13 bite-size projects)
0) Tooling & skeleton (Setup)

Goal: Be able to compile and run a Win32 GUI app.

Install: Visual Studio Community (Desktop C++ workload) or VS Code + MSVC + CMake.

Create a CMake or VS solution that builds a blank Win32 windowed app.
You’re done when: A borderless window opens and closes cleanly via Alt+F4.

1) Minimal overlay window

Goal: Fullscreen, borderless, always-on-top window with click-through toggle.

Use WS_POPUP + WS_EX_TOPMOST | WS_EX_LAYERED.

Toggle WS_EX_TRANSPARENT on Ctrl+Shift+B (edit vs click-through).
You’re done when:

Overlay covers the whole screen.

In “click-through,” desktop apps receive clicks under it.

In “edit,” the overlay receives mouse events.

2) Direct3D 11 init & render loop

Goal: Create D3D11 device/swapchain bound to your overlay and clear the screen.

Create device/context/swapchain; render loop with Present(1,0).

Handle WM_SIZE and recreate RTV on resize.
You’re done when: Window clears to a color at 60+ fps without stutter.

3) Fullscreen triangle + texture draw

Goal: Draw a fullscreen triangle and sample a texture in a pixel shader.

VS uses SV_VertexID (no vertex buffer); PS samples a bound SRV.

Upload a test texture (checkerboard) and show it.
You’re done when: You see your texture stretched full screen.

4) CPU mask (brush/eraser) + upload

Goal: Paint into a CPU R8 mask buffer with the mouse; upload to GPU each frame.

std::vector<uint8_t> mask(w*h); paint circles on WM_MOUSEMOVE while pressed.

Create a GPU R8 UNORM texture; update via UpdateSubresource (start naïve).

Add “Eraser” that paints 0’s.
You’re done when: You can toggle brush vs eraser and visualize the mask (e.g., draw mask as a greyscale overlay).

5) Desktop capture (Desktop Duplication API)

Goal: Show the live desktop inside your overlay.

Use IDXGIOutput1::DuplicateOutput → IDXGIOutputDuplication.

Per frame: AcquireNextFrame → copy to your SRV texture → ReleaseFrame.

Handle timeouts / DXGI_ERROR_ACCESS_LOST.
You’re done when: The overlay displays your real desktop feed (slight latency is fine for now).

6) Composite: orig ⟂ blurred by mask

Goal: Implement blur + composite in shaders.

Start with a simple 9-tap blur (demo-quality).

PS samples: orig = desktopTex, blurred = blur(orig), mask = maskTex.r; output lerp(orig, blurred, mask).
You’re done when: Painting the mask blurs only those regions of the live desktop.

7) Performance pass 1: separable blur

Goal: Replace the 9-tap with a two-pass separable blur (H then V).

Create two intermediate render targets; ping-pong horizontal/vertical passes.

Use Gaussian weights (5–9 taps per pass).
You’re done when: Blur radius looks good and frame rate is steady at full HD.

8) Performance pass 2: mask at ½ or ¼ res + dirty tiles

Goal: Cut bandwidth and CPU cost.

Keep the mask at quarter resolution; sample with bilinear upscaling in PS.

Track dirty tiles (e.g., 128×128). Only upload changed tiles with UpdateSubresource + D3D11_BOX.
You’re done when: Painting large areas doesn’t spike CPU or drop fps.

9) UX polish: tools & hotkeys

Goal: Usable toolbelt.

Brush size slider (keys [/]), blur strength slider (keys -/=).

Rectangular blur stickers: drag to move/resize; write into mask (filled rect).

Clear mask; save/load mask to PNG (R8) or RLE your tiles to JSON.
You’re done when: You can do a basic demo without touching the debugger.

10) Multi-monitor support

Goal: One overlay per monitor.

Enumerate displays via DXGI; create one borderless window per display.

One duplication stream per display (or switch to per-display capture).
You’re done when: All monitors can be blurred independently.

11) Reliability: device-lost, sleep, HDR/sRGB

Goal: Make it sturdy.

Handle DXGI_ERROR_DEVICE_REMOVED, re-create device/swapchain cleanly.

Respect formats (BGRA8 vs *_SRGB); add a simple color-space toggle for sanity.

Auto-recover after display changes, sleep/wake.
You’re done when: You can hot-plug monitors or sleep/resume and it keeps working.

12) Optional branch A: Per-app Present hook

Goal: Blur only inside a target process you own.

Add a separate DLL (MinHook) that hooks IDXGISwapChain::Present/ResizeBuffers.

Copy backbuffer → run your composite pass → call original Present.

Share the mask via shared memory or a shared Direct3D texture.
You’re done when: Your chosen app shows the blur; others don’t.

13) Optional branch B: macOS backend

Goal: Same UX on macOS (overlay + capture + blur).

Objective-C++ target: NSWindow (borderless, topmost, join-all-spaces).

Capture via ScreenCaptureKit; render via Metal; blur via MPSImageGaussianBlur.

Composite mix(orig, blurred, mask) in a fragment/compute pass.
You’re done when: Painting blur works on macOS with screen-recording permission.

Repo evolution (keep it tidy)
blur-overlay/
├─ app/
│  ├─ WinMain.cpp                 # Projects 1–11
│  ├─ d3d_device.cpp/.h          # 2–3
│  ├─ shaders/
│  │  ├─ fs_triangle_vs.hlsl     # 3
│  │  ├─ sample_ps.hlsl          # 3
│  │  ├─ blur_h_ps.hlsl          # 7
│  │  ├─ blur_v_ps.hlsl          # 7
│  │  └─ composite_ps.hlsl       # 6
│  ├─ duplication.cpp/.h         # 5
│  ├─ mask.cpp/.h                # 4,8,9
│  └─ util/ timers, logging
├─ hook/                          # 12 (optional)
│  ├─ dllmain.cpp
│  ├─ hook_present.cpp
│  └─ minhook/
├─ mac/                           # 13 (optional)
│  ├─ overlay_mac.mm
│  ├─ capture_scstream.mm
│  ├─ metal_renderer.mm
│  └─ shaders/*.metal
└─ CMakeLists.txt

Acceptance tests (quick checks each step)

Overlay click-through: Can you click buttons under your window when “edit” is off?

Resize safety: Drag screen edges, change DPI—no crashes, no stretched textures.

Duplication loop: No leaks; AcquireNextFrame/ReleaseFrame pairs balance; handles ACCESS_LOST.

Blur correctness: Small text near mask edges stays readable outside the blur.

Perf sanity: 1080p monitor holds 60 fps while painting fast; CPU < ~15% on mid-range machine (after step 8).

Optional warm-ups (if you prefer training wheels)

A) Re-implement steps 4 & 6 in Python + OpenCV first (brush into a mask, GaussianBlur, composite).

B) Swap OpenCV’s screen capture in later to prototype ideas quickly (but ship the C++/D3D version).

If you want, say the word and I’ll drop in a starter zip for Projects 1–3 (overlay + D3D11 + fullscreen triangle) so you can jump straight to the fun parts (mask painting & desktop capture).
