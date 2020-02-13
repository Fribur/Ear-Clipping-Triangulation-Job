# Ear Clipping Triangulation Jobs

Implementation of the Triangulation by Ear Clipping with the Unity Job System.
The basic algorithm is explained in [this paper](http://www.geometrictools.com/Documentation/TriangulationByEarClipping.pdf)

## How to use it
Define and populate the NativeArray<float2> containing the vertices of the polygon.
```C#
NativeArray<float2> verts = new NativeArray<float2>(4, Allocator.TempJob);
verts[0] = new float2(0f, 0f);
verts[1] = new float2(1f, 0f);
verts[2] = new float2(1f, 1f);
verts[3] = new float2(0f, 1f);
```

Define the NativeArray<int> that will contain the triangles of the mesh as output of the job.
```C#
int ntris = (verts.Length - 2) * 3;
NativeArray<int> outTriangles = new NativeArray<int>(ntris, Allocator.TempJob);
```

Create and Schedule the job defining if the polygon is winded counter-clockwise or not.
```C#
EarClippingNoHolesJob triangulatorJob = new EarClippingNoHolesJob()
{
    isCCW = false,
    InPoints = verts,
    OutTris = outTriangles
};
JobHandle handle = triangulatorJob.Schedule();       
```       

When the job is completed get the triangles.
```C#
int[] triangles = new int[tris.Length];
for (int i = 0; i < tris.Length; ++i)
{
    triangles[i] = outTriangles[i];
}   
``` 

Dispose every native collection used.
```C#
verts.Dispose();
outTriangles.Dispose();
``` 

## Limitations
- Doesn't support polygons with holes
- Not compatible with the Burst compiler
