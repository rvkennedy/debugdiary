---
title: "Units for Physically Based Rendering"
seoTitle: "Units for Physically Based Rendering"
seoDescription: "Units for Physically Based Rendering"
datePublished: Sat Jul 30 2016 12:56:00 GMT+0000 (Coordinated Universal Time)
cuid: clsd9xito000309l7dui1da94
slug: units-for-physically-based-rendering-1
canonical: https://dbgdiary.blogspot.com/2016/07/units-for-physically-based-rendering.html
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1707397883729/9c085e4f-bfb2-40fa-8b38-7d2366671f2c.png

---

Physically-based rendering (PBR) should really use physical units, though many PBR engines don't.  
  
  [![](https://cdn.hashnode.com/res/hashnode/image/upload/v1707397877339/5d702660-7739-43f4-9332-73cde382b2ba.png)](https://1.bp.blogspot.com/-fYKkIYJjAZk/V20qzt4PJKI/AAAAAAAADUY/w0PQajLayBkuZHpjqJz3pHmpAzGlG9m3ACLcB/s1600/SolarIrradiance.png)  
  
Sunlight is typically described as being in watts per square metre. But that represents the energy across the entire spectrum, and has no concept of colour.  
  
So for PBR, the units for directional light, e.g. sunlight are watts per square metre per nanometre.  
  
Sunlight comes from so far away that the direction of the light is essentially parallel. But a local light source like a light bulb emits its energy in all directions.  
  
So the irradiance due to a light bulb varies with distance from the bulb. If the total spectral power of the bulb, P (the _spectral flux_), is measured in watts per nanometre (for any given colour on the spectrum), suppose that the bulb has a radius of r metres, and thus a surface area of $4 \\pi r^2$ square metres. Then the irradiance, at the surface, will be $I=P/ (4 \\pi r^2) w/m^2/nm$, in the same units as sunlight.  
  
But away from the bulb, at distance R, the same power passes through a larger surface area. So again, $I = P / (4 \\pi R^2)$, where P is the same total spectral power as before.  
  
Thus $I(R) = I(r) (\\frac{r}{R})^2$ - the irradiance follows an inverse-square power law.  
  
So we don't use irradiance to measure point lights. If the light is uniformly distributed by direction, we can use spectral flux P, watts/nm.  
  
the radiance at any point is given in watts per steradian per nm, where there are 4pi steradians across the entire sphere.  
  

#### Rendering

The challenge with rendering is to recreate (on a lcd monitor, cinema screen, or in print) the radiance that the you would perceive if you were really looking at the thing the image represents.  
Imagine you want to create the experience of flying at ten thousand feet. It looks _something_ like this:  

[![](https://cdn.hashnode.com/res/hashnode/image/upload/v1707397878194/0fa397da-6a0e-4018-a949-30ea56567488.png)](https://3.bp.blogspot.com/-fzWYjzW4FTc/V5zKkuJ9-tI/AAAAAAAADV4/ciBmb8UrzEAMPra7PMIBSsRM4A5fJOQcgCLcB/s1600/photo_10k.png)

photo at 10,000 feet altitude

[![](https://cdn.hashnode.com/res/hashnode/image/upload/v1707397879228/4b7a4a12-8cb0-463e-80b4-ed58cf8098da.png)](https://2.bp.blogspot.com/-9mK7F5Y1644/V5z586a5cYI/AAAAAAAADWs/S4DfgzUzaNMcc0V5I4NbVF12qoVo_4MlgCLcB/s1600/render_10k.png)

trueSKY render at 10,000 feet altitude

  

But in rendering we don't (necessarily) want to recreate what the photo or video of something would look like: that's actually a more complex problem. The fundamental challenge of rendering is to see if we can recreate the real thing. Consider one point in the image, perhaps part of the sky. The light from that point that the eye would perceive is defined by a spectrum, like this:  

[![](https://cdn.hashnode.com/res/hashnode/image/upload/v1707397880058/423bda29-c0fc-49f0-b5fa-b9d9a96dfb09.png)](https://4.bp.blogspot.com/-y1hU2JE4xZY/V5zbEh21J3I/AAAAAAAADWY/rlpFnNbdnZ8rmvXSFXT4J445QkB-i9HFwCLcB/s1600/sky_spectral_radiance.png)

and the way the human eye would perceive it is defined by the response of its three types of cone (ignoring rods for now - those are for night-vision).

  

[![](https://cdn.hashnode.com/res/hashnode/image/upload/v1707397881013/68c62412-6692-443e-8c9c-e54f4e2e226e.png)](https://1.bp.blogspot.com/-a0xD3oix4Ek/V5zbGtTiglI/AAAAAAAADWc/qsVenwxeWdYLiabcOpBBJmDAM3usDyj-gCLcB/s1600/spec_rad_eye.png)

The three cone types correspond only roughly to red green and blue, and they overlap considerably. So they are called X, Y and Z. These [functions](https://graphics.stanford.edu/courses/cs148-10-summer/docs/2010--kerr--cie_xyz.pdf), mapped by the [CIE](http://www.cie.co.at/), don't describe a precise physiological response in the eye - but they do allow us to match perceived colours from different sources as the eye sees them. Having these functions, we can calculate three responses:  
  
$X= \\int\_{\\lambda} f\_x(\\lambda) E(\\lambda) d\\lambda $  
$Y= \\int f\_y E$  

$Z= \\int f\_z E$

  

X, Y and Z are the three numbers we want to reproduce.  
Now monitors have red, green, and blue elements to each pixel, and the wavelengths of these are pretty sharply concentrated. They're not single-frequency spikes like lasers, but they're clearly separated.  

[![](https://cdn.hashnode.com/res/hashnode/image/upload/v1707397881997/c7904f84-f062-455b-a971-9dbf4b8e4d49.png)](https://3.bp.blogspot.com/-glAZ3_gbSHg/V5z_eswJQzI/AAAAAAAADW8/5_QeimaUe58rknjaSdXyfeimRhhWs_o1ACLcB/s1600/monitor.png)

So in the "true" image we had one continuous spectrum, but the monitor gives out three distinct colours. You can calibrate your monitor to get its exact curves (see e.g. [here](http://display-corner.epfl.ch/index.php/ASUS_VG248QE)) although the calibration will only be valid until you adjust the monitor's settings.

  

We would like to reproduce the same three X, Y, and Z values as above: that will make the colour _and brightness_ of the point/pixel look the same as reality to the eye.

  

Of course, the eye response curves are meant to represent the typical _human_ eye. If your eyes don't match the standard curves - for example, if you're colour blind - that assumption is invalid, and the two radiances won't look the same to you.

  

$X= \\int f\_x E\_m$  
$Y= \\int f\_y E\_m$  

$Z= \\int f\_z E\_m$

  

where $E\_m$ is the monitor spectral radiance, which is a combination of what the red, green, and blue elements are putting out:

  

$E\_m(\\lambda)=R\_m(\\lambda)+G\_m(\\lambda)+B\_m(\\lambda)$

  
At any given $\\lambda$, we expect at most one of those values to be significant.  
  
So for a known spectral radiance distribution, we solve for Rm, Gm and Bm:  
  
$\\int f\_x E = \\int f\_x (R\_m+G\_m+B\_m)$  
$\\int f\_y E = \\int f\_y (R\_m+G\_m+B\_m)$  

$\\int f\_z E = \\int f\_z (R\_m+G\_m+B\_m)$

  

Note we can't simply say

  

$\\int f\_x E = \\int f\_x B\_m$

  

etc. X, Y and Z are _not_ exactly blue, green and red. More like blue, yellowy-green, and greeny-yellow-violet. Our eyes know how to interpret the infinite combinations of cone responses into colour and brightness.

  

Let's assume that the spectral profile of our monitor is known, and that generally:

  

$R\_m(\\lambda)=R \\times m\_R(\\lambda)$

  

where R is the brightness, from zero to one, of the red part of the pixel, and $m\_R$ is a known function for the monitor.

  

\\begin{align}  
\\int f\_x E &= \\int f\_x (R m\_R+G m\_G+B m\_B) \\\\  
 &=R \\int f\_x m\_R + G \\int f\_x m\_G + B \\int f\_x m\_B \\\\  
 \\end{align}  
  
So knowing the eye functions $f\_x$ etc., and the monitor functions $m\_R$ etc, the right-hand-side integrations can be precalculated, leaving us with:  
  
\\begin{align}  
\\int f\_x E &= R I\_xR + G I\_xG + B I\_xB \\\\  
\\int f\_y E &= R I\_yR + G I\_yG + B I\_yB \\\\  
\\int f\_z E &= R I\_zR + G I\_zG + B I\_zB \\\\  
 \\end{align}  
  
This is a 3x3 matrix equation: linear algebra.  
  
\\begin{align}  
c &= M c\_m  
\\end{align}  
  
where c is the vector of three "ground truth" integrals, $c\_m$ is the vector of three monitor rgb values (assuming a linear monitor - more on this later), and $M$ is the monitor-eye matrix, which is constant for a given monitor and viewer.  
  
While X is kind-of blue, and Z is kind-of red, we can't really regard any of the integral constants that make up M as being close enough to zero to be negligible, except for maybe $I\_xR$. We'll leave it in for now. We must get the matrix inverse of M to solve for $c\_m$. If we _knew_ the shape of $E(\\lambda)$ through the whole spectrum, and assuming we have all the monitor data, and assuming we have a viewer with typical human eyes, we'd be able to calculate the exact $c\_m$, the exact RGB values to send to the monitor that will reproduce the ground truth view. We would have to hope, as well, that when we've found $c\_m$, none of its members are greater than 1.0. Because monitors have low dynamic range, for now, we can't represent many of the brightness values that in real life we encounter every day.  
  
Much of the above must be taken on trust, or worked around. But what do we know about c? We probably haven't calculated the entire spectral radiance curve for the visual spectrum, for each pixel onscreen. We've _probably_ calculated three values. Again, red, green, and blue. And we must make an assumption about how those three numbers approximate the full spectrum.  
  
Suppose we assume that each of our three calculated values represents a range of the spectrum over which the spectral radiance is constant:  
  

[![](https://cdn.hashnode.com/res/hashnode/image/upload/v1707397882955/a22440da-a34c-4c9d-b0e3-5c666cb559e8.png)](https://4.bp.blogspot.com/-WZXxoHgxuzE/V50NcCHwSVI/AAAAAAAADXM/2jZ-Q_L7ICkN-OoByi51qysRkR1hWYHNgCLcB/s1600/rendered_eye.png)

We can refine this later with a better shape. But our three columns roughly approximate the full spectrum, and they allow us to calculate $c$ as follows:

  

  
\\begin{align}  
c\_x &= \\int f\_x E\_d \\\\  
c\_y &= \\int f\_y E\_d \\\\  
c\_z &= \\int f\_z E\_d \\\\  
 \\end{align}  
  
where $E\_d$ is our _rendered_ sr curve, which is:  
  
\\begin{align}  
E\_d &= R\_d (r\_0 \\le \\lambda < r\_1) \\\\  
&= G\_d (g\_0 \\le \\lambda < g\_1) \\\\  
&= B\_d (b\_0 \\le \\lambda < b\_1) \\\\  
 \\end{align}  
  
where $R\_d$ etc are the rendered spectral radiances. So we can now calculate $c$, or at least $c\_d$, the rendered approximation to the ground truth. And finally:  
  
$c\_m = M^{-1} $c\_d