---
title: Anslut Palo-nätverks data till Azure Sentinel | Microsoft Docs
description: Lär dig hur du ansluter Palo-nätverks data till Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: a4b21d67-1a72-40ec-bfce-d79a8707b4e1
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2019
ms.author: rkarlin
ms.openlocfilehash: 8b1331eb99fd3d061d231ae48c40a721911e74db
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73475847"
---
# <a name="connect-palo-alto-networks-to-azure-sentinel"></a>Anslut Palo-nätverk till Azure Sentinel



Den här artikeln beskriver hur du ansluter din Palo-enhet med-nätverk till Azure Sentinel. Med Palo-nätverkets data koppling kan du enkelt ansluta dina Palo-nätverks loggar med Azure Sentinel, Visa instrument paneler, skapa anpassade aviseringar och förbättra undersökningen. Användning av Palo-nätverk i Azure Sentinel ger dig mer insikter om din organisations Internet användning och förbättrar säkerhets åtgärdens funktioner. 


## <a name="how-it-works"></a>Så här fungerar det

Du måste distribuera en agent på en särskild Linux-dator (VM eller lokalt) för att stödja kommunikationen mellan Palo-och Azure-nätverk och Azure Sentinel. Följande diagram beskriver installationen i händelse av en virtuell Linux-dator i Azure.

 ![CEF i Azure](./media/connect-cef/cef-syslog-azure.png)

Detta kan också finnas om du använder en virtuell dator i ett annat moln eller på en lokal dator. 

 ![CEF lokalt](./media/connect-cef/cef-syslog-onprem.png)


## <a name="security-considerations"></a>Säkerhetsöverväganden

Se till att konfigurera datorns säkerhet enligt din organisations säkerhets princip. Du kan till exempel konfigurera nätverket så att det överensstämmer med företagets nätverks säkerhets princip och ändra portarna och protokollen i daemonen så att de överensstämmer med dina krav. Du kan använda följande instruktioner för att förbättra datorns säkerhets konfiguration:  [säker virtuell dator i Azure](../virtual-machines/linux/security-policy.md), [metod tips för nätverks säkerhet](../security/fundamentals/network-best-practices.md).

Om du vill använda TLS-kommunikation mellan säkerhets lösningen och syslog-datorn måste du konfigurera syslog-daemonen (rsyslog eller syslog-ng) för att kommunicera i TLS: [kryptera syslog-trafik med TLS-rsyslog](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html), [kryptera logg meddelanden med TLS – syslog – ng](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298).

 
## <a name="prerequisites"></a>Förutsättningar
Kontrol lera att Linux-datorn som du använder som proxy kör något av följande operativ system:

- 64-bitars
  - CentOS 6 och 7
  - Amazon Linux 2017,09
  - Oracle Linux 6 och 7
  - Red Hat Enterprise Linux Server 6 och 7
  - Debian GNU/Linux 8 och 9
  - Ubuntu Linux 14,04 LTS 16,04 LTS och 18,04 LTS
  - SUSE Linux Enterprise Server 12
- 32-bitars
   - CentOS 6
   - Oracle Linux 6
   - Red Hat Enterprise Linux Server 6
   - Debian GNU/Linux 8 och 9
   - Ubuntu Linux 14,04 LTS och 16,04 LTS
 
 - Daemon-versioner
   - Syslog-ng: 2,1-3.22.1
   - Rsyslog: V8
  
 - Syslog-RFC stöds
   - Syslog RFC 3164
   - Syslog RFC 5424
 
Kontrol lera att datorn också uppfyller följande krav: 
- Behörigheter
    - Du måste ha förhöjd behörighet (sudo) på datorn. 
- Programvarukrav
    - Kontrol lera att du har python igång på datorn
## <a name="step-1-deploy-the-agent"></a>STEG 1: Distribuera agenten

