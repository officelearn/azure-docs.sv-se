---
title: Återställa data i Azure till en Windows Server eller Windows-dator
description: Lär dig hur du återställer data som lagras i Azure till en Windows Server eller Windows-dator.
services: backup
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 9/7/2018
ms.author: saurse
ms.openlocfilehash: 20d2f289f4d40d773fde9f6b770dc49b87c34804
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2018
ms.locfileid: "44297255"
---
# <a name="restore-files-to-a-windows-server-or-windows-client-machine-using-resource-manager-deployment-model"></a>Återställ filer till en Windows-server eller Windows-klientdator med hjälp av Resource Manager-distributionsmodellen

Den här artikeln förklarar hur du återställer data från ett säkerhetskopieringsvalv. Om du vill återställa data, Använd guiden Återställ Data i Microsoft Azure Recovery Services MARS-agenten. När du återställer data, är det möjligt att:

* Återställa data på samma dator varifrån säkerhetskopieringarna vidtogs.
* Återställa data till en annan dator.

I januari 2017 släppte Microsoft en uppdatering för förhandsversionen av MARS-agenten. Tillsammans med felkorrigeringar, kan du använda omedelbar återställning, där du kan montera en skrivbar återställningspunktens ögonblicksbild som en volym för återställning. Sedan kan du utforska återställa volymen och kopiera filer till en lokal dator därmed selektivt återställningen.

