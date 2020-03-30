---
title: Anslut CEF-data till Förhandsversionen av Azure Sentinel| Microsoft-dokument
description: Lär dig hur du ansluter CEF-data till Azure Sentinel.
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
ms.date: 11/26/2019
ms.author: yelevin
ms.openlocfilehash: 8314614616c6b8969832d52fc684d47ba1bf0fe3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588356"
---
# <a name="connect-your-external-solution-using-common-event-format"></a>Ansluta den externa lösningen med gemensamt händelseformat


När du ansluter en extern lösning som skickar CEF-meddelanden finns det tre steg för att ansluta till Azure Sentinel:

STEG 1: [Anslut CEF genom att distribuera agenten](connect-cef-agent.md) STEP 2: [Utför lösningsspecifika steg](connect-cef-solution-config.md) STEG 3: Verifiera [anslutningen](connect-cef-verify.md)

I den här artikeln beskrivs hur anslutningen fungerar, ger förutsättningar och du får möjlighet att distribuera agenten på säkerhetslösningar som skickar CEF-meddelanden (Common Event Format) ovanpå Syslog. 

> [!NOTE] 
> Data lagras på den geografiska platsen för arbetsytan där du kör Azure Sentinel.

För att kunna göra den här anslutningen måste du distribuera en agent på en dedikerad Linux-dator (VIRTUELL eller lokalt) för att stödja kommunikationen mellan installationen och Azure Sentinel. I följande diagram beskrivs installationen i händelse av en Virtuell Linux-dator i Azure.

 ![FSE i Azure](./media/connect-cef/cef-syslog-azure.png)

Det finns även den här inställningen om du använder en virtuell dator i ett annat moln eller en lokal dator. 

 ![FSE på plats](./media/connect-cef/cef-syslog-onprem.png)


## <a name="security-considerations"></a>Säkerhetsöverväganden

Se till att konfigurera datorns säkerhet enligt organisationens säkerhetsprinciper. Du kan till exempel konfigurera nätverket så att det stämmer överens med företagets säkerhetsprincip och ändra portarna och protokollen i demonen så att de överensstämmer med dina krav. Du kan använda följande instruktioner för att förbättra datorns säkerhetskonfiguration:  [Säker virtuell dator i Azure](../virtual-machines/linux/security-policy.md), [Metodtips för nätverkssäkerhet](../security/fundamentals/network-best-practices.md).

Om du vill använda TLS-kommunikation mellan säkerhetslösningen och Syslog-datorn måste du konfigurera Syslog-demonen (rsyslog eller syslog-ng) för att kommunicera i TLS: [Kryptera Syslog Traffic med TLS -rsyslog](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html), [Kryptera loggmeddelanden med TLS –syslog-ng](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298).

 
## <a name="prerequisites"></a>Krav
Kontrollera att den Linux-maskin som du använder som proxy kör något av följande operativsystem:

- 64-bitars
  - CentOS 6 och 7
  - Amazon Linux 2017.09
  - Oracle Linux 6 och 7
  - Red Hat Enterprise Linux Server 6 och 7
  - Debian GNU/Linux 8 och 9
  - Ubuntu Linux 14.04 LTS, 16.04 LTS och 18.04 LTS
  - SUSE Linux Enterprise Server 12
- 32-bitars
   - CentOS 6
   - Oracle Linux 6
   - Red Hat Enterprise Linux Server 6
   - Debian GNU/Linux 8 och 9
   - Ubuntu Linux 14.04 LTS och 16.04 LTS
 
 - Daemon versioner
   - Syslog-ng: 2.1 - 3.22.1
   - Rsyslog: v8
  
 - Syslog RFC-datorer stöds
   - Syslog RFC 3164
   - Syslog RFC 5424
 
Se till att din maskin också uppfyller följande krav: 
- Behörigheter
    - Du måste ha förhöjda behörigheter (sudo) på din maskin. 
- Programvarukrav
    - Se till att python körs på din dator



## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter CEF-enheter till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Läs om hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats.md).

