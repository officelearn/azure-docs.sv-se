---
title: Säkerhetskopiera filer i Azure Stack virtuella datorer
description: Använda Azure Backup för att säkerhetskopiera och återställa Azure Stack-filer och program till Azure Stack-miljön.
services: backup
author: adigan
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 6/5/2018
ms.author: adigan
ms.openlocfilehash: 67d79f2aa41bab8a14d693098538d22ffeb05a4e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60848803"
---
# <a name="back-up-files-on-azure-stack"></a>Säkerhetskopiera filer på Azure Stack
Du kan använda Azure Backup för att skydda (eller säkerhetskopiera) filer och program på Azure Stack. Om du vill säkerhetskopiera filer och program, installera Microsoft Azure Backup Server som en virtuell dator som körs på Azure Stack. Du kan skydda filer på någon Azure Stack-server i samma virtuella nätverk. När du har installerat Azure Backup Server, lägga till Azure-diskar för att öka den lokala lagringen tillgänglig för kortsiktig säkerhetskopierade data. Azure Backup Server använder Azure storage för långsiktig kvarhållning.

> [!NOTE]
> Även om Azure Backup Server och System Center Data Protection Manager (DPM) är liknande, finns DPM inte stöd för användning med Azure Stack.
>

Den här artikeln beskriver inte installera Azure Backup Server i Azure Stack-miljö. Om du vill installera Azure Backup Server på Azure Stack finns i artikeln [installerar Azure Backup Server](backup-mabs-install-azure-stack.md).


## <a name="back-up-files-and-folders-in-azure-stack-vms-to-azure"></a>Säkerhetskopiera filer och mappar i Azure Stack-datorer till Azure

Öppna konsolen Azure Backup Server för att konfigurera Azure Backup Server för att skydda filer i Azure Stack-datorer. Konsolen ska du använda för att konfigurera skyddsgrupper och att skydda data på dina virtuella datorer.

1. I Azure Backup Server-konsolen klickar du på **Protection** och i verktygsfältet klickar du på **New** att öppna den **Skapa ny Skyddsgrupp** guiden.

   ![Konfigurera skydd i Azure Backup Server-konsolen](./media/backup-mabs-files-applications-azure-stack/1-mabs-menu-create-protection-group.png)

    Det kan ta några sekunder att öppna guiden. När guiden öppnas klickar du på **nästa** att gå vidare till den **Välj typ av Skyddsgrupp** skärmen.

   ![Öppnar guiden Ny Skyddsgrupp](./media/backup-mabs-files-applications-azure-stack/2-create-new-protection-group-wiz.png)

2. På den **Välj typ av Skyddsgrupp** väljer **servrar** och klicka på **nästa**.

    ![Öppnar guiden Ny Skyddsgrupp](./media/backup-mabs-files-applications-azure-stack/3-select-protection-group-type.png)

    Den **Välj gruppmedlemmar** skärmen öppnas. 

    ![Öppnar guiden Ny Skyddsgrupp](./media/backup-mabs-files-applications-azure-stack/4-opening-screen-choose-servers.png)

3. I den **Välj gruppmedlemmar** klickar du på **+** att expandera listan över underelementen. Markera kryssrutan för alla objekt som du vill skydda. När alla objekt har valts, klickar du på **nästa**.

    ![Öppnar guiden Ny Skyddsgrupp](./media/backup-mabs-files-applications-azure-stack/5-select-group-members.png)

    Microsoft rekommenderar att placera alla data som kommer att dela en skyddsprincip i en skyddsgrupp. Fullständig information om planering och distribution skyddsgrupper finns i System Center DPM-artikeln [distribuera Skyddsgrupper](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups?view=sc-dpm-1801).

