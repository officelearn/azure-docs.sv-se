---
title: Återställa data från en Azure Backup Server
description: Återställa data som du har skyddat till ett Recovery Services-valv från alla Azure Backup Server som är registrerade i valvet.
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: 2a89697899fc244848854978de4b25e79ef6f184
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74173507"
---
# <a name="recover-data-from-azure-backup-server"></a>Återställa data från Azure Backup Server

Du kan använda Azure Backup Server för att återställa data som du har säkerhetskopierat till ett Recovery Services-valv. Processen för att göra detta är integrerad i Azure Backup Server management console och liknar återställningsarbetsflödet för andra Azure Backup-komponenter.

> [!NOTE]
> Den här artikeln gäller för [System Center Data Protection Manager 2012 R2 med UR7 eller senare](https://support.microsoft.com/kb/3065246), kombinerat med den [senaste Azure Backup-agenten](https://aka.ms/azurebackup_agent).
>
>

Så här återställer du data från en Azure Backup Server:

1. Klicka på Lägg till extern DPM längst upp till vänster på skärmen på fliken **Återställning** i **hanteringskonsolen** för Azure Backup Server).

    ![Lägg till extern DPM](./media/backup-azure-alternate-dpm-server/add-external-dpm.png)
2. Hämta nya **valvautentiseringsuppgifter** från valvet som är associerat med **Azure Backup Server** där data återställs, välj Azure Backup Server från listan över Azure Backup Servers som är registrerade med Recovery Services-valvet och ange **krypteringslösenfrasen** som är associerad med servern vars data återställs.

    ![Externa DPM-autentiseringsuppgifter](./media/backup-azure-alternate-dpm-server/external-dpm-credentials.png)

   > [!NOTE]
   > Endast Azure Backup-servrar som är associerade med samma registreringsvalv kan återställa varandras data.
   >
   >

    När den externa Azure Backup Server har lagts till kan du bläddra bland data från den externa servern och den lokala Azure Backup Server från fliken **Återställning.**
3. Bläddra i den tillgängliga listan över produktionsservrar som skyddas av den externa Azure Backup Server och välj lämplig datakälla.

    ![Bläddra i extern DPM-server](./media/backup-azure-alternate-dpm-server/browse-external-dpm.png)
4. Välj **månad och år** i listrutan **Återställningspunkter,** välj önskat **återställningsdatum** för när återställningspunkten skapades och välj **återställningstiden**.

    En lista med filer och mappar visas i det nedre fönstret, som kan bläddras och återställas till valfri plats.

    ![Återställningspunkter för extern DPM-server](./media/backup-azure-alternate-dpm-server/external-dpm-recoverypoint.png)
5. Högerklicka på rätt objekt och klicka på **Återställ**.

    ![Extern DPM-återställning](./media/backup-azure-alternate-dpm-server/recover.png)
6. Granska **återställningsmarkeringen**. Kontrollera data och tid för säkerhetskopian som återställs, samt källan från vilken säkerhetskopian skapades. Om markeringen är felaktig klickar du på **Avbryt** om du vill navigera tillbaka till återställningsfliken för att välja lämplig återställningspunkt. Om markeringen är korrekt klickar du på **Nästa**.

    ![Sammanfattning av extern DPM-återställning](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-summary.png)
7. Välj **Återställ till en alternativ plats**. **Bläddra** till rätt plats för återställningen.

    ![Alternativ plats för extern DPM-återställning](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-alternate-location.png)
8. Välj alternativet som är relaterat för att **skapa kopia,** **Hoppa över**eller Skriva **över**.

   * **Skapa kopia** - skapar en kopia av filen om det finns en namnkollision.
   * **Hoppa över** - om det finns en namnkollision, återställer inte filen, vilket lämnar originalfilen.
   * **Skriv över** - om det finns en namnkollision, skriver över den befintliga kopian av filen.

     Välj lämpligt alternativ för att **återställa säkerheten**. Du kan använda säkerhetsinställningarna för måldatorn där data återställs eller de säkerhetsinställningar som gällde för produkten när återställningspunkten skapades.

     Identifiera om ett **meddelande** har skickats när återställningen har slutförts.

     ![Externa DPM-återställningsmeddelanden](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-notifications.png)
