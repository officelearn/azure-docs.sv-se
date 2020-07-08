---
title: Flytta ett Azure Event Hubs-namnområde till en annan region | Microsoft Docs
description: Den här artikeln visar hur du flyttar ett Azure Event Hubs-namnområde från den aktuella regionen till en annan region.
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: a70397772d22a65046f87877deab6263d4b2104f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85312958"
---
# <a name="move-an-azure-event-hubs-namespace-to-another-region"></a>Flytta ett Azure Event Hubs-namnområde till en annan region
Det finns olika scenarier där du vill flytta ditt befintliga Event Hubs-namnområde från en region till en annan. Du kanske till exempel vill skapa ett namn område med samma konfiguration för testning. Du kanske också vill skapa ett sekundärt namn område i en annan region som en del av [Disaster Recovery-planeringen](event-hubs-geo-dr.md#setup-and-failover-flow).

> [!NOTE]
> Den här artikeln visar hur du exporterar en Azure Resource Manager mall för ett befintligt Event Hubs-namnområde och sedan använder mallen för att skapa ett namn område med samma konfigurations inställningar i en annan region. Den här processen flyttar dock inte händelser som inte bearbetas än. Du måste bearbeta händelserna från det ursprungliga namn området innan du tar bort det.

## <a name="prerequisites"></a>Krav

- Kontrollera att tjänsterna och funktionerna som kontot använder stöds i målregionen.
- För förhandsgranskningsfunktioner ska du kontrollera att din prenumeration är vitlistad för målregionen.
- Om du har aktiverat **funktionen** för händelse hubbar i namn området flyttar du [Azure Storage eller Azure Data Lake Store gen 2](../storage/common/storage-account-move.md) eller [Azure Data Lake Store gen 1](../data-lake-store/data-lake-store-migration-cross-region.md) -konton innan du flyttar Event Hubs namn området. Du kan också flytta resurs gruppen som innehåller både lagrings utrymmet och Event Hubs namn områden till den andra regionen genom att följa stegen som beskrivs i den här artikeln. 
- Om Event Hubs namn området finns i ett **Event Hubs kluster** [skapar du ett dedikerat kluster](event-hubs-dedicated-cluster-create-portal.md) i **mål regionen** innan du går igenom stegen i den här artikeln. Du kan också använda [snabb starts mal len på GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-cluster-namespace-eventhub/) för att skapa ett Event Hubs-kluster. I mallen tar du bort namn områdes delen av JSON-filen för att endast skapa klustret. 

## <a name="prepare"></a>Förbereda
Kom igång genom att exportera en Resource Manager-mall. Den här mallen innehåller inställningar som beskriver ditt Event Hubs namn område.

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Välj **alla resurser** och välj sedan din Event Hubs namn område.

3. Välj **Settings**  >  **Exportera mall**för > inställningar.

4. Välj **Hämta** på sidan **Exportera mall** .

    ![Ladda ned Resource Manager-mall](./media/move-across-regions/download-template.png)

5. Leta upp zip-filen som du laddade ned från portalen och zippa upp filen till en valfri mapp.

   Den här zip-filen innehåller JSON-filerna som innehåller mallen och skripten för att distribuera mallen.


## <a name="move"></a>Flytta

Distribuera mallen för att skapa ett Event Hubs-namnområde i mål regionen. 


1. I Azure Portal väljer du **skapa en resurs**.

2. I **Sök på Marketplace**skriver du **mall distribution**och trycker sedan på **RETUR**.

3. Välj **malldistribution**.

4. Välj **Skapa**.

5. Välj **Bygg en egen mall i redigeraren**.

6. Välj **Läs in fil**och följ sedan anvisningarna för att läsa in **template.jspå** filen som du laddade ned i det sista avsnittet.

7. Spara mallen genom att välja **Spara** . 

8. Följ dessa steg på sidan **Anpassad distribution** : 

    1. Välj en Azure- **prenumeration**. 

    2. Välj en befintlig **resurs grupp** eller skapa en. Om käll namn området fanns i ett Event Hubs kluster väljer du den resurs grupp som innehåller klustret i mål regionen. 

    3. Välj **målplats eller region** . Om du har valt en befintlig resurs grupp är den här inställningen skrivskyddad. 

    4. Utför följande steg i avsnittet **Inställningar** :
    
        1. Ange det nya namn **områdets namn**. 

            ![Distribuera Resource Manager-mall](./media/move-across-regions/deploy-template.png)

        2. Om käll namn området fanns i ett **Event Hubs kluster**, anger du namn på **resurs grupp** och **Event Hubs kluster** som en del av ett **externt ID**. 

              ```
              /subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<CLUSTER'S RESOURCE GROUP>/providers/Microsoft.EventHub/clusters/<CLUSTER NAME>
              ```   
        3. Om händelsehubben i namn rymden använder ett lagrings konto för att fånga händelser anger du resurs gruppens namn och lagrings kontot för `StorageAccounts_<original storage account name>_external` fältet. 
            
            ```
            /subscriptions/0000000000-0000-0000-0000-0000000000000/resourceGroups/<STORAGE'S RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>
            ```    
    5. Markera kryss rutan **Jag accepterar villkoren som anges ovan** . 
    
    6. Nu väljer du **Välj Köp** för att starta distributions processen. 

## <a name="discard-or-clean-up"></a>Ta bort eller rensa
Efter distributionen kan du, om du vill börja om, ta bort **mål Event Hubs namn området**och upprepa stegen som beskrivs i avsnittet [förbereda](#prepare) och [Flytta](#move) i den här artikeln.

Om du vill genomföra ändringarna och slutföra flyttningen av en Event Hubs-namnrymd tar du bort **käll Event Hubs namn området**. Se till att du bearbetade alla händelser i namn området innan du tar bort namn området. 

Så här tar du bort ett Event Hubs-namnområde (källa eller mål) med hjälp av Azure Portal:

1. I fönstret Sök högst upp i Azure Portal skriver du **Event Hubs**och väljer **Event Hubs** från Sök resultat. Du ser Event Hubs namnrum i en lista.

2. Välj det mål namn område som ska tas bort och välj **ta bort** från verktygsfältet. 

    ![Ta bort namn område – knapp](./media/move-across-regions/delete-namespace-button.png)

3. På sidan **ta bort resurser*** kontrollerar du de markerade resurserna och bekräftar borttagningen genom att skriva **Ja**och väljer sedan **ta bort**. 

## <a name="next-steps"></a>Nästa steg

I den här självstudien flyttade du ett Azure Event Hubs-namnområde från en region till en annan och rensade käll resurserna.  Mer information om hur du flyttar resurser mellan regioner och haveri beredskap i Azure finns i:


- [Flytta resurser till en ny resursgrupp eller prenumeration](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Migrera virtuella Azure-datorer till en annan region](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
