---
title: Installera & distribuera Linux C#-agent
description: Lär dig hur du installerar Azure Security Center för IoT-agent på både 32- och 64-bitars Linux.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: b0982203-c3c8-4a0b-8717-5b5ac4038d8c
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/27/2019
ms.author: mlottner
ms.openlocfilehash: 40c6ea91fd84a0f088ed770cd7c4c3ea7b8b1c91
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311136"
---
# <a name="deploy-azure-security-center-for-iot-c-based-security-agent-for-linux"></a>Distribuera Azure Security Center for IoT C#-baserad säkerhetsagent för Linux

Den här guiden förklarar hur du installerar och distribuerar Azure Security Center för IoT C#-baserad säkerhetsagent på Linux.

I den här guiden får du lära du dig att:

> [!div class="checklist"]
> * Installera
> * Verifiera distributionen
> * Avinstallera agenten
> * Felsöka

## <a name="prerequisites"></a>Krav

För andra plattformar och agent smaker, se [Välj rätt säkerhetsagent](how-to-deploy-agent.md).

1. För att distribuera säkerhetsagenten krävs lokala administratörsrättigheter på den dator som du vill installera på.

1. [Skapa en säkerhetsmodul](quickstart-create-security-twin.md) för enheten.

## <a name="installation"></a>Installation

Så här distribuerar du säkerhetsagenten:

1. Ladda ned den senaste versionen till datorn från [GitHub](https://aka.ms/iot-security-github-cs).

1. Extrahera innehållet i paketet och navigera till mappen _/Install._

1. Lägga till körbehörigheter i **InstallSecurityAgent-skriptet** genom att köra`chmod +x InstallSecurityAgent.sh`

1. Kör sedan följande kommando med **rotprivilegier:**

   ```
   ./InstallSecurityAgent.sh -i -aui <authentication identity>  -aum <authentication method> -f <file path> -hn <host name>  -di <device id> -cl <certificate location kind>
   ```

   Mer information om autentiseringsparametrar finns i [Konfigurera autentisering](concept-security-agent-authentication-methods.md).

Det här skriptet utför följande åtgärder:

- Installerar förutsättningar.

- Lägger till en tjänstanvändare (med interaktiv inloggning inaktiverad).

- Installerar agenten som en **demon** - förutsätter att enheten använder **systemd** för klassisk distributionsmodell.

- Konfigurerar **sudoers** så att agenten kan utföra vissa uppgifter som root.

- Konfigurerar agenten med de angivna autentiseringsparametrarna.

Om du vill ha ytterligare hjälp kan du köra skriptet med hjälpparametern:`./InstallSecurityAgent.sh --help`

### <a name="uninstall-the-agent"></a>Avinstallera agenten

Om du vill avinstallera agenten kör du `./InstallSecurityAgent.sh -u`skriptet med parametern -u: .

> [!NOTE]
> Avinstallation tar inte bort några nödvändiga krav som saknades under installationen.

## <a name="troubleshooting"></a>Felsökning

1. Kontrollera distributionsstatusen genom att köra:

    `systemctl status ASCIoTAgent.service`

1. Aktivera loggning.
   Om agenten inte startar aktiverar du loggning för att få mer information.

   Aktivera loggningen genom att:

   1. Öppna konfigurationsfilen för redigering i alla Linux-redigerare:

        `vi /var/ASCIoTAgent/General.config`

   1. Redigera följande värden:

      ```
      <add key="logLevel" value="Debug"/>
      <add key="fileLogLevel" value="Debug"/>
      <add key="diagnosticVerbosityLevel" value="Some" />
      <add key="logFilePath" value="IotAgentLog.log"/>
      ```

       **Värdet logFilePath** kan konfigureras.

       > [!NOTE]
       > Vi rekommenderar att **du** inaktiverar loggning när felsökningen är klar. Om du lämnar loggning **ökar** loggfilens storlek och dataanvändning.

   1. Starta om agenten genom att köra:

       `systemctl restart ASCIoTAgent.service`

   1. Visa loggfilen för mer information om felet.

       Loggfilens plats är:`/var/ASCIoTAgent/IotAgentLog.log`

       Ändra sökvägen till filplatsen enligt det namn du valde för **logFilePath** i steg 2.

## <a name="next-steps"></a>Nästa steg

- Läs [översikt](overview.md) över Azure Security Center for IoT-tjänsten
- Läs mer om Azure Security Center för [IoT-arkitektur](architecture.md)
- Aktivera [tjänsten](quickstart-onboard-iot-hub.md)
- Läs [vanliga frågor och svar](resources-frequently-asked-questions.md)
- Förstå [aviseringar](concept-security-alerts.md)