4. I den **Välj Dataskyddsmetod** skärmen, Skriv ett namn för skyddsgruppen. Markera kryssrutan för **jag vill ha kortvarigt skydd med:** och **jag vill ha ett onlineskydd**. Klicka på **Nästa**.

    ![Öppnar guiden Ny Skyddsgrupp](./media/backup-mabs-files-applications-azure-stack/6-select-data-protection-method.png)

    Att välja **jag vill ha ett onlineskydd**, måste du först välja **jag vill ha kortvarigt skydd med:** Disk. Azure Backup Server skyddar inte till band, så att disken är det enda alternativet för kortsiktigt skydd.

5. I den **ange kortsiktiga mål** skärmen, väljer du hur lång tid att behålla återställningspunkter sparas på disk och när du vill spara inkrementella säkerhetskopieringar. Klicka på **Nästa**.

    > [!IMPORTANT]
    > Du bör **inte** avinstallationsalternativ driftåterställning (säkerhetskopiering) på Azure Backup Server-anslutna diskar i mer än fem dagar.
    >

    ![Öppnar guiden Ny Skyddsgrupp](./media/backup-mabs-files-applications-azure-stack/7-select-short-term-goals.png) 

    I stället för att välja ett intervall för inkrementell säkerhetskopiering att köra en snabb fullständig säkerhetskopiering precis före varje schemalagd återställningspunkt, klickar du på **precis innan en återställningspunkt**. Om du skyddar programarbetsbelastningar skapas återställningspunkter per frekvens synkroniseringsschemat i Azure Backup Server (förutsatt att programmet stöder inkrementella säkerhetskopior). Om programmet inte stöder inkrementella säkerhetskopieringar kan Azure Backup Server kör en snabb fullständig säkerhetskopiering.

    För **filen återställningspunkter**, ange när du vill skapa återställningspunkter. Klicka på **ändra** att ställa in dagar i veckan när återställningspunkter skapas och tider.

6. I den **granska diskallokering** kan du se storage lagringspoolens allokerade diskutrymme för skyddsgruppen.

    **Total datastorlek** är storleken på de data du vill säkerhetskopiera och **diskutrymme som ska etableras** på Azure Backup Server är det rekommendera utrymmet för skyddsgruppen. Azure Backup Server väljer den mest lämpliga säkerhetskopieringsvolymen, baserat på inställningarna. Du kan dock redigera säkerhetskopieringsvolym i allokering diskinformation. Välj önskad lagringsplats för arbetsbelastningarna i den nedrullningsbara menyn. Redigeringarna ändrar värdena för totalt lagringsutrymme och ledigt utrymme i fönstret tillgängliga disklagring. Underetablerat utrymme är mängden lagringsutrymme som Azure Backup Server föreslår att du lägger till volymen för att fortsätta med säkerhetskopieringar smidigt i framtiden.

7. I **Välj metod för skapande av replik**väljer du hur du vill hantera den första fullständiga datareplikeringen. Om du vill replikera via nätverket rekommenderar Azure du väljer en tid med låg belastning. Överväg att replikera data med flyttbara medier för stora mängder data eller bristfälliga nätverksförhållanden.

8. I **Välj alternativ för konsekvenskontroll**väljer du hur du vill automatisera konsekvenskontroller. Aktivera konsekvenskontroller ska köras endast när replikering av data har blivit inkonsekvent, eller enligt ett schema. Om du inte vill konfigurera automatiska konsekvenskontroller kan du köra en manuell kontroll när som helst genom att:
    * I den **Protection** område i Azure Backup Server-konsolen, högerklicka på skyddsgruppen och välj **utför konsekvenskontroll**.

9. Om du väljer att säkerhetskopiera till Azure, på den **ange onlineskyddsdata** ser du till att de arbetsbelastningar du vill säkerhetskopiera till Azure är markerade.

10. I **ange onlinesäkerhetskopieringsschema**, ange när inkrementella säkerhetskopieringar till Azure ska ske. 

    Du kan schemalägga säkerhetskopieringar att köras varje dag/vecka/månad/år och vilken tid/datum då de ska köras. Säkerhetskopieringar kan göras upp till två gånger per dag. Varje gång ett säkerhetskopieringsjobb körs skapas en återställningspunkt för data i Azure från kopian av säkerhetskopierade data som lagras i Azure Backup Server-disk.

