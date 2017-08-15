# KHR\_materials\_cmnBlinnPhong

## Contributors

* Norbert Nopper, UX3D, <mailto:nopper@ux3d.io>
* Thomas Kress, UX3D, <mailto:kress@ux3d.io>

## Status

Draft (not ratified yet)

## Dependencies

Written against the glTF 2.0 spec.

## Overview

This extension defines a Blinn-Phong material, which belongs to possible set of common materials for use with glTF 2.0. 

Even Physically Based Rendering (PBR) materials are state of the art and included in core glTF 2.0 or another extension, the demand for materials based on e.g. a Phong model is still there.

This extension defines one common material type, Blinn-Phong, as other or simpler materials can be described by having a zero factor. The Blinn-Phong is prefered over the Phong model, as it is more used. If a need for a Phong model is still needed, an extension named e.g. `cmnPhong` should be specified. 

## Extending Materials

The common Blinn-Phong material is defined by adding the `KHR_materials_cmnBlinnPhong` extension to any glTF material. 
For example, the following defines a material having all possible parameters.:

```
{
    "materials": [
        {
            "name": "All parameters",
            "extensions": {
                "KHR_materials_cmnBlinnPhong": {
                    "diffuseFactor" : [
                        0.8, 
                        0.8, 
                        0.8, 
                        1.0
                    ], 
                    "diffuseTexture" : {
                        "index" : 0
                    }, 
                    "shininessFactor" : 2.0, 
                    "specularFactor" : [
                        0.1, 
                        0.1, 
                        0.1
                    ],
                    "specularShininessTexture" : {
                        "index" : 1
                    }, 
                }
            }

        }
    ]
}
```
### Blinn - Phong

The Blinn-Phong material model is defined by the following properties:
* `diffuse` - Reflected diffuse color of the material
* `specular` - Specular color of the material
* `shininess` - Shininess factor of the specular color

This equation is complex and detailed via the ACM, so it is not detailed here. Refer to “Models of Light Reflection for Computer Synthesized Pictures,” SIGGRAPH 77, pp 192-198 http://portal.acm.org/citation.cfm?id=563893.

The following code illustrates the basic computation:

```
diffuseTerm   = diffuseFactor   * diffuseTexture 
specularTerm  = specularFactor  * specularShininessTexture.rgb 
shineTerm     = shininessFactor * specularShininessTexture.a
emissiveTerm  = emissiveFactor  * emissiveTexture // core Materials spec
occlusionTerm = occlusionFactor * occlusionTexture // core Materials spec 

color = emissiveTerm + occlusionTerm * (diffuseTerm * max(N * L, 0) + specularTerm * max(H * N, 0)^shineTerm)
```

where

* `N` – Normal vector
* `L` – Light vector
* `H` – Half-angle vector,calculated as halfway between the unit Eye and Light vectors, using the
equation H= normalize(I+L)

Textures are optional, and if provided, will modulate the base Factor value. Note that `specularShininessTexture` contains the specular modulation as the rgb components and shininess modulation as the alpha component. 

The following table lists the allowed types and ranges for the specular-glossiness model:

|                            |Type         |Description|Required|
|----------------------------|-------------|-----------|--------|
|**diffuseFactor**           | `number[4]` | The reflected diffuse factor of the material.|No, default: `[1.0,1.0,1.0,1.0]`|
|**diffuseTexture**          | [`textureInfo`](/specification/2.0/README.md#reference-textureInfo) | The diffuse texture.|No|
|**specularFactor**          | `number[3]` | The specular RGB color of the material.      |No, default: `[1.0,1.0,1.0]`|
|**shininessFactor**         | `number`    | The shininess of the material.|No, default: `1.0`          |
|**specularShininessTexture**| [`textureInfo`](/specification/2.0/README.md#reference-textureInfo)|The specular shininess texture.|No|

Additional properties are allowed.
