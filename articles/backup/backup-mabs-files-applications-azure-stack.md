---
title: Säkerhetskopiera filer i Azure-stacken virtuella datorer
description: Använda Azure Backup för att säkerhetskopiera och återställa Azure Stack-filer och program till Azure Stack-miljön.
services: backup
author: adiganmsft
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 6/5/2018
ms.author: adigan
ms.openlocfilehash: 2fb3bad56de781dd81d4c5f82b734c9420c75dee
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2018
ms.locfileid: "36751712"
---
# <a name="back-up-files-on-azure-stack"></a>Säkerhetskopiera filer på Azure-stacken
Du kan använda Azure Backup för att skydda (eller säkerhetskopiera) filer och program på Azure-stacken. Installera Microsoft Azure Backup Server för att säkerhetskopiera filer och program som en virtuell dator som körs på Azure-stacken. Du kan skydda filer på någon Azure Stack-server i samma virtuella nätverk. När du har installerat Azure Backup-Server, Lägg till Azure-diskar för att öka den lokala lagringen tillgänglig för kortsiktig säkerhetskopiering. Azure Backup-servern använder Azure-lagring för långsiktig kvarhållning.

> [!NOTE]
> Även om Azure Backup Server och System Center Data Protection Manager (DPM) är liknande, finns DPM inte stöd för användning med Azure-stacken.
>

Den här artikeln täcker inte installerar Azure Backup Server i Azure Stack-miljö. Om du vill installera Azure Backup Server på Azure-stacken, finns i artikeln [installerar Azure Backup Server](backup-mabs-install-azure-stack.md).


## <a name="back-up-files-and-folders-in-azure-stack-vms-to-azure"></a>Säkerhetskopiera filer och mappar i stacken virtuella Azure-datorer till Azure

Öppna konsolen Azure Backup Server för att konfigurera Azure Backup-Server för att skydda filer i stacken virtuella datorer i Azure virtuella datorer. Du ska använda konsolen för att konfigurera skyddsgrupper och för att skydda data på virtuella datorer.

1. Azure Backup Server-konsolen, klicka på **skydd** och klicka på i verktygsfältet, **ny** att öppna den **Skapa ny Skyddsgrupp** guiden.

   ![Konfigurera skydd i Azure Backup Server-konsolen](./media/backup-mabs-files-applications-azure-stack/1-mabs-menu-create-protection-group.png)

    Det kan ta några sekunder att öppna guiden. När guiden öppnas, klickar du på **nästa** att gå vidare till den **Välj typ av Skyddsgrupp** skärmen.

   ![Öppnar guiden Ny Skyddsgrupp](./media/backup-mabs-files-applications-azure-stack/2-create-new-protection-group-wiz.png)

2. På den **Välj typ av Skyddsgrupp** väljer **servrar** och på **nästa**.

    ![Öppnar guiden Ny Skyddsgrupp](./media/backup-mabs-files-applications-azure-stack/3-select-protection-group-type.png)

    Den **Välj gruppmedlemmar** skärmen öppnas. 

    ![Öppnar guiden Ny Skyddsgrupp](./media/backup-mabs-files-applications-azure-stack/4-opening-screen-choose-servers.png)

