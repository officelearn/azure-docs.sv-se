---
title: Guide för installation och distribution av Linux C-agent för Azure Security Center för IoT-agent| Microsoft-dokument
description: Lär dig hur du installerar Azure Security Center för IoT-agent på både 32- och 64-bitars Linux.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 3ccf2aec-106a-4d2c-8079-5f3e8f2afdcb
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 7578811c37cd0bbe47821dadacce5fa5974f56cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68812728"
---
# <a name="deploy-azure-security-center-for-iot-c-based-security-agent-for-linux"></a>Distribuera Azure Security Center for IoT C-baserad säkerhetsagent för Linux

Den här guiden förklarar hur du installerar och distribuerar Azure Security Center för IoT C-baserad säkerhetsagent på Linux.

I den här guiden får du lära du dig att: 
> [!div class="checklist"]
> * Installera
> * Verifiera distributionen
> * Avinstallera agenten
> * Felsöka 

## <a name="prerequisites"></a>Krav

För andra plattformar och agent smaker, se [Välj rätt säkerhetsagent](how-to-deploy-agent.md).

1. För att distribuera säkerhetsagenten krävs lokala administratörsrättigheter på den dator som du vill installera på (sudo).

1. [Skapa en säkerhetsmodul](quickstart-create-security-twin.md) för enheten.

## <a name="installation"></a>Installation 

Om du vill installera och distribuera säkerhetsagenten använder du följande arbetsflöde:


1. Ladda ned den senaste versionen till datorn från [GitHub](https://aka.ms/iot-security-github-c).

1. Extrahera innehållet i paketet och navigera till mappen _/src/installation._

1. Lägg till körbehörigheter i **InstallSecurityAgent-skriptet** genom att köra följande kommando:
    
   ```
   chmod +x InstallSecurityAgent.sh
   ```

1. Kör sedan: 

   ```
   ./InstallSecurityAgent.sh -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -i
   ```
   
   Mer information om autentiseringsparametrar finns i [Så här konfigurerar du autentisering.](concept-security-agent-authentication-methods.md)

Det här skriptet utför följande funktion:

1. Installerar förutsättningar.

2. Lägger till en tjänstanvändare (med interaktiv inloggning inaktiverad).

3. Installerar agenten som en **demon** - förutsätter att enheten använder **system som är systemad** för tjänsthantering.

4. Konfigurerar agenten med de autentiseringsparametrar som tillhandahålls. 

Om du vill ha ytterligare hjälp kan du köra skriptet med hjälpparametern: 
    
    ./InstallSecurityAgent.sh --help

### <a name="uninstall-the-agent"></a>Avinstallera agenten

Om du vill avinstallera agenten kör du skriptet med parametern –-uninstall:

    ./InstallSecurityAgent.sh -–uninstall

## <a name="troubleshooting"></a>Felsökning
Kontrollera distributionsstatusen genom att köra:

    systemctl status ASCIoTAgent.service


## <a name="next-steps"></a>Nästa steg
- Läs [översikt](overview.md) över Azure Security Center for IoT-tjänsten
- Läs mer om Azure Security Center för [IoT-arkitektur](architecture.md)
- Aktivera [tjänsten](quickstart-onboard-iot-hub.md)
- Läs [vanliga frågor och svar](resources-frequently-asked-questions.md)
- Förstå [säkerhetsaviseringar](concept-security-alerts.md)