I det här steget måste du välja den Linux-dator som ska fungera som en proxy mellan Azure Sentinel och din säkerhets lösning. Du måste köra ett skript på den proxyserver som:
- Installerar Log Analytics agenten och konfigurerar den efter behov för att lyssna efter syslog-meddelanden på port 514 via TCP och skicka CEF-meddelanden till din Azure Sentinel-arbetsyta.
- Konfigurerar syslog-daemonen att vidarebefordra CEF-meddelanden till Log Analytics agenten med port 25226.
- Ställer in syslog-agenten för att samla in data och skicka dem säkert till Log Analytics, där det är parsat och berikat.
 
 
1. Klicka på **data kopplingar** på Azure Sentinel-portalen och välj **Palo-nätverk** och **Öppna sedan kopplings sidan**. 

1. Under **Installera och konfigurera syslog-agenten**väljer du dator typ, antingen Azure, annat moln eller lokalt. 
   > [!NOTE]
   > Eftersom skriptet i nästa steg installerar Log Analytics agenten och ansluter datorn till din Azure Sentinel-arbetsyta, kontrol lera att datorn inte är ansluten till någon annan arbets yta.
1. Du måste ha förhöjd behörighet (sudo) på datorn. Se till att du har python på datorn med hjälp av följande kommando: `python –version`

1. Kör följande skript på din proxyserver.
   `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`
1. Kontrol lera att du inte får några fel eller varnings meddelanden medan skriptet körs.


 
## <a name="step-2-forward-palo-alto-networks-logs-to-the-syslog-agent"></a>STEG 2: vidarebefordra Palo-nätverk loggar till syslog-agenten

Konfigurera Palo-nätverk för att vidarebefordra syslog-meddelanden i CEF-format till Azure-arbetsytan via syslog-agenten:
1.  Gå till [konfigurations guider för common Event format (CEF)](https://docs.paloaltonetworks.com/resources/cef) och ladda ned PDF-filen för din installations typ. Följ alla instruktioner i guiden för att konfigurera Palo-enheter för att samla in CEF-händelser. 

1.  Gå till [Konfigurera Syslog-övervakning](https://aka.ms/asi-syslog-paloalto-forwarding) och följ steg 2 och 3 för att konfigurera CEF-händelse vidarebefordran från din Palo-enhets enhets nätverk till Azure Sentinel.

    1. Se till att ställa in **syslog-serverns format** på **BSD**.

       > [!NOTE]
       > Kopierings-och Inklistrings åtgärderna från PDF-filen kan ändra texten och infoga slumpmässiga tecken. Undvik detta genom att kopiera texten till en redigerare och ta bort eventuella tecken som kan bryta logg formatet innan du klistrar in det, som du kan se i det här exemplet.
 
        ![CEF text kopierings problem](./media/connect-cef/paloalto-text-prob1.png)

2. Om du vill använda det relevanta schemat i Log Analytics för Palo-nätverks händelser söker du efter **CommonSecurityLog**.

## <a name="step-3-validate-connectivity"></a>STEG 3: verifiera anslutningen

1. Öppna Log Analytics för att se till att loggarna tas emot med CommonSecurityLog-schemat.<br> Det kan ta upp till 20 minuter innan loggarna börjar visas i Log Analytics. 

1. Innan du kör skriptet rekommenderar vi att du skickar meddelanden från din säkerhetslösning för att se till att de vidarebefordras till den syslog-proxyserver som du har konfigurerat. 
1. Du måste ha förhöjd behörighet (sudo) på datorn. Se till att du har python på datorn med hjälp av följande kommando: `python –version`
1. Kör följande skript för att kontrol lera anslutningen mellan agenten, Azure Sentinel och din säkerhets lösning. Den kontrollerar att daemon-vidarebefordran har kon figurer ATS korrekt, lyssnar på rätt portar och att ingenting blockerar kommunikationen mellan daemonen och den Log Analytics agenten. Skriptet skickar även de blå meddelandena "TestCommonEventFormat" för att kontrol lera anslutning från slut punkt till slut punkt. <br>
 `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]`







## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Palo-enheter till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).

