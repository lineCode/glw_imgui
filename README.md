![](https://github.com/yak32/glw_imgui/workflows/status/badge.svg) [![License](https://img.shields.io/badge/License-BSD%202--Clause-orange.svg)](https://opensource.org/licenses/BSD-2-Clause)

# glw_imgui - C++ IMGUI implementation
Immediate Mode UI C++ implementation.

IMGUI is a code-driven, simple and bloat-free GUI system, widely used in modern game engines and games.
Best explanation of IMGUI concept is [here](https://www.youtube.com/watch?v=Z1qyvQsjK5Y).

#### IMGUI: creation, usage and rendering of a button in one line:
![Alt text](/../feature-screenshots/screenshots/cancel_button.png)
```c++

// simple button
if (gui.button("Cancel"))
	do_cancel_action();

```

All logic is hidden in the Gui::button() call, rendering commands are issued and added to the render queue. String pooling is used to pack strings in render queue and avoid memory allocation during rendering. Rendering is graphics API independent, platform specific rendering should be implemented (the reference implementation by SDL is provided in the demo project.)

The library has basic suppot for layouting (attachable toolbars, layout serialization), themes (right now colors only), lock-free multithreading (triple buffering).

### Gallery
Basic GUI manipulations, various controls, resizable and movable toolbars. Entire UI is built using IMGUI.
[![ScreenShot](/../feature-screenshots/screenshots/IMGUI_toolbars.png)](https://www.youtube.com/watch?v=TlJiuguyLVo)

The Editor of [Glow game engine](http://www.glow3d.com)



#### Build and run:

**Windows:**
Prerequisites: cmake, C++ compiler
```
mkdir build
cd build
cmake ..
cmake --build . --target install
```
Run demo.exe from demo/install/ folder.

**Linux:**
Prerequisites: cmake, C++ compiler, SDL2

Install SDL2
```
sudo apt-get install libsdl2-dev
```

Build:
```
mkdir build
cd build
cmake ..
cmake --build . --target install
cd ../demo/install/
./demo
```

**MacOS:**
Prerequisites: cmake, C++ compiler, SDL2

Install SDL2
```
brew update SDL2
```

Build:
```
mkdir build
cd build
cmake ..
cmake --build . --target install
cd ../demo/install/
./demo
```


### Sample UI update loop with various controls
```c++
	int mouse_x, mouse_y;

	uint keysPressed = handle_input(x, y);
	int w, h;
	SDL_GetWindowSize(gWindow, &w, &h);
	ui.set_text_align(ALIGN_LEFT);

	ui.begin_frame(w, h, x, y, -mouse_wheel, last_char, keysPressed);
	last_char = 0;
	mouse_wheel = 0;

	ui.begin_rollout(rollout);
	static bool collapsed = false;
	if (ui.collapse("Collapse", collapsed))
		collapsed = !collapsed;

	if (!collapsed) {
		ui.indent();
		ui.button("Button1");
		ui.button("Button2");
		ui.button("Button3");
		ui.button("Button4");
		ui.separator(true);

		static char combo_value[100] = "Item1";
		if (ui.combo("Combo Box", combo_value)) {
			if (ui.combo_item("Item1"))
				strcpy(combo_value, "Item1");
			if (ui.combo_item("Item2"))
				strcpy(combo_value, "Item2");
			if (ui.combo_item("Item3"))
				strcpy(combo_value, "Item3");
			if (ui.combo_item("Item4"))
				strcpy(combo_value, "Item4");
		}
		ui.separator();

		static bool checked = false;
		if (ui.check("Checkbox", checked))
			checked = !checked;

		ui.separator(true);
		ui.texture("texture.jpg");
		ui.rectangle(100);
		ui.texture(nullptr);

		ui.separator();
		ui.separator(true);
		ui.label("Edit text");
		static char edit_value[256];
		bool edit_finished = false;
		ui.edit(edit_value, 256, &edit_finished);
		ui.separator(true);

		ui.label("Label");
		ui.value("Value");
		static float val = 1.0f;
		ui.slider("Slider", &val, 0.0f, 10.0f, 1.0f);

		ui.separator(true);
		ui.label("Progress bar");
		static float progress = 7.0f;
		ui.progress(progress, 0.0f, 10.0f, 1.0f);

		ui.row(3);
		ui.button("Item1");
		ui.button("Item2");
		ui.button("Item3");
		ui.end_row();

		static char str_property[100] = "Property Val";
		ui.property("Property", str_property, 100, NULL);
		ui.draw_text(5, 5, 0, "Draw item", 0xffffffff);

		ui.unindent();
	}
	ui.end_rollout();

	ui.begin_rollout(vert_rollout);

	char str[100];
	static int selected = -1;
	for (int i = 0; i < 100; ++i) {
		sprintf(str, "item %d", i);
		if (ui.item(str, i == selected))
			selected = i;
	}

	ui.end_rollout();

	ui.end_frame();
```
![Alt text](/../feature-screenshots/screenshots/ref_ui.png)

## FAQ

The license?
[BSD](https://opensource.org/licenses/BSD-2-Clause)

### Dependencies
 STL, c-runtime.

### Performance
Rendering - almost allocation-free (and I will try to make it allocation-free completely in the future)

### Compatibility
  Working on Windows (tested with Visual Studio 2013, 2015), MacOS and Linux.

The library is based on IMGUI implementation from Recast library by Mikko Mononen
https://github.com/recastnavigation/recastnavigation


