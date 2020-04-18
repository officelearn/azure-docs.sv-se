---
title: Flytta ett namnområde för Azure Event Hubs till en annan region | Microsoft-dokument
description: Den här artikeln visar hur du flyttar ett Azure Event Hubs-namnområde från den aktuella regionen till en annan region.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 04/14/2020
ms.author: spelluru
ms.reviewer: shvija
ms.openlocfilehash: 2dfc9c517605bbb48bee0b306fb275464cfebe39
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606813"
---
# <a name="move-an-azure-event-hubs-namespace-to-another-region"></a>Flytta ett namnområde för Azure Event Hubs till en annan region
Det finns olika scenarier där du vill flytta ditt befintliga namnområde för eventhubbar från en region till en annan. Du kanske till exempel vill skapa ett namnområde med samma konfiguration för testning. Du kanske också vill skapa ett sekundärt namnområde i en annan region som en del av [katastrofåterställningsplanering](event-hubs-geo-dr.md#setup-and-failover-flow).

> [!NOTE]
> Den här artikeln visar hur du exporterar en Azure Resource Manager-mall för ett befintligt namnområde för eventhubbar och sedan använder mallen för att skapa ett namnområde med samma konfigurationsinställningar i en annan region. Den här processen flyttar dock inte händelser som inte har bearbetats ännu. Du måste bearbeta händelserna från det ursprungliga namnområdet innan du tar bort det.

## <a name="prerequisites"></a>Krav

- Kontrollera att de tjänster och funktioner som ditt konto använder stöds i målregionen.
- För förhandsgranskningsfunktioner kontrollerar du att din prenumeration är vitlistad för målregionen.
- Om du har **hämtningsfunktion** aktiverad för händelsehubbar i namnområdet flyttar du [Azure Storage eller Azure Data Lake Store Gen 2-](../storage/common/storage-account-move.md) eller Azure Data Lake Store Gen [1-konton](../data-lake-store/data-lake-store-migration-cross-region.md) innan du flyttar namnområdet Event Hubs. Du kan också flytta resursgruppen som innehåller namnområden för både lagrings- och händelsehubbar till den andra regionen genom att följa stegen som liknar de som beskrivs i den här artikeln. 
- Om namnområdet Event Hubs finns i ett **eventhubs-kluster** [skapar du ett dedikerat kluster](event-hubs-dedicated-cluster-create-portal.md) i **målregionen** innan du går igenom stegen i den här artikeln. 

## <a name="prepare"></a>Förbereda
Du kan komma igång genom att exportera en Resource Manager-mall. Den här mallen innehåller inställningar som beskriver namnområdet Händelserhubbar.

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Välj **Alla resurser** och välj sedan namnområdet Event Hubs.

3. Välj**mallen** **Exportera inställningar** > > inställningar .

4. Välj **Hämta** på sidan **Exportera mall.**

    ![Hämta resurshanteraren-mall](./media/move-across-regions/download-template.png)

5. Leta reda på ZIP-filen som du hämtade från portalen och packa upp filen till en valfri mapp.

   Den här zip-filen innehåller DE Json-filer som innehåller mallen och skripten för att distribuera mallen.


## <a name="move"></a>Flytta

Distribuera mallen för att skapa ett namnområde för händelsehubbar i målregionen. 


1. I Azure-portalen väljer du **Skapa en resurs**.

2. I **Sök på Marketplace** skriver du **malldistribution** och trycker sedan på **RETUR**.

3. Välj **Malldistribution**.

4. Välj **Skapa**.

5. Välj alternativet för att **skapa din egen mall i redigeringsprogrammet**.

6. Välj **Läs in filen**och följ sedan instruktionerna för att läsa in filen **template.json** som du hämtade i det sista avsnittet.

7. Välj **Spara** om du vill spara mallen. 

8. Gör så här på sidan **Anpassad distribution:** 

    1. Välj en **Azure-prenumeration**. 

    2. Markera en befintlig **resursgrupp** eller skapa en. Om källnamnområdet fanns i ett Event Hubs-kluster väljer du den resursgrupp som innehåller kluster i målregionen. 

    3. Välj **målplats** eller målregion. Om du har markerat en befintlig resursgrupp är den här inställningen skrivskyddad. 

    4. Gör följande i avsnittet **INSTÄLLNINGAR:**
    
        1. ange det nya **namnområdesnamnet**. 

            ![Distribuera Resurshanteraren-mall](./media/move-across-regions/deploy-template.png)

        2. Om källnamnområdet fanns i ett **eventhubs-kluster**anger du namn på **resursgrupp** och **eventhubbar** som en del av **externt ID**. 

              ```
              /subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<CLUSTER'S RESOURCE GROUP>/providers/Microsoft.EventHub/clusters/<CLUSTER NAME>
              ```   
        3. Om händelsehubben i namnområdet använder ett lagringskonto för att hämta händelser `StorageAccounts_<original storage account name>_external` anger du resursgruppsnamnet och lagringskontot för fältet. 
            
            ```
            /subscriptions/0000000000-0000-0000-0000-0000000000000/resourceGroups/<STORAGE'S RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>
            ```    
    5. Välj **kryssrutan Jag godkänner de villkor som anges ovan.** 
    
    6. Välj nu **Köp** för att starta distributionsprocessen. 

## <a name="discard-or-clean-up"></a>Ignorera eller rensa
Om du vill börja om efter distributionen kan du ta bort **namnområdet målhändelsehubbar**och upprepa stegen som beskrivs i avsnittet [Förbered](#prepare) och [flytta](#move) i den här artikeln.

Om du vill genomföra ändringarna och slutföra flytten av ett namnområde för händelsehubbar tar du bort **namnområdet för källhubbar**. Kontrollera att du har bearbetat alla händelser i namnområdet innan du tar bort namnområdet. 

Så här tar du bort ett namnområde för händelsehubbar (källa eller mål) med hjälp av Azure-portalen:

1. Skriv **Event Hubs**i sökfönstret högst upp i Azure-portalen och välj **Händelsehubbar** från sökresultat. Namnområdena Event Hubs visas i en lista.

2. Markera det målnamnområde som ska tas bort och välj **Ta bort** i verktygsfältet. 

    ![Knappen Ta bort namnområde - knappen](./media/move-across-regions/delete-namespace-button.png)

3. På sidan **Ta bort resurser*** kontrollerar du de markerade resurserna och bekräftar borttagningen genom att skriva **ja**och välj sedan **Ta bort**. 

## <a name="next-steps"></a>Nästa steg

I den här självstudien flyttade du ett Azure Event Hubs-namnområde från en region till en annan och rensade källresurserna.  Mer information om hur du flyttar resurser mellan regioner och haveriberedskap i Azure finns i:


- [Flytta resurser till en ny resursgrupp eller prenumeration](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Migrera virtuella Azure-datorer till en annan region](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
