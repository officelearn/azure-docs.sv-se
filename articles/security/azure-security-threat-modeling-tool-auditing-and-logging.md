---
title: Granskning och loggning - Microsoft Threat Modeling verktyget - Azure | Microsoft Docs
description: ändringar för hot som exponeras i verktyget Modeling hot
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: 8837dfaf156e5a4d07598f2c58694663a9ff5580
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37029989"
---
# <a name="security-frame-auditing-and-logging--mitigations"></a>Säkerhet ram: Granskning och loggning | Åtgärder 
| Produkter eller tjänster | Artikel |
| --------------- | ------- |
| **Dynamics CRM**    | <ul><li>[Identifiera känsliga enheter i din lösning och implementera granskning av ändringar](#sensitive-entities)</li></ul> |
| **Webbprogram** | <ul><li>[Se till att granskning och loggning är aktiverad för programmet](#auditing)</li><li>[Se till att loggrotationen och uppdelning](#log-rotation)</li><li>[Kontrollera att programmet inte logga känslig information](#log-sensitive-data)</li><li>[Se till att granskning och loggfiler har begränsad åtkomst](#log-restricted-access)</li><li>[Se till att användaren Management händelser som loggas](#user-management)</li><li>[Se till att systemet har inbyggda försvar mot missbruk](#inbuilt-defenses)</li><li>[Aktivera diagnostikloggning för web apps i Azure App Service](#diagnostics-logging)</li></ul> |
| **Databas** | <ul><li>[Kontrollera att inloggningen granskning är aktiverat på SQL Server](#identify-sensitive-entities)</li><li>[Aktivera hotidentifiering på Azure SQL](#threat-detection)</li></ul> |
| **Azure Storage** | <ul><li>[Använda Azure Storage Analytics för att granska åtkomsten till Azure Storage](#analytics)</li></ul> |
| **WCF** | <ul><li>[Implementera tillräcklig loggning](#sufficient-logging)</li><li>[Implementera hantering av tillräcklig Audit fel](#audit-failure-handling)</li></ul> |
| **Webb-API** | <ul><li>[Se till att granskning och loggning är aktiverad för webb-API](#logging-web-api)</li></ul> |
| **Fältet för IoT-Gateway** | <ul><li>[Se till att lämpliga granskning och loggning är aktiverad för fältet Gateway](#logging-field-gateway)</li></ul> |
| **Gateway för IoT-moln** | <ul><li>[Se till att lämpliga granskning och loggning är aktiverad för Gateway för moln](#logging-cloud-gateway)</li></ul> |

## <a id="sensitive-entities"></a>Identifiera känsliga enheter i din lösning och implementera granskning av ändringar

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Dynamics CRM | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg**                   | Identifiera enheter i din lösning som innehåller känsliga data och implementera granskning av ändringar på de entiteter och fält |

## <a id="auditing"></a>Se till att granskning och loggning är aktiverad för programmet

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg**                   | Aktivera granskning och loggning på alla komponenter. Granskningsloggar ska avbilda användarkontext. Identifiera alla viktiga händelser och loggar dessa händelser. Implementera centraliserad loggning |

## <a id="log-rotation"></a>Se till att loggrotationen och uppdelning

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg**                   | <p>Loggrotationen är en automatiserad process som används i systemadministration datum loggfiler arkiveras. Servrar som kör stora program ofta logga varje begäran: i händelse av skrymmande loggar loggrotationen är ett sätt att begränsa den totala storleken på loggfilerna samtidigt som analys av nya händelser. </p><p>Logga uppdelning i praktiken innebär det att du har för att lagra loggen filer på en annan partition som där OS/tillämpningsprogrammet körs för att motverka en Denial of service-attack eller nedgradering av ditt program dess prestanda</p>|

## <a id="log-sensitive-data"></a>Kontrollera att programmet inte logga känslig information

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg**                   | <p>Kontrollera att du inte logga alla känsliga data som en användare skickar till din webbplats. Sök efter avsiktliga loggning samt sidoeffekter på grund av problem med design. Exempel på känsliga data:</p><ul><li>Användarens autentiseringsuppgifter</li><li>Personnummer eller annan information</li><li>Kreditkortsnummer eller andra ekonomiska uppgifter</li><li>Information om hälsa</li><li>Privata nycklar eller andra data som kan användas för att dekryptera krypterade information</li><li>System- eller information som kan användas för effektivare angrepp programmet</li></ul>|

## <a id="log-restricted-access"></a>Se till att granskning och loggfiler har begränsad åtkomst

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg**                   | <p>Kontrollera att åtkomstbehörigheter till loggfiler är korrekt inställda. Programmet konton måste ha lässkyddad åtkomst och operatörer och supporttekniker ska ha läsåtkomst efter behov.</p><p>Administratörer-konton är de enda kontona som ska ha fullständig åtkomst. Kontrollera Windows ACL på loggfilerna för att säkerställa att de är korrekt begränsade:</p><ul><li>Konton som programmet ska ha lässkyddad åtkomst</li><li>Operatorer och supporttekniker ska ha läsåtkomst efter behov</li><li>Administratörer är de enda kontona som ska ha fullständig åtkomst</li></ul>|

## <a id="user-management"></a>Se till att användaren Management händelser som loggas

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg**                   | <p>Se till att programmet övervakar användaren management händelser, till exempel lyckade och misslyckade användarinloggningar återställning av lösenord, lösenordsändringar, kontoutelåsning, användarregistrering. Gör detta bidrar till att upptäcka och ta hänsyn till potentiellt misstänkt beteende. Det gör det också möjligt att samla in data för åtgärder. till exempel för att spåra vem som har åtkomst till programmet</p>|

## <a id="inbuilt-defenses"></a>Se till att systemet har inbyggda försvar mot missbruk

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg**                   | <p>Kontroller ska finnas som utlösa säkerhetsundantag vid missbruk av programmet. T.ex. om verifiering av indata är på plats och en angripare försöker att mata in skadlig kod som inte matchar regex, kan ett säkerhetsundantag uppkomma som kan vara ett preliminärt system missbrukas</p><p>Exempelvis bör ha säkerhetsundantag som loggats och åtgärder som vidtas för följande problem:</p><ul><li>Indataverifiering</li><li>CSRF överträdelser</li><li>Brute force (övre gräns för antalet begäranden per användare och resursen)</li><li>Överför filöverträdelser</li><ul>|

## <a id="diagnostics-logging"></a>Aktivera diagnostikloggning för web apps i Azure App Service

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **attribut**              | EnvironmentType – Azure |
| **Referenser**              | Gäller inte  |
| **Steg** | <p>Azure har inbyggd diagnostik som hjälper till med felsökning av webbappar i App Service. Det gäller även för API apps och mobilappar. App Service web apps ange diagnostikfunktion för att logga information från både webbservern och webbprogrammet.</p><p>Dessa är logiskt indelade i web serverdiagnostik- och programdiagnostik</p>|

## <a id="identify-sensitive-entities"></a>Kontrollera att inloggningen granskning är aktiverat på SQL Server

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **attribut**              | Gäller inte  |
| **Referenser**              | [Konfigurera granskning för inloggning](https://msdn.microsoft.com/library/ms175850.aspx) |
| **Steg** | <p>Granskning för databasen Server inloggningen måste aktiveras att identifiera/Bekräfta lösenord för att gissa attacker. Det är viktigt att samla in misslyckade inloggningsförsök. Samla in både lyckade och misslyckade inloggningsförsök ger extra förmån vid kriminaltekniska undersökningar</p>|

## <a id="threat-detection"></a>Aktivera hotidentifiering på Azure SQL

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | SQL Azure |
| **attribut**              | SQL Version - V12 |
| **Referenser**              | [Kom igång med Hotidentifiering för SQL-databas](https://azure.microsoft.com/documentation/articles/sql-database-threat-detection-get-started/)|
| **Steg** |<p>Hotidentifiering identifierar avvikande databasaktiviteter som indikerar potentiella säkerhetshot mot databasen. Det ger ett nytt lager av säkerhet som ger kunder möjlighet att identifiera och svara på potentiella hot allteftersom de sker genom att tillhandahålla säkerhetsaviseringar på avvikande aktiviteter.</p><p>Användare kan utforska misstänkta händelser med Azure SQL Database Auditing för att avgöra om de härrör från ett försök att komma åt, bryta mot eller utnyttja data i databasen.</p><p>Hotidentifiering gör det enkelt att adressen potentiella hot mot databasen utan att behöva vara en expert säkerhet eller hantera avancerade system för säkerhetsövervakning</p>|

## <a id="analytics"></a>Använda Azure Storage Analytics för att granska åtkomsten till Azure Storage

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Storage | 
| **SDL fas**               | Distribution |  
| **Tillämpliga tekniker** | Generisk |
| **attribut**              | Gäller inte |
| **Referenser**              | [Använda Storage Analytics för att övervaka tillstånd typ](https://azure.microsoft.com/documentation/articles/storage-security-guide/#storage-analytics) |
| **Steg** | <p>För varje lagringskonto aktivera en Azure Storage Analytics att utföra loggning och lagra data för mått. Storage analytics loggar innehåller viktig information, till exempel autentiseringsmetod som används av någon när de har åtkomst till lagring.</p><p>Det kan vara mycket användbart om du nära skyddar åtkomst till lagring. I Blob Storage du exempelvis anger alla behållare som privat och implementerar en SAS-tjänst i dina program. Du kan kontrollera loggarna regelbundet för att se om dina blobbar används med lagringskontonycklar, vilket kan tyda på ett sekretessbrott, eller om blobbarna är offentliga men de får inte vara.</p>|

## <a id="sufficient-logging"></a>Implementera tillräcklig loggning

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | WCF | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | .NET framework |
| **attribut**              | Gäller inte  |
| **Referenser**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [spikning kungariket](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_insufficient_logging) |
| **Steg** | <p>Bristen på en korrekt verifieringskedja efter en säkerhetsincident kan hindra kriminaltekniska arbete. Windows Communication Foundation (WCF) ger dig möjlighet att logga lyckade eller misslyckade autentiseringsförsök.</p><p>Loggning av misslyckade autentiseringsförsök Varna administratörer för potentiella brute force-attacker. På liknande sätt kan loggning av händelser för lyckad autentisering ge en användbar verifieringskedja när legitima konto äventyras. Aktivera granskningsfunktionen i WCF'S service säkerhet |

### <a name="example"></a>Exempel
Följande är ett exempel på konfiguration när granskning är aktiverat
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

## <a id="audit-failure-handling"></a>Implementera hantering av tillräcklig Audit fel

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | WCF | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | .NET framework |
| **attribut**              | Gäller inte  |
| **Referenser**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [spikning kungariket](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_insufficient_audit_failure_handling) |
| **Steg** | <p>Utvecklade lösningen är konfigurerad att inte generera ett undantag när det inte går att skriva till en granskningslogg. Om WCF är inte konfigurerad att utlösa ett undantag när det inte går att skriva till en granskningslogg, programmet meddelas inte om felet och granskning av kritiska säkerhetshändelser kanske inte upptäcks.</p>|

### <a name="example"></a>Exempel
Den `<behavior/>` element av WCF-konfigurationsfilen nedan instruerar WCF meddela inte programmet när WCF inte att skriva till en granskningslogg.
````
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
````
Konfigurera WCF för att meddela programmet när det inte går att skriva till en granskningslogg. Programmet ska ha ett alternativt notification-schema för att aviseringen den organisation som redovisningsspårning inte upprätthålls. 

## <a id="logging-web-api"></a>Se till att granskning och loggning är aktiverad för webb-API

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webb-API | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | Aktivera granskning och loggning på webb-API: er. Granskningsloggar ska avbilda användarkontext. Identifiera alla viktiga händelser och loggar dessa händelser. Implementera centraliserad loggning |

## <a id="logging-field-gateway"></a>Se till att lämpliga granskning och loggning är aktiverad för fältet Gateway

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Fältet för IoT-Gateway | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | <p>När flera enheter ansluter till en Gateway för fältet, kontrollera att anslutningsförsök autentiseringsstatus (lyckade eller misslyckade) för enskilda enheter är inloggad och underhålls på den Gateway som fältet.</p><p>Kontrollera också att granskning utförs när autentiseringsuppgifterna hämtas i fall där fältet Gateway för att underhålla IoT-hubb autentiseringsuppgifterna för enskilda enheter. Utveckla en process för att regelbundet skicka loggarna till Azure IoT-hubb/lagring lång sikt bevarande.</p> |

## <a id="logging-cloud-gateway"></a>Se till att lämpliga granskning och loggning är aktiverad för Gateway för moln

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Gateway för IoT-moln | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **attribut**              | Gäller inte  |
| **Referenser**              | [Introduktion till IoT-hubb operations övervakning](https://azure.microsoft.com/documentation/articles/iot-hub-operations-monitoring/) |
| **Steg** | <p>Design för att samla in och lagra granskningsdata som samlas in genom övervakning för IoT-hubb åtgärder. Aktivera övervakning följande kategorier:</p><ul><li>Enhetens identitet åtgärder</li><li>Enhet till moln kommunikation</li><li>Moln till enhet kommunikation</li><li>Anslutningar</li><li>Filöverföringar</li></ul>|