> [!NOTE]
> Den [januari 2017 Azure Backup update](https://support.microsoft.com/en-us/help/3216528?preview) krävs om du vill använda omedelbar återställning för att återställa data. Säkerhetskopierade data måste också skyddas i valv i nationella inställningar som anges i supportartikeln. Läs den [januari 2017 Azure Backup update](https://support.microsoft.com/en-us/help/3216528?preview) för den senaste listan över språk som har stöd för omedelbar återställning. Omedelbar återställning är **inte** för närvarande tillgängligt i alla språk.
>

Använd omedelbar återställning med Recovery Services-valv i Azure-portalen. Om du lagras data i Backup-valv, har de konverterats till Recovery Services-valv. Om du vill använda omedelbar återställning hämta MARS-uppdatering och följa procedurerna som nämner omedelbar återställning.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="use-instant-restore-to-recover-data-to-the-same-machine"></a>Använd omedelbar återställning för att återställa data på samma dator

Om du av misstag har tagit bort en fil och vill återställa den till samma dator (som säkerhetskopieringen görs), följande steg hjälper dig att återställa data.

1. Öppna den **Microsoft Azure Backup** Fäst i. Om du inte vet var snapin-modulen har installerats, Sök dator eller server för **Microsoft Azure Backup**.

    Skrivbordsappen ska visas i sökresultaten.

2. Klicka på **Återställ Data** så startas guiden.

    ![Återställa Data](./media/backup-azure-restore-windows-server/recover.png)

3. På den **komma igång** , att återställa data på samma server eller dator, väljer **den här servern (`<server name>`)** och klicka på **nästa**.

    ![Välj det här serveralternativet att återställa data på samma dator](./media/backup-azure-restore-windows-server/samemachine_gettingstarted_instantrestore.png)

4. På den **Välj återställningsläge** fönstret Välj **enskilda filer och mappar** och klicka sedan på **nästa**.

    ![Bläddra efter filer](./media/backup-azure-restore-windows-server/samemachine_selectrecoverymode_instantrestore.png)
> [!IMPORTANT]
> Alternativet för att återställa *enskilda filer och mappar* kräver .NET Framework 4.5.2 eller senare. Om du inte ser den *enskilda filer och mappar* alternativet, måste du uppgradera .NET Framework version 4.5.2 eller senare och försök igen.

> [!TIP]
> Den *enskilda filer och mappar* alternativet möjliggör snabb åtkomst till informationen för återställningspunkten. Det är lämpligt för att återställa enskilda filer med storlekar som tillsammans utgör inte mer än 80 GB i storlek och erbjudanden överföring/kopiera ökar hastigheten för upp till 6 Mbit/s under återställningen. Den *volym* alternativet återställer alla säkerhetskopierade data i en viss volym. Det här alternativet ger snabbare överföringshastighet (upp till 60 Mbit/s), vilket är idealiskt för att återställa stora storlek data eller hela volymer.

5. På den **Välj volym och datum** fönstret väljer du den volym som innehåller filerna och/eller mappar som du vill återställa.

    Välj en återställningspunkt i kalendern. Du kan återställa från valfri återställningspunkt i tid. Datum i **fetstil** anger tillgängligheten för minst en återställningspunkt. När du har valt ett datum om flera återställningspunkter är tillgängliga, Välj den specifika återställningspunkten från den **tid** nedrullningsbara menyn.

    ![Volym och datum](./media/backup-azure-restore-windows-server/samemachine_selectvolumedate_instantrestore.png)

6. När du har valt återställningspunkt att återställa, klickar du på **montera**.

    Azure Backup monterar lokal återställningspunkt och använder dem som en volym för återställning.

7. På den **Bläddra och återställa filer** fönstret klickar du på **Bläddra** att öppna Windows Explorer och hitta de filer och mappar som du vill.

    ![Återställningsalternativ](./media/backup-azure-restore-windows-server/samemachine_browserecover_instantrestore.png)


8. I Windows Explorer, kopierar du filerna och/eller mapparna du vill återställa och klistrar in dem på valfri plats som är lokala för servern eller datorn. Du kan öppna eller strömma filer direkt från återställningsvolymen och verifiera att du återställer rätt versioner.

    ![Kopiera och klistra in filer och mappar från monterad volym till lokal plats](./media/backup-azure-restore-windows-server/samemachine_copy_instantrestore.png)


9. När du har återställt filerna och/eller mappar, på den **Bläddra och återställningsfiler** fönstret klickar du på **demonteringen**. Klicka sedan på **Ja** att bekräfta att du vill demontera volymen.

    ![Demontera volymen och bekräfta](./media/backup-azure-restore-windows-server/samemachine_unmount_instantrestore.png)

    > [!Important]
    > Om du inte klickar på demonteringen förblir återställningsvolymen monterade i 6 timmar från den tidpunkt när den har monterad. Montera tiden är dock utökade upp till högst 24 timmar händelse av en pågående filkopiering. Inga säkerhetskopieringsåtgärder kommer att köras även om volymen är monterad. Alla säkerhetskopieringen som är schemalagda att köras under den tid när volymen har monterats kan körs när återställningsvolymen är demonterad.
    >


## <a name="use-instant-restore-to-restore-data-to-an-alternate-machine"></a>Använd omedelbar återställning för att återställa data till en annan dator
Om hela servern tappas bort, kan du fortfarande återställa data från Azure Backup till en annan dator. Följande steg illustrerar arbetsflödet.


De termer som används i de här stegen omfattar:

* *Källdatorn* – den ursprungliga datorn från säkerhetskopian skapades och som är för närvarande inte tillgänglig.
* *Måldatorn* – den datorn som data återställs.
* *Exemplet vault* – The Recovery Services-valv som den *källdatorn* och *måldatorn* registreras. <br/>

> [!NOTE]
> Säkerhetskopieringar kan inte återställas till en måldator som kör en tidigare version av operativsystemet. Till exempel återställts en säkerhetskopia som gjorts från en Windows 7 som datorn kan vara på en Windows 8 eller senare, dator. En säkerhetskopia som gjorts från en Windows 8-dator kan inte återställas till en dator med Windows 7.
>
>

1. Öppna den **Microsoft Azure Backup** Fäst i på den *måldatorn*.

2. Se till att den *måldatorn* och *källdatorn* är registrerade i samma Recovery Services-valvet.

3. Klicka på **Återställ Data** att öppna den **guiden Återställ Data**.

    ![Återställa Data](./media/backup-azure-restore-windows-server/recover.png)

4. På den **komma igång** väljer **en annan server**

    ![En annan Server](./media/backup-azure-restore-windows-server/alternatemachine_gettingstarted_instantrestore.png)

5. Ange valvautentiseringsfilen som motsvarar den *exempel vault*, och klicka på **nästa**.

    Om valvautentiseringsfilen är ogiltigt (eller har upphört att gälla) kan du ladda ned en ny valvautentiseringsfil från den *exempel vault* i Azure-portalen. När du har angett en giltig valvautentiseringen visas namnet på motsvarande Backup-valvet.


6. På den **Välj Backup Server** fönstret väljer du den *källdatorn* i listan visas datorer och ange lösenfrasen. Klicka sedan på **Nästa**.

    ![Lista över datorer](./media/backup-azure-restore-windows-server/alternatemachine_selectmachine_instantrestore.png)

7. På den **Välj återställningsläge** fönstret Välj **enskilda filer och mappar** och klicka på **nästa**.

    ![Search](./media/backup-azure-restore-windows-server/alternatemachine_selectrecoverymode_instantrestore.png)

8. På den **Välj volym och datum** fönstret väljer du den volym som innehåller filerna och/eller mappar som du vill återställa.

    Välj en återställningspunkt i kalendern. Du kan återställa från valfri återställningspunkt i tid. Datum i **fetstil** anger tillgängligheten för minst en återställningspunkt. När du har valt ett datum om flera återställningspunkter är tillgängliga, Välj den specifika återställningspunkten från den **tid** nedrullningsbara menyn.

    ![Sökobjekt](./media/backup-azure-restore-windows-server/alternatemachine_selectvolumedate_instantrestore.png)

9. Klicka på **montera** lokalt montera återställningspunkten som en volym för återställning på din *måldatorn*.

10. På den **Bläddra och återställa filer** fönstret klickar du på **Bläddra** att öppna Windows Explorer och hitta de filer och mappar som du vill.

    ![Kryptering](./media/backup-azure-restore-windows-server/alternatemachine_browserecover_instantrestore.png)

11. I Windows Explorer, kopiera filer och/eller mapparna från återställningsvolymen och klistra in dem till din *måldatorn* plats. Du kan öppna eller strömma filer direkt från återställningsvolymen och verifiera att rätt versioner återställs.

    ![Kryptering](./media/backup-azure-restore-windows-server/alternatemachine_copy_instantrestore.png)

12. När du har återställt filerna och/eller mappar, på den **Bläddra och återställningsfiler** fönstret klickar du på **demonteringen**. Klicka sedan på **Ja** att bekräfta att du vill demontera volymen.

    ![Kryptering](./media/backup-azure-restore-windows-server/alternatemachine_unmount_instantrestore.png)

    > [!Important]
    > Om du inte klickar på demonteringen förblir återställningsvolymen monterade i 6 timmar från den tidpunkt när den har monterad. Montera tiden är dock utökade upp till högst 24 timmar händelse av en pågående filkopiering. Inga säkerhetskopieringsåtgärder kommer att köras även om volymen är monterad. Alla säkerhetskopieringen som är schemalagda att köras under den tid när volymen har monterats kan körs när återställningsvolymen är demonterad.
    >

## <a name="next-steps"></a>Nästa steg
* Nu när du har återställt filer och mappar, kan du [hantera dina säkerhetskopior](backup-azure-manage-windows-server.md).
