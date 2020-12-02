---
title: Ta bort resurser från en flyttnings samling i Azure Resource-arbetskraften
description: Lär dig hur du tar bort resurser från en flyttnings samling i Azure Resource-arbetskraft.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 11/30/2020
ms.author: raynew
ms.openlocfilehash: 63548e2bf470c012e0dd8a5f879a51eeb631f453
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96459271"
---
# <a name="manage-move-collections-and-resource-groups"></a>Hantera flytta samlingar och resurs grupper

Den här artikeln beskriver hur du tar bort resurser från en flyttnings samling eller tar bort en flytta samling/resurs grupp i [Azure Resource-arbetskraften](overview.md). Flytta samlingar används när du flyttar Azure-resurser mellan Azure-regioner.

## <a name="remove-a-resource-portal"></a>Ta bort en resurs (portal)

Du kan ta bort resurser i en flyttnings samling i resurs förflyttnings portalen på följande sätt:

1. I **flera regioner** väljer du alla resurser som du vill ta bort från samlingen och väljer **ta bort**. 

    ![Knapp att välja för att ta bort](./media/remove-move-resources/portal-select-resources.png)

2. I **ta bort resurser** klickar du på **ta bort**.

    ![Knappen för att välja att ta bort resurser från en flytt samling](./media/remove-move-resources/remove-portal.png)

## <a name="remove-a-move-collectionresource-group-portal"></a>Ta bort en flytt samling/resurs grupp (portal)

Du kan ta bort en flytt samling/resurs grupp i portalen.

1. Följ anvisningarna i proceduren ovan för att ta bort resurser från samlingen. Om du tar bort en resurs grupp kontrollerar du att den inte innehåller några resurser.
2. Ta bort flyttnings samlingen eller resurs gruppen.  

## <a name="remove-a-resource-powershell"></a>Ta bort en resurs (PowerShell)

Ta bort en resurs (i vårt exempel PSDemoVM-datorer) från en samling med PowerShell, enligt följande:

```azurepowershell-interactive
# Remove a resource using the resource ID
Remove-AzResourceMoverMoveResource -SubscriptionId  <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus -Name PSDemoVM
```
**Förväntad utdata**

![Mata ut text efter borttagning av en resurs från en flyttnings samling](./media/remove-move-resources/remove-resource.png)

## <a name="remove-a-collection-powershell"></a>Ta bort en samling (PowerShell)

Ta bort en hel flytt samling med PowerShell enligt följande:

1. Följ anvisningarna ovan för att ta bort resurser i samlingen med hjälp av PowerShell.
2. Kör följande:

    ```azurepowershell-interactive
    # Remove a resource using the resource ID
    Remove-AzResourceMoverMoveCollection -SubscriptionId <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus -MoveCollectionName MoveCollection-centralus-westcentralus
    ```
    **Förväntad utdata**
    
    ![Mata ut text efter borttagning av en flytt samling](./media/remove-move-resources/remove-collection.png)

## <a name="vm-resource-state-after-removing"></a>Status för VM-resurs efter borttagning

Vad som händer när du tar bort en VM-resurs från en flyttnings samling beror på resursens tillstånd, som sammanfattas i tabellen.

