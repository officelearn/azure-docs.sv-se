---
title: Guide om hur du installerar och distribuerar Linux C# agent för Azure Security Center för förhandsversionen av IoT | Microsoft Docs
description: Lär dig hur du installerar Azure Security Center för IoT-agenten på både 32-bitars och 64-bitars Linux.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: b0982203-c3c8-4a0b-8717-5b5ac4038d8c
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: 4a53bcf77696d3aa53a4a404bfacd6f6d468885b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "58862111"
---
# <a name="deploy-azure-security-center-for-iot-c-based-security-agent-for-linux"></a>Distribuera Azure Security Center för IoT C#-baserade security-agenten för Linux

> [!IMPORTANT]
> Azure Security Center för IoT är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Den här handboken beskrivs hur du installerar och distribuerar Azure Security Center (ASC) för IoT C#-baserad säkerhetsagenten på Linux.

I den här guiden får du lära du dig att: 
> [!div class="checklist"]
> * Installera
> * Verifiera distributionen
> * Avinstallera agenten
> * Felsökning 

## <a name="prerequisites"></a>Nödvändiga komponenter

Andra plattformar och agenten varianter finns i [väljer rätt säkerhetsagenten](how-to-deploy-agent.md).

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