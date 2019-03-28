---
title: Självstudie för att installera och distribuera Linux C# agent för Azure Security Center för förhandsversionen av IoT | Microsoft Docs
description: Lär dig hur du installerar ASC för IoT-agenten på både 32-bitars och 64-bitars Linux.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: b0982203-c3c8-4a0b-8717-5b5ac4038d8c
ms.service: ascforiot
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: 50e21e1d0937f4543435fe2f2e5e01e06a2b1e3c
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541603"
---
# <a name="tutorial-deploy-asc-for-iot-c-based-security-agent-for-linux"></a>Självstudier: Distribuera ASC för IoT C#-baserade security-agenten för Linux

> [!IMPORTANT]
> ASC för IoT är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Den här självstudien beskrivs hur du installerar och distribuerar ASC för IoT C#-baserad säkerhetsagenten på Linux.

I den här guiden får du lära dig att: 
> [!div class="checklist"]
> * Installera
> * Verifiera distributionen
> * Avinstallera agenten
> * Felsöka 

## <a name="prerequisites"></a>Förutsättningar

Andra plattformar och agenten smaksättningar finns i [väljer rätt säkerhetsagenten](select-deploy-agent.md).

1. Om du vill distribuera säkerhetsagenten krävs lokala administratörsrättigheter på den dator som du vill installera på. 

1. [Skapa en modul för maskinvarusäkerhet](quickstart-create-security-twin.md) för enheten.

## <a name="installation"></a>Installation 

Om du vill distribuera säkerhetsagenten, gör du följande:

1. Ladda ned den senaste versionen på din dator från [Github](https://aka.ms/iot-security-github-cs).

1. Extrahera innehållet i paketet och navigera till den _/Install_ mapp.

1. Lägg till som körs behörigheter till den **InstallSecurityAgent skriptet** genom att köra `chmod +x InstallSecurityAgent.sh` 

1. Kör: 

   ```
   ./InstallSecurityAgent.sh -i -aui <authentication identity>  -aum <authentication method> -f <file path> -hn <host name>  -di <device id> -cl <certificate location kind>
   ```
   
   Se [hur du konfigurerar autentisering](concept-security-agent-authentication-methods.md) för mer information om autentiseringsparametrar.

Skriptet gör följande:

- Installerar nödvändiga komponenter.

- Lägger till en Serviceanvändare (med interaktiv inloggning inaktiverad).

- Installerar agent som en **Daemon** -detta förutsätter att enheten använder **systemd** för tjänsthantering.

- Konfigurerar **sudoers** så att agenten att utföra vissa uppgifter som rot.

- Konfigurerar agenten med de angivna autentiseringsparametrar.


Kör skript för ytterligare hjälp med parametern – hjälp: `./InstallSecurityAgent.sh --help`

### <a name="uninstall-the-agent"></a>Avinstallera agenten

Avinstallera agenten genom att köra skriptet med parametern – u: `./InstallSecurityAgent.sh -u`. 

> [!NOTE]
> Avinstallera tar inte bort allt saknas som installerades under installationen.

## <a name="troubleshooting"></a>Felsökning  

1. Kontrollera distributionens status genom att köra:

    `systemctl status ASCIoTAgent.service`

2. Aktivera loggning.  
   Om agenten inte kan starta, aktivera loggning för att få mer information.

   Aktivera loggning av:

   1. Öppna konfigurationsfilen för redigering i en Linux-Redigerare:

        `vi /var/ASCIoTAgent/General.config`

   1. Redigera följande värden: 

      ```
      <add key="logLevel" value="Debug"/>
      <add key="fileLogLevel" value="Debug"/>
      <add key="diagnosticVerbosityLevel" value="Some" /> 
      <add key="logFilePath" value="IotAgentLog.log"/>
      ```
       Den **logFilePath** värde kan konfigureras. 

       > [!NOTE]
       > Vi rekommenderar att aktivera loggning **av** när felsökningen har slutförts. Lämna loggning **på** ökar logga användning av storlek och data.

   1. Starta om agenten genom att köra:

       `systemctl restart ASCIoTAgent.service`

   1. Mer information om felet finns i loggfilen.  

       Plats för loggfil är: `/var/ASCIoTAgent/IotAgentLog.log`

       Ändra sökvägen till filen med det namn som du valde för den **logFilePath** i steg 2. 

## <a name="next-steps"></a>Nästa steg

- Läsa ASC för IoT-tjänsten [översikt](overview.md)
- Läs mer om ASC för IoT [arkitektur](architecture.md)
- Aktivera den [service](quickstart-onboard-iot-hub.md)
- Läs den [vanliga frågor och svar](resources-frequently-asked-questions.md)
- Förstå [aviseringar](concept-security-alerts.md)