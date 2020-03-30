---
title: Felsökningsguide för Azure Security Center för IoT Linux-säkerhetsagent| Microsoft-dokument
description: Felsöka att arbeta med Azure Security Center för IoT-säkerhetsagenter för Linux.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 55c8d3b6-3126-4246-8d07-ef88fe5ea84f
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2019
ms.author: mlottner
ms.openlocfilehash: 7f3bd4be3ef927f73643146a457bc551ef86a450
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68600571"
---
# <a name="security-agent-troubleshoot-guide-linux"></a>Felsöknings guide för säkerhetsagent (Linux)

I den här artikeln beskrivs hur du löser potentiella problem i startprocessen för säkerhetsagenter.

Azure Security Center för IoT-agenten startar själv direkt efter installationen. Agentstartprocessen inkluderar läsning av lokal konfiguration, anslutning till Azure IoT Hub och hämtning av fjärrtvillingkonfigurationen. Om något av dessa steg inte fungerar i något av dessa steg kan säkerhetsagenten misslyckas.

I den här felsökningsguiden får du lära dig hur du:
> [!div class="checklist"]
> * Verifiera om säkerhetsagenten körs
> * Hämta fel på säkerhetsagenten
> * Förstå och åtgärda fel på säkerhetsagenter 

## <a name="validate-if-the-security-agent-is-running"></a>Verifiera om säkerhetsagenten körs

1. Om du vill validera är säkerhetsagenten körs, vänta några minuter efter installation av agenten och och köra följande kommando. 
     <br>

    **C-agent**
    ```bash
    grep "ASC for IoT Agent initialized" /var/log/syslog
    ```
    **C# agent**
    ```bash
    grep "Agent is initialized!" /var/log/syslog
    ```
2. Om kommandot returnerar en tom rad kunde säkerhetsagenten inte startas.    

## <a name="force-stop-the-security-agent"></a>Force stoppa säkerhetsagenten 
Om säkerhetsagenten inte kan starta, stoppa agenten med följande kommando och fortsätt sedan till feltabellen nedan:

```bash
systemctl stop ASCIoTAgent.service
```
## <a name="get-security-agent-errors"></a>Hämta fel på säkerhetsagenten
1. Hämta säkerhetsagentfel genom att köra följande kommando:
    ```bash
    grep ASCIoTAgent /var/log/syslog
    ```
2. Kommandot hämta säkerhetsagentfel hämtar alla loggar som skapats av Azure Security Center för IoT-agenten. Använd följande tabell för att förstå felen och vidta rätt åtgärder för reparation. 

> [!Note]
> Felloggar visas i kronologisk ordning. Se till att notera tidsstämpeln för varje fel för att hjälpa din reparation. 

## <a name="restart-the-agent"></a>Starta om agenten

1. När du har lokaliserat och åtgärdat ett säkerhetsagentfel försöker du starta om agenten genom att köra följande kommando. 
    ```bash
    systemctl restart ASCIoTAgent.service
    ```
1. Upprepa föregående process för att hämta stopp och hämta felen om agenten fortsätter att misslyckas startprocessen. 

## <a name="understand-security-agent-errors"></a>Förstå säkerhetsagentfel

