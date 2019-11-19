---
title: Flytta Azure Backup Recovery Services valv
description: Instruktioner om hur du flyttar Recovery Services-valvet över Azure-prenumerationer och resurs grupper.
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 04/08/2019
ms.openlocfilehash: 7d0a6c47fa08774161d3a2b7507cdfecaf8c7197
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172892"
---
# <a name="move-a-recovery-services-vault-across-azure-subscriptions-and-resource-groups"></a>Flytta ett Recovery Services valv över Azure-prenumerationer och resurs grupper

Den här artikeln förklarar hur du flyttar ett Recovery Services valv som kon figurer ATS för Azure Backup över Azure-prenumerationer eller till en annan resurs grupp i samma prenumeration. Du kan använda Azure Portal eller PowerShell för att flytta ett Recovery Services-valv.

## <a name="supported-regions"></a>Regioner som stöds

Flytt av resurser för Recovery Services Vault stöds i östra Australien, östra Australien, centrala Kanada, Östra Kanada, Asien, sydöstra, Asien, östra, centrala USA, norra centrala USA, östra USA, östra 2; USA, södra centrala USA, västra centrala USA, västra centrala 2; USA, västra USA, västra Centrala Indien, södra Indien, Östra Japan, västra Japan, centrala Korea, södra Nord Europa, Västeuropa, södra Afrika, sydöstra Sydafrika, västra Afrika, Storbritannien, södra och Storbritannien, västra.

## <a name="prerequisites-for-moving-recovery-services-vault"></a>Krav för att flytta Recovery Services valv

