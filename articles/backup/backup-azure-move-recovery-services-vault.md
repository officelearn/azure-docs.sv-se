---
title: Flytta ett Recovery Services-valv för Azure-prenumerationer eller till en annan resursgrupp
description: Anvisningar för att flytta recovery services-valv i azure-prenumerationer och resursgrupper.
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: sogup
ms.openlocfilehash: 8d5d6ed6c14927c57279cf500518f3b3a86d591d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60699837"
---
# <a name="move-a-recovery-services-vault-across-azure-subscriptions-and-resource-groups"></a>Flytta ett Recovery Services-valv i Azure-prenumerationer och resursgrupper

Den här artikeln förklarar hur du flyttar ett Recovery Services-valv som konfigurerats för Azure Backup för Azure-prenumerationer eller till en annan resursgrupp i samma prenumeration. Du kan använda Azure portal eller PowerShell för att flytta ett Recovery Services-valv.

## <a name="supported-region"></a>Region som stöds

För att flytta resursen för Recovery Services-valv stöds i Östra Australien, östra södra, Kanada, centrala, Kanada, östra, Sydostasien, Östasien, centrala USA, norra centrala USA, östra USA, östra usa2, södra centrala USA, västra centrala USA, västra centrala USA östra 2, västra USA Centrala Indien, södra Indien, Japan, östra, Japan, västra, Korea, centrala Korea, södra, Nordeuropa, Västeuropa, Sydafrika, norra, Sydafrika, västra, Storbritannien, södra, Storbritannien, västra, Förenade Arabemiraten, centrala och Förenade Arabemiraten, norra.

## <a name="prerequisites-for-moving-recovery-services-vault"></a>Förutsättningar för att flytta Recovery Services-valv

