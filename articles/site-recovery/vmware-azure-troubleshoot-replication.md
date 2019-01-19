---
title: Felsöka problem med replikering för akutåterställning av virtuella VMware-datorer och fysiska servrar till Azure med hjälp av Azure Site Recovery | Microsoft Docs
description: Den här artikeln innehåller information om att felsöka vanliga replikeringsproblem under haveriberedskap för virtuella VMware-datorer och fysiska servrar till Azure med hjälp av Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 01/18/2019
ms.author: ramamill
ms.openlocfilehash: 5c2d33b39614ded95ac38e07c844b0a8cafa7cd2
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2019
ms.locfileid: "54411483"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>Felsöka problem med replikering för virtuella VMware-datorer och fysiska servrar

Du kan se ett specifikt felmeddelande när du skyddar dina virtuella VMware-datorer eller fysiska servrar med hjälp av Azure Site Recovery. Den här artikeln beskriver några vanliga problem som kan uppstå när du replikerar lokala virtuella VMware-datorer och fysiska servrar till Azure med hjälp av [Site Recovery](site-recovery-overview.md).

## <a name="initial-replication-issues"></a>Problem med inledande replikering

Inledande replikeringsfel orsakas ofta av problem med nätverksanslutningen mellan käll- och processervern eller mellan processervern och Azure. Du kan felsöka de här problemen genom att följa stegen i följande avsnitt i de flesta fall.

### <a name="check-the-source-machine"></a>Kontrollera källdatorn

I följande lista visas hur du kan kontrollera källdatorn:

*  Använda Telnet på kommandoraden på källservern, pinga processervern via HTTPS-porten (HTTPS-standardporten är 9443) genom att köra följande kommando. Kommandot kontrollerar för problem med nätverksanslutningen och problem som blockerar brandväggsporten.


   `telnet <process server IP address> <port>`


   > [!NOTE]
   > Använda Telnet för att testa anslutningen. Använd inte `ping`. Om Telnet inte är installerad, slutför du stegen i [installera Telnet-klienten](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx).

   Om du inte kan ansluta till processervern, kan du inkommande port 9443 på processervern. Exempel: du kan behöva tillåta inkommande port 9443 på processervern om nätverket har ett perimeternätverk eller avskärmat undernät. Kontrollera sedan om problemet kvarstår.

*  Kontrollera status för den **InMage Scout VX Agent – Sentinel/OutpostStart** service. Om tjänsten inte körs måste starta tjänsten och kontrollera sedan om problemet kvarstår.   

### <a name="check-the-process-server"></a>Kontrollera processervern

I följande lista visas hur du kan kontrollera processervern:

*  **Kontrollera om processervern aktivt skicka data till Azure**.

   1. Öppna Aktivitetshanteraren (tryck på Ctrl + Skift + Esc) på processervern.
   2. Välj den **prestanda** fliken och välj sedan den **öppna Resursövervakaren** länk. 
   3. På den **Resursövervakaren** väljer den **nätverk** fliken. Under **processer med nätverksaktivitet**, kontrollera om **cbengine.exe** aktivt skickar stora mängder data.

        ![Skärmbild som visar volymerna under processer med nätverksaktivitet](./media/vmware-azure-troubleshoot-replication/cbengine.png)

   Om cbengine.exe inte skickar stora mängder data kan du slutföra stegen i följande avsnitt.

*  **Kontrollera om processervern kan ansluta till Azure Blob storage**.

   Välj **cbengine.exe**. Under **TCP-anslutningar**, kontrollera om det finns en anslutning från processervern till Azure-bloggen lagrings-URL.

   ![Skärmbild som visar anslutning mellan cbengine.exe och URL: en för Azure Blob storage](./media/vmware-azure-troubleshoot-replication/rmonitor.png)

   Om det finns ingen nätverksanslutning från processervern till Azure-bloggen storage URL på Kontrollpanelen, välja **Services**. Kontrollera om följande tjänster körs:

   *  cxprocessserver
   *  InMage Scout VX Agent – Sentinel/Outpost
   *  Microsoft Azure Recovery Services-agent
   *  Microsoft Azure Site Recovery-tjänsten
   *  tmansvc

   Starta eller starta om alla tjänster som inte körs. Kontrollera om problemet kvarstår.

