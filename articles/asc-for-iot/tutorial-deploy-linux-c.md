---
title: Självstudie om att installera och distribuera agenten för Linux C av ASC om IoT-agenten förhandsversionen | Microsoft Docs
description: Lär dig hur du installerar ASC för IoT-agenten på både 32-bitars och 64-bitars Linux.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 3ccf2aec-106a-4d2c-8079-5f3e8f2afdcb
ms.service: ascforiot
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 04b41791915946820c9c7a1666ab10e880731e4b
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541586"
---
# <a name="tutorial-deploy-asc-for-iot-c-based-security-agent-for-linux"></a>Självstudier: Distribuera ASC för IoT-C-baserade security-agenten för Linux

> [!IMPORTANT]
> ASC för IoT är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Den här självstudien beskrivs hur du installerar och distribuerar ASC för IoT-C-baserade säkerhetsagenten på Linux.

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

Om du vill installera och distribuera säkerhetsagenten, gör du följande:

1. Ladda ned den senaste versionen på din dator från [Github](https://aka.ms/iot-security-github-cs).

1. Extrahera innehållet i paketet och navigera till den _/installation_ mapp.

1. Lägg till som körs behörigheter till den **InstallSecurityAgent skriptet** genom att köra följande:
    
    `chmod +x InstallSecurityAgent.sh` 

1. Kör: 

   ```
   ./InstallSecurityAgent.sh -i -aui <authentication identity>  -aum <authentication method> -f <file path> -hn <host name>  -di <device id> -cl <certificate location kind>
   ```
   
   Se [hur du konfigurerar autentisering](concept-security-agent-authentication-methods.md) för mer information om autentiseringsparametrar.

Skriptet gör följande:

1. Installerar nödvändiga komponenter.

2. Lägger till en Serviceanvändare (med interaktiv inloggning inaktiverad).

3. Installerar agent som en **Daemon** -förutsätter att enheten använder **systemd** för tjänsthantering.

4. Konfigurerar **sudoers** så att agenten att utföra vissa uppgifter som rot.

5. Agenten konfigureras med de tillhandahållna parametrarna för autentisering. 

Kör skript för ytterligare hjälp med parametern – hjälp: 
    
    `./InstallSecurityAgent.sh --help`

### <a name="uninstall-the-agent"></a>Avinstallera agenten

Kör skriptet med parametern – u avinstallera agenten:

    `./InstallSecurityAgent.sh -u`. 

```
 .\InstallSecurityAgent.sh –uninstall / -u
``` 

## <a name="troubleshooting"></a>Felsökning
Kontrollera distributionens status genom att köra:

```
systemctl status ASCIoTAgent.service
```


## <a name="next-steps"></a>Nästa steg
- Läsa ASC för IoT-tjänsten [översikt](overview.md)
- Läs mer om ASC för IoT [arkitektur](architecture.md)
- Aktivera den [service](quickstart-onboard-iot-hub.md)
- Läs den [vanliga frågor och svar](resources-frequently-asked-questions.md)
- Förstå [säkerhetsaviseringar](concept-security-alerts.md)