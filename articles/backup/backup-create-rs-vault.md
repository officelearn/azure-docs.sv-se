---
title: Skapa recovery services-valv
description: I den här artikeln får du lära dig hur du skapar Recovery Services-valv som lagrar säkerhetskopior och återställningspunkter.
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 439f102e8f13bff63ab388be8f10df07ab2dc7d2
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80672852"
---
# <a name="create-a-recovery-services-vault"></a>skapar ett Recovery Services-valv

Ett Recovery Services-valv är en entitet som lagrar säkerhetskopior och återställningspunkter som skapats över tid. Valvet för återställningstjänster innehåller också de principer för säkerhetskopiering som är associerade med skyddade virtuella datorer.

Så här skapar du ett Recovery Services-valv:

1. Logga in på din prenumeration i [Azure-portalen](https://portal.azure.com/).

2. På menyn till vänster väljer du **Alla tjänster**.

    ![Välj Alla tjänster](./media/backup-create-rs-vault/click-all-services.png)

3. I dialogrutan **Alla tjänster** anger du **Recovery Services**. Listan över resurser filtrerar enligt dina indata. Välj **Recovery Services-valv**i listan över resurser .

    ![Ange och välja Recovery Services-valv](./media/backup-create-rs-vault/all-services.png)

    Listan över Recovery Services-valv i prenumerationen visas.

4. På instrumentpanelen **för Återställningstjänster** väljer du **Lägg till**.

    ![Lägga till ett recovery services-valv](./media/backup-create-rs-vault/add-button-create-vault.png)

    Dialogrutan **Återställningstjänster** öppnas. Ange värden för **gruppen Namn,** **Prenumeration,** **Resurs**och **Plats**.

    ![Konfigurera valvet för återställningstjänster](./media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Namn**: Ange ett eget namn för att identifiera valvet. Namnet måste vara unikt för Azure-prenumerationen. Ange ett namn som har minst två, men inte fler än 50 tecken. Namnet får börja med en bokstav och består endast av bokstäver, siffror och bindestreck.
   - **Prenumeration**: Välj den prenumeration som ska användas. Om du bara är medlem i en prenumeration visas det namnet. Om du är osäker på vilken prenumeration du ska använda använder du standardprenumerationen (föreslagen). Det finns bara flera alternativ om ditt arbets- eller skolkonto är kopplat till mer än en Azure-prenumeration.
   - **Resursgrupp**: Använd en befintlig resursgrupp eller skapa en ny. Om du vill visa listan över tillgängliga resursgrupper i prenumerationen väljer du **Använd befintlig**och väljer sedan en resurs i listrutan. Om du vill skapa en ny resursgrupp väljer du **Skapa ny** och anger namnet. Fullständig information om resursgrupper finns i [översikt över Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
   - **Plats**: Välj det geografiska området för valvet. Om du vill skapa ett valv för att skydda virtuella datorer **måste** valvet finnas i samma region som de virtuella datorerna.

      > [!IMPORTANT]
      > Om du inte är säker på var den virtuella datorn är är stänger du dialogrutan. Gå till listan över virtuella datorer i portalen. Om du har virtuella datorer i flera regioner skapar du ett recovery services-valv i varje region. Skapa valvet på den första platsen innan du skapar valvet för en annan plats. Du behöver inte ange lagringskonton för att lagra säkerhetskopieringsdata. Valvet för återställningstjänster och Azure Backup-tjänsten hanterar det automatiskt.
      >
      >

5. När du är redo att skapa valvet för Återställningstjänster väljer du **Skapa**.

    ![Skapa valvet för återställningstjänster](./media/backup-create-rs-vault/click-create-button.png)

    Det kan ta ett tag att skapa valvet för Återställningstjänster. Övervaka statusmeddelanden i området **Meddelanden** längst upp till höger på portalen. När valvet har skapats visas det i listan över Recovery Services-valv. Om du inte ser valvet väljer du **Uppdatera**.

     ![Uppdatera listan över valv för säkerhetskopiering](./media/backup-create-rs-vault/refresh-button.png)

## <a name="set-storage-redundancy"></a>Ställ in lagringsredundans

Azure Backup hanterar automatiskt lagring för valvet. Du måste ange hur lagringen ska replikeras.

1. På bladet **Recovery Services-valv** klickar du på det nya valvet. Klicka på **Egenskaper**under avsnittet **Inställningar** .
2. Klicka på **Uppdatera**under **Säkerhetskopieringskonfiguration**i **Egenskaper**.

3. Markera lagringsreplikeringstypen och klicka på **Spara**.

     ![Ange lagringskonfigurationen för det nya valvet](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

   - Vi rekommenderar att om du använder Azure som en primär slutpunkt för lagring för säkerhetskopiering fortsätter du att använda standardinställningen **Geo-redundant.**
   - Om du inte använder Azure som en slutpunkt för primär lagring av säkerhetskopior väljer du **Lokalt redundant**, vilket minskar kostnaderna för Azure-lagring.
   - Läs mer om [geo](../storage/common/storage-redundancy-grs.md) och [lokal](../storage/common/storage-redundancy-lrs.md) redundans.

> [!NOTE]
> Ändra **Storage Replication-typ** (Lokalt redundant/ Geo-redundant) för ett recovery-tjänstvalv måste göras innan du konfigurerar säkerhetskopior i valvet. När du har konfigurerat säkerhetskopiering inaktiveras alternativet att ändra och du kan inte ändra **typen Lagringsreplikering**.

## <a name="set-cross-region-restore"></a>Ange återställning mellan regioner

Som ett av återställningsalternativen kan du återställa virtuella Azure-virtuella datorer i en sekundär region som är en [Azure-region.](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) Med det här alternativet kan du:

- genomföra övningar när det finns ett revisions- eller efterlevnadskrav
- återställa den virtuella datorn eller dess disk om det finns en katastrof i den primära regionen.

Om du vill välja den här funktionen väljer du **Aktivera återställning mellan regioner** från bladet Konfiguration för **säkerhetskopiering.**

För den här processen finns det priskonsekvenser som det är på lagringsnivå.

>[!NOTE]
>Innan du börjar:
>
>- Granska [supportmatrisen](backup-support-matrix.md#cross-region-restore) för en lista över hanterade typer och regioner som stöds.
>- Funktionen Återställning av korsläge (CRR) förhandsgranskas nu i alla offentliga Azure-regioner.
>- CRR är en opt-in-funktion på valvnivå för alla GRS-valv (inaktiverat som standard).
>- Använd följande kommando för att gå in på din prenumeration för den här funktionen:<br>
>  `Register-AzProviderFeature -FeatureName CrossRegionRestore -ProviderNamespace Microsoft.RecoveryServices`
>- Om du är med i den här funktionen under offentlig begränsad förhandsversion innehåller e-postmeddelandet för granskningsgodkännande information om prispolicyer.
>- När du har valt in kan det ta upp till 48 timmar innan säkerhetskopieringsobjekten är tillgängliga i sekundära regioner.
>- För närvarande stöds CRR endast för hantering av säkerhetskopieringstyper - ARM Azure VM (klassisk Azure VM stöds inte).  När ytterligare hanteringstyper stöder CRR registreras de **automatiskt.**

### <a name="configure-cross-region-restore"></a>Konfigurera återställning mellan regioner

Ett valv som skapats med GRS-redundans innehåller alternativet att konfigurera funktionen Återställning av korsregion. Varje GRS valv kommer att ha en banner, som kommer att länka till dokumentationen. Om du vill konfigurera CRR för valvet går du till bladet Säkerhetskopieringskonfiguration, som innehåller alternativet att aktivera den här funktionen.

 ![Banderoll för konfiguration för säkerhetskopiering](./media/backup-azure-arm-restore-vms/banner.png)

1. Gå till Recovery Services-valv > inställningar > egenskaper från portalen.
2. Klicka på **Aktivera återställning av korsregion i det här valvet** för att aktivera funktionen.

   ![Innan du klickar på Aktivera återställning mellan regioner i det här valvet](./media/backup-azure-arm-restore-vms/backup-configuration1.png)

   ![När du har klickat på Aktivera återställning mellan regioner i det här valvet](./media/backup-azure-arm-restore-vms/backup-configuration2.png)

Lär dig hur du [visar säkerhetskopieringsobjekt i den sekundära regionen](backup-azure-arm-restore-vms.md#view-backup-items-in-secondary-region).

Lär dig hur du [återställer i den sekundära regionen](backup-azure-arm-restore-vms.md#restore-in-secondary-region).

Lär dig hur du [övervakar sekundära jobb för att återställa sekundära regioner](backup-azure-arm-restore-vms.md#monitoring-secondary-region-restore-jobs).

## <a name="modifying-default-settings"></a>Ändra standardinställningar

Vi rekommenderar starkt att du granskar standardinställningarna för **lagringsreplikeringstyp** och **säkerhetsinställningar** innan du konfigurerar säkerhetskopior i valvet.

- **Storage Replication-typ** som standard är inställd **på Geo-redundant**. När du har konfigurerat säkerhetskopian inaktiveras alternativet att ändra. Följ dessa [steg](https://docs.microsoft.com/azure/backup/backup-create-rs-vault#set-storage-redundancy) för att granska och ändra inställningarna.

- **Mjuk borttagning** som standard är **Aktiverad** på nyskapade valv för att skydda säkerhetskopierade data från oavsiktliga eller skadliga borttagningar. Följ dessa [steg](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#disabling-soft-delete) för att granska och ändra inställningarna.

## <a name="next-steps"></a>Nästa steg

[Läs mer](backup-azure-recovery-services-vault-overview.md) Valven för återställningstjänster.
[Läs mer](backup-azure-delete-vault.md) Ta bort recovery services-valv.
