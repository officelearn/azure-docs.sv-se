---
title: Serialisering av tillförlitlig samlingsobjekt
description: Lär dig mer om Azure Service Fabric Reliable Collections-objektseriering, inklusive standardstrategin och hur du definierar anpassad serialisering.
ms.topic: conceptual
ms.date: 5/8/2017
ms.openlocfilehash: 666e1bb45a9c75ee143f15a0d871d6ae1408eca9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75639555"
---
# <a name="reliable-collection-object-serialization-in-azure-service-fabric"></a>Reliable Collection-objektseriering i Azure Service Fabric
Reliable Collections replikerar och bevarar sina objekt för att se till att de är hållbara över datorfel och strömavbrott.
Både för att replikera och för att bevara objekt måste tillförlitliga samlingar serialisera dem.

Tillförlitliga samlingar får rätt serialiserare för en viss typ från Reliable State Manager.
Reliable State Manager innehåller inbyggda serialiserare och gör att anpassade serialiserare kan registreras för en viss typ.

## <a name="built-in-serializers"></a>Inbyggda serialiserare

Reliable State Manager innehåller inbyggd serialiserare för vissa vanliga typer, så att de kan serialiseras effektivt som standard. För andra typer faller Reliable State Manager tillbaka för att använda [DataContractSerializer](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractserializer(v=vs.110).aspx).
Inbyggda serialiserare är mer effektiva eftersom de vet att deras typer inte kan ändras och de behöver inte inkludera information om typen som dess typnamn.

Reliable State Manager har inbyggd serialiserare för följande typer: 
- GUID
- bool
- byte
- sbyte (sbyte)
- byte[]
- char
- sträng
- decimal
- double
- float
- int
- uint
- long
- ulong (ulong)
- short
- ushort (ushort)

## <a name="custom-serialization"></a>Anpassad serialisering

Anpassade serialiserare används ofta för att öka prestanda eller för att kryptera data över kabeln och på disken. Bland andra skäl är anpassade serialiserare ofta effektivare än generiska serialiserare eftersom de inte behöver serialisera information om typen. 

[IReliableStateManager.TryAddStateSerializer\<T>](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.ireliablestatemanager.tryaddstateserializer) används för att registrera en anpassad serialiserare för den angivna typen T. Denna registrering bör ske i byggandet av StatefulServiceBase för att säkerställa att innan återställningen startar, alla tillförlitliga samlingar har tillgång till relevant serializer för att läsa sina beständiga data.

```csharp
public StatefulBackendService(StatefulServiceContext context)
  : base(context)
  {
    if (!this.StateManager.TryAddStateSerializer(new OrderKeySerializer()))
    {
      throw new InvalidOperationException("Failed to set OrderKey custom serializer");
    }
  }
```

> [!NOTE]
> Anpassade serialiserare ges företräde framför inbyggda serialiserare. Till exempel när en anpassad serialiserare för int registreras, används den för att serialisera heltal i stället för den inbyggda serialiseraren för int.

### <a name="how-to-implement-a-custom-serializer"></a>Så här implementerar du en anpassad serialiserare

En anpassad serialiserare måste implementera [IStateSerializer\<T>-gränssnittet.](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.istateserializer-1)

> [!NOTE]
> IStateSerializer\<T> innehåller en överbelastning för Skriv och läsa som tar in ytterligare ett T som kallas basvärde. Detta API är för differentiell serialisering. För närvarande är funktionen för differentiell serialisering inte exponerad. Därför är dessa två överbelastningar inte anropas förrän differentiell serialisering exponeras och aktiveras.

Följande är en anpassad exempeltyp som heter OrderKey som innehåller fyra egenskaper

```csharp
public class OrderKey : IComparable<OrderKey>, IEquatable<OrderKey>
{
    public byte Warehouse { get; set; }

    public short District { get; set; }

    public int Customer { get; set; }

    public long Order { get; set; }

    #region Object Overrides for GetHashCode, CompareTo and Equals
    #endregion
}
```