De flesta säkerhetsagentfel visas i följande format: 
```
Azure Security Center for IoT agent encountered an error! Error in: {Error Code}, reason: {Error sub code}, extra details: {error specific details}
```
| Felkod | Felunderkod | Felinformation | Åtgärda C | Åtgärda C # |
|:-----------|:---------------|:--------|:------------|:------------|
| Lokal konfiguration | Konfiguration som saknas saknas | En konfiguration saknas i den lokala konfigurationsfilen. Felmeddelandet ska ange vilken nyckel som saknas. | Lägg till den saknade nyckeln i filen /var/LocalConfiguration.json, se [cs-localconfig-reference](azure-iot-security-local-configuration-c.md) för mer information.| Lägg till den saknade nyckeln i filen General.config, se [c#-localconfig-reference](azure-iot-security-local-configuration-csharp.md) för mer information. |
| Lokal konfiguration | Cant Parse Konfiguration | Det går inte att tolka ett konfigurationsvärde. Felmeddelandet ska ange vilken nyckel som inte kan tolkas. Det går inte att tolka ett konfigurationsvärde antingen på grund av att värdet inte är i den förväntade typen eller om värdet ligger utanför intervallet. | Åtgärda värdet för nyckeln i filen /var/LocalConfiguration.json så att den matchar lokalkonfigurationsschemat finns i [c#-localconfig-reference](azure-iot-security-local-configuration-csharp.md) för mer information. |  Åtgärda värdet för nyckeln i Filen General.config så att den matchar schemat, se [cs-localconfig-reference](azure-iot-security-local-configuration-c.md) för mer information.|
| Lokal konfiguration | Filformat | Det gick inte att tolka konfigurationsfilen. | Konfigurationsfilen är skadad, hämtar agenten och installerar om. | |
| Fjärrkonfiguration | Timeout | Agenten kunde inte hämta azureiotsecurity-modulen tvilling inom timeout-perioden. | Kontrollera att autentiseringskonfigurationen är korrekt och försök igen. | Agenten kunde inte hämta azureiotsecurity-modulen tvilling inom timeout-perioden. | Kontrollera att autentiseringskonfigurationen är korrekt och försök igen. |
| Autentisering | Filen finns inte | Filen i den angivna sökvägen finns inte. | Kontrollera att filen finns i den angivna sökvägen eller gå till filen **LocalConfiguration.json** och ändra **FilePath-konfigurationen.** | Kontrollera att filen finns i den angivna sökvägen eller gå till filen **Authentication.config** och ändra **filePath-konfigurationen.**|
| Autentisering | Tillstånd för fil | Agenten har inte tillräcklig behörighet för att öppna filen. | Ge **asciotagen-användaren** läsbehörighet för filen i den angivna sökvägen. | Kontrollera att filen är tillgänglig. |
| Autentisering | Filformat | Den angivna filen är inte i rätt format. | Kontrollera att filen är i rätt format. Filtyperna som stöds är PFX och .pem. | Kontrollera att filen är en giltig certifikatfil. |
| Autentisering | Behörighet saknas | Agenten kunde inte autentisera mot IoT Hub med de angivna autentiseringsuppgifterna. | Verifiera autentiseringskonfigurationen i LocalConfiguration-filen, gå igenom autentiseringskonfigurationen och se till att alla detaljer är korrekta, verifiera att hemligheten i filen matchar den autentiserade identiteten. | Verifiera autentiseringskonfigurationen i Authentication.config, gå igenom autentiseringskonfigurationen och se till att alla detaljer är korrekta och verifiera sedan att hemligheten i filen matchar den autentiserade identiteten.
| Autentisering | Hittades inte | Enheten / modulen hittades. | Verifiera autentiseringskonfiguration - kontrollera att värdnamnet är korrekt, enheten finns i IoT Hub och har en azureiotsecurity twin-modul. |  Verifiera autentiseringskonfiguration - kontrollera att värdnamnet är korrekt, enheten finns i IoT Hub och har en azureiotsecurity twin-modul. |
| Autentisering | Konfiguration som saknas saknas | En konfiguration saknas i filen *Authentication.config.* Felmeddelandet ska ange vilken nyckel som saknas. | Lägg till den saknade nyckeln i filen *LocalConfiguration.json.*| Lägg till den saknade nyckeln i filen *Authentication.config* finns i [c#-localconfig-reference](azure-iot-security-local-configuration-csharp.md) för mer information. |
| Autentisering | Cant Parse Konfiguration | Det går inte att tolka ett konfigurationsvärde. Felmeddelandet ska ange vilken nyckel som inte kan tolkas. Ett konfigurationsvärde kan inte tolkas eftersom värdet antingen inte är av den förväntade typen eller om värdet ligger utanför intervallet. |Åtgärda värdet för nyckeln i filen **LocalConfiguration.json.** |Åtgärda värdet för nyckeln i **Authentication.config-filen** så att schemat matchars, se [cs-localconfig-reference](azure-iot-security-local-configuration-c.md) för mer information.|
|

## <a name="restart-the-agent"></a>Starta om agenten
1. När du har lokaliserat och åtgärdat ett säkerhetsagentfel startar du om agenten genom att köra följande kommando:

    ```bash
    systemctl restart ASCIoTAgent.service
    ```
2. Om det behövs upprepar du föregående processer för att tvinga stoppa agenten och hämta felen om agenten fortsätter att misslyckas med startprocessen. 

## <a name="next-steps"></a>Nästa steg
- Läs [översikt](overview.md) över Azure Security Center for IoT-tjänsten
- Läs mer om Azure Security Center för [IoT-arkitektur](architecture.md)
- Aktivera Azure Security Center för [IoT-tjänsten](quickstart-onboard-iot-hub.md)
- Läs [vanliga frågor och svar](resources-frequently-asked-questions.md) om Azure Security Center för IoT-tjänsten
- Lär dig hur du kommer åt [rådata om säkerhet](how-to-security-data-access.md)
- Förstå [rekommendationer](concept-recommendations.md)
- Förstå [säkerhetsaviseringar](concept-security-alerts.md)
