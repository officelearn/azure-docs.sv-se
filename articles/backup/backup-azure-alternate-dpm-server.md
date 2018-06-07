---
title: Återställa data från ett Azure Backup-Server
description: Återställa data som du har skyddat till Recovery Services-valvet Azure Backup Server registrerad för att valvet.
services: backup
author: nkolli1
manager: shreeshd
ms.service: backup
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: adigan
ms.openlocfilehash: 8559532f873e8073e736f881374fec1c080d08c3
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34604411"
---
# <a name="recover-data-from-azure-backup-server"></a>Återställa data från Azure Backup Server
Du kan använda Azure Backup Server för att återställa data som du har säkerhetskopierat till Recovery Services-valvet. Processen för att göra så är integrerat i hanteringskonsolen för Azure Backup Server och liknar återställningsarbetsflöde för andra Azure Backup-komponenter.

> [!NOTE]
> Den här artikeln gäller för [System Center Data Protection Manager 2012 R2 med UR7 eller senare] (https://support.microsoft.com/en-us/kb/3065246), kombinerat med den [senaste Azure Backup-agenten](http://aka.ms/azurebackup_agent).
>
>

Återställa data från ett Azure Backup-Server:

1. Från den **Recovery** fliken i konsolen för hantering av Azure Backup Server klickar du på **lägga till extern DPM** (på upp till vänster på skärmen).   
    ![Lägg till extern DPM](./media/backup-azure-alternate-dpm-server/add-external-dpm.png)
2. Hämta nya **valvet autentiseringsuppgifter** från valvet som är associerade med den **Azure Backup Server** där data återställs välja Azure Backup-Server i listan med Azure Backup-servrar är registrerade med Recovery Services-valvet och ange den **krypteringslösenfrasen** som associeras med den server vars data återställs.

    ![Extern DPM-autentiseringsuppgifter](./media/backup-azure-alternate-dpm-server/external-dpm-credentials.png)

   > [!NOTE]
   > Endast Azure Backup-servrar som är kopplade till samma registrering valvet kan återställa varandras data.
   >
   >

    När den externa Azure Backup-servern har lagts till, kan du bläddra data för den externa servern och den lokala Azure Backup-servern från den **Recovery** fliken.
3. Bläddra tillgängliga i produktionsservrar som skyddas av den externa Azure Backup-servern och välj lämplig datakälla.

    ![Bläddra externa DPM-servern](./media/backup-azure-alternate-dpm-server/browse-external-dpm.png)
4. Välj **månad och år** från den **återställningspunkter** listrutan, Välj den nödvändiga **Recovery datum** för när återställningspunkten skapades, och välj den **återställningstid**.

    En lista över filer och mappar som visas längst ned i fönstret, som kan bläddra och återställa till valfri plats.

    ![Återställningspunkter för externa DPM-Server](./media/backup-azure-alternate-dpm-server/external-dpm-recoverypoint.png)
5. Högerklicka på objektet och klickar på **återställa**.

    ![Externa DPM-återställning](./media/backup-azure-alternate-dpm-server/recover.png)
6. Granska de **återställa markeringen**. Kontrollera data och tid för säkerhetskopian som återställs, samt källan som säkerhetskopian skapades från. Om markeringen är felaktig, klickar du på **Avbryt** att gå tillbaka till återställningsfliken att välja lämplig återställningspunkt. Om markeringen är korrekt, klickar du på **nästa**.

    ![Externa DPM-återställning sammanfattning](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-summary.png)
7. Välj **Återställ till en alternativ plats**. **Bläddra** till rätt plats för återställningen.

    ![Extern DPM alternativ återställningsplats](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-alternate-location.png)
8. Välj det alternativ som rör **Skapa kopia**, **hoppa över**, eller **Skriv över**.

   * **Skapa kopia** -skapar en kopia av filen om det finns en namnkollision.
   * **Hoppa över** – om det finns en namnkollision kan inte återställa filen som lämnar den ursprungliga filen.
   * **Skriv över** – om det finns en namnkollision skriver över den befintliga kopian av filen.

     Välj lämpligt alternativ till **Återställ säkerhet**. Du kan tillämpa säkerhetsinställningar på måldatorn där data återställs eller säkerhetsinställningar som var gäller för produkt för när återställningspunkten skapades.

     Identifiera om en **meddelande** skickas när återställningen har slutförts.

     ![Extern DPM Recovery meddelanden](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-notifications.png)
9. Den **sammanfattning** skärmen visar de alternativ som är valt hittills. När du klickar på **”återställa”**, data återställs till lämplig lokal plats.

    ![Extern DPM Recovery alternativ sammanfattning](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-options-summary.png)

   > [!NOTE]
   > Återställningsjobbet kan övervakas i den **övervakning** fliken i Azure Backup-Server.
   >
   >

    ![Övervaka återställning](./media/backup-azure-alternate-dpm-server/monitoring-recovery.png)
10. Du kan klicka på **Rensa extern DPM** på den **Recovery** för DPM-servern att ta bort vyn i den externa DPM-servern.

    ![Rensa extern DPM](./media/backup-azure-alternate-dpm-server/clear-external-dpm.png)

## <a name="troubleshooting-error-messages"></a>Felsökning av felmeddelanden
| Nej. | Felmeddelande | Felsökningsanvisningar |
|:---:|:--- |:--- |
| 1. |Den här servern är inte registrerad för valvet som är angivet av valvautentiseringen. |**Orsak:** det här felet uppstår när valvautentiseringsfilen som valts inte tillhör Recovery Services-valvet som är associerade med Azure Backup-Server där återställningen utförs. <br> **Lösning:** hämta valvautentiseringsfilen från Recovery Services-valvet till Azure Backup-Server är registrerad. |
| 2. |Återställningsbara data är inte tillgänglig eller den valda servern är inte en DPM-server. |**Orsak:** det finns inga andra Azure Backup-servrar registrerade Recovery Services-valvet servrar har ännu inte överföra metadata eller den valda servern är inte en Azure Backup Server (även kallat Windows Server eller Windows-klient). <br> **Lösning:** om det finns andra Azure Backup-servrar registrerade Recovery Services-valvet, kontrollera att den senaste Azure Backup-agenten är installerad. <br>Om det finns andra Azure Backup-servrar registrerade Recovery Services-valv, vänta en dag efter installationen för att starta återställningsprocessen. Nattetid kommer att överföra metadata för alla skyddade säkerhetskopieringar till molnet. Data blir tillgängliga för återställning. |
| 3. |Ingen annan DPM-server är registrerad för valvet. |**Orsak:** inga andra Azure Backup-servrar som är registrerade för valvet som återställningen görs.<br>**Lösning:** om det finns andra Azure Backup-servrar registrerade Recovery Services-valvet, kontrollera att den senaste Azure Backup-agenten är installerad.<br>Om det finns andra Azure Backup-servrar registrerade Recovery Services-valv, vänta en dag efter installationen för att starta återställningsprocessen. Nattetid filöverföringar metadata för alla skyddade säkerhetskopieringar till molnet. Data blir tillgängliga för återställning. |
| 4. |Den angivna krypteringslösenfrasen matchar inte lösenfrasen som associeras med följande server: **<server name>** |**Orsak:** används håller på att kryptera data från Azure Backup Server-data som återställs krypteringslösenfrasen matchar inte den angivna krypteringslösenfrasen. Agenten är det gick inte att dekryptera data. Därför misslyckas återställningen.<br>**Lösning:** ange exakt samma krypteringslösenfrasen som är associerade med Azure Backup-Server som data återställs. |

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="why-cant-i-add-an-external-dpm-server-after-installing-ur7-and-latest-azure-backup-agent"></a>Varför kan jag lägga till en extern DPM-server när du har installerat UR7 och senaste Azure Backup-agenten?

För DPM-servrar med datakällor som skyddas till molnet (med hjälp av en samlad uppdatering tidigare än Update Rollup 7) måste du vänta minst en dag efter installation av UR7 och senaste Azure Backup-agenten för att starta **lägga till extern DPM-servern**. En dag tidsperiod behövs för att överföra metadata för DPM-skyddsgrupper till Azure. Skydd grupp metadata har laddats upp första gången via ett jobb varje natt.

### <a name="what-is-the-minimum-version-of-the-microsoft-azure-recovery-services-agent-needed"></a>Vad är den lägsta versionen av Microsoft Azure Recovery Services-agenten behövs?

Den lägsta versionen av Microsoft Azure Recovery Services-agenten eller Azure Backup-agenten som krävs för att aktivera den här funktionen är 2.0.8719.0.  Så här visar agentens version: Öppna Kontrollpanelen **>** alla objekt **>** program och funktioner **>** Microsoft Azure Recovery Services-agenten. Om versionen som är mindre än 2.0.8719.0, hämtar och installerar den [senaste Azure Backup-agenten](https://go.microsoft.com/fwLink/?LinkID=288905).

![Rensa extern DPM](./media/backup-azure-alternate-dpm-server/external-dpm-azurebackupagentversion.png)

## <a name="next-steps"></a>Nästa steg:
• [Azure Backup vanliga frågor och svar](backup-azure-backup-faq.md)