Följande är ett exempel på implementering\<av IStateSerializer OrderKey>.
Observera att Läs och skriv överbelastningar som tar i baseValue, anropa sina respektive överbelastning för framåtkompatibilitet.

```csharp
public class OrderKeySerializer : IStateSerializer<OrderKey>
{
  OrderKey IStateSerializer<OrderKey>.Read(BinaryReader reader)
  {
      var value = new OrderKey();
      value.Warehouse = reader.ReadByte();
      value.District = reader.ReadInt16();
      value.Customer = reader.ReadInt32();
      value.Order = reader.ReadInt64();

      return value;
  }

  void IStateSerializer<OrderKey>.Write(OrderKey value, BinaryWriter writer)
  {
      writer.Write(value.Warehouse);
      writer.Write(value.District);
      writer.Write(value.Customer);
      writer.Write(value.Order);
  }
  
  // Read overload for differential de-serialization
  OrderKey IStateSerializer<OrderKey>.Read(OrderKey baseValue, BinaryReader reader)
  {
      return ((IStateSerializer<OrderKey>)this).Read(reader);
  }

  // Write overload for differential serialization
  void IStateSerializer<OrderKey>.Write(OrderKey baseValue, OrderKey newValue, BinaryWriter writer)
  {
      ((IStateSerializer<OrderKey>)this).Write(newValue, writer);
  }
}
```

## <a name="upgradability"></a>Uppgraderingsbarhet
I en [rullande programuppgradering](service-fabric-application-upgrade.md)tillämpas uppgraderingen på en delmängd noder, en uppgraderingsdomän i taget. Under den här processen kommer vissa uppgraderingsdomäner att finnas på den nyare versionen av ditt program och vissa uppgraderingsdomäner kommer att finnas på den äldre versionen av ditt program. Under distributionen måste den nya versionen av ditt program kunna läsa den gamla versionen av dina data, och den gamla versionen av ditt program måste kunna läsa den nya versionen av dina data. Om dataformatet inte är framåt- och bakåtkompatibelt kan uppgraderingen misslyckas, eller ännu värre, data kan gå förlorade eller skadas.

Om du använder inbyggd serialiserare behöver du inte oroa dig för kompatibilitet.
Men om du använder en anpassad serialiserare eller DataContractSerializer måste data vara oändligt bakåt och framåt kompatibla.
Med andra ord måste varje version av serialiserare kunna serialisera och av serialisera alla versioner av typen.

Datakontraktsanvändare bör följa de väldefinierade versionsreglerna för att lägga till, ta bort och ändra fält. Datakontrakt har också stöd för att hantera okända fält, koppla in i serialisering och deserialiseringsprocessen och hantera klassarv. Mer information finns i [Använda datakontrakt](https://msdn.microsoft.com/library/ms733127.aspx).

Anpassade serialiserare användare bör följa riktlinjerna för serialiserare de använder för att se till att det är bakåt och framåt kompatibel.
Vanligt sätt att stödja alla versioner är att lägga till storleksinformation i början och bara lägga till valfria egenskaper.
På så sätt kan varje version läsa så mycket den kan och hoppa över den återstående delen av strömmen.

## <a name="next-steps"></a>Nästa steg
  * [Serialisering och uppgradering](service-fabric-application-upgrade-data-serialization.md)
  * [Utvecklarreferens för tillförlitliga samlingar](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
  * [Om du uppgraderar programmet med Visual Studio](service-fabric-application-upgrade-tutorial.md) går du igenom en programuppgradering med Visual Studio.
  * [Om du uppgraderar programmet med Powershell](service-fabric-application-upgrade-tutorial-powershell.md) får du en programuppgradering med PowerShell.
  * Styr hur programmet uppgraderas med hjälp av [uppgraderingsparametrar](service-fabric-application-upgrade-parameters.md).
  * Lär dig hur du använder avancerade funktioner när du uppgraderar programmet genom att referera till [Avancerade ämnen](service-fabric-application-upgrade-advanced.md).
  * Åtgärda vanliga problem i programuppgraderingar genom att referera till stegen i [Felsökning av programuppgraderingar](service-fabric-application-upgrade-troubleshooting.md).
