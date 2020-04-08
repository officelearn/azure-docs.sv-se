---
title: Så här flyttar du valv för Azure Backup Recovery Services
description: Instruktioner om hur du flyttar återställningstjänster i Azure-prenumerationer och resursgrupper.
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 04/08/2019
ms.openlocfilehash: 3cfd442d49de2661d68de3c4e4b3575119504eb4
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804426"
---
# <a name="move-a-recovery-services-vault-across-azure-subscriptions-and-resource-groups"></a>Flytta ett Recovery Services-valv över Azure-prenumerationer och resursgrupper

I den här artikeln beskrivs hur du flyttar ett Recovery Services-valv som konfigurerats för Azure Backup över Azure-prenumerationer eller till en annan resursgrupp i samma prenumeration. Du kan använda Azure-portalen eller PowerShell för att flytta ett Recovery Services-valv.

## <a name="supported-regions"></a>Regioner som stöds

Resursflytt för Recovery Services-valvet stöds i Australien Öst, Australien Sydöstra, Kanada Central, Kanada Öst, Sydostasien, Östasien, Centralas usa, Norra centrala USA, Östra USA, Östra USA2, Södra centrala USA, Västra centrala USA, Västra USA2, Västra USA, Västra USA, Centrala Indien, Södra Indien, Östra Japan, Japan Väst, Korea Central, Korea Syd- och Sydamerika, Västeuropa, Sydafrika Nord afrika , Sydafrika West, Storbritannien Syd och Storbritannien Väst.

## <a name="unsupported-regions"></a>Regioner som inte stöds

Frankrike Central, Frankrike Södra, Tyskland Nordost, Tyskland Central, US Gov Iowa, Kina North, Kina North2, Kina East, Kina East2

## <a name="prerequisites-for-moving-recovery-services-vault"></a>Förutsättningar för att flytta Recovery Services-valv

