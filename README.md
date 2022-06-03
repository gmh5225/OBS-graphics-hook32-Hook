# OBS-graphics-hook32-Hook
Not mine. Only for saving.
https://www.unknowncheats.me/forum/anti-cheat-bypass/501609-obs-graphics-hook32-hook.html

I've studied many types of hooking before, and this kind of stuff caught my attention because it could be used to produce stream-proof rendering.

So I'd like to share it with those beginners out there.
![image](https://user-images.githubusercontent.com/13917777/171690576-a550822a-5615-44e4-9080-29857e82a50a.png)

"graphics-hook32.dll + 32DC8" is our target address, which should look like this in your IDA or [https://github.com/obsproject/obs-st...pture.cpp#L651](https://github.com/obsproject/obs-studio/blob/efd26b25ea9f201a104dfeca4228b397e9d415e4/plugins/win-capture/graphics-hook/d3d9-capture.cpp#L651)

![image](https://user-images.githubusercontent.com/13917777/171690929-87040845-a273-4ae7-bbce-e0643896f559.png)


After that, all you have to do is replace the pointer ( the old way ).
Since mov eax, Hook_Present_Addr
Hook_Present_Addr = &Present_hook
??? PROFIT Ready to P2C KEKEKEKEKEKW

```C++
void MainThread()
{
	while (!obsBase) // check if graphics-hook32.dll is not loaded
	{
		obsBase = (DWORD)GetModuleHandleA(XorStr("graphics-hook32.dll"));
		if (!obsBase)
			Sleep(200);
		else
			isOBSFound = true; 
	}
	if (isOBSFound)
	{
		renderOBSPresent = obsBase + 0x32DC8;
		while (!*reinterpret_cast<uintptr_t*>(renderOBSPresent))
		{
			Sleep(200);
		}
		OrigPresent = SwapPointer<Present_t>((uintptr_t)renderOBSPresent, (uintptr_t)&Hooked_Present);
	}
}
```

-----------------------------------------------------------------------------------------------------------------------------------
The function pointer we're replacing is the "RealPresent" shown on ida not the present_begin(RealPresent function is a .data ptr). You could also hook the present_begin and achieve the same result. This is also applicable for the x64 version of obs.

Note: this only works for game capture and d3d9 game