11. I **Ange princip för onlinebevarande**, ange hur återställningspunkter som skapats från säkerhetskopior varje dag/vecka/månad/år ska finnas kvar i Azure.

12. I **Välj onlinereplikering**, ange hur den första fullständiga replikeringen av data sker. 

13. På **sammanfattning**, granska dina inställningar. När du klickar på **Skapa grupp**, den första datareplikeringen inträffar. När replikering av data är klar på den **Status** sidan skyddsgruppens status visas som **OK**. Det första säkerhetskopieringsjobbet sker i enlighet med skyddet gruppinställningar.

## <a name="recover-file-data"></a>Återställa fildata

Använda Azure Backup Server-konsolen för att återställa data till den virtuella datorn.

1. I Azure Backup Server-konsolen i navigeringsfältet, klickar du på **Recovery** och bläddra efter de data som du vill återställa. Välj data i resultatfönstret.

2. I kalendern i avsnittet med återställningspunkter, fet stil anger återställningspunkter är tillgängliga. Välj datum att återställa.

3. I den **Återställningsbart objekt** fönstret, Välj det objekt som du vill återställa.

4. I den **åtgärder** fönstret klickar du på **återställa** att öppna Återställningsguiden.

5. Du kan återställa data på följande sätt:

    * **Återställ till den ursprungliga platsen** – det här alternativet fungerar inte om datorn är ansluten via VPN. I stället använda en alternativ plats och kopiera sedan data från den platsen.
    * **Återställ till en alternativ plats**

6. Ange återställningsalternativ:

    * För **återställningsfunktionssätt för befintlig version**väljer **Skapa kopia**, **hoppa över**, eller **överskrivning**. Skriv över är tillgängligt endast när du återställer till den ursprungliga platsen.
    * För **Återställ säkerhet**, Välj **tillämpa inställningar på måldatorn** eller **tillämpa säkerhetsinställningar för återställningspunktversionen**.
    * För **begränsning av nätverksbandbredd**, klickar du på **ändra** att aktivera begränsning av nätverksbandbredd.
    * **Meddelande** klickar du på **skicka ett e-postmeddelande när återställningen är slutförd**, och ange vilka mottagare som ska få meddelandet. Avgränsa e-postadresser med kommatecken.
    * När du har gjort valen klickar du på **nästa**

7. Granska återställningsinställningarna och klickar på **återställa**. 

    > [!Note] 
    > När återställningsjobbet pågår, avbryts alla Synkroniseringsjobb för de valda återställningsobjekt.
    >

Om du använder Modern Backup Storage (MBS) stöds inte återställning av slutanvändare File Server (EUR). File Server EUR är ett beroende på Volume Shadow Copy Service (VSS), som Modern Backup Storage inte använder. Om EUR är aktiverad, kan du använda följande steg för att återställa data:

1. Navigera till de skyddade filerna och högerklicka på filen och välj **egenskaper**.

2. På den **egenskaper** -menyn klickar du på **tidigare versioner** och välj den version som du vill återställa.

## <a name="view-azure-backup-server-with-a-vault"></a>Visa Azure Backup Server med ett valv
Om du vill visa Azure Backup Server entiteter i Azure-portalen, kan du följa följande steg:
1. Öppna Recovery Services-valvet.
2. Klicka på infrastruktur för säkerhetskopiering.
3. Visa Säkerhetskopieringshanteringsservrar.

## <a name="see-also"></a>Se också
Information om hur du använder Azure Backup Server för att skydda andra arbetsbelastningar finns i följande artiklar:
- [Säkerhetskopiera SharePoint-servergrupp](https://docs.microsoft.com/azure/backup/backup-mabs-sharepoint-azure-stack)
- [Säkerhetskopiera SQLServer](https://docs.microsoft.com/azure/backup/backup-mabs-sql-azure-stack)
