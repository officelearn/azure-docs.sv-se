---
title: Återställa data från en Azure Backup Server
description: Återställer du data som du har skyddat till ett Recovery Services-valv från alla Azure Backup Server som registrerats som valvet.
services: backup
author: kasinh
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: kasinh
ms.openlocfilehash: d1fb3434f0d3954a07980963866bcd7cce004379
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60650937"
---
# <a name="recover-data-from-azure-backup-server"></a>Återställa data från Azure Backup Server
Du kan använda Azure Backup Server för att återställa data som du har säkerhetskopierat till ett Recovery Services-valv. Processen för att göra så är integrerad i Azure Backup Server-hanteringskonsolen och liknar återställningsarbetsflöde för andra Azure Backup-komponenter.

> [!NOTE]
> Den här artikeln gäller för [System Center Data Protection Manager 2012 R2 med UR7 eller senare](https://support.microsoft.com/en-us/kb/3065246), kombinerat med den [senaste Azure Backup-agenten](https://aka.ms/azurebackup_agent).
>
>

Återställa data från en Azure Backup Server:

1. Från den **Recovery** fliken i konsolen för hantering av Azure Backup Server klickar du på **Lägg till extern DPM** (på upp till vänster på skärmen).   
    ![Lägg till extern DPM](./media/backup-azure-alternate-dpm-server/add-external-dpm.png)
2. Ladda ned nya **valv autentiseringsuppgifter** från valvet som är associerade med den **Azure Backup Server** där data återställs kan välja Azure Backup Server i listan med Azure Backup-servrar har registrerats med Recovery Services-valv och ange den **krypteringslösenfrasen** som är associerade med den server vars data återställs.

    ![Externa DPM-autentiseringsuppgifter](./media/backup-azure-alternate-dpm-server/external-dpm-credentials.png)

   > [!NOTE]
   > Endast Azure Backup-servrar som är associerade med samma registrering valv kan återställa varandras data.
   >
   >

    När den externa Azure Backup Server har lagts till genom du söka data i den externa servern och den lokala Azure Backup-servern från den **Recovery** fliken.
3. Bläddra i listan över produktionsservrar som skyddas av den externa Azure Backup Server och välj lämplig datakälla.

    ![Bläddra externa DPM-servern](./media/backup-azure-alternate-dpm-server/browse-external-dpm.png)
4. Välj **månaden och året** från den **återställningspunkter** nedrullningsbar listruta, Välj de nödvändiga **återställningsdatum** för när återställningspunkten skapades och väljer **Återställningstid**.

    En lista över filer och mappar som visas längst ned i fönstret, vilket kan söks igenom och återställas till valfri plats.

    ![Återställningspunkter för externa DPM-Server](./media/backup-azure-alternate-dpm-server/external-dpm-recoverypoint.png)
5. Högerklicka på objektet och klicka på **återställa**.

    ![Externa DPM-återställning](./media/backup-azure-alternate-dpm-server/recover.png)
6. Granska den **återställa val av**. Kontrollera data och tid för säkerhetskopian som återställs, samt källan från vilken säkerhetskopian skapades. Om markeringen är felaktig, klickar du på **Avbryt** att gå tillbaka till återställningsfliken att välja lämpliga återställningspunkt. Om markeringen är korrekt, klickar du på **nästa**.

    ![Externa DPM-återställning sammanfattning](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-summary.png)
7. Välj **Återställ till en alternativ plats**. **Bläddra** på rätt plats för återställningen.

    ![Extern DPM alternativ återställningsplats](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-alternate-location.png)
8. Välj det alternativ som rör **Skapa kopia**, **hoppa över**, eller **överskrivning**.

   * **Skapa kopia** -skapar en kopia av filen om det finns en.
   * **Hoppa över** – om det finns en namn-kollision kan inte återställa filen som lämnar den ursprungliga filen.
   * **Skriv över** – om det finns en namn-kollision skriver över den befintliga kopian av filen.

     Välj lämpligt alternativ till **Återställ säkerhet**. Du kan tillämpa säkerhetsinställningar på måldatorn där data återställs eller säkerhetsinställningar som gäller för produkten när återställningspunkten skapades.

     Identifiera om en **meddelande** skickas när återställningen har slutförts.

     ![Extern DPM Recovery meddelanden](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-notifications.png)
9. Den **sammanfattning** skärmen visar de alternativ som valts hittills. När du klickar på **”återställa”**, återställs datan till lämpliga lokala plats.

    ![Extern DPM Recovery alternativ sammanfattning](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-options-summary.png)

   > [!NOTE]
   > Återställningsjobbet kan övervakas i den **övervakning** fliken av Azure Backup Server.
   >
   >

    ![Övervakning av återställning](./media/backup-azure-alternate-dpm-server/monitoring-recovery.png)
10. Du kan klicka på **Rensa extern DPM** på den **Recovery** fliken DPM-servern att ta bort vyn för den externa DPM-servern.

    ![Rensa extern DPM](./media/backup-azure-alternate-dpm-server/clear-external-dpm.png)

## <a name="troubleshooting-error-messages"></a>Felsöka felmeddelanden
| Nej. | Felmeddelande | Felsökningsanvisningar |
|:---:|:--- |:--- |
| 1. |Den här servern är inte registrerad för valvet som är angivet av valvautentiseringen. |**Orsak:** Det här felet visas när valvautentiseringsfilen valt inte hör till Recovery Services-valvet som är associerade med Azure Backup Server där återställningen utförs. <br> **Lösning:** Hämta valvautentiseringsfilen från Recovery Services-valvet som Azure Backup Server har registrerats. |
| 2. |Antingen är återställningsbara data är inte tillgänglig eller den valda servern är inte en DPM-server. |**Orsak:** Det finns inga andra Azure Backup-servrar har registrerats till Recovery Services-valv eller servrar har ännu inte har överfört metadata eller den valda servern är inte en Azure Backup Server (även kallat Windows Server eller Windows-klient). <br> **Lösning:** Om det finns andra Azure Backup-servrar har registrerats Recovery Services-valvet kan du kontrollera att den senaste Azure Backup-agenten är installerad. <br>Om det finns andra Azure Backup-servrar har registrerats Recovery Services-valvet, vänta på en dag efter installationen för att starta återställningsprocessen. Nattetid kommer att överföra metadata för alla skyddade säkerhetskopior till molnet. Data blir tillgängliga för återställning. |
| 3. |Ingen annan DPM-server är registrerad i valvet. |**Orsak:** Det finns inga andra Azure Backup-servrar som är registrerade för valvet som återställningen görs.<br>**Lösning:** Om det finns andra Azure Backup-servrar har registrerats Recovery Services-valvet kan du kontrollera att den senaste Azure Backup-agenten är installerad.<br>Om det finns andra Azure Backup-servrar har registrerats Recovery Services-valvet, vänta på en dag efter installationen för att starta återställningsprocessen. Nattetid Överför metadata för alla skyddade säkerhetskopior till molnet. Data blir tillgängliga för återställning. |
| 4. |Den angivna krypteringslösenfrasen matchar inte lösenfrasen som associeras med följande server:  **\<servernamn >** |**Orsak:** Den angivna krypteringslösenfrasen matchar inte i den krypterade lösenfrasen som används vid kryptering av data från Azure Backup Server-data som återställs. Agenten kan inte dekryptera data. Därför misslyckas återställningen.<br>**Lösning:** Ange exakt samma krypteringslösenfrasen som är associerade med Azure Backup Server vars data återställs. |

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="why-cant-i-add-an-external-dpm-server-after-installing-ur7-and-latest-azure-backup-agent"></a>Varför kan jag lägga till en extern DPM-server när du har installerat UR7 och senaste Azure Backup-agenten?

För DPM-servrar med datakällor som skyddas till molnet (med hjälp av en samlad uppdatering tidigare än Update Rollup-7) måste du vänta minst en dag efter att ha installerat UR7 och senaste Azure Backup-agenten för att starta **Lägg till extern DPM server**. Endagsevenemang tidsperiod behövs för att ladda upp metadata för DPM-skyddsgrupper till Azure. Skydd grupp metadata har laddats upp första gången via ett jobb varje natt.

### <a name="what-is-the-minimum-version-of-the-microsoft-azure-recovery-services-agent-needed"></a>Vad är den lägsta versionen av Microsoft Azure Recovery Services-agenten som behövs?

Den lägsta versionen av Microsoft Azure Recovery Services-agenten eller Azure Backup-agenten som krävs för att aktivera den här funktionen är 2.0.8719.0.  Visa agentens version: Öppna Kontrollpanelen **>** alla Kontrollpanelen objekt **>** program och funktioner **>** Microsoft Azure Recovery Services-agenten. Om versionsnumret är mindre än 2.0.8719.0, hämta och installera den [senaste Azure Backup-agenten](https://go.microsoft.com/fwLink/?LinkID=288905).

![Rensa extern DPM](./media/backup-azure-alternate-dpm-server/external-dpm-azurebackupagentversion.png)

## <a name="next-steps"></a>Nästa steg:
• [Azure Backup vanliga frågor och svar](backup-azure-backup-faq.md)
