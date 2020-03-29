---
title: Uppgradera MARS-agenten (Microsoft Azure Recovery Services)
description: Lär dig hur du uppgraderar MARS-agenten (Microsoft Azure Recovery Services).
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: 49daf438b855d19961519d93b6c3ec535de4756f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672908"
---
# <a name="upgrade-the-microsoft-azure-recovery-services-mars-agent"></a>Uppgradera MARS-agenten (Microsoft Azure Recovery Services)

I den här artikeln får du lära dig hur du:

* Identifiera servrar med tidigare versioner av MARS-agenten
* Uppdatera MARS-installationer på dessa servrar

## <a name="identify-servers-with-earlier-versions-of-the-mars-agent"></a>Identifiera servrar med tidigare versioner av MARS-agenten

För installationer av Azure Backup-agent och Azure backup-server:

1. Navigera till Recovery Services Vault där du har registrerade servrar som eventuellt säkerhetskopieras av äldre versioner av agenten. Du hittar en representativ lista över valv med äldre Azure Backup-agenter i Azure Backup Update-aviseringar från Azure.
1. Välj Infrastruktur **för** **säkerhetskopiering under** avsnittet Hantera i avsnittet **Inställningar** på vänster sida i Recovery Services Vault.
1. Om du vill identifiera Azure-säkerhetskopieringsagenter som installerats som en del av Azure Backup-serverinstallationer går du till servrar för **säkerhetskopieringshantering** under **Hanteringsservrar**. Detta kommer att lista de servrar som har Azure Backup serverinstallationer tillsammans med versionsnumret för den associerade Azure Backup agent.

    ![Lista över MARS-agenter som installerats som en del av Azure Backup-serverinstallationer](./media/upgrade-mars-agent/backup-management-servers.png)

1. Om du vill kontrollera agentversioner för MARS-agentinstallationer (Microsoft Azure Recovery Services) eller Azure Backup-agenten går du till Skyddade servrar under **Hanteringsservrar** . **Management Servers** Välj sedan **Azure Backup agent** under Säkerhetskopieringshanteringstyp. Detta kommer att lista de servrar som har Azure Backup agent installationer tillsammans med versionsnumret för installationen.

    ![Lista över servrar med MARS-agenten installerad](./media/upgrade-mars-agent/protected-servers.png)

1. Sortera versionskolumnen för Azure Backup Agent genom att klicka på kolumnen **Agentversion** för MARS-agentinstallationer eller kolumnen **Azure Backup Agent Version** för Azure-säkerhetskopieringsserverinstallationer.

1. Det föregående steget ger dig listan över servrar med Azure Backup-agenter som har versioner som är lägre än 2.0.9083.0 eller agentversioner som anges som tomma. Dessa är de servrar där Azure Backup agenter måste uppdateras.

## <a name="update-the-mars-agent-installation-on-the-server"></a>Uppdatera INSTALLATIONEN AV MARS-agenten på servern

När du har identifierat servrarna som behöver en Azure Backup-agentuppdatering utför du följande steg för varje identifierad server (med hjälp av Azure-säkerhetskopieringsserver eller MARS-agenten). [Hämta den senaste versionen av Azure backup agent](https://aka.ms/azurebackup_agent) innan du följer stegen nedan.

1. Klicka på en rad som har Azure Backup-agent lägre än 2.0.9083.0 eller tom. Då öppnas serverinformationsskärmen.

    ![Skyddade servrar med inaktuella agentversioner](./media/upgrade-mars-agent/old-agent-version.png)

    ![Azure-säkerhetskopieringsservrar med inaktuella agentversioner](./media/upgrade-mars-agent/backup-management-servers-old-versions.png)

1. Klicka på **Anslut** för att ta emot en anslutningsfil för fjärrskrivbord för att ansluta till servern eller ansluta direkt till servern via fjärrskrivbordsanslutningen på servern.

    ![Ansluta till servern via fjärrskrivbordsanslutning](./media/upgrade-mars-agent/connect-to-server.png)

    >[!NOTE]
    > Om den angivna servern inte finns eller har inaktiverats kan du ignorera de återstående stegen nedan och hoppa till nästa server.

1. Ange dina administrativa inloggningsuppgifter och logga in.

1. Om serverns eller serverns proxy har begränsad internetåtkomst kontrollerar du att brandväggsinställningarna på servern/proxyn är konfigurerade så att url:en som är lämplig för det Azure-moln som du använder:

    Azure-molnet | URL
    -- | ---
    Azure-molnet (offentligt) |   `https://login.windows.net`
    Azure China 21Vianet Cloud   | `https://login.chinacloudapi.cn`
    Azure US Government Cloud |   `https://login.microsoftonline.us`
    Azure tyska molnet  |  `https://login.microsoftonline.de`

1. Kopiera installationsprogrammet för azure-säkerhetskopieringsagenten till servern.

    ![Kopiera installationsprogrammet för uppdatering av Azure-säkerhetskopiera agent till servern](./media/upgrade-mars-agent/copy-agent-installer.png)

1. Kör installationsprogrammet. Uppgraderingsguiden för Microsoft Azure Recovery Services Agent öppnas.

    ![Uppgraderingsguiden för Microsoft Azure Recovery Services Agent](./media/upgrade-mars-agent/agent-upgrade-wizard.png)

1. Klicka på **Nästa**.

1. Klicka på **Uppgradera**.

    ![Installation av Microsoft Azure Recovery Services Agent](./media/upgrade-mars-agent/upgrade-installation.png)

1. Den slutliga bekräftelseskärmen anger att Azure Backup-agenten har uppdaterats.

## <a name="for-system-center-data-protection-manager-sc-dpm-customers"></a>För SC DPM-kunder (System Center Data Protection Manager)

Om du har installerat Azure-säkerhetskopieringsagenter på SC DPM-servrarna (System Center Data Protection Manager) måste du följa stegen nedan för att identifiera om dina DPM-servrar behöver en uppdatering av Azure-säkerhetskopieringsagenten:

1. Logga in på SC DPM-servern som administratör.
2. Öppna DPM-konsolen.
3. Klicka på **Hantering** i konsolens nedre vänstra navigering.
4. Leta efter versionsinformationen för Azure Backup agent-versionen i den vänstra navigeringen.
5. Om versionen är lägre än 2.0.9083.0 laddar du ned den senaste Azure-installationsprogrammet för säkerhetskopieringsagenten och kör installationsprogrammet på DPM-servern för att uppdatera Azure Backup-agenten.

Upprepa stegen ovan för alla DPM-servrar i din miljö.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [säkerhetskopierar Windows-datorer med hjälp av Azure Backup MARS-agenten](backup-windows-with-mars-agent.md)
