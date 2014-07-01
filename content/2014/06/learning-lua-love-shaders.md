Title: learning about lua love shaders
Date: 2014-06-30 10:57
Tags: lua, shaders
Category: lua
Slug: learning-about-lua-shaders
Author: tonetheman@gmail.com
Summary: who knew lua/love could do shaders

I did not know lua/love had shaders. I found a great [article on starting shaders](http://blogs.love2d.org/content/beginners-guide-shaders) today. Which was great in getting me started.

What is even funnier about this I have only basic knowledge of how shaders work. But I am really good at reading code and adapting things. Once I saw how shaders worked in love/lua I remembered a shader site I had seen before and began trying to translate that code over to lua.

```lua

local myShader = nil
local time

function love.load()
	time = 0
    myShader = love.graphics.newShader[[

extern number time;

	vec4 effect( vec4 color, Image texture, vec2 texture_coords, vec2 screen_coords ){
		

// Star Nest by Pablo Román Andrioli
// Modified a lot.

// This content is under the MIT License.

#define iterations 15
#define formuparam 0.530

#define volsteps 10
#define stepsize 0.120

#define zoom   1.900
#define tile   0.850
#define speed  0.0

#define brightness 0.0015
#define darkmatter 0.400
#define distfading 0.760
#define saturation 0.0010		
		
			// changed these two lines for lua
			vec2 uv=screen_coords.xy/love_ScreenSize.xy-.5;
			uv.y*=love_ScreenSize.y/love_ScreenSize.x;
			
			
			vec3 dir=vec3(uv*zoom,1.);
	
			float a2=time*speed+.5;
			float a1=0.0;
			mat2 rot1=mat2(cos(a1),sin(a1),-sin(a1),cos(a1));
			mat2 rot2=rot1;//mat2(cos(a2),sin(a2),-sin(a2),cos(a2));
			dir.xz*=rot1;
			dir.xy*=rot2;
			
			//from.x-=time;
			//mouse movement
			vec3 from=vec3(0.,0.,0.);
			from+=vec3(.05*time,.05*time,-2.);			
	from.xz*=rot1;
	from.xy*=rot2;
	
	//volumetric rendering
	float s=.1,fade=.2;
	vec3 v=vec3(0.4);
	for (int r=0; r<volsteps; r++) {
		vec3 p=from+s*dir*.5;
		p = abs(vec3(tile)-mod(p,vec3(tile*2.))); // tiling fold
		float pa,a=pa=0.;
		for (int i=0; i<iterations; i++) { 
			p=abs(p)/dot(p,p)-formuparam; // the magic formula
			a+=abs(length(p)-pa); // absolute sum of average change
			pa=length(p);
		}
		float dm=max(0.,darkmatter-a*a*.001); //dark matter
		a*=a*a*2.; // add contrast
		if (r>3) fade*=1.-dm; // dark matter, don't render near
		//v+=vec3(dm,dm*.5,0.);
		v+=fade;
		v+=vec3(s,s*s,s*s*s*s)*a*brightness*fade; // coloring based on distance
		fade*=distfading; // distance fading
		s+=stepsize;
	}
	v=mix(vec3(length(v)),v,saturation); //color adjust
	return vec4(v*.01,1.);



			}
    ]]
end
 
 function love.update(dt)
	time = time + dt;
	myShader:send("time",time)
end

function love.draw()
    love.graphics.setShader(myShader)

	love.graphics.rectangle("fill", 0, 0, 800, 600 )
	 
    love.graphics.setShader()
end
```

I found this code online and modified it only slightly to get it to work.

The first changes I had to make were this section

```lua
    // changed these two lines for lua
    vec2 uv=screen_coords.xy/love_ScreenSize.xy-.5;
    uv.y*=love_ScreenSize.y/love_ScreenSize.x;
```

Originally those lines looked like this

```lua
//get coords and direction
	vec2 uv=gl_FragCoord.xy/resolution.xy-.5;
	uv.y*=resolution.y/resolution.x;
```
The difference is that gl_FragCoord is ampped to screen_coords in love/lua and resolution is mapped to love_ScreenSize.

The other thing that also took me a little time to figure out was the time variable. This part of the shader code

```lua
extern number time;
```
allows you to have an input variable. I send the variable over during the love.update function.

```lua
myShader:send("time",time)
```
Once again I am just starting shader programming but this lets you update the shader based on an ever increasing time value.
