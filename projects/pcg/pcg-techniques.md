---
title: PCG Techniques
created: 2024-07-12
draft: true
publish: false
tags:
---
# PCG Techniques

# Map generation
### Voronoi / Delaunay triangulation
The neightbouring regions are well defined

### Noise
- Voronoi Noise
	- Regions in space
- OpenSimplex / perlin noise
	- Countignous, good with octaves
- [FMB](https://en.wikipedia.org/wiki/Fractional_Brownian_motion)
- [Erosion simulation with the analytical derivative of the noise](https://youtu.be/C9RyEiEzMiU?t=1817)
		- [Great explonation](https://www.youtube.com/watch?v=gsJHzBTPG0Y&t=312s)

### Erosion with analytical derivative

```cpp
// Meta parameters
int octaves = ...;
int seed = ...;
float lacuranity = ...;
float gain = ...;

// Algorithm
vec2f p = {x, y, z};

float sum = 0.5f;
float freq = 1.0f;
float amp = 1.0f;
vec2f dsum = {0, 0};

for (int i = 0; i < octaves; ++i) {
	vec3f grad = noise_derivative(p * freq, seed);
	dsum += amp * grad.x / (1 + dot(dsum, dsum));
	freq *= lacuranity;
	amp *= gain;
}

```

# Content generation
## Barnacling
Around larger objects put smaller ones, that makes them more natural
One could image multiple rings around objects that are increasingly smaller in size until it becomes the average around the object
## Footing
When two pieces connect the connection should have a perceivable feedback
## Greebling
Gluing objects togheter
## Resources
- [Spore insipered talk](https://www.youtube.com/watch?v=WumyfLEa6bU&t=957s&pp=ygUwZ2NkIHByYWN0aWNhbCBwcm9jZWR1bGFyIGdlbmVyYXRpb24gZm9yIGV2ZXJ5b25l)
	- Worlds, Behaviour, almost any content
- [Collection of good talks](https://www.youtube.com/results?search_query=gcd+practical+procedular+generation+for+everyone)
- [Map generation original](https://mewo2.com/notes/terrain/)
	- [Github code](https://github.com/mewo2/terrain/blob/master/terrain.js)
	
- [Contitnent generation with Voroni](https://squeakyspacebar.github.io/2017/07/12/Procedural-Map-Generation-With-Voronoi-Diagrams.html)
- [Blog series on Map generation](https://heredragonsabound.blogspot.com/2016/10/is-it-noisy-in-here.html)