- Flytta över resursgrupper, både källa och mål resursgrupper är låst skrivningen under valvet och ta bort. Mer information finns i den här [artikeln](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).
- Administratörsprenumeration har behörighet att flytta ett valv.
- Målprenumerationen måste finnas i samma klientorganisation som käll-prenumeration och dess tillstånd ska aktiveras för att flytta valv mellan prenumerationer.
- Du måste ha behörighet att utföra skrivåtgärder på målresursgruppen.
- Flytta valvet ändras bara resursgruppen. Recovery Services-valvet kommer att finnas på samma plats och kan inte ändras.
- Du kan flytta endast en Recovery Services-valv per region i taget.
- Om en virtuell dator inte flytta med Recovery Services-valv mellan prenumerationer eller till en ny resursgrupp, förblir aktuella VM återställningspunkterna intakta i valvet tills de upphör att gälla.
- Om den virtuella datorn flyttas med valvet eller inte, kan du alltid återställa den virtuella datorn från den sparade historiken för säkerhetskopiering i valvet.
- Azure Disk Encryption kräver att nyckelvalvet och virtuella datorer finns i samma Azure-region och prenumeration.
- Om du vill flytta en virtuell dator med hanterade diskar finns i den här [artikeln](https://azure.microsoft.com/blog/move-managed-disks-and-vms-now-available/).
- Alternativ för att flytta resurser som har distribuerats via den klassiska modellen varierar beroende på om du flyttar resurser inom en prenumeration eller till en ny prenumeration. Mer information finns i den här [artikeln](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources#classic-deployment-limitations).
- Principer för säkerhetskopiering som definierats för valvet bevaras när valvet flyttas mellan prenumerationer eller till en ny resursgrupp.
- Finns inte stöd för att flytta valv med Azure Files, Azure File Sync eller SQL i virtuella IaaS-datorer över prenumerationer och resursgrupper.
- Om du flyttar ett valv som innehåller VM säkerhetskopierade data mellan prenumerationer, måste du flytta dina virtuella datorer till samma prenumeration och Använd samma målresursgruppen för att fortsätta säkerhetskopieringar.<br>

> [!NOTE]
>
> Recovery Services-valv som konfigurerats för användning med **Azure Site Recovery** kan inte flytta ännu. Om du har konfigurerat virtuella datorer (Azure IaaS, Hyper-V, VMware) eller fysiska datorer för disaster recovery med hjälp av den **Azure Site Recovery**, flyttåtgärden kommer att blockeras. Resursen flytta funktionen för Site Recovery-tjänsten är inte tillgänglig ännu.


## <a name="use-azure-portal-to-move-recovery-services-vault-to-different-resource-group"></a>Använd Azure-portalen för att flytta Recovery Services-valv till annan resursgrupp

Att flytta en recovery services-valvet och dess kopplade resurser till en annan resursgrupp

1. Logga in på [Azure Portal](https://portal.azure.com/).
2. Öppna listan med **Recovery Services-valv** och väljer du valvet som du vill flytta. När instrumentpanelen för valvet öppnas visas enligt följande bild.

   ![Öppna Återställ Service-valv](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

   Om du inte ser den **Essentials** för ditt valv, klickar du på ikonen för listrutan. Du bör nu se Essentials-information för ditt valv.

   ![Fliken Essentials](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. I menyn vault översikt klickar du på **ändra** bredvid den **resursgrupp**, för att öppna den **flytta resurser** bladet.

   ![Ändra resursgrupp](./media/backup-azure-move-recovery-services/change-resource-group.png)

4. I den **flytta resurser** bladet för den valda valvet det rekommenderas att flytta valfritt relaterade resurser genom att markera kryssrutan som visas i följande bild.

   ![Flytta prenumeration](./media/backup-azure-move-recovery-services/move-resource.png)

5. Att lägga till målresursgruppen, i den **resursgrupp** listrutan väljer du en befintlig resurs gruppen eller klicka på **skapa en ny grupp** alternativet.

   ![Skapa resurs](./media/backup-azure-move-recovery-services/create-a-new-resource.png)

6. När du lägger till resursgruppen, bekräfta **jag förstår att verktyg och skript som är kopplade till flyttade resurser inte fungerar förrän jag uppgraderar dem för att använda nya resurs-ID** alternativ och klickar sedan på **OK** ska slutföras Flytta valvet.

   ![Bekräftelsemeddelande](./media/backup-azure-move-recovery-services/confirmation-message.png)


## <a name="use-azure-portal-to-move-recovery-services-vault-to-a-different-subscription"></a>Använd Azure-portalen för att flytta Recovery Services-valv till en annan prenumeration

Du kan flytta ett Recovery Services-valv och dess kopplade resurser till en annan prenumeration

1. Logga in på [Azure Portal](https://portal.azure.com/).
2. Öppna listan över Recovery Services-valv och väljer du valvet som du vill flytta. När instrumentpanelen för valvet öppnas visas enligt följande bild.

    ![Öppna Återställ Service-valv](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

    Om du inte ser den **Essentials** för ditt valv, klickar du på ikonen för listrutan. Du bör nu se Essentials-information för ditt valv.

    ![Fliken Essentials](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. I menyn vault översikt klickar du på **ändra** bredvid **prenumeration**, för att öppna den **flytta resurser** bladet.

   ![Ändra prenumeration](./media/backup-azure-move-recovery-services/change-resource-subscription.png)

4. Välj resurserna som ska flyttas, här vi rekommenderar att du använder den **Markera alla** möjlighet att välja alla valfria resurser.

   ![Flytta resursen](./media/backup-azure-move-recovery-services/move-resource-source-subscription.png)

5. Välj målprenumerationen från den **prenumeration** listrutan, där du vill att valvet som ska flyttas.
6. Att lägga till målresursgruppen, i den **resursgrupp** listrutan väljer du en befintlig resurs gruppen eller klicka på **skapa en ny grupp** alternativet.

   ![Lägg till prenumeration](./media/backup-azure-move-recovery-services/add-subscription.png)

7. Klicka på **jag förstår att verktyg och skript som är kopplade till flyttade resurser inte fungerar förrän jag uppgraderar dem för att använda nya resurs-ID** alternativet för att bekräfta och klicka sedan på **OK**.

> [!NOTE]
> Mellan prenumeration säkerhetskopiering (RS valvet och skyddade virtuella datorerna finns i olika prenumerationer) är inte ett scenario som stöds. Dessutom lagringsredundans alternativet från lokalt redundant lagring (LRS) till globala redundant lagring (GRS) och vice versa kan inte ändras under åtgärden för att flytta valv.
>
>

## <a name="use-powershell-to-move-recovery-services-vault"></a>Använd PowerShell för att flytta Recovery Services-valv

Flytta ett Recovery Services-valv till en annan resursgrupp genom att använda den `Move-AzureRMResource` cmdlet. `Move-AzureRMResource` kräver resursnamn och typ av resurs. Du kan hämta båda från den `Get-AzureRmRecoveryServicesVault` cmdlet.

```
$destinationRG = "<destinationResourceGroupName>"
$vault = Get-AzureRmRecoveryServicesVault -Name <vaultname> -ResourceGroupName <vaultRGname>
Move-AzureRmResource -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

För att flytta resurser till en annan prenumeration, innehåller den `-DestinationSubscriptionId` parametern.

```
Move-AzureRmResource -DestinationSubscriptionId "<destinationSubscriptionID>" -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

Efter körning av ovanstående cmdletar, blir du ombedd att bekräfta att du vill flytta de angivna resurserna. Typ **Y** att bekräfta. Efter en lyckad validering flyttar resursen.

## <a name="use-cli-to-move-recovery-services-vault"></a>Använd CLI för att flytta Recovery Services-valv

Använd följande cmdlet för att flytta ett Recovery Services-valv till en annan resursgrupp:

```
az resource move --destination-group <destinationResourceGroupName> --ids <VaultResourceID>
```

Om du vill flytta till en ny prenumeration, ange den `--destination-subscription-id` parametern.

## <a name="post-migration"></a>Efter migreringen

1. Du måste ange/verifiera åtkomstkontroller för resursgrupper.  
2. Backup rapporterings- och övervakningsfunktionen måste konfigureras igen för valvet inlägget flytten har slutförts. Den tidigare konfigurationen går förlorade vid flytt.



## <a name="next-steps"></a>Nästa steg

Du kan flytta många olika typer av resurser mellan resursgrupper och prenumerationer.

Mer information finns i [Flytta resurser till en ny resursgrupp eller prenumeration](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).
