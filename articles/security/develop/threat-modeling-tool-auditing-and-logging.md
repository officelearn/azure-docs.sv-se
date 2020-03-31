---
title: Granskning och loggning – Microsoft Threat Modeling Tool - Azure | Microsoft-dokument
description: mildrande åtgärder för hot som exponeras i hotmodelleringsverktyget
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: c9d20b3259cf4ea7af263d5e31145ad372db0c77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68728419"
---
# <a name="security-frame-auditing-and-logging--mitigations"></a>Säkerhetsram: Granskning och loggning | Mitigations 

| Produkt/tjänst | Artikel |
| --------------- | ------- |
| **Dynamics CRM**    | <ul><li>[Identifiera känsliga enheter i din lösning och implementera ändringsgranskning](#sensitive-entities)</li></ul> |
| **Webbprogram** | <ul><li>[Se till att granskning och loggning tillämpas på programmet](#auditing)</li><li>[Se till att loggrotation och separation är på plats](#log-rotation)</li><li>[Se till att programmet inte loggar känsliga användardata](#log-sensitive-data)</li><li>[Se till att gransknings- och loggfiler har begränsad åtkomst](#log-restricted-access)</li><li>[Se till att användarhanteringshändelser loggas](#user-management)</li><li>[Se till att systemet har inbyggt försvar mot missbruk](#inbuilt-defenses)</li><li>[Aktivera diagnostikloggning för webbappar i Azure App Service](#diagnostics-logging)</li></ul> |
| **Databas** | <ul><li>[Kontrollera att inloggningsgranskning är aktiverat på SQL Server](#identify-sensitive-entities)</li><li>[Aktivera hotidentifiering på Azure SQL](#threat-detection)</li></ul> |
| **Azure-lagring** | <ul><li>[Använda Azure Storage Analytics för att granska åtkomsten till Azure Storage](#analytics)</li></ul> |
| **WCF** | <ul><li>[Implementera tillräcklig loggning](#sufficient-logging)</li><li>[Implementera tillräcklig hantering av granskningsfel](#audit-failure-handling)</li></ul> |
| **Webb-API** | <ul><li>[Kontrollera att granskning och loggning tillämpas på webb-API](#logging-web-api)</li></ul> |
| **IoT-fältgateway** | <ul><li>[Se till att lämplig granskning och loggning tillämpas på Fältgateway](#logging-field-gateway)</li></ul> |
| **IoT Cloud Gateway** | <ul><li>[Se till att lämplig granskning och loggning tillämpas på Cloud Gateway](#logging-cloud-gateway)</li></ul> |

## <a name="identify-sensitive-entities-in-your-solution-and-implement-change-auditing"></a><a id="sensitive-entities"></a>Identifiera känsliga enheter i din lösning och implementera ändringsgranskning

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Dynamics CRM | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg**                   | Identifiera entiteter i din lösning som innehåller känsliga data och implementera ändringsgranskning på dessa entiteter och fält |

## <a name="ensure-that-auditing-and-logging-is-enforced-on-the-application"></a><a id="auditing"></a>Se till att granskning och loggning tillämpas på programmet

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg**                   | Aktivera granskning och loggning på alla komponenter. Granskningsloggar bör fånga användarkontexten. Identifiera alla viktiga händelser och logga dessa händelser. Implementera centraliserad loggning |

## <a name="ensure-that-log-rotation-and-separation-are-in-place"></a><a id="log-rotation"></a>Se till att loggrotation och separation är på plats

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg**                   | <p>Loggrotation är en automatiserad process som används vid systemadministration där daterade loggfiler arkiveras. Servrar som kör stora program loggar ofta varje begäran: inför skrymmande loggar är loggrotation ett sätt att begränsa den totala storleken på loggarna samtidigt som analysen av de senaste händelserna tillåts. </p><p>Loggseparation innebär i princip att du måste lagra dina loggfiler på en annan partition som där ditt OS / program körs på för att avvärja en Denial of Service-attack eller nedgradering av ditt program dess prestanda</p>|

## <a name="ensure-that-the-application-does-not-log-sensitive-user-data"></a><a id="log-sensitive-data"></a>Se till att programmet inte loggar känsliga användardata

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg**                   | <p>Kontrollera att du inte loggar några känsliga data som en användare skickar till din webbplats. Kontrollera om det finns avsiktlig loggning samt biverkningar orsakade av designproblem. Exempel på känsliga data är:</p><ul><li>Användarreferenser</li><li>Personnummer eller annan identifierande information</li><li>Kreditkortsnummer eller annan finansiell information</li><li>Hälsoinformation</li><li>Privata nycklar eller andra data som kan användas för att dekryptera krypterad information</li><li>System- eller applikationsinformation som kan användas för att mer effektivt angripa</li></ul>|

## <a name="ensure-that-audit-and-log-files-have-restricted-access"></a><a id="log-restricted-access"></a>Se till att gransknings- och loggfiler har begränsad åtkomst

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg**                   | <p>Kontrollera att åtkomsträttigheterna till loggfiler är korrekt inställda. Programkonton bör ha skrivskyddad åtkomst och operatörer och supportpersonal bör ha skrivskyddad åtkomst efter behov.</p><p>Administratörskonton är de enda konton som ska ha fullständig åtkomst. Kontrollera Windows ACL på loggfiler för att säkerställa att de är ordentligt begränsade:</p><ul><li>Programkonton bör ha skrivskyddad åtkomst</li><li>Operatörer och stödpersonal bör ha skrivskyddad åtkomst efter behov</li><li>Administratörer är de enda konton som ska ha full åtkomst</li></ul>|

## <a name="ensure-that-user-management-events-are-logged"></a><a id="user-management"></a>Se till att användarhanteringshändelser loggas

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg**                   | <p>Se till att programmet övervakar användarhanteringshändelser som lyckade och misslyckade användarinloggningar, återställning av lösenord, lösenordsändringar, kontoutelåsning, användarregistrering. Detta hjälper till att identifiera och reagera på potentiellt misstänkt beteende. Det gör det också möjligt att samla in verksamhetsdata; till exempel för att spåra vem som har åtkomst till programmet</p>|

## <a name="ensure-that-the-system-has-inbuilt-defenses-against-misuse"></a><a id="inbuilt-defenses"></a>Se till att systemet har inbyggt försvar mot missbruk

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg**                   | <p>Kontroller bör finnas på plats som kastar säkerhetsundantag i händelse av missbruk av programmet. T.ex. Om indatavalidering är på plats och en angripare försöker injicera skadlig kod som inte matchar regex, kan ett säkerhetsundantag genereras som kan vara ett tecken på systemmissbruk</p><p>Det rekommenderas till exempel att säkerhetsundantag loggas och åtgärder vidtas för följande problem:</p><ul><li>Indataverifiering</li><li>CSRF kränkningar</li><li>Brute force (övre gräns för antal begäranden per användare per resurs)</li><li>Överträdelser av filuppladdning</li><ul>|

## <a name="enable-diagnostics-logging-for-web-apps-in-azure-app-service"></a><a id="diagnostics-logging"></a>Aktivera diagnostikloggning för webbappar i Azure App Service

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | EnvironmentType - Azure |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | <p>Azure tillhandahåller inbyggd diagnostik för att hjälpa till med felsökning av en App Service-webbapp. Det gäller även API-appar och mobilappar. App Service-webbappar tillhandahåller diagnostikfunktioner för loggningsinformation från både webbservern och webbprogrammet.</p><p>Dessa är logiskt uppdelade i webbserver diagnostik och applikationsdiagnostik</p>|

## <a name="ensure-that-login-auditing-is-enabled-on-sql-server"></a><a id="identify-sensitive-entities"></a>Kontrollera att inloggningsgranskning är aktiverat på SQL Server

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Konfigurera inloggningsgranskning](https://msdn.microsoft.com/library/ms175850.aspx) |
| **Steg** | <p>Granskning av inloggningsgranskning av databasserver måste vara aktiverad för att identifiera/bekräfta lösenordsgissningsattacker. Det är viktigt att fånga misslyckade inloggningsförsök. Att fånga både lyckade och misslyckade inloggningsförsök ger ytterligare fördelar vid kriminaltekniska undersökningar</p>|

## <a name="enable-threat-detection-on-azure-sql"></a><a id="threat-detection"></a>Aktivera hotidentifiering på Azure SQL

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | SQL Azure |
| **Attribut**              | SQL-version - V12 |
| **Referenser**              | [Komma igång med hotidentifiering av SQL Database](https://azure.microsoft.com/documentation/articles/sql-database-threat-detection-get-started/)|
| **Steg** |<p>Hotidentifiering identifierar avvikande databasaktiviteter som anger potentiella säkerhetshot mot databasen. Det ger ett nytt lager av säkerhet, vilket gör det möjligt för kunder att identifiera och svara på potentiella hot när de inträffar genom att tillhandahålla säkerhetsaviseringar på avvikande aktiviteter.</p><p>Användare kan utforska misstänkta händelser med hjälp av Azure SQL Database Auditing för att avgöra om de är ett resultat av ett försök att komma åt, bryta eller utnyttja data i databasen.</p><p>Hotidentifiering gör det enkelt att hantera potentiella hot mot databasen utan att behöva vara säkerhetsexpert eller hantera avancerade säkerhetsövervakningssystem</p>|

## <a name="use-azure-storage-analytics-to-audit-access-of-azure-storage"></a><a id="analytics"></a>Använda Azure Storage Analytics för att granska åtkomsten till Azure Storage

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Storage | 
| **SDL-fas**               | Distribution |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt |
| **Referenser**              | [Använda Storage Analytics för att övervaka auktoriseringstyp](https://azure.microsoft.com/documentation/articles/storage-security-guide/#storage-analytics) |
| **Steg** | <p>För varje lagringskonto kan man aktivera Azure Storage Analytics för att utföra loggnings- och lagringsstatistik. Lagringsanalysloggarna innehåller viktig information, till exempel autentiseringsmetod som används av någon när de kommer åt lagring.</p><p>Detta kan vara till stor hjälp om du är tätt bevakning tillgång till lagring. I Blob Storage kan du till exempel ställa in alla behållare till privata och implementera användningen av en SAS-tjänst i hela programmen. Sedan kan du kontrollera loggarna regelbundet för att se om dina blobbar nås med hjälp av lagringskontonycklarna, vilket kan tyda på ett säkerhetsbrott eller om blobbar är offentliga men de bör inte vara det.</p>|

## <a name="implement-sufficient-logging"></a><a id="sufficient-logging"></a>Implementera tillräcklig loggning

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | WCF | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | .NET Framework |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify Kungariket](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_insufficient_logging) |
| **Steg** | <p>Avsaknaden av en ordentlig verifieringskedja efter en säkerhetsincident kan försvåra kriminaltekniska insatser. Windows Communication Foundation (WCF) erbjuder möjligheten att logga lyckade och/eller misslyckade autentiseringsförsök.</p><p>Loggning misslyckades autentiseringsförsök kan varna administratörer för potentiella brute-force attacker. På samma sätt kan loggning av lyckade autentiseringshändelser ge en användbar granskningsspårning när ett legitimt konto komprometteras. Aktivera WCF:s säkerhetsgranskningsfunktion för tjänsten |

### <a name="example"></a>Exempel
Följande är en exempelkonfiguration med granskning aktiverad
```
<system.serviceModel>
    <behaviors>
        <serviceBehaviors>
            <behavior name=""NewBehavior"">
                <serviceSecurityAudit auditLogLocation=""Default""
                suppressAuditFailure=""false"" 
                serviceAuthorizationAuditLevel=""SuccessAndFailure""
                messageAuthenticationAuditLevel=""SuccessAndFailure"" />
                ...
            </behavior>
        </servicebehaviors>
    </behaviors>
</system.serviceModel>
```

## <a name="implement-sufficient-audit-failure-handling"></a><a id="audit-failure-handling"></a>Implementera tillräcklig hantering av granskningsfel

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | WCF | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | .NET Framework |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify Kungariket](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_insufficient_audit_failure_handling) |
| **Steg** | <p>Utvecklad lösning är konfigurerad för att inte generera ett undantag när den inte skriver till en granskningslogg. Om WCF är konfigurerat för att inte utlösa ett undantag när det inte går att skriva till en granskningslogg, meddelas programmet inte om felet och granskning av kritiska säkerhetshändelser kanske inte inträffar.</p>|

### <a name="example"></a>Exempel
Elementet `<behavior/>` i WCF-konfigurationsfilen nedan instruerar WCF att inte meddela programmet när WCF inte skriver till en granskningslogg.
```
<behaviors>
    <serviceBehaviors>
        <behavior name="NewBehavior">
            <serviceSecurityAudit auditLogLocation="Application"
            suppressAuditFailure="true"
            serviceAuthorizationAuditLevel="Success"
            messageAuthenticationAuditLevel="Success" />
        </behavior>
    </serviceBehaviors>
</behaviors>
```
Konfigurera WCF så att programmet meddelas när det inte går att skriva till en granskningslogg. Programmet bör ha ett alternativt meddelandeschema för att varna organisationen om att granskningshistorik inte underhålls. 

## <a name="ensure-that-auditing-and-logging-is-enforced-on-web-api"></a><a id="logging-web-api"></a>Kontrollera att granskning och loggning tillämpas på webb-API

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webb-API | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Aktivera granskning och loggning på webb-API:er. Granskningsloggar bör fånga användarkontexten. Identifiera alla viktiga händelser och logga dessa händelser. Implementera centraliserad loggning |

## <a name="ensure-that-appropriate-auditing-and-logging-is-enforced-on-field-gateway"></a><a id="logging-field-gateway"></a>Se till att lämplig granskning och loggning tillämpas på Fältgateway

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | IoT-fältgateway | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | <p>När flera enheter ansluter till en Fältgateway ska du se till att anslutningsförsök och autentiseringsstatus (framgång eller fel) för enskilda enheter loggas och underhålls på Fältgatewayen.</p><p>I de fall där Field Gateway underhåller IoT Hub-autentiseringsuppgifterna för enskilda enheter, se också till att granskning utförs när dessa autentiseringsuppgifter hämtas. Utveckla en process för att regelbundet överföra loggarna till Azure IoT Hub/storage för långsiktig kvarhållning.</p> |

## <a name="ensure-that-appropriate-auditing-and-logging-is-enforced-on-cloud-gateway"></a><a id="logging-cloud-gateway"></a>Se till att lämplig granskning och loggning tillämpas på Cloud Gateway

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | IoT Cloud Gateway | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Introduktion till övervakning av IoT Hub-operationer](https://azure.microsoft.com/documentation/articles/iot-hub-operations-monitoring/) |
| **Steg** | <p>Design för insamling och lagring av granskningsdata som samlats in via IoT Hub Operations Monitoring. Aktivera följande övervakningskategorier:</p><ul><li>Åtgärder för enhetsidentitet</li><li>Kommunikation mellan enheter och moln</li><li>Kommunikation mellan molnet och enheten</li><li>Anslutningar</li><li>Filuppladdningar</li></ul>|