- När valvet flyttas över resurs grupper är både käll-och mål resurs grupperna låsta för att förhindra Skriv-och borttagnings åtgärder. Mer information finns i den här [artikeln](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).
- Endast administratörs prenumerationen har behörighet att flytta ett valv.
- För att flytta valv mellan prenumerationer måste mål prenumerationen finnas i samma klient organisation som käll prenumerationen och dess tillstånd ska vara aktiverat.
- Du måste ha behörighet att utföra Skriv åtgärder i mål resurs gruppen.
- Om du flyttar valvet ändras bara resurs gruppen. Recovery Services valvet kommer att finnas på samma plats och kan inte ändras.
- Du kan bara flytta ett Recovery Services valv per region i taget.
- Om en virtuell dator inte flyttas med Recovery Services valvet över prenumerationer, eller till en ny resurs grupp, förblir den aktuella återställnings punkten för virtuella datorer intakt i valvet tills den upphör att gälla.
- Oavsett om den virtuella datorn har flyttats med valvet eller inte, kan du alltid återställa den virtuella datorn från den sparade säkerhets kopierings historiken i valvet.
- Azure Disk Encryption kräver att nyckel valvet och de virtuella datorerna finns i samma Azure-region och prenumeration.
- Information om hur du flyttar en virtuell dator med hanterade diskar finns i den här [artikeln](https://azure.microsoft.com/blog/move-managed-disks-and-vms-now-available/).
- Alternativen för att flytta resurser som distribueras via den klassiska modellen varierar beroende på om du flyttar resurserna i en prenumeration eller till en ny prenumeration. Mer information finns i den här [artikeln](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).
- Säkerhets kopierings principer som definierats för valvet behålls när valvet flyttas över prenumerationer eller till en ny resurs grupp.
- Det går inte att flytta valvet med Azure Files, Azure File Sync eller SQL i IaaS VM: ar över prenumerationer och resurs grupper.
- Om du flyttar ett valv som innehåller data för säkerhets kopiering av virtuella datorer, mellan prenumerationer, måste du flytta dina virtuella datorer till samma prenumeration och använda samma mål resurs grupp namn för virtuell dator (som i den gamla prenumerationen) för att fortsätta med säkerhets kopieringen.

> [!NOTE]
>
> Det går inte att flytta Recovery Services valv som kon figurer ATS för användning med **Azure Site Recovery** . Om du har konfigurerat några virtuella datorer (Azure IaaS, Hyper-V, VMware) eller fysiska datorer för haveri beredskap med hjälp av **Azure Site Recovery**, blockeras flyttnings åtgärden. Funktionen för att flytta resurser för Site Recovery-tjänsten är inte tillgänglig ännu.

## <a name="use-azure-portal-to-move-recovery-services-vault-to-different-resource-group"></a>Använd Azure Portal för att flytta Recovery Services valv till en annan resurs grupp

Så här flyttar du ett Recovery Services-valv och dess associerade resurser till en annan resurs grupp

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Öppna listan över **Recovery Services valv** och välj det valv som du vill flytta. När instrument panelen för valvet öppnas visas den som på bilden nedan.

   ![Öppna Recover service Vault](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

   Om du inte ser information om **Essentials** för ditt valv klickar du på den nedrullningsbara ikonen. Du bör nu se information om Essentials för ditt valv.

   ![Fliken information om Essentials](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. I valv översikts menyn klickar du på **ändra** bredvid **resurs gruppen**för att öppna bladet **Flytta resurser** .

   ![Ändra resurs grupp](./media/backup-azure-move-recovery-services/change-resource-group.png)

4. För det valda valvet på bladet **Flytta resurser** rekommenderar vi att du flyttar de valfria relaterade resurserna genom att markera kryss rutan som visas i följande bild.

   ![Flytta prenumeration](./media/backup-azure-move-recovery-services/move-resource.png)

5. Lägg till mål resurs gruppen genom att välja en befintlig resurs grupp i list rutan **resurs grupp** eller klicka på **skapa en ny grupp** .

   ![Skapa resurs](./media/backup-azure-move-recovery-services/create-a-new-resource.png)

6. När du har lagt till resurs gruppen bekräftar **du att verktyg och skript som är kopplade till flyttade resurser inte fungerar förrän jag uppdaterar dem så att de använder alternativet nya resurs-ID: n** och klickar sedan på **OK** för att slutföra flyttningen av valvet.

   ![Bekräftelse meddelande](./media/backup-azure-move-recovery-services/confirmation-message.png)

## <a name="use-azure-portal-to-move-recovery-services-vault-to-a-different-subscription"></a>Använd Azure Portal för att flytta Recovery Services valv till en annan prenumeration

Du kan flytta ett Recovery Services valv och dess associerade resurser till en annan prenumeration

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Öppna listan över Recovery Services valv och välj det valv som du vill flytta. När instrument panelen för valvet öppnas visas följande bild.

    ![Öppna Recover service Vault](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

    Om du inte ser information om **Essentials** för ditt valv klickar du på den nedrullningsbara ikonen. Du bör nu se information om Essentials för ditt valv.

    ![Fliken information om Essentials](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. Klicka på **ändra** bredvid **prenumeration**på menyn valv översikt för att öppna bladet **Flytta resurser** .

   ![Ändra prenumeration](./media/backup-azure-move-recovery-services/change-resource-subscription.png)

4. Välj de resurser som ska flyttas, här rekommenderar vi att du använder alternativet **Välj alla** för att markera alla valfria resurser.

   ![flytta resurs](./media/backup-azure-move-recovery-services/move-resource-source-subscription.png)

5. Välj mål prenumerationen i list rutan **prenumeration** där du vill att valvet ska flyttas.
6. Lägg till mål resurs gruppen genom att välja en befintlig resurs grupp i list rutan **resurs grupp** eller klicka på **skapa en ny grupp** .

   ![Lägg till prenumeration](./media/backup-azure-move-recovery-services/add-subscription.png)

7. Klicka på **Jag förstår att verktyg och skript som är kopplade till flyttade resurser inte fungerar förrän jag uppdaterar dem för att använda nya resurs-ID** -alternativ för att bekräfta, och klicka sedan på **OK**.

> [!NOTE]
> Säkerhets kopiering mellan prenumerationer (RS Vault och skyddade virtuella datorer finns i olika prenumerationer) är inte ett scenario som stöds. Dessutom går det inte att ändra lagrings alternativet för redundans från lokal redundant lagring (LRS) till global redundant lagring (GRS) och vice versa i flytt åtgärden för valv.
>
>

## <a name="use-powershell-to-move-recovery-services-vault"></a>Använd PowerShell för att flytta Recovery Services valv

Om du vill flytta ett Recovery Services valv till en annan resurs grupp använder du `Move-AzureRMResource`-cmdleten. `Move-AzureRMResource` kräver resurs namn och typ av resurs. Du kan hämta båda från `Get-AzureRmRecoveryServicesVault`-cmdlet: en.

```powershell
$destinationRG = "<destinationResourceGroupName>"
$vault = Get-AzureRmRecoveryServicesVault -Name <vaultname> -ResourceGroupName <vaultRGname>
Move-AzureRmResource -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

Om du vill flytta resurserna till en annan prenumeration inkluderar du parametern `-DestinationSubscriptionId`.

```powershell
Move-AzureRmResource -DestinationSubscriptionId "<destinationSubscriptionID>" -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

När du har kört ovanstående cmdlets uppmanas du att bekräfta att du vill flytta de angivna resurserna. Skriv in **Y** för att bekräfta. När verifieringen är klar flyttas resursen.

## <a name="use-cli-to-move-recovery-services-vault"></a>Använd CLI för att flytta Recovery Services valv

Om du vill flytta ett Recovery Services valv till en annan resurs grupp använder du följande cmdlet:

```azurecli
az resource move --destination-group <destinationResourceGroupName> --ids <VaultResourceID>
```

Om du vill flytta till en ny prenumeration, ange den `--destination-subscription-id` parametern.

## <a name="post-migration"></a>Post-migrering

1. Ange/verifiera åtkomst kontrollerna för resurs grupperna.  
2. Funktionen för rapportering och övervakning av säkerhets kopiering måste konfigureras igen för valvet publicera flyttningen slutförd. Den tidigare konfigurationen går förlorad under flytt åtgärden.

## <a name="next-steps"></a>Nästa steg

Du kan flytta många olika typer av resurser mellan resurs grupper och prenumerationer.

Mer information finns i [Flytta resurser till en ny resursgrupp eller prenumeration](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).
