---
title: Ansluta Syslog-data till Azure Sentinel | Microsoft-dokument
description: Lär dig hur du ansluter Syslog-data till Azure Sentinel.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588084"
---
# <a name="connect-your-external-solution-using-syslog"></a>Anslut din externa lösning med Syslog

Du kan ansluta alla lokala enheter som stöder Syslog till Azure Sentinel. Detta görs med hjälp av en agent baserad på en Linux-dator mellan installationen och Azure Sentinel. Om din Linux-dator finns i Azure kan du strömma loggarna från din installation eller program till en dedikerad arbetsyta som du skapar i Azure och ansluta den. Om din Linux-dator inte finns i Azure kan du strömma loggarna från din installation till en dedikerad på lokal virtuell dator eller dator där du installerar agenten för Linux. 

> [!NOTE]
> Om din apparat stöder Syslog CEF är anslutningen mer fullständig och du bör välja det här alternativet och följa instruktionerna i [Anslutning av data från FSE](connect-common-event-format.md).

## <a name="how-it-works"></a>Hur det fungerar

Syslog är ett händelseloggningsprotokoll som är gemensamt för Linux. Program skickar meddelanden som kan lagras på den lokala datorn eller levereras till en Syslog-insamlare. När Log Analytics-agenten för Linux är installerad konfigureras den lokala Syslog-demonen för att vidarebefordra meddelanden till agenten. Agenten skickar sedan meddelandet till Azure Monitor där en motsvarande post skapas.

Mer information finns [i Syslog-datakällor i Azure Monitor](../azure-monitor/platform/data-sources-syslog.md).

> [!NOTE]
> - Agenten kan samla in loggar från flera källor, men måste installeras på dedikerad proxydator.
> - Om du vill stödja anslutningsappar för både FSE och Syslog på samma virtuella dator utför du följande steg för att undvika att du duplicerar data:
>    1. Följ instruktionerna för att [ansluta din FSE](connect-common-event-format.md).
>    2. Om du vill ansluta Syslog-data går du till **Inställningar** > **Arbetsyteinställningar** > **Avancerade inställningar** > **Data** > **Syslog** och anger tjänsterna och deras prioriteter så att de inte är samma anläggningar och egenskaper som du använde i cef-konfigurationen. <br></br>Om du väljer **Använd under konfiguration på mina datorer**tillämpas dessa setings på alla virtuella datorer som är anslutna till den här arbetsytan.


## <a name="connect-your-syslog-appliance"></a>Anslut syslog-apparaten

1. I Azure Sentinel väljer du **Data-kopplingar** och väljer sedan **Syslog-kopplingen.**

2. På **bladet Syslog** väljer du **Öppna kopplingssida**.

3. Installera Linux-agenten:
    
    - Om din virtuella Linux-dator finns i Azure väljer du **Hämta och installera agent på den virtuella Azure Linux-datorn**. I bladet **Virtuella datorer** väljer du de virtuella datorerna som agenten ska installeras på och klickar sedan på **Anslut**.
    - Om din Linux-dator inte finns i Azure väljer du **Hämta och installera agent på Linux-datorn som inte är Azure.** I **direktagentbladet** kopierar du kommandot för **DOWNLOAD AND ONBOARD AGENT FOR LINUX** och kör det på din dator. 
    
   > [!NOTE]
   > Se till att du konfigurerar säkerhetsinställningar för dessa datorer enligt organisationens säkerhetsprinciper. Du kan till exempel konfigurera nätverksinställningarna så att de överensstämmer med organisationens nätverkssäkerhetsprincip och ändra portarna och protokollen i demonen så att de överensstämmer med säkerhetskraven.

4. Välj **Öppna konfigurationen för avancerade inställningar på arbetsytan**.