9. På **skärmen Sammanfattning** visas de alternativ som hittills valts. När du klickar på **"Återställ"** återställs data till lämplig lokal plats.

    ![Sammanfattning av externa DPM-återställningsalternativ](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-options-summary.png)

   > [!NOTE]
   > Återställningsjobbet kan övervakas på fliken **Övervakning** i Azure Backup Server.
   >
   >

    ![Övervakning av återhämtning](./media/backup-azure-alternate-dpm-server/monitoring-recovery.png)
10. Du kan klicka på **Rensa extern DPM** på fliken **Återställning** på DPM-servern om du vill ta bort vyn för den externa DPM-servern.

    ![Rensa extern DPM](./media/backup-azure-alternate-dpm-server/clear-external-dpm.png)

## <a name="troubleshooting-error-messages"></a>Felsöka felmeddelanden

| Nej. | Felmeddelande | Felsökningsanvisningar |
|:---:|:--- |:--- |
| 1. |Den här servern är inte registrerad i valvet som anges av valvautentiseringsuppgifterna. |**Orsak:** Det här felet visas när den valda valvautentiseringsfilen inte tillhör recovery services-valvet som är associerat med Azure Backup Server där återställningen försöker. <br> **Upplösning:** Hämta arkivautentiseringsfilen från Recovery Services-valvet som Azure Backup Server är registrerad på. |
| 2. |Antingen är återställningsbara data inte tillgängliga eller så är den valda servern inte en DPM-server. |**Orsak:** Det finns inga andra Azure Backup-servrar som är registrerade i Recovery Services-valvet, eller så har servrarna ännu inte laddat upp metadata, eller så är den valda servern inte en Azure Backup Server (med Windows Server eller Windows Client). <br> **Upplösning:** Om det finns andra Azure Backup-servrar som är registrerade i Recovery Services-valvet kontrollerar du att den senaste Azure Backup-agenten är installerad. <br>Om det finns andra Azure Backup-servrar registrerade i Recovery Services-valvet väntar du en dag efter installationen för att starta återställningsprocessen. Det nattliga jobbet kommer att ladda upp metadata för alla skyddade säkerhetskopior till molnet. Uppgifterna kommer att vara tillgängliga för återställning. |
| 3. |Ingen annan DPM-server är registrerad i det här valvet. |**Orsak:** Det finns inga andra Azure Backup-servrar som är registrerade i valvet som återställningen försöker sig på.<br>**Upplösning:** Om det finns andra Azure Backup-servrar som är registrerade i Recovery Services-valvet kontrollerar du att den senaste Azure Backup-agenten är installerad.<br>Om det finns andra Azure Backup-servrar registrerade i Recovery Services-valvet väntar du en dag efter installationen för att starta återställningsprocessen. Det nattliga jobbet överför metadata för alla skyddade säkerhetskopior till molnet. Uppgifterna kommer att vara tillgängliga för återställning. |
| 4. |Den angivna krypteringslösenfrasen matchar inte lösenfrasen som är associerad med följande server: ** \<servernamn>** |**Orsak:** Krypteringslösenfrasen som används i processen för att kryptera data från Azure Backup Server data som återställs matchar inte krypteringslösenfrasen som tillhandahålls. Agenten kan inte dekryptera data. Därför misslyckas återhämtningen.<br>**Upplösning:** Ange exakt samma krypteringslösenfras som är associerad med Azure Backup Server vars data återställs. |

## <a name="next-steps"></a>Nästa steg

Läs andra vanliga frågor och svar:

* [Vanliga frågor](backup-azure-vm-backup-faq.md) om säkerhetskopiering av Virtuella Azure-datorer
* [Vanliga frågor](backup-azure-file-folder-backup-faq.md) om Azure Backup-agenten