###  <a name="remove-vm-state"></a>Ta bort VM-tillstånd
**Resurs tillstånd** | **Virtuell dator** | **Nätverk**
--- | --- | --- 
**Tillagt i flyttnings samlingen** | Ta bort från flytta samling. | Ta bort från flytta samling. 
**Matchade beroenden/förberedelse** | Ta bort från flytta samling  | Ta bort från flytta samling. 
**Förberedelse pågår**<br/> (eller någon annan status som pågår) | Borttagnings åtgärden Miss lyckas med felet.  | Borttagnings åtgärden Miss lyckas med felet.
**Det gick inte att förbereda** | Ta bort från flyttnings samlingen.<br/>Ta bort allt som skapats i mål regionen, inklusive replik diskar. <br/><br/> Infrastruktur resurser som skapas under flytten måste tas bort manuellt. | Ta bort från flyttnings samlingen.  
**Påbörja flytt väntar** | Ta bort från flytta samling.<br/><br/> Ta bort allt som skapats i mål regionen, inklusive VM, replik diskar osv.  <br/><br/> Infrastruktur resurser som skapas under flytten måste tas bort manuellt. | Ta bort från flytta samling.
**Det gick inte att påbörja flytt** | Ta bort från flytta samling.<br/><br/> Ta bort allt som skapats i mål regionen, inklusive VM, replik diskar osv.  <br/><br/> Infrastruktur resurser som skapas under flytten måste tas bort manuellt. | Ta bort från flytta samling.
**Incheckning väntar** | Vi rekommenderar att du tar bort flytten så att mål resurserna tas bort först.<br/><br/> Resursen går tillbaka till steget **påbörja flytt väntar** och du kan fortsätta därifrån. | Vi rekommenderar att du tar bort flytten så att mål resurserna tas bort först.<br/><br/> Resursen går tillbaka till steget **påbörja flytt väntar** och du kan fortsätta därifrån. 
**Incheckning misslyckades** | Vi rekommenderar att du tar bort så att mål resurserna tas bort först.<br/><br/> Resursen går tillbaka till steget **påbörja flytt väntar** och du kan fortsätta därifrån. | Vi rekommenderar att du tar bort flytten så att mål resurserna tas bort först.<br/><br/> Resursen går tillbaka till steget **påbörja flytt väntar** och du kan fortsätta därifrån.
**Borttagningen slutfördes** | Resursen går tillbaka till steget **påbörja flytt väntar** .<br/><br/> Den tas bort från flyttnings samlingen, tillsammans med allt som skapats på mål-VM, replik diskar, valv osv.  <br/><br/> Infrastruktur resurser som skapas under flytten måste tas bort manuellt. <br/><br/> Infrastruktur resurser som skapas under flytten måste tas bort manuellt. |  Resursen går tillbaka till steget **påbörja flytt väntar** .<br/><br/> Den tas bort från flyttnings samlingen.
**Borttagningen misslyckades** | Vi rekommenderar att du tar bort flyttningarna så att mål resurserna tas bort först.<br/><br/> Efter det återgår resursen till läget **Starta flytt väntar** och du kan fortsätta därifrån. | Vi rekommenderar att du tar bort flyttningarna så att mål resurserna tas bort först.<br/><br/> Efter det återgår resursen till läget **Starta flytt väntar** och du kan fortsätta därifrån.
**Borttagning av väntande källa** | Borttagen från flyttnings samlingen.<br/><br/> Det tar inte bort något som skapats i mål regionen.  | Borttagen från flyttnings samlingen.<br/><br/> Det tar inte bort något som skapats i mål regionen.
**Det gick inte att ta bort källan** | Borttagen från flyttnings samlingen.<br/><br/> Det tar inte bort något som skapats i mål regionen. | Borttagen från flyttnings samlingen.<br/><br/> Det tar inte bort något som skapats i mål regionen.

## <a name="sql-resource-state-after-removing"></a>Status för SQL-resurs efter borttagning

Vad som händer när du tar bort en Azure SQL-resurs från en flyttnings samling beror på resursens tillstånd, som sammanfattas i tabellen.

**Resurs tillstånd** | **SQL** 
--- | --- 
**Tillagt i flyttnings samlingen** | Ta bort från flytta samling. 
**Matchade beroenden/förberedelse** | Ta bort från flytta samling 
**Förberedelse pågår**<br/> (eller någon annan status som pågår)  | Borttagnings åtgärden Miss lyckas med felet. 
**Det gick inte att förbereda** | Ta bort från flytta samling<br/><br/>Ta bort allt som skapats i mål regionen. 
**Påbörja flytt väntar** |  Ta bort från flytta samling<br/><br/>Ta bort allt som skapats i mål regionen. SQL-databasen finns nu och kommer att tas bort. 
**Det gick inte att påbörja flytt** | Ta bort från flytta samling<br/><br/>Ta bort allt som skapats i mål regionen. SQL-databasen finns just nu och måste tas bort. 
**Incheckning väntar** | Vi rekommenderar att du tar bort flytten så att mål resurserna tas bort först.<br/><br/> Resursen går tillbaka till steget **påbörja flytt väntar** och du kan fortsätta därifrån.
**Incheckning misslyckades** | Vi rekommenderar att du tar bort flytten så att mål resurserna tas bort först.<br/><br/> Resursen går tillbaka till steget **påbörja flytt väntar** och du kan fortsätta därifrån. 
**Borttagningen slutfördes** |  Resursen går tillbaka till steget **påbörja flytt väntar** .<br/><br/> Den tas bort från flyttnings samlingen, tillsammans med allt som skapats på målet, inklusive SQL-databaser. 
**Borttagningen misslyckades** | Vi rekommenderar att du tar bort flyttningarna så att mål resurserna tas bort först.<br/><br/> Efter det återgår resursen till läget **Starta flytt väntar** och du kan fortsätta därifrån. 
**Borttagning av väntande källa** | Borttagen från flyttnings samlingen.<br/><br/> Det tar inte bort något som skapats i mål regionen. 
**Det gick inte att ta bort källan** | Borttagen från flyttnings samlingen.<br/><br/> Det tar inte bort något som skapats i mål regionen. 

## <a name="next-steps"></a>Nästa steg

Försök att [flytta en virtuell dator](tutorial-move-region-virtual-machines.md) till en annan region med resurs förflyttning.
