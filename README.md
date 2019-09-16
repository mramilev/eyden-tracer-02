# Practical Assignment 2
## Problem 2.2
### Encapsulate camera and primitives from main application logic (Points 5)
1. Fork the current repository
2. Study the new framework-code of Shader.h, ShaderFlat.h, ShaderEyeLight.h, Scene.h, and main.cpp
3. A pointer ```CPrim* hit``` is now contained in your ```Ray``` structure. After a ray has been successfully intersected with a primitive, store the primitive’s address in ```hit``` (if the hit ditance is smaller than ```ray.t```).
4. In the class ```CScene``` you find a method ```Add(Primitive*)```. Change your code accordingly using the appropriate vector defined in the class.
5. Rather then intersecting each primitive in the main function we will now use the ```Intersect(Ray& ray)``` method of the ```Scene``` class. After modification the method should iterate over all primitives, intersect them and return true or false depending on if we had a valid hit with the scene data or not.
6. The loop of main.cpp calls the ```CScene::RayTrace(Ray& ray)``` method. This method should call ```Intersect()``` and depending on a hit or not return a white or black color.
## Problem 2.3
### The Surface-Shader Concept (Points 10 + 10 + 30)
A _surface-shader_ is a small program that is assigned to a primitive and is responsible for computing the color of each ray hitting this primitive. For example, a _flat shader_ might just return a constant color for a primitive, whereas another shader might compute more complex effects such as lighting, shadows, or texturing.

In this exercise, you will add some missing parts of the given basic shader framework to your ray tracer and implement two simple shaders:
1. Implement a simple flat shader. Proceed as follows:
    - The shader class has a pure virtual function ```Vec3f CShader::Shade(Ray& ray)``` , which has to be implemented in all derived shaders.
    - Implement the ```CShaderFlat::Shade(Ray& ray)``` method. The method should just return the color passed in the constructor of ```CShaderFlat```.
    - Each primitive has a pointer ```Shader* shader``` in the new framework and a corresponding modified Constructor definition. Adjust the Constructor code appropriate. For example, our red sphere could be initialized using ```CSphere s1(Vec3f(-2.0f, 1.7f, 0), 2, new CShaderFlat(Vec3f(1, 0, 0), &scene));```. As we will see later some shaders need access to
the scene data (_e.g_ for light or shadow calculations), this is why each shader gets a pointer to the scene objects.
    - Finally, if, for instance, the primitive intersected by a ray has been stored in ```CPrim* hit```, the appropriate color can then be computed by calling ```hit->shader->Shade(ray)```; Change your code in ```RayTrace``` such that not black or white is returned but the color from the primitive with the closest hit or the background color if a ray does not hit a primitive.
2. Implement the ```Shade```-method in the eye light shader, which uses the angle between the incoming ray and the surface normal at the hit point to achieve a better impression of the actual primitive’s shape. The resulting color should be calculated according to: 
_result_ = |cos(_theta_)|·_color_
where _theta_ is the angle between the primitive surface normal and the ray direction. As the shader now needs to know some information about the primitive (_i.e._ the surface normal), some modifications are necessary:
    - Implement the ```Vec3f Primitive::GetNormal(Ray& ray)``` method in all classes derived from ```CPrim```. ```GetNormal(Ray& ray)``` should return the normalized normal of the primitive. The ray parameter passed to ```GetNormal(Ray& ray)``` should be a ray that has been successfully intersected before, so you can assume that the intersection stored in this ray corresponds to the actual primitive. For example, ray.org + ray.t * ray.dir should be a point on the primitive.
    - Implement the shading function ```CShaderEyelight::Shade(Ray& ray)``` using ```ray.hit->GetNormal(ray)``` to retrieve the surface normal of the primitive. With the surface normal the above given formula can be applied. If the test scene specified in main.cpp is rendered with these two shaders it should look like:
    
![]()
## Problem 2.4
### Phong Shading and Point Light sources
TBA
## Problem 2.5
### Shadows (Points 20)
To add more realism to the phong model we want now to incorporate shadows into it. Proceed as follows:
- Implement the method ```Occluded()``` in the ```CScene``` class, which should check if something blocks the light.
- Modify ```CShaderPhong::Shade()``` to check for occlusion.
If everything is implemented correct your images should look like this:

![]()
## Problem 2.6
### Area Lights (Points 20)
As you have learned in the last exercise, shadows can add important visual information to an image. Until now we have only considered point lights. Point lights create _hard shadows_ because a point light can not be partly occluded and is either blocked or not. To render more realistic shadows we need amore advanced light source. _Area Lights_ are able to produce _soft shadows_ which are more natural. In this exercise we implement a ```CLightArea``` (in LightArea.h) which is defined by four points in space:
- Calculate the normal and the area of the LightArea in the constructor.
- Calculate the intensity as described in the lecture by generating a random sample position on the area light (using ```dgm::random::U()``` and bi-linear interpolation).
- Add ```CLightArea areaLight(&scene, lightIntensity, Vec3f(-1.5f, 10.0f, -1.5f), Vec3f(1.5f, 10.0f, 1.5f), Vec3f(1.5f, 10.0f, -1.5f), Vec3f(-1.5f, 10.0f, 1.5f));``` to main.cpp and remove the point lights.
- Render an image with 1000 shadow rays per pixel
If everything is implemented correct your images should look like this:

![]()
