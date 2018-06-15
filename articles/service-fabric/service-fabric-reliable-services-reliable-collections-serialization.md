---
title: Tillförlitliga samling objekt serialisering i Azure Service Fabric | Microsoft Docs
description: Azure Service Fabric tillförlitliga samlingar objektet serialisering
services: service-fabric
documentationcenter: .net
author: mcoskun
manager: timlt
editor: masnider,rajak
ms.assetid: 9d35374c-2d75-4856-b776-e59284641956
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 5/8/2017
ms.author: mcoskun
ms.openlocfilehash: b02d8924749abb0e2fe815b555d55767bf1e5cc1
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34207673"
---
# <a name="reliable-collection-object-serialization-in-azure-service-fabric"></a>Tillförlitliga samling objekt serialisering i Azure Service Fabric
Tillförlitliga samlingar replikera och deras objekt att kontrollera att de är beständig mellan datorn fel och strömavbrott är kvar.
Både replikeras och för att spara objekt, måste tillförlitlig samlingar serialisera dem.

Tillförlitliga samlingar hämta lämplig serialisering för en viss typ från tillförlitliga Tillståndshanterare.
Tillåter anpassade serializers som ska registreras för en viss typ tillförlitlig Tillståndshanterare eftersom det innehåller inbyggda serializers.

## <a name="built-in-serializers"></a>Inbyggda Serializers

Tillstånd för tillförlitlig Manager innehåller inbyggda serialiseraren för vissa typer av vanliga så att de kan serialiseras effektivt som standard. För andra typer tillförlitliga Tillståndshanterare faller tillbaka om du vill använda den [DataContractSerializer](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractserializer(v=vs.110).aspx).
Inbyggda serializers är effektivare eftersom de redan känner till deras typer kan inte ändra och de behöver inte inkludera information om vilken typ som dess namn.

Tillförlitlig Tillståndshanterare har inbyggd serialisering för följande typer: 
- GUID
- bool
- byte
- sbyte
- byte
- Char
- sträng
- Decimal
- double
- flyt
- int
- uint
- lång
- ulong
- kort
- ushort

## <a name="custom-serialization"></a>Anpassad serialisering

Anpassade serializers används ofta för att öka prestandan eller för att kryptera data över nätverket och på disken. Anpassade serializers är ofta effektivare än allmänna serialiseraren anledningarna eftersom de inte behöver att serialisera information om typen. 

[IReliableStateManager.TryAddStateSerializer<T> ](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.ireliablestatemanager.tryaddstateserializer--1?Microsoft_ServiceFabric_Data_IReliableStateManager_TryAddStateSerializer__1_Microsoft_ServiceFabric_Data_IStateSerializer___0__) används för att registrera en anpassad serialisering för den angivna typen T. Denna registrering ska inträffa i konstruktion StatefulServiceBase så att alla tillförlitliga samlingar innan återställningen startas har åtkomst till relevanta serialiseraren att läsa deras beständiga data.

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
> Anpassade serializers ges företräde över inbyggda serializers. Till exempel när en anpassad serialisering för int registreras används den för att serialisera heltal i stället för inbyggd serialisering för int.

### <a name="how-to-implement-a-custom-serializer"></a>Hur du implementerar en anpassad serialisering

En anpassad serialiserare måste implementera den [IStateSerializer<T> ](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.istateserializer-1) gränssnitt.

> [!NOTE]
> IStateSerializer<T> innehåller en överlagring för skrivning och Läs som tar en ytterligare ton kallas Basvärde. Detta API är för differentiell serialisering. För närvarande visas differentiell serialisering funktionen inte. Dessa två överlagringar kallas därför inte förrän differentiell serialisering exponeras och aktiverat.

Följande är ett exempel anpassad typ som kallas OrderKey som innehåller fyra egenskaper

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

Följande är exempel på implementering av IStateSerializer<OrderKey>.
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
I en [löpande uppgradering av programmet](service-fabric-application-upgrade.md), uppgraderingen tillämpas på en del noder, en domän i taget. Vissa uppgraderingsdomäner görs på den nya versionen av ditt program under den här processen och vissa uppgraderingsdomäner görs på den äldre versionen av programmet. Den nya versionen av programmet måste kunna läsa den tidigare versionen av data under driftsättningen, och den gamla versionen av programmet måste kunna läsa den nya versionen av dina data. Om formatet inte är kompatibel med framåt och bakåt, misslyckas uppgraderingen eller värre, data kan försvinna eller skadas.

Om du använder inbyggd serialisering, behöver du inte bekymra dig om kompatibilitet.
Men om du använder en anpassad serialiserare eller DataContractSerializer måste data vara oändligt kompatibel bakåt och framåt.
Varje version av serialiseraren måste med andra ord ska kunna serialisera och deserialisera någon version av typen.

Data kontrakt-användare bör följa väldefinierade versionshantering regler för att lägga till, ta bort och ändra fält. Datakontrakt har också stöd för behandlar okänt fält och koppla upp i processen för serialisering och deserialisering samt hantera klassarv. Mer information finns i [med datakontrakt](https://msdn.microsoft.com/library/ms733127.aspx).

Anpassad serialisering användare bör följa riktlinjerna i serialiseraren som de använder för att kontrollera att den är bakåtkompatibilitet och vidarebefordrar kompatibel.
Vanliga sätt stöder alla versioner är att lägga till informationen i början och bara att lägga till valfria egenskaper.
Det här sättet varje version kan läsa mycket kan och hoppa över resten av dataströmmen.

## <a name="next-steps"></a>Nästa steg
  * [Serialisering och uppgradering](service-fabric-application-upgrade-data-serialization.md)
  * [För utvecklare för tillförlitlig samlingar](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
  * [Uppgradera ditt program med hjälp av Visual Studio](service-fabric-application-upgrade-tutorial.md) vägleder dig genom en uppgradering av programmet med hjälp av Visual Studio.
  * [Uppgradera ditt program med hjälp av Powershell](service-fabric-application-upgrade-tutorial-powershell.md) vägleder dig genom en uppgradering av programmet med hjälp av PowerShell.
  * Styra hur programmet ska uppgraderas med hjälp av [uppgradera parametrar](service-fabric-application-upgrade-parameters.md).
  * Lär dig hur du använder avancerade funktioner när du uppgraderar ditt program genom att referera till [avancerade ämnen](service-fabric-application-upgrade-advanced.md).
  * Lösa vanliga problem i programuppgraderingar genom att referera till stegen i [felsökning programuppgraderingar](service-fabric-application-upgrade-troubleshooting.md).
