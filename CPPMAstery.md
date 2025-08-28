
Beginner-Friendly Project Ladder (C++ → Blur Overlay)
Project 1: Hello World (console)

Learn: how to write, compile, and run C++ code.

Code:

#include <iostream>
int main() {
    std::cout << "Hello, world!\n";
    return 0;
}


✅ Done when: it prints "Hello, world!" in a terminal.

Project 2: Basic Windows App (blank window)

Learn: how to create a Win32 window (no graphics yet).

Code: just WinMain, WndProc, CreateWindowEx, message loop.
✅ Done when: A blank resizable window pops up.

Project 3: Transparent overlay window

Learn: Win32 extended window styles.

Goal: Fullscreen, borderless, always-on-top window.

Add WS_EX_LAYERED | WS_EX_TRANSPARENT → click-through toggle with a key.
✅ Done when: Your window covers the screen, but you can still click apps underneath.

Project 4: Drawing a color (D3D11 basics)

Learn: initialize Direct3D 11.

Goal: Create a device/swapchain, clear the screen blue each frame.
✅ Done when: Your overlay is just a solid color (no flicker).

Project 5: Fullscreen triangle

Learn: how shaders work.

Goal: Draw a triangle that covers the window, color it with a pixel shader.
✅ Done when: You see a red (or any color) screen from your shader.

Project 6: Textures

Learn: load a bitmap and show it.

Goal: Draw a fullscreen triangle, sample the texture in the pixel shader.
✅ Done when: The image fills your overlay window.

Project 7: Mask buffer (brush + eraser)

Learn: handle mouse input.

Goal: Keep a 2D byte array (mask), paint circles on mouse move. Upload to GPU texture.
✅ Done when: You can visualize mask (white = painted, black = empty).

Project 8: Screen capture

Learn: Desktop Duplication API.

Goal: Capture screen frames into a texture.
✅ Done when: Your overlay shows the live desktop feed.

Project 9: Composite shader

Learn: mix two images.

Goal: Blur the captured desktop texture, then mix original & blurred based on mask alpha.
✅ Done when: Painting mask causes blur in painted areas only.

Project 10: Separable blur

Learn: efficiency.

Goal: Implement horizontal+vertical passes for faster blur.
✅ Done when: Blur radius looks nice at 60fps.

Project 11: Performance tweaks

Goal: Use half/quarter res mask, only upload dirty tiles.
✅ Done when: Large brush strokes don’t lag.

Project 12: Multi-monitor support

Learn: Enumerate DXGI outputs.

Goal: Run one overlay per display, each capturing its own monitor.
✅ Done when: Both monitors can be blurred independently.

Project 13 (Optional): Per-app hook

Learn: MinHook or Detours.

Goal: Hook IDXGISwapChain::Present inside an app you own, blur inside only that window.
✅ Done when: One app is blurred, rest of desktop is not.

Project 14 (Optional): macOS backend

Learn: Objective-C++ basics, Metal shaders.

Goal: Replicate overlay on macOS with ScreenCaptureKit + Metal blur.
✅ Done when: Painting blur works on macOS after granting Screen Recording permission.

Philosophy

Each project is small, self-contained, and teaches exactly one new thing:

First, plain C++ syntax.

Then, Windows basics (window, overlay).

Then, graphics basics (D3D11 + shaders).

Then, specific features (mask, capture, blur).

By the time you reach Project 9, you’ll already have the core blur overlay working 🎉.
Projects 10–14 are about making it fast, robust, and cross-platform.