- Under valvflyttning mellan resursgrupper är både käll- och målresursgrupperna låsta vilket förhindrar skriv- och borttagningsåtgärder. Mer information finns i den här [artikeln](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).
- Endast administratörsprenumeration har behörighet att flytta ett valv.
- För att flytta valv över prenumerationer måste målprenumerationen finnas i samma klient som källprenumerationen och dess tillstånd ska aktiveras.
- Du måste ha behörighet att utföra skrivåtgärder i målgruppen för målresursen.
- Om du flyttar valvet ändras bara resursgruppen. Valvet för återställningstjänster finns på samma plats och kan inte ändras.
- Du kan bara flytta ett Recovery Services-valv per region åt gången.
- Om en virtuell dator inte flyttas med valvet för Återställningstjänster över prenumerationer eller till en ny resursgrupp förblir de aktuella återställningspunkterna för virtuella datorer intakta i valvet tills de upphör att gälla.
- Oavsett om den virtuella datorn flyttas med valvet eller inte, kan du alltid återställa den virtuella datorn från den behållna säkerhetskopieringshistoriken i valvet.
- Azure Disk Encryption kräver att nyckelvalvet och virtuella datorer finns i samma Azure-region och prenumeration.
- Om du vill flytta en virtuell dator med hanterade diskar läser du den här [artikeln](https://azure.microsoft.com/blog/move-managed-disks-and-vms-now-available/).
- Alternativen för att flytta resurser som distribueras via modellen Klassisk varierar beroende på om du flyttar resurserna i en prenumeration eller till en ny prenumeration. Mer information finns i den här [artikeln](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).
- Principer för säkerhetskopiering som definierats för valvet behålls när valvet flyttas över prenumerationer eller till en ny resursgrupp.
- Det går inte att flytta valv med Azure Files, Azure File Sync eller SQL i virtuella IaaS-datorer mellan prenumerationer och resursgrupper.
- Om du flyttar ett valv som innehåller säkerhetskopierade virtuella datorer, mellan prenumerationer, måste du flytta dina virtuella datorer till samma prenumeration och använda samma mål-VM-resursgruppnamn (som det var i gamla prenumerationer) för att fortsätta säkerhetskopieringar.

> [!NOTE]
> Det går inte att flytta Recovery Services-valv för Azure Backup i Azure-regioner.<br><br>
> Om du har konfigurerat virtuella datorer (Azure IaaS, Hyper-V, VMware) eller fysiska datorer för haveriberedskap med **Azure Site Recovery**blockeras flyttåtgärden. Om du vill flytta valv för Azure Site Recovery läser du [den här artikeln](https://docs.microsoft.com/azure/site-recovery/move-vaults-across-regions) om du vill veta mer om hur du flyttar valv manuellt.

## <a name="use-azure-portal-to-move-recovery-services-vault-to-different-resource-group"></a>Använda Azure-portalen för att flytta Recovery Services-valv till en annan resursgrupp

Så här flyttar du ett återhämtningstjänstvalv och tillhörande resurser till en annan resursgrupp

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Öppna listan över **Recovery Services-valv** och välj det valv som du vill flytta. När instrumentpanelen för valvet öppnas visas den som visas i följande bild.

   ![Öppna återställningstjänstvalvet](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

   Om du inte ser **Essentials-informationen** för valvet klickar du på listrutan. Du bör nu se Essentials-informationen för ditt valv.

   ![Fliken Viktig information](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. Klicka på **Ändra bredvid** gruppen **Resurs**på menyn För att öppna bladet **Flytta resurser** på valvs översiktsmenyn.

   ![Ändra resursgrupp](./media/backup-azure-move-recovery-services/change-resource-group.png)

4. För det markerade valvet i bladet **Flytta resurser** rekommenderas att de valfria relaterade resurserna flyttas genom att markera kryssrutan som visas i följande bild.

   ![Flytta prenumeration](./media/backup-azure-move-recovery-services/move-resource.png)

5. Om du vill lägga till målgruppen för målresurs väljer du en befintlig resursgrupp i listrutan **Resursgrupp** eller klickar på **Skapa ett nytt gruppalternativ.**

   ![Skapa resurs](./media/backup-azure-move-recovery-services/create-a-new-resource.png)

6. När du har lagt till resursgruppen bekräftar **jag att jag förstår att verktyg och skript som är associerade med flyttade resurser inte fungerar förrän jag uppdaterar dem för att använda nya resurs-ID:n** och sedan klickar på **OK** för att slutföra flytten av valvet.

   ![Bekräftelsemeddelande](./media/backup-azure-move-recovery-services/confirmation-message.png)

## <a name="use-azure-portal-to-move-recovery-services-vault-to-a-different-subscription"></a>Använda Azure-portalen för att flytta Recovery Services-valvet till en annan prenumeration

Du kan flytta ett Recovery Services-valv och dess associerade resurser till en annan prenumeration

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Öppna listan över Recovery Services-valv och välj det valv som du vill flytta. När instrumentpanelen för valvet öppnas visas följande bild.

    ![Öppna återställningstjänstvalvet](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

    Om du inte ser **Essentials-informationen** för valvet klickar du på listrutan. Du bör nu se Essentials-informationen för ditt valv.

    ![Fliken Viktig information](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. Klicka på **Ändra bredvid** **Prenumeration**på arkivens översiktsmeny för att öppna bladet **Flytta resurser.**

   ![Ändra prenumeration](./media/backup-azure-move-recovery-services/change-resource-subscription.png)

4. Välj de resurser som ska flyttas, här rekommenderar vi att du använder alternativet **Välj alla** för att välja alla angivna valfria resurser.

   ![flytta resurs](./media/backup-azure-move-recovery-services/move-resource-source-subscription.png)

5. Välj målprenumerationen i listrutan **Prenumeration,** där du vill att valvet ska flyttas.
6. Om du vill lägga till målgruppen för målresurs väljer du en befintlig resursgrupp i listrutan **Resursgrupp** eller klickar på **Skapa ett nytt gruppalternativ.**

   ![Lägg till prenumeration](./media/backup-azure-move-recovery-services/add-subscription.png)

7. Klicka på **Jag förstår att verktyg och skript som är associerade med flyttade resurser inte kommer att fungera förrän jag uppdaterar dem för att använda nya resurs-ID** för att bekräfta, och klicka sedan på **OK**.

> [!NOTE]
> Säkerhetskopiering av korsabonnemang (RS-valv och skyddade virtuella datorer finns i olika prenumerationer) är inte ett scenario som stöds. Alternativet lagringsredundans från lokal redundant lagring (LRS) till global redundant lagring (GRS) och vice versa kan inte heller ändras under flytten av valvet.
>
>

## <a name="use-powershell-to-move-recovery-services-vault"></a>Använda PowerShell för att flytta Valvet för Återställningstjänster

Om du vill flytta ett Recovery Services-valv till en annan resursgrupp använder du cmdleten. `Move-AzureRMResource` `Move-AzureRMResource`kräver resursnamn och resurstyp. Du kan få `Get-AzureRmRecoveryServicesVault` båda från cmdlet.

```powershell
$destinationRG = "<destinationResourceGroupName>"
$vault = Get-AzureRmRecoveryServicesVault -Name <vaultname> -ResourceGroupName <vaultRGname>
Move-AzureRmResource -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

Om du vill flytta resurserna `-DestinationSubscriptionId` till en annan prenumeration inkluderar du parametern.

```powershell
Move-AzureRmResource -DestinationSubscriptionId "<destinationSubscriptionID>" -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

När du har kört de ovan nämnda cmdlets, kommer du att bli ombedd att bekräfta att du vill flytta de angivna resurserna. Skriv **Y** för att bekräfta. Efter en lyckad validering flyttas resursen.

## <a name="use-cli-to-move-recovery-services-vault"></a>Använd CLI för att flytta Recovery Services-valv

Om du vill flytta ett Recovery Services-valv till en annan resursgrupp använder du följande cmdlet:

```azurecli
az resource move --destination-group <destinationResourceGroupName> --ids <VaultResourceID>
```

Om du vill flytta till `--destination-subscription-id` en ny prenumeration anger du parametern.

## <a name="post-migration"></a>Efter migreringen

1. Ange/verifiera åtkomstkontrollerna för resursgrupperna.  
2. Funktionen Säkerhetskopieringsrapportering och övervakning måste konfigureras igen för valvposten som flytten är klar. Den tidigare konfigurationen kommer att gå förlorad under flytten.

## <a name="next-steps"></a>Nästa steg

Du kan flytta många olika typer av resurser mellan resursgrupper och prenumerationer.

Mer information finns i [Flytta resurser till en ny resursgrupp eller prenumeration](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).