*  **Kontrollera om processervern kan ansluta till Azure offentlig IP-adress via port 443**.

   Öppna filen senaste CBEngineCurr.errlog i %programfiles%\Microsoft Azure Recovery Services Agent\Temp. I filen, söka efter **443** eller efter strängen **anslutningsförsöket misslyckades**.

   ![Skärmbild som visar felet loggas i Temp-mappen](./media/vmware-azure-troubleshoot-replication/logdetails1.png)

   Om problem visas på kommandoraden på processervern, kan du använda Telnet för att pinga din Azure offentlig IP-adress (IP-adressen maskeras i föregående bild). Du kan hitta din Azure offentlig IP-adress i filen CBEngineCurr.currLog via port 443:

   `telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443`

   Om du inte kan ansluta, kontrollera om Åtkomstproblemet på grund av brandväggen eller proxyservern inställningar enligt beskrivningen i nästa steg.

*  **Kontrollera om IP-adressbaserade brandväggsregler på processervern blockerar åtkomst**.

   Om du använder IP-adressbaserade brandväggsregler på servern kan hämta den fullständiga listan med [Microsoft Azure datacenter IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653). Lägg till IP-adressintervall i brandväggskonfigurationen av för att se till att brandväggen tillåter kommunikation till Azure (och HTTPS-standardporten, 443). Tillåt IP-adressintervall för Azure-regionen för din prenumeration och Azure regionen västra USA (används för access control och Identitetshantering).

*  **Kontrollera om en URL-baserad brandvägg på processervern blockerar åtkomst**.

   Om du använder en URL-baserad brandväggsregel på servern, lägger du till URL: er i följande tabell för att brandväggskonfigurationen:

