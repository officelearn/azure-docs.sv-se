---
title: Reliable Collection-objekt-serialisering i Azure Service Fabric | Microsoft Docs
description: Azure Service Fabric Reliable Collections objektserialisering
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: masnider,rajak
ms.assetid: 9d35374c-2d75-4856-b776-e59284641956
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 5/8/2017
ms.author: aljo
ms.openlocfilehash: ee19be45915b3ff1253ec721f4334fead19647b8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60723615"
---
# <a name="reliable-collection-object-serialization-in-azure-service-fabric"></a>Reliable Collection-objekt-serialisering i Azure Service Fabric
Tillförlitliga samlingar replikera och bevara sina objekt att kontrollera att de är beständig mellan datorn fel och strömavbrott.
Både att replikera och att bevara objekt måste tillförlitliga samlingar att serialisera dem.

Tillförlitliga samlingar hämta lämplig serialiserare för en viss typ från Reliable State Manager.
Gör att anpassade serializers registreras för en viss typ Reliable State Manager eftersom det innehåller inbyggda serializers.

## <a name="built-in-serializers"></a>Inbyggda Serializers

Reliable State Manager omfattar inbyggd serialisering för några vanliga typer, så att de kan serialiseras effektivt som standard. För andra typer av Reliable State Manager faller tillbaka om du vill använda den [DataContractSerializer](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractserializer(v=vs.110).aspx).
Inbyggda serializers är effektivare eftersom de redan känner till deras typer kan inte ändra och de inte behöver inkludera information om vilken typ som dess namn.

Reliable State Manager har inbyggda serialisering för följande typer: 
- Guid
- bool
- byte
- sbyte
- byte
- char
- string
- decimal
- double
- flyt
- int
- uint
- lång
- ulong
- kort
- ushort

## <a name="custom-serialization"></a>Anpassad serialisering

Anpassade serializers används ofta för att öka prestanda eller för att kryptera data i rörelse och på disk. En av anledningarna är anpassade serializers ofta mer effektiva än allmänna serialiserare eftersom de inte behöver att serialisera information om vilken typ. 

[IReliableStateManager.TryAddStateSerializer<T> ](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.ireliablestatemanager.tryaddstateserializer) används för att registrera en anpassad serialisering för den angivna typen T. Denna registrering ska inträffa i konstruktion StatefulServiceBase så att alla Reliable Collections innan återställningen påbörjas har åtkomst till relevanta serialiserare att läsa deras beständiga data.

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
> Anpassade serializers ges företräde över inbyggda serializers. Till exempel när en anpassad serialisering för int registreras används för att serialisera heltal i stället för inbyggd serialisering för int.

### <a name="how-to-implement-a-custom-serializer"></a>Så här implementerar du en anpassad serialiserare

En anpassad serialiserare måste implementera de [IStateSerializer<T> ](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.istateserializer-1) gränssnitt.

> [!NOTE]
> IStateSerializer<T> innehåller en överlagring för Skriv- och läsåtgärder som tar en ytterligare ton kallas basvärdet. Detta API är för differentiell serialisering. Differentiell serialisering funktionen exponeras för närvarande inte. Dessa två överlagringar kallas därför inte förrän differentiell serialisering är tillgängliga och aktiverade.

Följande är ett exempel anpassade typer kallas OrderKey som innehåller fyra egenskaper

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

Följande är ett exempel på en implementering av IStateSerializer\<OrderKey >.
Observera att läsa och skriva överlagringar som i baseValue, anropa sina respektive överlagring för vidarebefordran kompatibilitet.

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

## <a name="upgradability"></a>Möjligheterna
I en [löpande uppgradering av programmet](service-fabric-application-upgrade.md), uppgraderingen gäller för en delmängd av noderna, en uppgraderingsdomän i taget. Vissa uppgraderingsdomäner blir den nyare versionen av ditt program under den här processen och vissa uppgraderingsdomäner ska innehålla den äldre versionen av ditt program. Den nya versionen av ditt program måste kunna läsa den gamla versionen av dina data under distributionen, och den gamla versionen av ditt program måste kunna läsa den nya versionen av dina data. Om dataformatet inte är kompatibel med framåt och bakåt, misslyckas uppgraderingen eller ännu värre data kan försvinna eller skadas.

Om du använder inbyggda serialiserare, behöver du inte bekymra dig om kompatibilitet.
Men om du använder en anpassad serialiserare eller DataContractSerializer måste data vara oändligt bakåt och framåt kompatibel.
Med andra ord måste varje version av serialiserare kan serialisera och deserialisera någon version av typen.

Data kontrakt användare bör följa väldefinierade versionshantering regler för att lägga till, ta bort och ändra fält. Datakontrakt har också stöd för behandlar okänt fält, anslutning i processen för serialisering och deserialisering och ta itu med klassarv. Mer information finns i [med datakontrakt](https://msdn.microsoft.com/library/ms733127.aspx).

Anpassade serialiserare användare bör följer riktlinjerna av serialiserare som de använder för att se till att den är bakåtkompatibilitet och vidarebefordrar kompatibel.
Vanliga sätt att stödja alla versioner är att lägga till information om urvalsstorlek i början och endast att lägga till valfria egenskaper.
Det här sättet varje version kan läsa som mycket kan och hoppa över resten av dataströmmen.

## <a name="next-steps"></a>Nästa steg
  * [Serialisering och uppgradering](service-fabric-application-upgrade-data-serialization.md)
  * [Utvecklarreferens för tillförlitliga samlingar](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
  * [Uppgradera ditt program med hjälp av Visual Studio](service-fabric-application-upgrade-tutorial.md) vägleder dig genom en uppgradering av programmet med Visual Studio.
  * [Uppgradera ditt program med hjälp av Powershell](service-fabric-application-upgrade-tutorial-powershell.md) vägleder dig genom en uppgradering av programmet med hjälp av PowerShell.
  * Styra hur programmet uppgraderas med hjälp av [uppgradera parametrar](service-fabric-application-upgrade-parameters.md).
  * Lär dig hur du använder avancerade funktioner när du uppgraderar ditt program genom att referera till [avancerade ämnen](service-fabric-application-upgrade-advanced.md).
  * Åtgärda vanliga problem i programuppgraderingar genom att referera till stegen i [felsöka programuppgraderingar](service-fabric-application-upgrade-troubleshooting.md).
