UI Overview {#ui-overview}
========================================

* `Urho3D/UI/` is legacy built-in Urho3D UI system. It is the most flexible UI solution, but it requires the biggest amount of hand-holding as well. You can do everything, but you will have to write a lot of code to make it work smoothly. Not actively maintained, bug fixes and minor tweaks only.
* `Urho3D/RmlUI/` is the integration of RmlUI library. Work in progress, but it is already useful. HTML/CSS like UI which requires certain knowledge to setup, but it supports data bindings and UI generation natively. DPI-aware. [Check out RmlUI documentation](https://mikke89.github.io/RmlUiDoc/).
* `Urho3D/SystemUI/` is the integration of Dear ImGUI library. Unlike other two UIs, it supports multiple native windows. It's not recommended to use it for actual game. Use this UI for Editor add-ons or other tools. [Check out Dear ImGUI documentation](https://github.com/ocornut/imgui/wiki).
