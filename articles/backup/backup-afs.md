---
title: Säkerhetskopiera Azure-filresurser i Azure-portalen
description: Lär dig hur du använder Azure-portalen för att säkerhetskopiera Azure-filresurser i Recovery Services-valvet
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: c1dea6925bad96be178f875567077fafa4db9326
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938219"
---
# <a name="back-up-azure-file-shares-in-a-recovery-services-vault"></a>Säkerhetskopiera Azure-filresurser i ett Recovery Services-valv

I den här artikeln beskrivs hur du använder Azure-portalen för att säkerhetskopiera [Azure-filresurser](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

I den här artikeln får du lära dig hur du:

* Skapa ett Recovery Services-valv.
* Identifiera filresurser och konfigurera säkerhetskopior.
* Kör ett säkerhetskopieringsjobb på begäran för att skapa en återställningspunkt.

## <a name="prerequisites"></a>Krav

* Identifiera eller skapa ett [Recovery Services-valv](#create-a-recovery-services-vault) i samma region som lagringskontot som är värd för filresursen.
* Kontrollera att filresursen finns i någon av de [lagringskontotyper som stöds](#limitations-for-azure-file-share-backup-during-preview).

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>Begränsningar för säkerhetskopiering av Azure-fildelning under förhandsversionen

Säkerhetskopiering för Azure-filresurser finns i förhandsversion. Azure-filresurser i både general-purpose v1- och general-purpose v2-lagringskonton stöds. Här är begränsningarna för säkerhetskopiering av Azure-filresurser:

* Stöd för säkerhetskopiering av Azure-filresurser i lagringskonton med [zonuppsagd lagring](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) (ZRS) är för närvarande begränsat till [dessa regioner](https://docs.microsoft.com/azure/backup/backup-azure-files-faq#in-which-geos-can-i-back-up-azure-file-shares).
* Azure Backup stöder för närvarande konfigurera schemalagda en gång dagligen säkerhetskopior av Azure-filresurser.
* Det maximala antalet schemalagda säkerhetskopieringar per dag är en.
* Det maximala antalet säkerhetskopieringar på begäran per dag är fyra.
* Använd [resurslås](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest) på lagringskontot för att förhindra oavsiktlig borttagning av säkerhetskopior i ditt Recovery Services-valv.
* Ta inte bort ögonblicksbilder som skapats av Azure Backup. Om du tar bort ögonblicksbilder kan det leda till förlust av återställningspunkter eller återställningsfel.
* Ta inte bort filresurser som skyddas av Azure Backup. Den aktuella lösningen tar bort alla ögonblicksbilder som tagits av Azure Backup när filresursen har tagits bort, så att alla återställningspunkter går förlorade.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-replication"></a>Ändra lagringsreplikering

Som standard använder valv [geo-redundant lagring (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).

* Om valvet är din primära säkerhetskopieringsmekanism rekommenderar vi att du använder GRS.
* Du kan använda [lokalt redundant lagring (LRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) som ett billigt alternativ.

Så här ändrar du lagringsreplikeringstypen:

1. Välj **Egenskaper** under avsnittet **Inställningar** i det nya valvet.

1. Välj **Uppdatera**under **Konfiguration av säkerhetskopiering**på sidan **Egenskaper** .

1. Välj lagringsreplikeringstyp och välj **Spara**.

    ![Uppdatera konfiguration för säkerhetskopiering](./media/backup-afs/backup-configuration.png)

> [!NOTE]
> Du kan inte ändra lagringsreplikeringstypen när valvet har konfigurerats och innehåller säkerhetskopior. Om du vill göra detta måste du återskapa valvet.
>

## <a name="discover-file-shares-and-configure-backup"></a>Identifiera filresurser och konfigurera säkerhetskopiering

1. Öppna [Azure portal](https://portal.azure.com/)valvet för Återställningstjänster som du vill använda för att säkerhetskopiera filresursen i Azure-portalen.

1. I instrumentpanelen **för Återställningstjänster** väljer du **+Säkerhetskopiering**.

   ![Recovery Services-valv](./media/backup-afs/recovery-services-vault.png)

    a. I **Säkerhetskopieringsmål**anger du Var **Azure**körs **din arbetsbelastning?**

    ![Välj Azure File Share som säkerhetskopieringsmål](./media/backup-afs/backup-goal.png)

    b.  I **Vad vill du säkerhetskopiera?** **Azure File Share**

    c.  Välj **Säkerhetskopiering** om du vill registrera Filresurstillägget för Azure i valvet.

    ![Välj Säkerhetskopiering om du vill associera Azure-filresursen med valvet](./media/backup-afs/register-extension.png)

1. När du har valt **Säkerhetskopiering**öppnas fönstret **Säkerhetskopiering** och du uppmanas att välja ett lagringskonto från en lista över identifierade lagringskonton som stöds. De är antingen associerade med det här valvet eller finns i samma region som valvet, men ännu inte associerade till någon Recovery Services-valv.

   ![Välj lagringskonto](./media/backup-afs/select-storage-account.png)

1. Välj ett konto i listan över identifierade lagringskonton och välj **OK**. Azure söker i lagringskontot efter filresurser som kan säkerhetskopieras. Om du nyligen har lagt till filresurserna och inte ser dem i listan kan du ge filresurserna lite tid att visas.

    ![Identifiera filresurser](./media/backup-afs/discovering-file-shares.png)

1. Välj en eller flera av de filresurser som du vill säkerhetskopiera i listan **Filresurser.** Välj **OK**.

1. När du har valt filresurser växlar menyn **Säkerhetskopiering** till **principer för säkerhetskopiering**. På den här menyn väljer du antingen en befintlig princip för säkerhetskopiering eller skapar en ny. Välj sedan **Aktivera säkerhetskopiering**.

    ![Välja säkerhetskopieringspolicy](./media/backup-afs/select-backup-policy.png)

När du har angett en princip för säkerhetskopiering tas en ögonblicksbild av filresurserna vid den schemalagda tiden. Återställningspunkten behålls också för den valda perioden.

## <a name="create-an-on-demand-backup"></a>Skapa en säkerhetskopiering på begäran

Ibland kanske du vill generera en ögonblicksbild av säkerhetskopiering, eller återställningspunkt, utanför de tider som schemalagts i säkerhetskopieringsprincipen. En vanlig orsak till att generera en säkerhetskopiering på begäran är direkt efter att du har konfigurerat säkerhetskopieringsprincipen. Baserat på schemat i säkerhetskopieringsprincipen kan det ta timmar eller dagar tills en ögonblicksbild tas. För att skydda dina data fram tills säkerhetskopieringspolicyn aktiveras, bör du starta en säkerhetskopiering på begäran. Det krävs ofta att du skapar en säkerhetskopiering på begäran innan du gör planerade ändringar i filresurserna.

### <a name="create-a-backup-job-on-demand"></a>Skapa ett säkerhetskopieringsjobb på begäran

1. Öppna valvet för Återställningstjänster som du använde för att säkerhetskopiera filresursen. Välj **Säkerhetskopierade objekt** under avsnittet **Skyddade objekt** i fönstret **Översikt.**

   ![Välj säkerhetskopieringsobjekt](./media/backup-afs/backup-items.png)

1. När du har valt **Säkerhetskopieringsobjekt**visas en ny ruta med alla **typer av säkerhetskopieringshantering** **bredvid fönstret Översikt.**

   ![Lista över typer av hantering av säkerhetskopiering](./media/backup-afs/backup-management-types.png)

1. Välj **Azure Storage (Azure Files)** i listan **Hantering av säkerhetskopiering** . Du ser en lista över alla filresurser och motsvarande lagringskonton som backas upp med hjälp av det här valvet.

   ![Säkerhetsobjekt för Azure Storage (Azure Files)](./media/backup-afs/azure-files-backup-items.png)

1. Välj den filresurs du vill använda i listan över Azure-filresurser. Information om **säkerhetskopieringsobjekt** visas. Välj Säkerhetskopiering nu på menyn **Säkerhetskopieringsobjekt** . **Backup now** Eftersom det här säkerhetskopieringsjobbet finns på begäran finns det ingen bevarandeprincip som är associerad med återställningspunkten.

   ![Välj säkerhetskopiering nu](./media/backup-afs/backup-now.png)

1. Fönstret **Säkerhetskopiering nu** öppnas. Ange den senaste dag som du vill behålla återställningspunkten för. Du kan ha en maximal kvarhållning på 10 år för en säkerhetskopiering på begäran.

   ![Välj kvarhållningsdatum](./media/backup-afs/retention-date.png)

1. Välj **OK** för att bekräfta säkerhetskopieringsjobbet på begäran som körs.

1. Övervaka portalmeddelandena för att hålla reda på slutförande av säkerhetskopieringsjobb. Du kan övervaka jobbframsteget i instrumentpanelen i valvet. Välj **Säkerhetskopieringsjobb** > **pågår**.

## <a name="next-steps"></a>Nästa steg

Lär dig att:
* [Återställa Azure-filresurser](restore-afs.md)
* [Hantera säkerhetskopior av Azure-fildelning](manage-afs-backup.md)
