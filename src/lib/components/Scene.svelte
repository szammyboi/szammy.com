<script>
    import { T, useThrelte } from '@threlte/core'
    import { ContactShadows, Grid, OrbitControls, Portal } from '@threlte/extras'
    import { Sheet, SheetObject, createTransformer } from '@threlte/theatre';

      import * as TheatreStuff from '@theatre/core';

    const { scene } = useThrelte()
    let lightHelper

    const hex = d => Number(d).toString(16).padStart(2, '0')
  </script>

<SheetObject key="Grid" let:Declare>
  <Declare props={{thick: 0.8, color: {r: 255, g: 0, b: 0}}} let:values>
    <Grid 
    backgroundOpacity={0}
    sectionColor="#000000"
    sectionThickness={0}
    infiniteGrid
    cellThickness={values.thick}
    cellColor={"#" + hex(values.color.r) + hex(values.color.g) + hex(values.color.b)}
    
  />
  </Declare>
</SheetObject>


<T.PerspectiveCamera
makeDefault
position={[-10, 10, 10]}
fov={15}
>
<OrbitControls
  autoRotate
  enableZoom={true}
  enableDamping
  autoRotateSpeed={0.0}
  target.y={1.5}
/>
</T.PerspectiveCamera>

<ContactShadows
  scale={10}
  blur={2}
  far={2.5}
  opacity={0.5}
/>

<SheetObject
  let:Transform
  let:Sync
  key="Torus"
>
  <Transform>
    <T.Mesh
      position={[0, 0, 0]}
      rotation.x={5}
      rotation.y={71}
      castShadow
    >
      <T.TorusKnotGeometry args={[0.5, 0.15, 100, 12, 2, 3]} />
      <T.MeshStandardMaterial color="#F85122">
        <Sync
          color
          roughness
          metalness
        />
      </T.MeshStandardMaterial>
      <Sync
          castShadow
          receiveShadow
        />
    </T.Mesh>
  </Transform>
</SheetObject>

<SheetObject
  let:Transform
  let:Sync
  key="Box"
>
  <Transform>
    <T.Mesh
      position={[0, 0, 0]}
      castShadow
    >
      <T.BoxGeometry args={[1, 1, 1]} />
      <T.MeshStandardMaterial color="#F85122">
        <Sync
          color
          roughness
          metalness
        />
      </T.MeshStandardMaterial>
      <Sync
          castShadow
          receiveShadow
        />
    </T.Mesh>
  </Transform>
</SheetObject>

<SheetObject
    let:Transform={TargetTransform}
    let:Sync
    key="Target"
    on:change={() => {
        if (!lightHelper) return
        lightHelper.update()
    }}
>
    <TargetTransform>
        <T.Object3D let:ref={targetRef}>
            <Portal object={scene}>
                <SheetObject
                let:Transform={LightTransform}
                let:Sync
                key="Light"
                on:change={() => {
                    if (!lightHelper) return
                    lightHelper.update()
                }}
                >   
                    <LightTransform>
                        <T.DirectionalLight let:ref={lightRef} target={targetRef} intensity={5}>
                            <Portal object={scene}>
                                <T.DirectionalLightHelper args={[lightRef]} bind:ref={lightHelper}/>
                            </Portal>
                            <Sync
                            castShadow
                            receiveShadow
                            color
                            intensity
                            />
                        </T.DirectionalLight>
                    </LightTransform>
                </SheetObject>
            </Portal>
            
        </T.Object3D>
    </TargetTransform>
    
</SheetObject>

<T.AmbientLight intensity={0.2} />
