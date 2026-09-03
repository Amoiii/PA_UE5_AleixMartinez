# 💥 Dynamic Mesh Destruction in UE5

[![Destruction Showcase](https://img.youtube.com/vi/8E3b-e5DZGU/maxresdefault.jpg)](https://youtu.be/8E3b-e5DZGU)


## 📝 Overview
This project is a technical exploration of real-time physics and dynamic mesh destruction within Unreal Engine 5. Built as a Tech Art and Physics showcase, it focuses on implementing responsive fracture mechanics using the Chaos Physics engine while maintaining optimal performance during gameplay.

- **Engine:** Unreal Engine 5
- **Language:** C++ / Blueprints
- **Role:** Solo Developer (100%) - Tech Artist / Programmer

## 🛠️ Key Features
- **Real-Time Fracturing:** Procedural mesh destruction leveraging UE5's Chaos Physics solver to create realistic impact zones.
- **Performance Optimization:** Optimized object lifecycle and physics collision checks to prevent severe frame drops when generating multiple dynamic shards simultaneously.
- **Material & VFX Integration:** Implemented dynamic materials that update upon fracture, exposing interior textures and triggering particle effects on impact.

## 💻 Technical Implementation
*A look under the hood at how the fracture logic and impact calculations are handled to balance visual fidelity and engine performance:*

```cpp
void AInteractableItem::ProcessFractureImpact(FVector HitLocation, FVector HitNormal, float ImpactForce)
{
    // 1. Ocultar la malla intacta y deshabilitar sus colisiones
    BaseMesh->SetVisibility(false);
    BaseMesh->SetCollisionEnabled(ECollisionEnabled::NoCollision);

    // 2. Revelar la geometría fracturada (Geometry Collection) y activar físicas
    FracturedMesh->SetVisibility(true);
    FracturedMesh->SetSimulatePhysics(true);
    FracturedMesh->SetCollisionEnabled(ECollisionEnabled::QueryAndPhysics);

    // 3. Calcular el vector de fuerza direccional basado en la normal del impacto
    FVector ImpactDirection = -HitNormal.GetSafeNormal();
    FVector AppliedImpulse = ImpactDirection * (ImpactForce * MassMultiplier);

    // 4. Aplicar el impulso físico exactamente en el punto de contacto para dispersión realista
    FracturedMesh->AddImpulseAtLocation(AppliedImpulse, HitLocation);

    // 5. Instanciar efectos visuales y de audio optimizados en las coordenadas del golpe
    UGameplayStatics::SpawnEmitterAtLocation(GetWorld(), FractureVFX, HitLocation);
    UGameplayStatics::PlaySoundAtLocation(this, FractureSound, HitLocation);
}
