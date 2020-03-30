---
title: Återställa data i Azure till en Windows-server
description: I den här artikeln kan du läsa om hur du återställer data som lagras i Azure till en Windows-server eller Windows-dator med MICROSOFT Azure Recovery Services (MARS)Agent.
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 09/07/2018
ms.openlocfilehash: 25ca8eecaeb615f071340188a23fae7978ddb75c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409821"
---
# <a name="restore-files-to-windows-by-using-the-azure-resource-manager-deployment-model"></a>Återställa filer till Windows med hjälp av distributionsmodellen för Azure Resource Manager

I den här artikeln beskrivs hur du återställer data från ett valv för säkerhetskopiering. Om du vill återställa data använder du guiden Återställ data i MICROSOFT Azure Recovery Services (MARS)Agent. Du kan:

* Återställ data till samma dator som säkerhetskopiorna har säkerhetskopierats från.
* Återställ data till en annan dator.

Använd funktionen Omedelbar återställning för att montera en ögonblicksbild av skrivbara återställningspunkter som en återställningsvolym. Du kan sedan utforska återställningsvolymen och kopiera filer till en lokal dator och på så sätt selektivt återställa filer.

> [!NOTE]
> [Uppdateringen av Azure Backup i januari 2017](https://support.microsoft.com/help/3216528/azure-backup-update-for-microsoft-azure-recovery-services-agent-januar) krävs om du vill använda omedelbar återställning för att återställa data. Dessutom måste säkerhetskopieringsdata skyddas i valv i språk som anges i supportartikeln. Se uppdateringen för [Azure Backup i januari 2017](https://support.microsoft.com/help/3216528/azure-backup-update-for-microsoft-azure-recovery-services-agent-januar) för den senaste listan över språk som stöder omedelbar återställning.
>

Använd Instant Restore med Recovery Services-valv i Azure-portalen. Om du har lagrat data i valv för säkerhetskopiering har de konverterats till Recovery Services-valv. Om du vill använda Omedelbar återställning laddar du ned MARS-uppdateringen och följer procedurerna som nämner Omedelbar återställning.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="use-instant-restore-to-recover-data-to-the-same-machine"></a>Använd Omedelbar återställning för att återställa data till samma dator

Om du av misstag har tagit bort en fil och vill återställa den till samma dator (från vilken säkerhetskopian vidtas), hjälper följande steg dig att återställa data.

1. Öppna snapin-modulen **Microsoft Azure Backup**. Om du inte vet var snapin-modulen installerades söker du i datorn eller servern efter **Microsoft Azure Backup**.

    Skrivbordsappen ska visas i sökresultaten.

2. Välj **Återställ data** om du vill starta guiden.

    ![Skärmbild av Azure Backup, med återställningsdata markerade](./media/backup-azure-restore-windows-server/recover.png)

3. Om du vill återställa data till samma server eller dator på sidan **Komma igång** väljer du Den här servern **(`<server name>`)** > **Nästa**.

    ![Skärmbild av sidan Komma igång med guiden Hämta data](./media/backup-azure-restore-windows-server/samemachine_gettingstarted_instantrestore.png)

4. På sidan Välj > **återställningsläge** väljer du **Enskilda filer och mappar** **Nästa**.

    ![Skärmbild av sidan Välj återställningsläge för återställningsläge för återställningsläge för återställningsläge för återställningsläge](./media/backup-azure-restore-windows-server/samemachine_selectrecoverymode_instantrestore.png)
   > [!IMPORTANT]
   > Alternativet för att återställa enskilda filer och mappar kräver .NET Framework 4.5.2 eller senare. Om alternativet Enskilda **filer och mappar** inte visas måste du uppgradera .NET Framework till version 4.5.2 eller senare och försöka igen.
 
   > [!TIP]
   > Alternativet **Enskilda filer och mappar** ger snabb åtkomst till återställningspunktsdata. Den är lämplig för att återställa enskilda filer, med storlekar på totalt högst 80 GB, och erbjuder överföring eller kopiering hastigheter upp till 6 MBps under återhämtning. Alternativet **Volym** återställer alla säkerhetskopierade data i en angiven volym. Det här alternativet ger snabbare överföringshastigheter (upp till 60 MBps), vilket är idealiskt för att återställa stora data eller hela volymer.

5. På sidan **Välj volym och Datum** väljer du den volym som innehåller de filer och mappar som du vill återställa.

    Välj en återställningspunkt i kalendern. Datum i **fetstil** anger tillgängligheten för minst en återställningspunkt. Om flera återställningspunkter är tillgängliga inom ett enda datum väljer du den specifika återställningspunkten på den **nedrullningsna** menyn Tid.

    ![Skärmbild av sidan Välj volym och datum i guiden Återställ data](./media/backup-azure-restore-windows-server/samemachine_selectvolumedate_instantrestore.png)

6. När du har valt återställningspunkten att återställa väljer du **Montera**.

    Azure Backup monterar den lokala återställningspunkten och använder den som en återställningsvolym.

7. På sidan **Bläddra och återställ filer** väljer du **Bläddra** för att öppna Utforskaren och hitta de filer och mappar du vill använda.

    ![Skärmbild av sidan Bläddra bland och återställ filer i guiden Återställ data](./media/backup-azure-restore-windows-server/samemachine_browserecover_instantrestore.png)

8. I Utforskaren kopierar du de filer och mappar som du vill återställa och klistrar in dem på valfri plats som är lokal på servern eller datorn. Du kan öppna eller strömma filerna direkt från återställningsvolymen och kontrollera att du återställer rätt versioner.

    ![Skärmbild av Utforskaren, med Kopiera markerad](./media/backup-azure-restore-windows-server/samemachine_copy_instantrestore.png)

9. När du är klar väljer du **Avmontera**på sidan **Bläddra och återställ filer** . Välj sedan **Ja** för att bekräfta att du vill avmontera volymen.

    ![Skärmbild av sidan Bläddra bland och återställ filer i guiden Återställ data](./media/backup-azure-restore-windows-server/samemachine_unmount_instantrestore.png)

    > [!Important]
    > Om du inte väljer **Avmontera**förblir återställningsvolymen monterad i 6 timmar från den tidpunkt då den monterades. Monteringstiden förlängs dock upp till högst 24 timmar vid en pågående filkopia. Inga säkerhetskopieringsåtgärder körs medan volymen är monterad. Alla säkerhetskopieringsåtgärder som schemalagts att köras under den tid då volymen är monterad körs efter att återställningsvolymen har avmonterats.
    >

## <a name="use-instant-restore-to-restore-data-to-an-alternate-machine"></a>Använda Omedelbar återställning för att återställa data till en alternativ dator

Om hela servern går förlorad kan du fortfarande återställa data från Azure Backup till en annan dator. Följande steg illustrerar arbetsflödet.

De här stegen omfattar följande terminologi:

* *Källmaskin* – Den ursprungliga datorn som säkerhetskopian togs från och som för närvarande inte är tillgänglig.
* *Måldatorn* – Den dator som data återställs till.
* *Provvalv* – Valvet för återställningstjänster som källdatorn och måldatorn är registrerade på.

> [!NOTE]
> Säkerhetskopior kan inte återställas till en måldator som kör en tidigare version av operativsystemet. En säkerhetskopia som hämtas från en Windows 7-dator kan till exempel återställas på en Dator med Windows 7 (eller senare). En säkerhetskopia som tas från en Windows 8-dator kan inte återställas till en Windows 7-dator.
>
>

1. Öppna snapin-modulen **Microsoft Azure Backup** på måldatorn.

2. Kontrollera att måldatorn och källdatorn är registrerade i samma Recovery Services-valv.

3. Välj **Återställ data** om du vill öppna guiden Återställ **data**.

    ![Skärmbild av Azure Backup, med återställningsdata markerade](./media/backup-azure-restore-windows-server/recover.png)

4. På sidan **Komma igång** väljer du En **annan server**.

    ![Skärmbild av sidan Komma igång med guiden Hämta data](./media/backup-azure-restore-windows-server/alternatemachine_gettingstarted_instantrestore.png)

5. Ange valvautentiseringsfilen som motsvarar exempelvalvet och välj **Nästa**.

    Om arkivautentiseringsfilen är ogiltig (eller har upphört att gälla) hämtar du en ny arkivautentiseringsfil från exempelvalvet i Azure-portalen. När du har anger en giltig valvautentiseringsuppgifter visas namnet på motsvarande säkerhetskopieringsvalv.

6. På sidan **Välj säkerhetskopieringsserver** väljer du källdatorn i listan över datorer som visas och tillhandahåller lösenfrasen. Välj sedan **Nästa**.

    ![Skärmbild av sidan Välj säkerhetskopieringsserver för återställning av data](./media/backup-azure-restore-windows-server/alternatemachine_selectmachine_instantrestore.png)

7. På sidan Välj >  **återställningsläge** väljer du **Enskilda filer och mappar****Nästa**.

    ![Skärmbild av sidan Välj återställningsläge för återställningsläge för återställningsläge för återställningsläge för återställningsläge](./media/backup-azure-restore-windows-server/alternatemachine_selectrecoverymode_instantrestore.png)

8. På sidan **Välj volym och Datum** väljer du den volym som innehåller de filer och mappar som du vill återställa.

    Välj en återställningspunkt i kalendern. Datum i **fetstil** anger tillgängligheten för minst en återställningspunkt. Om flera återställningspunkter är tillgängliga inom ett enda datum väljer du den specifika återställningspunkten på den **nedrullningsna** menyn Tid.

    ![Skärmbild av sidan Välj volym och datum i guiden Återställ data](./media/backup-azure-restore-windows-server/alternatemachine_selectvolumedate_instantrestore.png)

9. Välj **Montera** om du vill montera återställningspunkten lokalt som en återställningsvolym på målmaskinen.

10. På sidan **Bläddra och återställ filer** väljer du **Bläddra** för att öppna Utforskaren och hitta de filer och mappar du vill ha.

    ![Skärmbild av sidan Bläddra bland och återställ filer i guiden Återställ data](./media/backup-azure-restore-windows-server/alternatemachine_browserecover_instantrestore.png)

11. I Utforskaren kopierar du filerna och mapparna från återställningsvolymen och klistrar in dem på målmaskinens plats. Du kan öppna eller strömma filerna direkt från återställningsvolymen och kontrollera att rätt versioner återställs.

    ![Skärmbild av Utforskaren, med Kopiera markerad](./media/backup-azure-restore-windows-server/alternatemachine_copy_instantrestore.png)

12. När du är klar väljer du **Avmontera**på sidan **Bläddra och återställ filer** . Välj sedan **Ja** för att bekräfta att du vill avmontera volymen.

    ![Skärmbild av sidan Bläddra bland och återställ filer i guiden Återställ data](./media/backup-azure-restore-windows-server/alternatemachine_unmount_instantrestore.png)

    > [!Important]
    > Om du inte väljer **Avmontera**förblir återställningsvolymen monterad i 6 timmar från den tidpunkt då den monterades. Monteringstiden förlängs dock upp till högst 24 timmar vid en pågående filkopia. Inga säkerhetskopieringsåtgärder körs medan volymen är monterad. Alla säkerhetskopieringsåtgärder som schemalagts att köras under den tid då volymen är monterad körs efter att återställningsvolymen har avmonterats.
    >

## <a name="next-steps"></a>Nästa steg

* Nu när du har återställt dina filer och mappar kan du [hantera dina säkerhetskopior](backup-azure-manage-windows-server.md).

* Hitta [vanliga frågor om säkerhetskopiering av filer och mappar](backup-azure-file-folder-backup-faq.md).