5. Välj **Data** > **Syslog**på bladet **Avancerade inställningar** . Lägg sedan till faciliteterna för kontakten att samla in.
    
    Lägg till de faciliteter som syslog-apparaten innehåller i dess logghuvuden. Du kan se den här konfigurationen i Syslog-apparaten i **Syslog-d** i `/etc/rsyslog.d/security-config-omsagent.conf` mappen och i **r-Syslog** från `/etc/syslog-ng/security-config-omsagent.conf`.
    
    Om du vill använda avvikande SSH-inloggningsidentifiering med de data som du samlar in lägger du till **auth** och **authpriv**. Mer information finns i [följande avsnitt.](#configure-the-syslog-connector-for-anomalous-ssh-login-detection)

6. När du har lagt till alla anläggningar som du vill övervaka och justerat eventuella allvarlighetsgradalternativ för var och en markerar du kryssrutan **Använd under konfigurationen på mina datorer**.

7. Välj **Spara**. 

8. Kontrollera att du skickar de faciliteter som du har angett på syslog-apparaten.

9. Om du vill använda det relevanta schemat i Azure Monitor för syslogloggarna söker du efter **Syslog**.

10. Du kan använda funktionen Kusto som beskrivs i [Använda funktioner i Azure Monitor-loggfrågor](../azure-monitor/log-query/functions.md) för att tolka dina Syslog-meddelanden. Du kan sedan spara dem som en ny Log Analytics-funktion som ska användas som en ny datatyp.

### <a name="configure-the-syslog-connector-for-anomalous-ssh-login-detection"></a>Konfigurera Syslog-anslutningen för avvikande SSH-inloggningsidentifiering

> [!IMPORTANT]
> Avvikande SSH-inloggningsidentifiering är för närvarande i offentlig förhandsversion.
> Den här funktionen tillhandahålls utan ett servicenivåavtal och rekommenderas inte för produktionsarbetsbelastningar.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Sentinel kan använda machine learning (ML) på syslog-data för att identifiera avvikande Secure Shell (SSH) inloggningsaktivitet. Scenarier inkluderar:

- Omöjlig resa – när två lyckade inloggningshändelser inträffar från två platser som är omöjliga att nå inom tidsramen för de två inloggningshändelserna.
- Oväntad plats – platsen där en lyckad inloggningshändelse inträffade är misstänkt. Platsen har till exempel inte setts nyligen.
 
Den här identifieringen kräver en specifik konfiguration av Syslog-dataanslutningen: 

1. För steg 5 i föregående procedur, se till att både **auth** och **authpriv** väljs som anläggningar för att övervaka. Behåll standardinställningarna för allvarlighetsgradsalternativen så att alla är markerade. Ett exempel:
    
    > [!div class="mx-imgBorder"]
    > ![Anläggningar som krävs för avvikande SSH-inloggningsdetektering](./media/connect-syslog/facilities-ssh-detection.png)

2. Ge tillräckligt med tid för att syslog information ska samlas in. Navigera sedan till **Azure Sentinel - Loggar**och kopiera och klistra in följande fråga:
    
        Syslog |  where Facility in ("authpriv","auth")| extend c = extract( "Accepted\\s(publickey|password|keyboard-interactive/pam)\\sfor ([^\\s]+)",1,SyslogMessage)| where isnotempty(c) | count 
    
    Ändra **tidsintervallet** om det behövs och välj **Kör**.
    
    Om det resulterande antalet är noll, bekräfta konfigurationen av anslutningen och att de övervakade datorerna har en lyckad inloggningsaktivitet för den tidsperiod som du angav för din fråga.
    
    Om det resulterande antalet är större än noll, är dina syslog-data lämpliga för avvikande SSH-inloggningsidentifiering. Du aktiverar den här identifieringen från >  **Analytics-regelmallar** > **(förhandsversion) Avvikande SSH-inloggningsidentifiering**. **Analytics**

## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Syslog lokala enheter till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Läs om hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Använd arbetsböcker](tutorial-monitor-your-data.md) för att övervaka dina data.

