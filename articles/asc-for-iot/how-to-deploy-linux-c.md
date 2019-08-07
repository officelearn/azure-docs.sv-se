---
title: Guide för att installera och distribuera Linux C-agenten för Azure Security Center för IoT-agent | Microsoft Docs
description: Lär dig hur du installerar Azure Security Center för IoT-agenten på både 32-bitars och 64-bitars Linux.
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
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812728"
---
# <a name="deploy-azure-security-center-for-iot-c-based-security-agent-for-linux"></a>Distribuera Azure Security Center för IoT C-baserad säkerhets agent för Linux

Den här guiden beskriver hur du installerar och distribuerar Azure Security Center för IoT C-baserad säkerhets agent på Linux.

I den här guiden får du lära du dig att: 
> [!div class="checklist"]
> * Installera
> * Verifiera distributionen
> * Avinstallera agenten
> * Felsöka 

## <a name="prerequisites"></a>Förutsättningar

För andra plattformar och agent-varianter, se [Välj rätt säkerhets agent](how-to-deploy-agent.md).

1. Om du vill distribuera säkerhets agenten krävs lokala administratörs rättigheter på den dator som du vill installera på (sudo).

1. [Skapa en säkerhetsmodul](quickstart-create-security-twin.md) för enheten.

## <a name="installation"></a>Installation 

Använd följande arbets flöde om du vill installera och distribuera säkerhets agenten:


1. Hämta den senaste versionen till datorn från [GitHub](https://aka.ms/iot-security-github-c).

1. Extrahera innehållet i paketet och navigera till mappen _/src/installation_ .

1. Lägg till behörigheter som körs i **InstallSecurityAgent-skriptet** genom att köra följande kommando:
    
   ```
   chmod +x InstallSecurityAgent.sh
   ```

1. Kör sedan: 

   ```
   ./InstallSecurityAgent.sh -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -i
   ```
   
   Mer information om autentiseringsmetoder finns i [Konfigurera autentisering](concept-security-agent-authentication-methods.md) .

Det här skriptet utför följande funktion:

1. Installerar nödvändiga komponenter.

2. Lägger till en tjänst användare (med interaktiv inloggning inaktive rad).

3. Installerar agenten som en **daemon** – förutsätter att enheten använder **system** för tjänst hantering.

4. Konfigurerar agenten med de autentiseringsmetoder som anges. 

Om du behöver ytterligare hjälp kör du skriptet med parametern – Help: 
    
    ./InstallSecurityAgent.sh --help

### <a name="uninstall-the-agent"></a>Avinstallera agenten

Avinstallera agenten genom att köra skriptet med parametern –-Uninstall:

    ./InstallSecurityAgent.sh -–uninstall

## <a name="troubleshooting"></a>Felsökning
Kontrol lera distributions statusen genom att köra:

    systemctl status ASCIoTAgent.service


## <a name="next-steps"></a>Nästa steg
- Läs [översikten över](overview.md) Azure Security Center för IoT-tjänsten
- Läs mer om Azure Security Center för IoT- [arkitektur](architecture.md)
- Aktivera [tjänsten](quickstart-onboard-iot-hub.md)
- Läs [vanliga frågor och svar](resources-frequently-asked-questions.md)
- Förstå [säkerhets aviseringar](concept-security-alerts.md)
