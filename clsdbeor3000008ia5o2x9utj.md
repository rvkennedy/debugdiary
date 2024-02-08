---
title: "A virtual keyboard in Dear ImGui"
seoTitle: "A virtual keyboard in Dear ImGui"
datePublished: Wed Nov 10 2021 13:48:14 GMT+0000 (Coordinated Universal Time)
cuid: clsdbeor3000008ia5o2x9utj
slug: a-virtual-keyboard-in-dear-imgui-1
canonical: https://roderickkennedy.com/dbgdiary/a-virtual-keyboard-in-dear-imgui
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1707397842785/0690a622-5c25-4d9b-a7f8-5b6c4da89bb3.png

---

There are a few kinks implementing a virtual keyboard in [Dear ImGui](https://github.com/ocornut/imgui) - the main problem is getting key inputs from the buttons into the InputText item.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1707397840566/e41e4667-822d-4ed6-88d2-c2d546ee2163.png align="left")

Clicking a button changes focus to the button - away from the InputText. You can switch it back on the next pass, but ImGui won’t recognize it as being ready for input until two frames later.  
So I implemented a counter called “refocus”. It’s set to zero every time we click a keyboard button. It increments every frame. Only when it gets to 2 or greater do we pull the inputs from out of our buffer.

Our variables are:

std::vector keys\_pressed;

int refocus=0;

Here’s the code:

ImGui::Begin("Virtual Keyboard"); io.KeysDown\[VK\_BACK\] = false; if(refocus==0) { ImGui::SetKeyboardFocusHere(); } else if(refocus&gt;=2) { while(keys\_pressed.size()) { int k=keys\_pressed\[0\]; if(k==VK\_BACK) { io.KeysDown\[k\] = true; } else { io.AddInputCharacter(k); } keys\_pressed.erase(keys\_pressed.begin()); } } static char buf\[500\]; if(ImGui::InputText("", buf, IM\_ARRAYSIZE(buf))) { current\_url=buf; } refocus++;

We define a simple lambda function to add a line of keys to the keyboard:

auto KeyboardLine = \[&io,this\](const char\* key) { size\_t num = strlen(key); for (size\_t i = 0; i &lt; num; i++) { char key\_label\[\] = "X"; key\_label\[0\] = \*key; if (ImGui::Button(key\_label,ImVec2(46,32))) { refocus=0; keys\_pressed.push\_back(\*key); } key++; if (i&lt;num-1) ImGui::SameLine(); } };

This allows us to add the number keys simply with:

KeyboardLine("1234567890-"); ImGui::SameLine();

Now here’s our backspace arrow button:

if (ImGui::Button(ICON\_FK\_LONG\_ARROW\_LEFT,ImVec2(92,32))) { refocus=0; keys\_pressed.push\_back(ImGuiKey\_Backspace); }

Using a Text() call to insert a bit of padding in front of the next row of keys:

ImGui::Text(" "); ImGui::SameLine(); KeyboardLine("qwertyuiop"); ImGui::Text(" "); ImGui::SameLine(); KeyboardLine("asdfghjkl"); ImGui::SameLine(); if (ImGui::Button("Return",ImVec2(92,32))) { refocus=0; keys\_pressed.push\_back(ImGuiKey\_Enter); } ImGui::Text(" "); ImGui::SameLine(); KeyboardLine("zxcvbnm,./"); ImGui::End();

And done.