3. I den **Välj gruppmedlemmar** klickar du på **+** att expandera listan över subitems. Markera kryssrutan för alla objekt som du vill skydda. När alla objekt har valts klickar du på **nästa**.

    ![Öppnar guiden Ny Skyddsgrupp](./media/backup-mabs-files-applications-azure-stack/5-select-group-members.png)

    Microsoft rekommenderar att publicera alla data som ska dela protection-principen i en skyddsgrupp. Mer information om planering och distribution av skyddsgrupper finns i System Center DPM-artikeln [distribuera Skyddsgrupper](https://docs.microsoft.com/en-us/system-center/dpm/create-dpm-protection-groups?view=sc-dpm-1801).

4. I den **Välj Dataskyddsmetod** skärmen, Skriv ett namn för skyddsgruppen. Markera kryssrutan för **jag vill ha kortvarigt skydd med:** och **jag vill onlineskydd**. Klicka på **Nästa**.

    ![Öppnar guiden Ny Skyddsgrupp](./media/backup-mabs-files-applications-azure-stack/6-select-data-protection-method.png)

    Att välja **jag vill onlineskydd**, måste du först välja **jag vill ha kortvarigt skydd med:** Disk. Azure Backup-Server skyddar inte till band, så att disken är det enda alternativet för kortsiktigt skydd.

5. I den **ange kortvariga mål** väljer du hur lång tid att behålla återställningspunkter sparas på disk och när du vill spara säkerhetskopior. Klicka på **Nästa**.

    > [!IMPORTANT]
    > Du bör **inte** behålla operativa återställningsdata (säkerhetskopiering) på Azure Backup-Server-anslutna diskar för mer än fem dagar.
    >

    ![Öppnar guiden Ny Skyddsgrupp](./media/backup-mabs-files-applications-azure-stack/7-select-short-term-goals.png) 

    I stället för att välja ett intervall för inkrementell säkerhetskopiering för att köra en snabb fullständig säkerhetskopiering före varje schemalagd återställningspunkt, klickar du på **precis innan en återställningspunkt**. Om du skyddar programarbetsbelastningar skapas Azure Backup Server återställningspunkter per frekvens synkroniseringsschemat (förutsatt att programmet stöder inkrementell säkerhetskopiering). Om programmet inte stöder inkrementell säkerhetskopiering, Azure Backup-Server kör en snabb fullständig säkerhetskopiering.

    För **filen återställningspunkter**, ange när du vill skapa återställningspunkter. Klicka på **ändra** Ange dagar och tider i veckan när återställningspunkter skapas.

6. I den **granska diskallokering** kan du se lagring lagringspoolens allokerade diskutrymme för skyddsgruppen.

    **Total storlek på Data** är storleken på data som du vill säkerhetskopiera och **diskutrymme som ska etableras** på Azure Backup Server är det rekommenderade utrymmet för skyddsgruppen. Azure Backup-servern väljer perfekt säkerhetskopieringsvolymen baserat på inställningarna. Du kan dock redigera säkerhetskopieringsvolymen valen i allokering diskinformation. Välj önskade lagring för arbetsbelastningar, i den nedrullningsbara menyn. Redigeringarna ändra värdena för totalt lagringsutrymme och ledigt utrymme i fönstret tillgängliga disklagring. Underprovisioned utrymme är mängden lagringsutrymme som Azure Backup Server föreslår att du lägger till i volymen, för att fortsätta med säkerhetskopiering smidigt i framtiden.

7. I **Välj metod för skapande av replik**, Välj hur du vill hantera den första fullständiga datareplikeringen. Om du vill replikera via nätverket rekommenderar Azure du väljer en tid. Överväg att replikera data med flyttbara media för stora mängder data eller bristfälliga nätverksförhållanden.

8. I **Välj alternativ för konsekvenskontroll**, Välj hur du vill automatisera konsekvenskontroller. Aktivera konsekvenskontroller ska köras endast när datareplikering blir inkonsekvent, eller enligt ett schema. Om du inte vill konfigurera automatisk konsekvenskontroll kör du en manuell kontroll när som helst genom att:
    * I den **skydd** i Azure Backup Server-konsolen, högerklicka på skyddsgruppen och välj **utför konsekvenskontroll**.

9. Om du väljer att säkerhetskopiera till Azure på den **ange onlineskyddsdata** sidan kontrollerar du de arbetsbelastningar som du vill säkerhetskopiera till Azure är markerade.

10. I **Ange schema för onlinesäkerhetskopiering**, ange när inkrementella säkerhetskopieringar till Azure ska ske. 

    Du kan schemalägga säkerhetskopieringar att köras varje dag veckan/år-månad och den tid/datum då de ska köras. Säkerhetskopieringar kan inträffa upp till två gånger per dag. Varje gång ett säkerhetskopieringsjobb körs skapas en data-återställningspunkt i Azure från en kopia av säkerhetskopierade data lagras på disk för Azure Backup-Server.

11. I **ange bevarandeprincip**, ange hur återställningspunkter som skapats från varje dag/vecka/månad/år säkerhetskopior bevaras i Azure.

12. I **Välj onlinereplikeringsmetoden**, ange hur den första fullständiga replikeringen av data sker. 

13. På **sammanfattning**, granskar du inställningarna. När du klickar på **Skapa grupp**, den första datareplikeringen inträffar. När datareplikeringen är klar på den **Status** sidan skyddsgruppens status visas som **OK**. Inledande säkerhetskopieringsjobbet sker i enlighet med skydd gruppinställningar.

Frågor som behöver besvara: hur du för att expandera disklagring för Azure-stacken kortsiktig disklagring. Vad är riktlinjer som behöver anropas ut förklarar kortsiktig disklagring?

## <a name="recover-file-data"></a>Återställa fildata

Använda Azure Backup Server konsolen för att återställa data till den virtuella datorn.

1. I Azure Backup Server-konsolen på navigeringsfältet klickar du på **Recovery** och Sök efter de data som du vill återställa. Välj data i resultatfönstret.

2. I kalendern i avsnittet med återställningspunkter fet stil anger återställningspunkter är tillgängliga. Välj datum att återställa en återställningspunkt.

3. I den **Återställningsbart objekt** rutan, Välj ett objekt som du vill återställa.

4. I den **åtgärder** rutan klickar du på **återställa** att öppna Återställningsguiden.

5. Du kan återställa data på följande sätt:

    * **Återställ till den ursprungliga platsen** -det här alternativet fungerar inte om datorn är ansluten via VPN. I stället använda en alternativ plats och sedan kopiera data från den platsen.
    * **Återställ till en alternativ plats**

6. Ange återställningsalternativ:

    * För **återställningsfunktionssätt för befintlig version**väljer **Skapa kopia**, **hoppa över**, eller **Skriv över**. Skriv över är bara tillgänglig när återställning till den ursprungliga platsen.
    * För **Återställ säkerhet**, Välj **tillämpa inställningar på måldatorn** eller **tillämpa säkerhetsinställningar för återställningspunktversionen**.
    * För **begränsning av nätverksbandbredd**, klickar du på **ändra** att aktivera begränsning av nätverksbandbredd.
    * **Meddelande** klickar du på **skicka ett e-postmeddelande när återställningen är slutförd**, och ange vilka mottagare som tar emot meddelandet. Avgränsa e-postadresser med kommatecken.
    * När du har gjort valen klickar du på **nästa**

7. Granska återställningsinställningarna och klicka på **återställa**. 

    > [!Note] 
    > Alla Synkroniseringsjobb för det valda återställningsobjekt avbryts medan återställningsjobbet pågår.
    >

Om du använder moderna Backup Storage (MB) stöds inte återställning av slutanvändare File Server (EUR). Filen Server EUR har ett beroende på Volume Shadow Copy Service (VSS), som inte använder moderna Backup Storage. Om EUR är aktiverad, återställer du data med följande steg:

1. Navigera till de skyddade filerna och högerklicka på filen och markera **egenskaper**.

2. På den **egenskaper** -menyn klickar du på **tidigare versioner** och välj den version som du vill återställa.

## <a name="view-azure-backup-server-with-a-vault"></a>Visa Azure Backup-Server med ett valv
Om du vill visa Azure Backup Server entiteter i Azure-portalen kan du följa följande steg:
1. Öppna Recovery Services-valvet.
2. Klicka på infrastrukturen för säkerhetskopiering.
3. Visa säkerhetskopiering hanteringsservrar.

## <a name="see-also"></a>Se också
Information om hur du använder Azure Backup Server för att skydda finns andra arbetsbelastningar i följande artiklar:
- [Säkerhetskopiera SharePoint-grupp](https://docs.microsoft.com/en-us/azure/backup/backup-mabs-sharepoint-azure-stack)
- [Säkerhetskopiera SQLServer](https://docs.microsoft.com/en-us/azure/backup/backup-mabs-sql-azure-stack)
