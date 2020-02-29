---
title: Skapa Recovery Services-valv
description: I den här artikeln lär du dig hur du skapar Recovery Services valv som lagrar säkerhets kopior och återställnings punkter.
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: eb7dcb4038260b1edac57a2163a1c0eae29ee452
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2020
ms.locfileid: "77920118"
---
# <a name="create-a-recovery-services-vault"></a>Skapa ett Recovery Services-valv

Ett Recovery Services-valv är en entitet som lagrar säkerhets kopior och återställnings punkter som skapats med tiden. Recovery Services valvet innehåller också de säkerhets kopierings principer som är associerade med de skyddade virtuella datorerna.

Så här skapar du ett Recovery Services-valv:

1. Logga in på din prenumeration i [Azure-portalen](https://portal.azure.com/).

2. På menyn till vänster väljer du **Alla tjänster**.

    ![Välj Alla tjänster](./media/backup-create-rs-vault/click-all-services.png)

3. I dialogrutan **Alla tjänster** anger du **Recovery Services**. Listan över resurser filtreras enligt dina inaktuella inaktuella. Välj **Recovery Services valv**i listan över resurser.

    ![Ange och välja Recovery Services-valv](./media/backup-create-rs-vault/all-services.png)

    Listan över Recovery Services-valv i prenumerationen visas.

4. På instrument panelen för **Recovery Services valv** väljer du **Lägg till**.

    ![Lägg till ett Recovery Services-valv](./media/backup-create-rs-vault/add-button-create-vault.png)

    Dialog rutan **Recovery Services valv** öppnas. Ange värden för **namn**, **prenumeration**, **resurs grupp**och **plats**.

    ![Konfigurera Recovery Services-valvet](./media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Namn**: Ange ett eget namn som identifierar valvet. Namnet måste vara unikt för Azure-prenumerationen. Ange ett namn som innehåller minst två, men högst 50 tecken. Namnet måste börja med en bokstav och får bara bestå av bokstäver, siffror och bindestreck.
   - **Prenumeration**: Välj den prenumeration som ska användas. Om du är medlem i endast en prenumeration ser du det namnet. Om du inte är säker på vilken prenumeration du ska använda använder du standard prenumerationen (rekommenderas). Det finns flera alternativ bara om ditt arbets-eller skol konto är associerat med fler än en Azure-prenumeration.
   - **Resurs grupp**: Använd en befintlig resurs grupp eller skapa en ny. Om du vill se en lista över tillgängliga resurs grupper i din prenumeration väljer du **Använd befintlig**och väljer sedan en resurs i list rutan. Om du vill skapa en ny resurs grupp väljer du **Skapa ny** och anger namnet. Fullständig information om resurs grupper finns i [Azure Resource Manager översikt](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
   - **Plats**: Välj det geografiska området för valvet. För att skapa ett valv för att skydda virtuella datorer **måste** valvet vara i samma region som de virtuella datorerna.

      > [!IMPORTANT]
      > Om du inte är säker på var din virtuella dator finns stänger du dialog rutan. Gå till listan över virtuella datorer i portalen. Om du har virtuella datorer i flera regioner skapar du ett Recovery Services valv i varje region. Skapa valvet på den första platsen innan du skapar valvet för en annan plats. Du behöver inte ange lagrings konton för att lagra säkerhetskopierade data. Recovery Services valvet och Azure Backups tjänstens referens som automatiskt.
      >
      >

5. När du är redo att skapa Recovery Services-valvet väljer du **skapa**.

    ![Skapa Recovery Services-valvet](./media/backup-create-rs-vault/click-create-button.png)

    Det kan ta en stund att skapa Recovery Services-valvet. Övervaka status meddelanden i **meddelande** fältet i det övre högra hörnet i portalen. När valvet har skapats visas det i listan över Recovery Services-valv. Om du inte ser ditt valv väljer du **Uppdatera**.

     ![Uppdatera listan över säkerhets kopierings valv](./media/backup-create-rs-vault/refresh-button.png)

## <a name="set-storage-redundancy"></a>Ange redundans för lagring

Azure Backup hanterar automatiskt lagring för valvet. Du måste ange hur lagringen ska replikeras.

1. På bladet **Recovery Services-valv** klickar du på det nya valvet. Under avsnittet **Inställningar** klickar du på **Egenskaper**.
2. I **Egenskaper**, under **säkerhets kopierings konfiguration**, klickar du på **Uppdatera**.

3. Välj typ av lagrings replikering och klicka på **Spara**.

     ![Ange lagringskonfigurationen för det nya valvet](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

   - Vi rekommenderar att om du använder Azure som primär slut punkt för lagring av säkerhets kopior fortsätter du att använda standardvärdet **Geo-redundant** .
   - Om du inte använder Azure som en slutpunkt för primär lagring av säkerhetskopior väljer du **Lokalt redundant**, vilket minskar kostnaderna för Azure-lagring.
   - Lär dig mer om [geo](../storage/common/storage-redundancy-grs.md) och [lokal](../storage/common/storage-redundancy-lrs.md) redundans.

> [!NOTE]
> Att ändra **typ av lagrings replikering** (lokalt redundant/Geo-redundant) för ett Recovery Services-valv måste göras innan du konfigurerar säkerhets kopieringar i valvet. När du har konfigurerat säkerhets kopiering inaktive ras alternativet att ändra och du kan inte ändra **typen av lagrings replik**.

## <a name="set-cross-region-restore"></a>Ange återställning av kors region

Som en av återställnings alternativen kan du med återställningen mellan regioner (CRR) återställa virtuella Azure-datorer i en sekundär region, som är en [Azure-kopplad region](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). Med det här alternativet kan du:

- genomför övningar när det finns ett krav på granskning eller efterlevnad
- Återställ den virtuella datorn eller disken om det finns en katastrof i den primära regionen.

Om du vill välja den här funktionen väljer du **Aktivera återställning av kors region** från bladet **säkerhets kopierings konfiguration** .

För den här processen finns det prissättnings effekter som på lagrings nivå.

>[!NOTE]
>Innan du börjar:
>
>- Granska [support matrisen](backup-support-matrix.md#cross-region-restore) för en lista över hanterade typer och regioner som stöds.
>- Funktionen för återställning av kors region (CRR) är för närvarande endast tillgänglig i följande regioner: västra centrala USA, västra USA 2, östra Australien och sydöstra Australien. 
>- CRR är ett alternativ för att välja en valv nivå för alla GRS-valv (inaktiverat som standard).
>- Använd följande kommando för att publicera din prenumeration för den här funktionen:<br>
>  `Register-AzProviderFeature -FeatureName CrossRegionRestore -ProviderNamespace Microsoft.RecoveryServices`
>- Om du är inloggad på den här funktionen under en offentlig begränsad för hands version innehåller e-postmeddelandet om godkännande av pris information.
>- Efter väljer kan det ta upp till 48 timmar innan säkerhets kopierings objekten är tillgängliga i sekundära regioner.
>- För närvarande stöds inte CRR för säkerhets kopierings hanterings typ-ARM Azure VM (den klassiska virtuella Azure-datorn stöds inte).  När ytterligare hanterings typer har stöd för CRR, registreras de **automatiskt** .

### <a name="configure-cross-region-restore"></a>Konfigurera återställning mellan regioner

Ett valv som skapats med GRS-redundans omfattar alternativet att konfigurera funktionen för återställning av kors region. Varje GRS-valv kommer att ha en banderoll som länkar till dokumentationen. Om du vill konfigurera CRR för valvet går du till bladet säkerhets kopierings konfiguration, som innehåller alternativet att aktivera den här funktionen.

 ![Banderoll för säkerhets kopierings konfiguration](./media/backup-azure-arm-restore-vms/banner.png)

1. Från portalen går du till Recovery Services valv > Inställningar > Egenskaper.
2. Aktivera funktionen genom att klicka på **Aktivera återställning mellan regioner i det här valvet** .

   ![Innan du klickar på Aktivera återställning mellan regioner i det här valvet](./media/backup-azure-arm-restore-vms/backup-configuration1.png)

   ![När du har klickat på Aktivera återställning mellan regioner i det här valvet](./media/backup-azure-arm-restore-vms/backup-configuration2.png)

Lär dig hur du [visar säkerhets kopierings objekt i den sekundära regionen](backup-azure-arm-restore-vms.md#view-backup-items-in-secondary-region).

Lär dig hur du [återställer i den sekundära regionen](backup-azure-arm-restore-vms.md#restore-in-secondary-region).

Lär dig hur du [övervakar återställnings jobb för sekundär region](backup-azure-arm-restore-vms.md#monitoring-secondary-region-restore-jobs).

## <a name="modifying-default-settings"></a>Ändra standardinställningar

Vi rekommenderar starkt att du granskar standardinställningarna för typ och **säkerhets inställningar** för **lagringsprovider** innan du konfigurerar säkerhets kopieringar i valvet.

- **Typen av lagringsprovider** är som standard inställd på **Geo-redundant**. När du har konfigurerat säkerhets kopieringen inaktive ras alternativet att ändra. Följ dessa [steg](https://docs.microsoft.com/azure/backup/backup-create-rs-vault#set-storage-redundancy) om du vill granska och ändra inställningarna.

- **Mjuk borttagning** är **aktiverat** som standard på nyligen skapade valv för att skydda säkerhets kopierings data från oavsiktliga eller skadliga borttagningar. Följ dessa [steg](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#disabling-soft-delete) om du vill granska och ändra inställningarna.

## <a name="next-steps"></a>Nästa steg

[Läs mer om](backup-azure-recovery-services-vault-overview.md) Recovery Services valv.
[Läs mer om](backup-azure-delete-vault.md) Ta bort Recovery Services valv.
