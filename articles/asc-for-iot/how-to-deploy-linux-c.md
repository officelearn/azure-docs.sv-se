---
title: Guide om hur du installerar och distribuerar Linux C-agenten av ASC för IoT-agenten förhandsversionen | Microsoft Docs
description: Lär dig hur du installerar ASC för IoT-agenten på både 32-bitars och 64-bitars Linux.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 3ccf2aec-106a-4d2c-8079-5f3e8f2afdcb
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/28/2019
ms.author: mlottner
ms.openlocfilehash: 2b18a09e5b79e7b3d3ea837e937397ac92491f9f
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58619857"
---
# <a name="deploy-asc-for-iot-c-based-security-agent-for-linux"></a>Distribuera ASC för IoT-C-baserade security-agenten för Linux

> [!IMPORTANT]
> ASC för IoT är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Den här guiden beskriver hur du installerar och distribuerar ASC för IoT-C-baserade säkerhetsagenten på Linux.

I den här guiden får du lära du dig att: 
> [!div class="checklist"]
> * Installera
> * Verifiera distributionen
> * Avinstallera agenten
> * Felsöka 

## <a name="prerequisites"></a>Förutsättningar

Andra plattformar och agenten varianter finns i [väljer rätt säkerhetsagenten](how-to-deploy-agent.md).

1. Om du vill distribuera säkerhetsagenten krävs lokala administratörsrättigheter på den dator som du vill installera på (sudo).

1. [Skapa en modul för maskinvarusäkerhet](quickstart-create-security-twin.md) för enheten.

## <a name="installation"></a>Installation 

Om du vill installera och distribuera säkerhetsagenten, gör du följande:


1. Ladda ned den senaste versionen på din dator från [Github](https://aka.ms/iot-security-github-c).

1. Extrahera innehållet i paketet och navigera till den _/Install_ mapp.

1. Lägg till som körs behörigheter till den **InstallSecurityAgent skriptet** genom att köra följande:
    
   ```
   chmod +x InstallSecurityAgent.sh
   ```

1. Kör: 

   ```
   ./InstallSecurityAgent.sh -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -i
   ```
   
   Se [hur du konfigurerar autentisering](concept-security-agent-authentication-methods.md) för mer information om autentiseringsparametrar.

Skriptet gör följande:

1. Installerar nödvändiga komponenter.

2. Lägger till en Serviceanvändare (med interaktiv inloggning inaktiverad).

3. Installerar agent som en **Daemon** -förutsätter att enheten använder **systemd** för tjänsthantering.

4. Agenten konfigureras med de tillhandahållna parametrarna för autentisering. 

Kör skript för ytterligare hjälp med parametern – hjälp: 
    
    ./InstallSecurityAgent.sh --help

### <a name="uninstall-the-agent"></a>Avinstallera agenten

Avinstallera agenten genom att köra skriptet med –-avinstallera parameter:

    ./InstallSecurityAgent.sh -–uninstall

## <a name="troubleshooting"></a>Felsökning
Kontrollera distributionens status genom att köra:

    systemctl status ASCIoTAgent.service


## <a name="next-steps"></a>Nästa steg
- Läsa ASC för IoT-tjänsten [översikt](overview.md)
- Läs mer om ASC för IoT [arkitektur](architecture.md)
- Aktivera den [service](quickstart-onboard-iot-hub.md)
- Läs den [vanliga frågor och svar](resources-frequently-asked-questions.md)
- Förstå [säkerhetsaviseringar](concept-security-alerts.md)