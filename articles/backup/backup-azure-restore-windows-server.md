---
title: Återställa data i Azure till en Windows server eller Windows-dator
description: Lär dig hur du återställer data som lagras i Azure till en Windows server eller Windows-dator.
services: backup
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 9/7/2018
ms.author: saurse
ms.openlocfilehash: 1e8e9365567c19400b86dc60d966eb965b83591d
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2018
ms.locfileid: "51281762"
---
# <a name="restore-files-to-windows-by-using-the-azure-resource-manager-deployment-model"></a>Återställa filer till Windows med hjälp av Azure Resource Manager-distributionsmodellen

Den här artikeln förklarar hur du återställer data från ett säkerhetskopieringsvalv. Om du vill återställa data, Använd guiden Återställ Data i Microsoft Azure Recovery Services MARS-agenten. Du kan:

* Återställa data på samma dator varifrån säkerhetskopieringarna vidtogs.
* Återställ data till en annan dator.

Använd funktionen omedelbar återställning för att montera en skrivbar återställningspunktens ögonblicksbild som en volym för återställning. Du kan sedan utforska återställa volymen och kopiera filer till en lokal dator därmed selektivt återställningen.

> [!NOTE]
> Den [januari 2017 Azure Backup update](https://support.microsoft.com/en-us/help/3216528?preview) krävs om du vill använda omedelbar återställning för att återställa data. Säkerhetskopierade data skyddas i valv i nationella inställningar som anges i supportartikeln. Läs den [januari 2017 Azure Backup update](https://support.microsoft.com/en-us/help/3216528?preview) för den senaste listan över språk som har stöd för omedelbar återställning.
>

Använd omedelbar återställning med Recovery Services-valv i Azure-portalen. Om du lagras data i Backup-valv, har de konverterats till Recovery Services-valv. Om du vill använda omedelbar återställning hämta MARS-uppdatering och följa procedurerna som nämner omedelbar återställning.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="use-instant-restore-to-recover-data-to-the-same-machine"></a>Använd omedelbar återställning för att återställa data på samma dator

Om du av misstag har tagit bort en fil och vill återställa den till samma dator (som säkerhetskopieringen görs), följande steg hjälper dig att återställa data.

1. Öppna snapin-modulen **Microsoft Azure Backup**. Om du inte vet där snapin-modulen har installerats, Sök dator eller server för **Microsoft Azure Backup**.

    Skrivbordsappen ska visas i sökresultaten.

2. Välj **Återställ Data** så startas guiden.

    ![Skärmbild av Azure Backup med Återställ Data markerat](./media/backup-azure-restore-windows-server/recover.png)

3. På den **komma igång** att återställa data på samma server eller dator, väljer du **den här servern (`<server name>`)** > **nästa**.

    ![Skärmbild av återställa Data guiden komma igång-sidan](./media/backup-azure-restore-windows-server/samemachine_gettingstarted_instantrestore.png)

4. På den **Välj återställningsläge** väljer **enskilda filer och mappar** > **nästa**.

    ![Skärmbild av återställa Data guiden Välj återställningsläge sidan](./media/backup-azure-restore-windows-server/samemachine_selectrecoverymode_instantrestore.png)
  > [!IMPORTANT]
  > Alternativet för att återställa enskilda filer och mappar kräver .NET Framework 4.5.2 eller senare. Om du inte ser den **enskilda filer och mappar** alternativet, måste du uppgradera .NET Framework version 4.5.2 eller senare, och försök igen.

  > [!TIP]
  > Den **enskilda filer och mappar** alternativet möjliggör snabb åtkomst till informationen för återställningspunkten. Den är lämplig för att återställa enskilda filer med storlekar som tillsammans utgör inte mer än 80 GB och ger överföring eller kopiera ökar hastigheten för upp till 6 Mbit/s under återställningen. Den **volym** alternativet återställer alla säkerhetskopierade data i en angiven volym. Det här alternativet ger överföringen går snabbare hastigheter (upp till 60 Mbit/s), vilket är idealiskt för att återställa stora data eller hela volymer.

5. På den **Välj volym och datum** väljer du den volym som innehåller de filer och mappar som du vill återställa.

    Välj en återställningspunkt i kalendern. Datum i **fetstil** anger tillgängligheten för minst en återställningspunkt. Om flera återställningspunkter är tillgängliga i ett enda datum, väljer du den specifika återställningspunkten från den **tid** nedrullningsbara menyn.

    ![Skärmbild av återställa Data guiden Välj volym och datum sidan](./media/backup-azure-restore-windows-server/samemachine_selectvolumedate_instantrestore.png)

6. Välj återställningspunkt att återställa och välj **montera**.

    Azure Backup monterar lokal återställningspunkt och använder dem som en volym för återställning.

7. På den **Bläddra och återställa filer** väljer **Bläddra** att öppna Windows Explorer och hitta de filer och mappar som du vill.

    ![Skärmbild av återställa Data guiden Bläddra och återställa filer](./media/backup-azure-restore-windows-server/samemachine_browserecover_instantrestore.png)


8. Kopiera filer och mappar som du vill återställa i Windows Explorer och klistra in dem till valfri plats som är lokala för servern eller datorn. Du kan öppna eller strömma filer direkt från återställningsvolymen och verifiera att du återställer rätt versioner.

    ![Skärmbild av Windows Explorer med kopia markerat](./media/backup-azure-restore-windows-server/samemachine_copy_instantrestore.png)


9. När du är klar på den **Bläddra och återställa filer** väljer **demonteringen**. Välj sedan **Ja** att bekräfta att du vill demontera volymen.

    ![Skärmbild av återställa Data guiden Bläddra och återställa filer](./media/backup-azure-restore-windows-server/samemachine_unmount_instantrestore.png)

    > [!Important]
    > Om du inte väljer **demonteringen**, återställningsvolymen är monterade i 6 timmar från den tidpunkt när den är monterad. Montera tiden är dock längre upp till 24 timmar vid en pågående filkopiering. Inga säkerhetskopieringsåtgärder kommer att köras även om volymen är monterad. Alla säkerhetskopieringen som är schemalagda att köras under den tid när volymen är monterad körs när återställningsvolymen är demonterad.
    >


## <a name="use-instant-restore-to-restore-data-to-an-alternate-machine"></a>Använd omedelbar återställning för att återställa data till en annan dator
Om hela servern tappas bort, kan du fortfarande återställa data från Azure Backup till en annan dator. Följande steg illustrerar arbetsflödet.


De här stegen är följande termer:

* *Källdatorn* – den ursprungliga datorn från säkerhetskopian skapades och som är för närvarande inte tillgänglig.
* *Måldatorn* – den datorn som data återställs.
* *Exemplet vault* – The Recovery Services-valv som källdatorn och måldatorn är registrerade. <br/>

> [!NOTE]
> Säkerhetskopieringar kan inte återställas till en måldator som kör en tidigare version av operativsystemet. Till exempel kan en säkerhetskopia som gjorts från en dator med Windows 7 återställas på en dator med Windows 8 (eller senare). En säkerhetskopia som gjorts från en Windows 8-dator kan inte återställas till en dator med Windows 7.
>
>

1. Öppna den **Microsoft Azure Backup** snapin-modulen på måldatorn.

2. Kontrollera att måldatorn och källdatorn registreras på samma Recovery Services-valv.

3. Välj **Återställ Data** att öppna den **guiden Återställ Data**.

    ![Skärmbild av Azure Backup med Återställ Data markerat](./media/backup-azure-restore-windows-server/recover.png)

4. På den **komma igång** väljer **en annan server**.

    ![Skärmbild av återställa Data guiden komma igång-sidan](./media/backup-azure-restore-windows-server/alternatemachine_gettingstarted_instantrestore.png)

5. Ange valvautentiseringsfilen som motsvarar till exempel valvet och välj **nästa**.

    Om valvautentiseringsfilen är ogiltigt (eller har upphört att gälla), kan du hämta en ny valvautentiseringsfil från exemplet valv i Azure-portalen. När du har angett en giltig valvautentiseringen visas namnet på motsvarande säkerhetskopieringsvalvet.


6. På den **Välj Backup Server** väljer källdatorn i listan visas datorer, och ange lösenfrasen. Välj sedan **Nästa**.

    ![Skärmbild av återställa Data guiden Välj Backup Server-sida](./media/backup-azure-restore-windows-server/alternatemachine_selectmachine_instantrestore.png)

7. På den **Välj återställningsläge** väljer **enskilda filer och mappar** > **nästa**.

    ![Skärmbild av återställa Data guiden Välj återställningsläge sidan](./media/backup-azure-restore-windows-server/alternatemachine_selectrecoverymode_instantrestore.png)

8. På den **Välj volym och datum** väljer du den volym som innehåller de filer och mappar som du vill återställa.

    Välj en återställningspunkt i kalendern. Datum i **fetstil** anger tillgängligheten för minst en återställningspunkt. Om flera återställningspunkter är tillgängliga i ett enda datum, väljer du den specifika återställningspunkten från den **tid** nedrullningsbara menyn.

    ![Skärmbild av återställa Data guiden Välj volym och datum sidan](./media/backup-azure-restore-windows-server/alternatemachine_selectvolumedate_instantrestore.png)

9. Välj **montera** lokalt montera återställningspunkten som en volym för återställning på måldatorn.

10. På den **Bläddra och återställa filer** väljer **Bläddra** att öppna Windows Explorer och hitta de filer och mappar som du vill.

    ![Skärmbild av återställa guiden Bläddra och återställa datafiler sidan](./media/backup-azure-restore-windows-server/alternatemachine_browserecover_instantrestore.png)

11. Kopiera filer och mappar från återställningsvolymen i Windows Explorer och klistra in dem till din dator målplats. Du kan öppna eller strömma filer direkt från återställningsvolymen och verifiera att rätt versioner återställs.

    ![Skärmbild av Windows Explorer med kopia markerat](./media/backup-azure-restore-windows-server/alternatemachine_copy_instantrestore.png)

12. När du är klar på den **Bläddra och återställa filer** väljer **demonteringen**. Välj sedan **Ja** att bekräfta att du vill demontera volymen.

    ![Skärmbild av återställa guiden Bläddra och återställa datafiler sidan](./media/backup-azure-restore-windows-server/alternatemachine_unmount_instantrestore.png)

    > [!Important]
    > Om du inte väljer **demonteringen**, återställningsvolymen är monterade i 6 timmar från den tidpunkt när den är monterad. Montera tiden är dock längre upp till 24 timmar vid en pågående filkopiering. Inga säkerhetskopieringsåtgärder kommer att köras även om volymen är monterad. Alla säkerhetskopieringen som är schemalagda att köras under den tid när volymen är monterad körs när återställningsvolymen är demonterad.
    >

## <a name="next-steps"></a>Nästa steg
Nu när du har återställt filer och mappar, kan du [hantera dina säkerhetskopior](backup-azure-manage-windows-server.md).
