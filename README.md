This pixel shader will highlight out of gamut pixels by fully desaturating to grayscale the pixels which lie within gamut.

 It can be used as a post-resize shader in MPC-BE (and toggled it on/off with ctrl+alt+p) along with HDR passthrough via madVR. Others have tested and confirmed it works fine with SyncRenderer and EVR as well.
 
 After checking gamut geometrically in xyY, I am now also clipping to P3 RGB and checking dEITP vs original pixel. I did this because I wanted to try to quantify how perceivable the differences are rather than treating all pixels technically beyond P3 the same, and I'm using a dEITP threshold to 1 to eliminate pixels which are technically beyond P3 but don't even qualify as a JND (just noticeable difference) even when simply compared to "dumb" clipping to P3.

Here's the general approach for each pixel:
1. Convert PQ to linear
2. Convert 2020 RGB to XYZ
3. Convert XYZ to xyY
4. Check if inside gamut triangle
5. Convert XYZ to smaller gamut RGB
6. Clip to smaller gamut
7. Convert back to XYZ
8. Convert back to 2020 RGB
9. Check dEITP of original pixel vs clipped pixel

Usage in MPC-BE (MPC-HC is very similar):
1. Place code in a .hlsl file such as outofgamut.hlsl and place the .hlsl file in the MPC-BE Shaders folder (normally AppData\Roaming\MPC-BE\Shaders)
2. In MPC-BE, goto Shaders...Select shaders... and select the shader and add it as a post-resize shader, and enable post-resize shaders.
3. All post-resize shaders can be toggled on/off using ctrl+alt+p
