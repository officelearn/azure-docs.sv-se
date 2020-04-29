---
title: Anslut syslog-data till Azure Sentinel | Microsoft Docs
description: Lär dig hur du ansluter syslog-data till Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 73fd55fc24fd94dc88bba2f591c32480f77c7d5d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "77588084"
---
# <a name="connect-your-external-solution-using-syslog"></a>Anslut din externa lösning med syslog

Du kan ansluta alla lokala installationer som stöder Syslog till Azure Sentinel. Detta görs med hjälp av en agent som baseras på en Linux-dator mellan enheten och Azure Sentinel. Om Linux-datorn finns i Azure kan du strömma loggarna från din enhet eller ditt program till en dedikerad arbets yta som du skapar i Azure och ansluter den. Om Linux-datorn inte finns i Azure kan du strömma loggarna från din installation till en dedikerad lokal VM eller dator där du installerar agenten för Linux. 

> [!NOTE]
> Om din installation stöder Syslog-CEF är anslutningen mer fullständig och du bör välja det här alternativet och följa anvisningarna i att [ansluta data från CEF](connect-common-event-format.md).

## <a name="how-it-works"></a>Så här fungerar det

Syslog är ett händelse loggnings protokoll som är gemensamt för Linux. Program kommer att skicka meddelanden som kan lagras på den lokala datorn eller levereras till en syslog-insamlare. När Log Analytics-agenten för Linux installeras konfigureras den lokala syslog-daemonen för att vidarebefordra meddelanden till agenten. Agenten skickar sedan meddelandet till Azure Monitor där en motsvarande post skapas.

Mer information finns i [syslog-datakällor i Azure Monitor](../azure-monitor/platform/data-sources-syslog.md).

> [!NOTE]
> - Agenten kan samla in loggar från flera källor, men måste installeras på en dedikerad proxyserver.
> - Om du vill ha stöd för anslutningar för både CEF och syslog på samma virtuella dator utför du följande steg för att undvika att duplicera data:
>    1. Följ anvisningarna för att [ansluta din CEF](connect-common-event-format.md).
>    2. Om du vill ansluta syslog-data går du till **Inställningar** > **arbets ytan** > inställningar**Avancerade inställningar** > **data** > **syslog** och anger funktionerna och deras prioriteringar så att de inte är samma funktioner och egenskaper som du använde i CEF-konfigurationen. <br></br>Om du väljer **Använd konfigurationen nedan för mina datorer**tillämpas dessa inställningarna på alla virtuella datorer som är anslutna till den här arbets ytan.


## <a name="connect-your-syslog-appliance"></a>Anslut syslog-enheten

1. I Azure Sentinel väljer du **data kopplingar** och väljer sedan **syslog** -anslutningsprogrammet.

2. På bladet **syslog** väljer du **Öppna kopplings sida**.

3. Installera Linux-agenten:
    
    - Om din virtuella Linux-dator är i Azure väljer du **Ladda ned och installera agent på virtuell Azure Linux-dator**. På bladet **virtuella datorer** väljer du de virtuella datorer där du vill installera agenten och klickar sedan på **Anslut**.
    - Om Linux-datorn inte finns i Azure väljer du **Ladda ned och installera agent på Linux-datorer som inte är Azure-datorer**. På bladet **Direct agent** kopierar du kommandot för att **Ladda ned och integrera agent för Linux** och köra det på din dator. 
    
   > [!NOTE]
   > Se till att konfigurera säkerhets inställningar för de här datorerna enligt din organisations säkerhets princip. Du kan till exempel konfigurera nätverks inställningarna så att de överensstämmer med organisationens nätverks säkerhets princip och ändra portarna och protokollen i daemonen så att de överensstämmer med säkerhets kraven.

4. Välj **Öppna konfiguration av avancerade inställningar för arbets ytan**.

5. På bladet **Avancerade inställningar** väljer du **data** > **syslog**. Lägg sedan till de anläggningar som ska samlas in av kopplingen.
    
    Lägg till de anläggningar som syslog-apparaten innehåller i sina logg rubriker. Du kan se den här konfigurationen i syslog-enheten i **syslog-d** i `/etc/rsyslog.d/security-config-omsagent.conf` mappen och i **r-syslog** från `/etc/syslog-ng/security-config-omsagent.conf`.
    
    Om du vill använda avvikande identifiering av SSH-inloggning med de data som du samlar in lägger du till **auth** och **authpriv**. Mer information finns i [följande avsnitt](#configure-the-syslog-connector-for-anomalous-ssh-login-detection) .

6. När du har lagt till alla funktioner som du vill övervaka och justerat eventuella allvarlighets grad alternativ för var och en, markerar du kryss rutan **Använd konfigurationen nedan för mina datorer**.

7. Välj **Spara**. 

8. Kontrol lera att du skickar de anläggningar som du har angett på syslog-enheten.

9. Om du vill använda det relevanta schemat i Azure Monitor för syslog-loggarna söker du efter **syslog**.

10. Du kan använda funktionen Kusto som beskrivs i [använda funktioner i Azure Monitor logg frågor](../azure-monitor/log-query/functions.md) för att parsa syslog-meddelanden. Du kan sedan spara dem som en ny Log Analytics funktion som ska användas som ny datatyp.

### <a name="configure-the-syslog-connector-for-anomalous-ssh-login-detection"></a>Konfigurera syslog-anslutningen för identifiering av avvikande SSH-inloggning

> [!IMPORTANT]
> Identifiering av avvikande SSH-inloggning är för närvarande en offentlig för hands version.
> Den här funktionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Sentinel kan använda Machine Learning (ML) till syslog-data för att identifiera en SSH-inloggning (avvikande säker Shell). Scenarierna är:

- Omöjlig resa – när två lyckade inloggnings händelser inträffar från två platser som är omöjliga att uppnå inom ramen för de två inloggnings händelserna.
- Oväntad plats – platsen där en lyckad inloggnings händelse inträffade är misstänkt. Till exempel visas inte platsen nyligen.
 
Den här identifieringen kräver en speciell konfiguration av syslog-datakopplingen: 

1. I steg 5 i föregående procedur ser du till att både **auth** -och **authpriv** är markerade som anläggningar att övervaka. Behåll standardinställningarna för allvarlighets grad alternativen så att alla är markerade. Ett exempel:
    
    > [!div class="mx-imgBorder"]
    > ![Anläggningar som krävs för identifiering av avvikande SSH-inloggning](./media/connect-syslog/facilities-ssh-detection.png)

2. Tillåt att syslog-informationen samlas in tillräckligt med tid. Gå sedan till **Azure Sentinel-logs**och kopiera och klistra in följande fråga:
    
        Syslog |  where Facility in ("authpriv","auth")| extend c = extract( "Accepted\\s(publickey|password|keyboard-interactive/pam)\\sfor ([^\\s]+)",1,SyslogMessage)| where isnotempty(c) | count 
    
    Ändra **tidsintervallet** vid behov och välj **Kör**.
    
    Om det resulterande antalet är noll bekräftar du konfigurationen av anslutningen och att de övervakade datorerna har lyckats med inloggnings aktiviteten för den tids period som du har angett för din fråga.
    
    Om det resulterande antalet är större än noll är dina syslog-data lämpliga för avvikande identifiering av SSH-inloggning. Du aktiverar den här identifieringen från **Analytics** >  **rule templates** > **(för hands version) avvikande identifiering av SSH-inloggning**.

## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter syslog-lokala enheter till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Använd arbets böcker](tutorial-monitor-your-data.md) för att övervaka dina data.

