---
title: Granskning och loggning - Microsoft Threat Modeling Tool – Azure | Microsoft Docs
description: åtgärder för hot som exponeras i Threat Modeling Tool
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: 1d67f981991796b81ba3ab6540631e6d62be8077
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2019
ms.locfileid: "55092185"
---
# <a name="security-frame-auditing-and-logging--mitigations"></a>Security ram: Granskning och loggning | Åtgärder 
| Produkt/tjänst | Artikel |
| --------------- | ------- |
| **Dynamics CRM**    | <ul><li>[Identifiera känsliga entiteter i din lösning och implementering av en ändring av auktoriseringsprincip](#sensitive-entities)</li></ul> |
| **Webbprogram** | <ul><li>[Se till att granskning och loggning är aktiverad för programmet](#auditing)</li><li>[Kontrollera att loggrotation och uppdelning är på plats](#log-rotation)</li><li>[Kontrollera att programmet inte logga känslig information](#log-sensitive-data)</li><li>[Se till att gransknings- och loggfiler har begränsad åtkomst](#log-restricted-access)</li><li>[Kontrollera att händelser för hantering av användare är inloggad](#user-management)</li><li>[Kontrollera att systemet har inbyggd försvar mot missbruk](#inbuilt-defenses)</li><li>[Aktivera diagnostikloggning för webbappar i Azure App Service](#diagnostics-logging)</li></ul> |
| **Databas** | <ul><li>[Kontrollera att inloggningen granskning är aktiverat på SQL Server](#identify-sensitive-entities)</li><li>[Aktivera hotidentifiering i Azure SQL](#threat-detection)</li></ul> |
| **Azure Storage** | <ul><li>[Med Azure Storage Analytics kan du granska åtkomst i Azure Storage](#analytics)</li></ul> |
| **WCF** | <ul><li>[Implementera tillräcklig loggning](#sufficient-logging)</li><li>[Implementera tillräckligt med gransknings-fel och hantera](#audit-failure-handling)</li></ul> |
| **Webb-API** | <ul><li>[Se till att granskning och loggning är aktiverad för webb-API](#logging-web-api)</li></ul> |
| **IoT Field Gateway** | <ul><li>[Se till att lämpliga granskning och loggning är aktiverad för fält-Gateway](#logging-field-gateway)</li></ul> |
| **IoT Cloud Gateway** | <ul><li>[Se till att lämpliga granskning och loggning är aktiverad för Molngatewayen](#logging-cloud-gateway)</li></ul> |

## <a id="sensitive-entities"></a>Identifiera känsliga entiteter i din lösning och implementering av en ändring av auktoriseringsprincip

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Dynamics CRM | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg**                   | Identifiera enheter i din lösning som innehåller känsliga data och implementering av en ändring av auktoriseringsprincip på dessa entiteter och fält |

## <a id="auditing"></a>Se till att granskning och loggning är aktiverad för programmet

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg**                   | Aktivera granskning och loggning på samtliga komponenter. Granskningsloggar bör läsa in användarkontext. Identifiera alla viktiga händelser och loggar dessa händelser. Implementera centraliserad loggning |

## <a id="log-rotation"></a>Kontrollera att loggrotation och uppdelning är på plats

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg**                   | <p>Loggrotation är en automatiserad process som används i systemadministration daterat loggfiler arkiveras. Servrar som kör stora program ofta logga varje begäran: om skrymmande loggar loggrotationen är ett sätt att begränsa den totala storleken på loggarna samtidigt som fortfarande analys av de senaste händelserna. </p><p>Logga uppdelning i princip innebär att du att lagra loggen filer på en annan partition som där din OS eller ditt program körs för att motverka en DoS-angrepp eller nedgradera programmets prestanda</p>|

## <a id="log-sensitive-data"></a>Kontrollera att programmet inte logga känslig information

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg**                   | <p>Kontrollera att du inte logga alla känsliga data som en användare skickar till din webbplats. Sök efter avsiktliga loggning samt sidoeffekter på grund av problem med design. Exempel på känsliga data:</p><ul><li>Användarautentiseringsuppgifter</li><li>E-postadress eller annan information</li><li>Kreditkortsnummer eller andra finansiell information</li><li>Information om hälsa</li><li>Privata nycklar eller andra data som kan användas för att dekryptera krypterad information</li><li>System- eller information som kan användas för effektivare angrepp programmet</li></ul>|

## <a id="log-restricted-access"></a>Se till att gransknings- och loggfiler har begränsad åtkomst

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg**                   | <p>Kontrollera åtkomsträttigheter till loggfiler är inställda på rätt sätt. Programmet konton bör ha lässkyddad och operatörer och supportpersonal ha skrivskyddad åtkomst efter behov.</p><p>Administratörer-konton är de enda kontona som ska ha fullständig åtkomst. Kontrollera Windows ACL på loggfilerna för att säkerställa att de är korrekt begränsade:</p><ul><li>Konton som programmet ska ha lässkyddad åtkomst</li><li>Operatorer och supportpersonal ha skrivskyddad åtkomst efter behov</li><li>Administratörer är de enda kontona som ska ha fullständig åtkomst</li></ul>|

## <a id="user-management"></a>Kontrollera att händelser för hantering av användare är inloggad

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg**                   | <p>Se till att programmet övervakar användaren management händelser, till exempel lyckade och misslyckade användarinloggningar, återställning av lösenord, lösenordsändringar, kontoutelåsning, användarregistrering. Gör det här hjälper dig att identifiera och reagera på potentiellt misstänkt beteende. Gör det också möjligt att samla in driftdata; till exempel för att spåra vem som har åtkomst till programmet</p>|

## <a id="inbuilt-defenses"></a>Kontrollera att systemet har inbyggd försvar mot missbruk

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg**                   | <p>Kontroller bör finnas på plats som utlöser säkerhetsundantag när det gäller program missbruk. T.ex. om verifiering av indata är på plats och en angripare försöker mata in skadlig kod som inte matchar regex, kan ett säkerhetsundantag uppkomma som kan vara ett preliminärt system missbrukas</p><p>Exempelvis bör ha security undantag som loggats och åtgärder som vidtas för följande problem:</p><ul><li>Indataverifiering</li><li>CSRF överträdelser</li><li>Brute force (övre gräns för antal begäranden per användare per resurs)</li><li>Ladda upp filöverträdelser</li><ul>|

## <a id="diagnostics-logging"></a>Aktivera diagnostikloggning för webbappar i Azure App Service

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | EnvironmentType - Azure |
| **Referenser**              | Gäller inte  |
| **Steg** | <p>Azure har inbyggd diagnostik som hjälper till med felsökning av webbappar i App Service. Det gäller även för API-appar och mobilappar. App Service web apps ger diagnostisk funktionalitet för att logga information från både webbservern och webbprogrammet.</p><p>Dessa är logiskt indelade i web serverdiagnostik- och programdiagnostik</p>|

## <a id="identify-sensitive-entities"></a>Kontrollera att inloggningen granskning är aktiverat på SQL Server

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Konfigurera granskning för inloggning](https://msdn.microsoft.com/library/ms175850.aspx) |
| **Steg** | <p>Granskning av Database Server inloggningen måste aktiveras att identifiera/Bekräfta lösenord gissa attacker. Det är viktigt att samla in misslyckade inloggningsförsök. Samla in både lyckade och misslyckade inloggningsförsök ger extra förmån under kriminaltekniska undersökningar</p>|

## <a id="threat-detection"></a>Aktivera hotidentifiering i Azure SQL

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | SQL Azure |
| **Attribut**              | SQL Version - V12 |
| **Referenser**              | [Kom igång med SQL Database Threat Detection](https://azure.microsoft.com/documentation/articles/sql-database-threat-detection-get-started/)|
| **Steg** |<p>Hotidentifiering identifierar avvikande databasaktiviteter som indikerar potentiella säkerhetshot mot databasen. Det ger ett nytt lager av säkerhet som ger kunder möjlighet att upptäcka och svara på potentiella hot allteftersom de sker genom att tillhandahålla säkerhetsaviseringar om avvikande aktiviteter.</p><p>Användare kan utforska misstänkta händelser med Azure SQL Database-granskning för att avgöra om de är följden av ett försök att komma åt, tränga in i eller utnyttja data i databasen.</p><p>Hotidentifiering gör det enkelt att hantera potentiella hot mot databasen utan att behöva vara säkerhetsexpert eller hantera avancerade säkerhetsövervakningssystem</p>|

## <a id="analytics"></a>Med Azure Storage Analytics kan du granska åtkomst i Azure Storage

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Storage | 
| **SDL fas**               | Distribution |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte |
| **Referenser**              | [Använda Storage Analytics för att övervaka auktoriseringstyp](https://azure.microsoft.com/documentation/articles/storage-security-guide/#storage-analytics) |
| **Steg** | <p>För varje storage-konto kan en Azure Storage Analytics att utföra loggning och lagra måttdata. Storage analytics-loggarna ger viktig information, till exempel autentiseringsmetod som används av någon när de har åtkomst till lagring.</p><p>Detta kan vara väldigt användbart om du nära skyddar åtkomst till lagring. I Blob Storage kan du exempelvis anger alla behållare som privat och implementera användningen av en SAS-tjänst i dina program. Du kan kontrollera loggarna regelbundet för att se om dina blobar ska hämtas med hjälp av lagringskontonycklar, vilket kan tyda på ett sekretessbrott, eller om att blobarna är offentliga men de får inte vara.</p>|

## <a id="sufficient-logging"></a>Implementera tillräcklig loggning

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | WCF | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | .NET Framework |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [spikning Nordirland](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_insufficient_logging) |
| **Steg** | <p>Bristen på en korrekt granskningsloggen när en säkerhetsincident kan hindra kriminaltekniska arbete. Windows Communication Foundation (WCF) ger dig möjlighet att logga lyckade och/eller misslyckade autentiseringsförsök.</p><p>Loggning av misslyckade autentiseringsförsök kan varna administratörer av potentiella brute force-attacker. På samma sätt kan kan logga händelser för lyckad autentisering ge en användbar granskningsloggen när en legitim konto komprometteras. Aktivera granskningsfunktionen i WCF'S service säkerhet |

### <a name="example"></a>Exempel
Följande är ett exempel på en konfiguration med granskning är aktiverat
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

## <a id="audit-failure-handling"></a>Implementera tillräckligt med gransknings-fel och hantera

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | WCF | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | .NET Framework |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [spikning Nordirland](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_insufficient_audit_failure_handling) |
| **Steg** | <p>Utvecklade lösningen konfigureras inte för att generera ett undantag när det inte går att skriva till en granskningslogg. Om WCF är inte konfigurerad att utlöser ett undantag när det inte går att skriva till en granskningslogg, programmet kommer inte att meddelas om felet och granskning av viktiga säkerhetshändelser inte upptäcks.</p>|

### <a name="example"></a>Exempel
Den `<behavior/>` element i WCF-konfigurationsfilen nedan instruerar WCF inte meddela programmet när WCF inte kan skriva till en granskningslogg.
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
Konfigurera WCF för att meddela programmet när det inte går att skriva till en granskningslogg. Programmet bör ha ett annat Meddelandeschema på plats till den organisation som spårning som inte bevaras avisering. 

## <a id="logging-web-api"></a>Se till att granskning och loggning är aktiverad för webb-API

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webb-API | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | Aktivera granskning och loggning på webb-API: er. Granskningsloggar bör läsa in användarkontext. Identifiera alla viktiga händelser och loggar dessa händelser. Implementera centraliserad loggning |

## <a id="logging-field-gateway"></a>Se till att lämpliga granskning och loggning är aktiverad för fält-Gateway

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | IoT Field Gateway | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | <p>När flera enheter ansluter till en fält-Gateway, kontrollera att anslutningsförsök och autentiseringsstatus (lyckade eller misslyckade) för enskilda enheter loggas och finns på fält-Gateway.</p><p>Kontrollera också att granskning utförs när dessa autentiseringsuppgifter hämtas i fall där fält-Gateway för att underhålla IoT Hub-autentiseringsuppgifter för enskilda enheter. Utveckla en process för att regelbundet överför loggarna till Azure IoT Hub/storage för lång kvarhållningstid.</p> |

## <a id="logging-cloud-gateway"></a>Se till att lämpliga granskning och loggning är aktiverad för Molngatewayen

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | IoT Cloud Gateway | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Introduktion till IoT Hub åtgärdsövervakning](https://azure.microsoft.com/documentation/articles/iot-hub-operations-monitoring/) |
| **Steg** | <p>Design för att samla in och lagra granskningsdata som samlats in via IoT Hub Operations Monitoring. Aktivera övervakning följande kategorier:</p><ul><li>Identitet åtgärder</li><li>Enhet-till-molnet kommunikation</li><li>Moln-till-enhet-kommunikation</li><li>Anslutningar</li><li>Filöverföringar</li></ul>|