[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  

*  **Kontrollera om proxyinställningar på processervern blockerar åtkomst**.

   Om du använder en proxyserver, se till att namnet på proxyservern har åtgärdats av DNS-servern. Du kan kontrollera det värde som du angav när du ställer in konfigurationsservern, välja registernyckeln **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure plats Recovery\ProxySettings**.

   Kontrollera sedan att samma inställningar som används av Azure Site Recovery-agenten för att skicka data: 
      
   1. Sök efter **Microsoft Azure Backup**. 
   2. Öppna **Microsoft Azure Backup**, och välj sedan **åtgärd** > **ändra egenskaper för**. 
   3. På den **proxykonfiguration** fliken bör du se Proxyadressen. Proxyadressen måste vara densamma som den Proxyadressen som visas i registerinställningarna. Annars kan du ändra den till samma adress.

*  **Kontrollera om bandbredden som begränsning är begränsad på processervern**.

   Öka bandbredden och kontrollera om problemet kvarstår.

## <a name="source-machine-isnt-listed-in-the-azure-portal"></a>Källdatorn visas inte i Azure portal

När du försöker markera källdatorn för att aktivera replikering med Site Recovery kanske datorn inte är tillgängliga för en av följande orsaker:

* **Två virtuella datorer med samma instans UUID**: Om två virtuella datorer under vCenter har samma instans UUID, visas den första virtuella datorn som identifierats av konfigurationsservern på Azure-portalen. Se till att inga två virtuella datorer har samma instans UUID för att lösa problemet. Det här scenariot är vanligt i instanser där en säkerhetskopierade virtuell dator blir aktiv och är inloggad i vår identifieringsposter. Referera till [Azure Site Recovery VMware till Azure: Hur du rensar dubbletter eller inaktuella poster](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx) att lösa.
* **Felaktig vCenter användarautentiseringsuppgifter**: Se till att du har lagt till korrekt vCenter-autentiseringsuppgifter när du ställer in konfigurationsservern med hjälp av OVF-mall eller en enhetlig installation. För att kontrollera de autentiseringsuppgifter som du har lagt till under installationen, se [ändra autentiseringsuppgifter för automatisk identifiering](vmware-azure-manage-configuration-server.md#modify-credentials-for-automatic-discovery).
* **inte tillräcklig behörighet för vCenter**: Om de behörigheter som finns att få åtkomst till vCenter inte har behörigheterna som krävs kan kan identifiera virtuella datorer misslyckas. Se till att behörigheterna som beskrivs i [förbereda ett konto för automatisk identifiering](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) läggs till vCenter-användarkonto.
* **Azure Site Recovery-hanteringsservrar**: Om den virtuella datorn används som hanteringsserver under en eller flera av följande roller - Configuration server /scale-out processervern / Master-målserver, och du inte kommer att kunna välja den virtuella datorn från portalen. Management-servrar kan inte replikeras.
* **Redan skyddad/redundansväxlats via Azure Site Recovery services**: Om den virtuella datorn är redan skyddad eller har redundansväxlats genom Site Recovery, är den virtuella datorn inte kan väljas för skydd i portalen. Se till att den virtuella datorn som du söker i portalen inte redan skyddas av en annan användare eller en annan prenumeration.
* **vCenter som inte är anslutna**: Kontrollera att vCenter är i anslutet tillstånd. Du kan kontrollera genom att gå till Recovery Services-valv > Site Recovery-infrastruktur > Konfigurationsservrar > Klicka på respektive konfigurationsservern > öppnas ett blad på din direkt med information om associerade servrar. Kontrollera om vCenter är ansluten. Om tillståndet ”inte ansluten”, Lös problemet och sedan [uppdatera konfigurationsservern](vmware-azure-manage-configuration-server.md#refresh-configuration-server) på portalen. Därefter kan listas virtuella datorer på portalen.
* **ESXi avstängd**: Om ESXi-värd som den virtuella datorn finns i avstängd, har virtuella datorn visas inte eller kan inte väljas på Azure portal. Starta ESXi-värd [uppdatera konfigurationsservern](vmware-azure-manage-configuration-server.md#refresh-configuration-server) på portalen. Därefter kan listas virtuella datorer på portalen.
* **Väntar på omstart**: Om det finns en väntande omstart på den virtuella datorn, kommer sedan du inte att kunna välja dator på Azure-portalen. Se till att slutföra väntande omstart-aktiviteter [uppdatera konfigurationsservern](vmware-azure-manage-configuration-server.md#refresh-configuration-server). Därefter kan listas virtuella datorer på portalen.
* **Det gick inte att hitta IP**: Om den virtuella datorn inte har en giltig IP-adress som är kopplade till den, kommer sedan du inte att kunna välja dator på Azure-portalen. Se till att tilldela en giltig IP-adress till den virtuella datorn, [uppdatera konfigurationsservern](vmware-azure-manage-configuration-server.md#refresh-configuration-server). Därefter kan listas virtuella datorer på portalen.

## <a name="protected-virtual-machines-are-greyed-out-in-the-portal"></a>Skyddade virtuella datorer är gråmarkerat ut i portalen

Virtuella datorer som replikeras under Site Recovery är inte tillgängliga i Azure-portalen om det finns dubblettvärden i systemet. Om du vill lära dig mer om att ta bort inaktuella poster och lösa problemet, referera till [Azure Site Recovery VMware till Azure: Hur du rensar dubbletter eller inaktuella poster](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx).

## <a name="next-steps"></a>Nästa steg

Om du behöver mer hjälp kan du ställa din fråga i den [Azure Site Recovery-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). Vi har en aktiv community och en av våra tekniker kan hjälpa dig.
