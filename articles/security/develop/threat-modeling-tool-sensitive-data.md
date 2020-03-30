---
title: Känsliga data – Microsofts verktyg för hotmodellering – Azure | Microsoft-dokument
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
ms.openlocfilehash: ab22e9843ca133274361838eeb49abbe326588dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502225"
---
# <a name="security-frame-sensitive-data--mitigations"></a>Säkerhetsram: Känsliga uppgifter | Mitigations 
| Produkt/tjänst | Artikel |
| --------------- | ------- |
| **Gräns för datorförtroende** | <ul><li>[Se till att binärfiler är fördunklade om de innehåller känslig information](#binaries-info)</li><li>[Överväg att använda krypterat filsystem (EFS) används för att skydda konfidentiella användarspecifika data](#efs-user)</li><li>[Se till att känsliga data som lagras av programmet i filsystemet är krypterade](#filesystem)</li></ul> | 
| **Webbprogram** | <ul><li>[Se till att känsligt innehåll inte cachelagras i webbläsaren](#cache-browser)</li><li>[Kryptera delar av Web Apps konfigurationsfiler som innehåller känsliga data](#encrypt-data)</li><li>[Inaktivera uttryckligen HTML-attributet för automatisk komplettering i känsliga formulär och indata](#autocomplete-input)</li><li>[Se till att känsliga data som visas på användarskärmen är maskerade](#data-mask)</li></ul> | 
| **Databas** | <ul><li>[Implementera dynamisk datamaskering för att begränsa känsliga dataexponeringsanvändare som inte är privilegierade användare](#dynamic-users)</li><li>[Se till att lösenord lagras i saltat hash-format](#salted-hash)</li><li>[Kontrollera att känsliga data i databaskolumner är krypterade](#db-encrypted)</li><li>[Kontrollera att kryptering på databasnivå (TDE) är aktiverat](#tde-enabled)</li><li>[Kontrollera att säkerhetskopiering av databaser är krypterade](#backup)</li></ul> | 
| **Webb-API** | <ul><li>[Se till att känsliga data som är relevanta för webb-API inte lagras i webbläsarens lagring](#api-browser)</li></ul> | 
| Azure-dokument DB | <ul><li>[Kryptera känsliga data som lagras i Azure Cosmos DB](#encrypt-docdb)</li></ul> | 
| **Azure IaaS VM-förtroendegräns** | <ul><li>[Använda Azure Disk Encryption för att kryptera diskar som används av virtuella datorer](#disk-vm)</li></ul> | 
| **Förtroendegräns för serviceinfrastruktur** | <ul><li>[Kryptera hemligheter i Service Fabric-program](#fabric-apps)</li></ul> | 
| **Dynamics CRM** | <ul><li>[Utför säkerhetsmodellering och använd affärsenheter/team där det behövs](#modeling-teams)</li><li>[Minimera åtkomsten till delningsfunktionen på kritiska entiteter](#entities)</li><li>[Utbilda användare om riskerna med funktionen Dynamics CRM Share och god säkerhetspraxis](#good-practices)</li><li>[Inkludera en regel för utvecklingsstandarder som visar konfigurationsinformation i undantagshantering](#exception-mgmt)</li></ul> | 
| **Azure-lagring** | <ul><li>[Använda Azure Storage Service Encryption (SSE) för data i vila (förhandsversion)](#sse-preview)</li><li>[Använda kryptering på klientsidan för att lagra känsliga data i Azure Storage](#client-storage)</li></ul> | 
| **Mobil klient** | <ul><li>[Kryptera känsliga data eller PII-data som skrivits till telefoner lokal lagring](#pii-phones)</li><li>[Fördunkla genererade binärfiler innan de distribueras till slutanvändare](#binaries-end)</li></ul> | 
| **WCF** | <ul><li>[Ange clientCredentialType till Certifikat eller Windows](#cert)</li><li>[WCF-säkerhetsläget är inte aktiverat](#security)</li></ul> | 

## <a name="ensure-that-binaries-are-obfuscated-if-they-contain-sensitive-information"></a><a id="binaries-info"></a>Se till att binärfiler är fördunklade om de innehåller känslig information

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Gräns för datorförtroende | 
| **SDL-fas**               | Distribution |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Se till att binärfiler är fördunklade om de innehåller känslig information som affärshemligheter, känslig affärslogik som inte bör vändas. Detta för att stoppa reverse engineering av sammansättningar. Verktyg `CryptoObfuscator` som kan användas för detta ändamål. |

## <a name="consider-using-encrypted-file-system-efs-is-used-to-protect-confidential-user-specific-data"></a><a id="efs-user"></a>Överväg att använda krypterat filsystem (EFS) används för att skydda konfidentiella användarspecifika data

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Gräns för datorförtroende | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Överväg att använda Krypterat filsystem (EFS) används för att skydda konfidentiella användarspecifika data från motståndare med fysisk åtkomst till datorn. |

## <a name="ensure-that-sensitive-data-stored-by-the-application-on-the-file-system-is-encrypted"></a><a id="filesystem"></a>Se till att känsliga data som lagras av programmet i filsystemet är krypterade

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Gräns för datorförtroende | 
| **SDL-fas**               | Distribution |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Se till att känsliga data som lagras av programmet i filsystemet krypteras (t.ex. med hjälp av DPAPI), om EFS inte kan verkställas |

## <a name="ensure-that-sensitive-content-is-not-cached-on-the-browser"></a><a id="cache-browser"></a>Se till att känsligt innehåll inte cachelagras i webbläsaren

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Generiska, webbformulär, MVC5, MVC6 |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Webbläsare kan lagra information för cachelagring och historik. Dessa cachelagrade filer lagras i en mapp, till exempel mappen Temporära Internet-filer när det gäller Internet Explorer. När dessa sidor refereras igen visar webbläsaren dem från cacheminnet. Om känslig information visas för användaren (t.ex. deras adress, kreditkortsuppgifter, personnummer eller användarnamn) kan denna information lagras i webbläsarens cacheminne och därför hämtas genom att undersöka webbläsarens cacheminne eller genom att helt enkelt trycka på webbläsarens "Back"-knappen. Ange svarshuvudvärdet för cachekontroll till "no-store" för alla sidor. |

### <a name="example"></a>Exempel
```XML
<configuration>
  <system.webServer>
   <httpProtocol>
    <customHeaders>
        <add name="Cache-Control" value="no-cache" />
        <add name="Pragma" value="no-cache" />
        <add name="Expires" value="-1" />
    </customHeaders>
  </httpProtocol>
 </system.webServer>
</configuration>
```

### <a name="example"></a>Exempel
Detta kan implementeras via ett filter. Följande exempel kan användas: 
```csharp
public override void OnActionExecuting(ActionExecutingContext filterContext)
        {
            if (filterContext == null || (filterContext.HttpContext != null && filterContext.HttpContext.Response != null && filterContext.HttpContext.Response.IsRequestBeingRedirected))
            {
                //// Since this is MVC pipeline, this should never be null.
                return;
            }

            var attributes = filterContext.ActionDescriptor.GetCustomAttributes(typeof(System.Web.Mvc.OutputCacheAttribute), false);
            if (attributes == null || **Attributes**.Count() == 0)
            {
                filterContext.HttpContext.Response.Cache.SetNoStore();
                filterContext.HttpContext.Response.Cache.SetCacheability(HttpCacheability.NoCache);
                filterContext.HttpContext.Response.Cache.SetExpires(DateTime.UtcNow.AddHours(-1));
                if (!filterContext.IsChildAction)
                {
                    filterContext.HttpContext.Response.AppendHeader("Pragma", "no-cache");
                }
            }

            base.OnActionExecuting(filterContext);
        }
``` 

## <a name="encrypt-sections-of-web-apps-configuration-files-that-contain-sensitive-data"></a><a id="encrypt-data"></a>Kryptera delar av Web Apps konfigurationsfiler som innehåller känsliga data

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Kryptera konfigurationsavsnitt i ASP.NET 2.0 med DPAPI](https://msdn.microsoft.com/library/ff647398.aspx), [Ange en skyddad konfigurationsprovider](https://msdn.microsoft.com/library/68ze1hb2.aspx), [Använda Azure Key Vault för att skydda programhemligheter](https://azure.microsoft.com/documentation/articles/guidance-multitenant-identity-keyvault/) |
| **Steg** | Konfigurationsfiler som Web.config, appsettings.json används ofta för att lagra känslig information, inklusive användarnamn, lösenord, databasanslutningssträngar och krypteringsnycklar. Om du inte skyddar den här informationen är ditt program sårbart för angripare eller illvilliga användare som hämtar känslig information som kontoanvändarnamn och lösenord, databasnamn och servernamn. Baserat på distributionstypen (azure/on-prem) krypterar du känsliga avsnitt av konfigurationsfiler med DPAPI eller tjänster som Azure Key Vault. |

## <a name="explicitly-disable-the-autocomplete-html-attribute-in-sensitive-forms-and-inputs"></a><a id="autocomplete-input"></a>Inaktivera uttryckligen HTML-attributet för automatisk komplettering i känsliga formulär och indata

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [MSDN: komplettera automatiskt attribut](https://msdn.microsoft.com/library/ms533486(VS.85).aspx), [Använda Komplettera automatiskt i HTML](https://msdn.microsoft.com/library/ms533032.aspx), HTML [Sanering Sårbarhet](https://technet.microsoft.com/security/bulletin/MS10-071), [Komplettera automatiskt.,igen?!](https://blog.mindedsecurity.com/2011/10/autocompleteagain.html) |
| **Steg** | Attributet komplettera automatiskt anger om ett formulär ska ha automatisk komplettering på eller av. När komplettera automatiskt slutförs värdena automatiskt baserat på värden som användaren har angett tidigare. När till exempel ett nytt namn och lösenord anges i ett formulär och formuläret skickas, frågar webbläsaren om lösenordet ska sparas. Därefter när formuläret visas fylls namnet och lösenordet i automatiskt eller fylls i när namnet anges. En angripare med lokal åtkomst kan hämta lösenordet för klartext från webbläsarens cacheminne. Som standard är komplettera automatiskt aktiverat och måste uttryckligen inaktiveras. |

### <a name="example"></a>Exempel
```csharp
<form action="Login.aspx" method="post " autocomplete="off" >
      Social Security Number: <input type="text" name="ssn" />
      <input type="submit" value="Submit" />    
</form>
```

## <a name="ensure-that-sensitive-data-displayed-on-the-user-screen-is-masked"></a><a id="data-mask"></a>Se till att känsliga data som visas på användarskärmen är maskerade

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Känsliga uppgifter som lösenord, kreditkortsnummer, SSN etc. bör maskeras när de visas på skärmen. Detta för att förhindra obehörig personal från att komma åt data (t.ex. skuldra-surfing lösenord, stödpersonal tittar SSN nummer av användare). Kontrollera att dessa dataelement inte syns i oformaterad text och att de är korrekt maskerade. Detta måste tas om hand samtidigt som de accepteras som ingång (t.ex. inmatningstyp="lösenord") samt att visa tillbaka på skärmen (t.ex. visa endast de fyra sista siffrorna i kreditkortsnumret). |

## <a name="implement-dynamic-data-masking-to-limit-sensitive-data-exposure-non-privileged-users"></a><a id="dynamic-users"></a>Implementera dynamisk datamaskering för att begränsa känsliga dataexponeringsanvändare som inte är privilegierade användare

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Sql Azure, OnPrem |
| **Attribut**              | SQL-version - V12, SQL-version - MsSQL2016 |
| **Referenser**              | [Dynamisk datamaskering](https://msdn.microsoft.com/library/mt130841) |
| **Steg** | Syftet med dynamisk datamaskering är att begränsa exponeringen av känsliga data, vilket hindrar användare som inte bör ha tillgång till data från att visa dem. Dynamisk datamaskering syftar inte till att hindra databasanvändare från att ansluta direkt till databasen och köra uttömmande frågor som exponerar delar av känsliga data. Dynamisk datamaskering kompletterar andra sql server-säkerhetsfunktioner (granskning, kryptering, säkerhet på radnivå...) och det rekommenderas starkt att använda den här funktionen tillsammans med dem dessutom för att bättre skydda känsliga data i Databas. Observera att den här funktionen stöds endast av SQL Server som börjar med 2016 och Azure SQL Database. |

## <a name="ensure-that-passwords-are-stored-in-salted-hash-format"></a><a id="salted-hash"></a>Se till att lösenord lagras i saltat hash-format

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Lösenords hashing med .NET Crypto API:er](https://docs.asp.net/en/latest/security/data-protection/consumer-apis/password-hashing.html) |
| **Steg** | Lösenord bör inte lagras i anpassade användararkivdatabaser. Lösenord hashar bör lagras med saltvärden istället. Se till att saltet för användaren alltid är unikt och du tillämpar b-krypta, s-krypta eller PBKDF2 innan du lagrar lösenordet, med en minsta arbetsfaktor iteration räkna med 150.000 slingor för att eliminera risken för brute tvinga.| 

## <a name="ensure-that-sensitive-data-in-database-columns-is-encrypted"></a><a id="db-encrypted"></a>Kontrollera att känsliga data i databaskolumner är krypterade

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | SQL-version - Alla |
| **Referenser**              | [Kryptera känsliga data i SQL-servern](https://technet.microsoft.com/library/ff848751(v=sql.105).aspx)– [Så här krypterar du en kolumn med data i SQL Server](https://msdn.microsoft.com/library/ms179331), Kryptera efter [certifikat](https://msdn.microsoft.com/library/ms188061) |
| **Steg** | Känsliga uppgifter som kreditkortsnummer måste krypteras i databasen. Data kan krypteras med kryptering på kolumnnivå eller av en programfunktion med hjälp av krypteringsfunktionerna. |

## <a name="ensure-that-database-level-encryption-tde-is-enabled"></a><a id="tde-enabled"></a>Kontrollera att kryptering på databasnivå (TDE) är aktiverat

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Förstå SQL Server Transparent datakryptering (TDE)](https://technet.microsoft.com/library/bb934049(v=sql.105).aspx) |
| **Steg** | Funktionen Transparent datakryptering (TDE) i SQL-servern hjälper till att kryptera känsliga data i en databas och skydda de nycklar som används för att kryptera data med ett certifikat. Detta förhindrar att alla utan nycklar använder data. TDE skyddar data "i vila", vilket innebär data och loggfiler. Det ger möjlighet att följa många lagar, förordningar och riktlinjer som fastställts i olika branscher. |

## <a name="ensure-that-database-backups-are-encrypted"></a><a id="backup"></a>Kontrollera att säkerhetskopiering av databaser är krypterade

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | SQL Azure, OnPrem |
| **Attribut**              | SQL-version - V12, SQL-version - MsSQL2014 |
| **Referenser**              | [Kryptering av säkerhetskopiering av SQL-databas](https://msdn.microsoft.com/library/dn449489) |
| **Steg** | SQL Server kan kryptera data när du skapar en säkerhetskopia. Genom att ange krypteringsalgoritmen och krypteraren (ett certifikat eller asymmetrisk nyckel) när man skapar en säkerhetskopia kan man skapa en krypterad säkerhetskopia. |

## <a name="ensure-that-sensitive-data-relevant-to-web-api-is-not-stored-in-browsers-storage"></a><a id="api-browser"></a>Se till att känsliga data som är relevanta för webb-API inte lagras i webbläsarens lagring

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webb-API | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | MVC 5, MVC 6 |
| **Attribut**              | Identitetsprovider – ADFS, identitetsprovider – Azure AD |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | <p>I vissa implementeringar lagras känsliga artefakter som är relevanta för webb-API:s autentisering i webbläsarens lokala lagring. T.ex. Azure AD-autentiseringsartefakter som adal.idtoken, adal.nonce.idtoken, adal.access.token.key, adal.token.keys, adal.state.login, adal.session.state, adal.expiration.key etc.</p><p>Alla dessa artefakter är tillgängliga även efter ut logga ut eller webbläsare är stängd. Om en motståndare får tillgång till dessa artefakter kan han/hon återanvända dem för att komma åt de skyddade resurserna (API:er). Kontrollera att alla känsliga artefakter som är relaterade till webb-API inte lagras i webbläsarens lagring. I de fall där lagring på klientsidan är oundviklig (t.ex. ensidig sida program (SPA) som utnyttjar Implicit OpenIdConnect /OAuth flöden måste lagra åtkomsttoken lokalt), använd lagringsalternativ med inte har uthållighet. e.g., föredrar SessionStorage till LocalStorage.</p>| 

### <a name="example"></a>Exempel
Nedanstående JavaScript-kodavsnitt kommer från ett anpassat autentiseringsbibliotek som lagrar autentiseringsartefakter i lokal lagring. Sådana genomföranden bör undvikas. 
```javascript
ns.AuthHelper.Authenticate = function () {
window.config = {
instance: 'https://login.microsoftonline.com/',
tenant: ns.Configurations.Tenant,
clientId: ns.Configurations.AADApplicationClientID,
postLogoutRedirectUri: window.location.origin,
cacheLocation: 'localStorage', // enable this for IE, as sessionStorage does not work for localhost.
};
```

## <a name="encrypt-sensitive-data-stored-in-cosmos-db"></a><a id="encrypt-docdb"></a>Kryptera känsliga data som lagras i Cosmos DB

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure-dokument DB | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Kryptera känsliga data på programnivå innan du lagrar i dokument DB eller lagra känsliga data i andra lagringslösningar som Azure Storage eller Azure SQL| 

## <a name="use-azure-disk-encryption-to-encrypt-disks-used-by-virtual-machines"></a><a id="disk-vm"></a>Använda Azure Disk Encryption för att kryptera diskar som används av virtuella datorer

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure IaaS VM-förtroendegräns | 
| **SDL-fas**               | Distribution |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Använda Azure Disk Encryption för att kryptera diskar som används av dina virtuella datorer](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines) |
| **Steg** | <p>Azure Disk Encryption är en ny funktion som för närvarande är i förhandsversion. Med den här funktionen kan du kryptera OS-diskar och datadiskar som används av en virtuell IaaS-dator. För Windows krypteras enheterna med hjälp av BitLocker-krypteringsteknik av branschstandard. För Linux krypteras diskarna med DM-Crypt-tekniken. Detta är integrerat med Azure Key Vault så att du kan styra och hantera diskkrypteringsnycklarna. Azure Disk Encryption-lösningen stöder följande tre kundkrypteringsscenarier:</p><ul><li>Aktivera kryptering på nya virtuella IaaS-datorer som skapats från kundkrypterade VHD-filer och krypteringsnycklar som tillhandahålls av kunden, som lagras i Azure Key Vault.</li><li>Aktivera kryptering på nya virtuella IaaS-datorer som skapats från Azure Marketplace.</li><li>Aktivera kryptering på befintliga virtuella IaaS-datorer som redan körs i Azure.</li></ul>| 

## <a name="encrypt-secrets-in-service-fabric-applications"></a><a id="fabric-apps"></a>Kryptera hemligheter i Service Fabric-program

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Förtroendegräns för serviceinfrastruktur | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Miljö - Azure |
| **Referenser**              | [Hantera hemligheter i Service Fabric-program](https://azure.microsoft.com/documentation/articles/service-fabric-application-secret-management/) |
| **Steg** | Hemligheter kan vara känslig information, till exempel lagringsanslutningssträngar, lösenord eller andra värden som inte ska hanteras i oformaterad text. Använd Azure Key Vault för att hantera nycklar och hemligheter i tjänstinfrastrukturprogram. |

## <a name="perform-security-modeling-and-use-business-unitsteams-where-required"></a><a id="modeling-teams"></a>Utför säkerhetsmodellering och använd affärsenheter/team där det behövs

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Dynamics CRM | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Utför säkerhetsmodellering och använd affärsenheter/team där det behövs |

## <a name="minimize-access-to-share-feature-on-critical-entities"></a><a id="entities"></a>Minimera åtkomsten till delningsfunktionen på kritiska entiteter

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Dynamics CRM | 
| **SDL-fas**               | Distribution |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Minimera åtkomsten till delningsfunktionen på kritiska entiteter |

## <a name="train-users-on-the-risks-associated-with-the-dynamics-crm-share-feature-and-good-security-practices"></a><a id="good-practices"></a>Utbilda användare om riskerna med funktionen Dynamics CRM Share och god säkerhetspraxis

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Dynamics CRM | 
| **SDL-fas**               | Distribution |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Utbilda användare om riskerna med funktionen Dynamics CRM Share och god säkerhetspraxis |

## <a name="include-a-development-standards-rule-proscribing-showing-config-details-in-exception-management"></a><a id="exception-mgmt"></a>Inkludera en regel för utvecklingsstandarder som visar konfigurationsinformation i undantagshantering

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Dynamics CRM | 
| **SDL-fas**               | Distribution |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Inkludera en utvecklingsstandarder regel som beskriver visar config detaljer i undantagshantering utanför utveckling. Test för detta som en del av kod recensioner eller regelbunden inspektion.|

## <a name="use-azure-storage-service-encryption-sse-for-data-at-rest-preview"></a><a id="sse-preview"></a>Använda Azure Storage Service Encryption (SSE) för data i vila (förhandsversion)

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Storage | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | StorageType - Blob |
| **Referenser**              | [Kryptering av Azure Storage Service för data i vila (förhandsversion)](https://azure.microsoft.com/documentation/articles/storage-service-encryption/) |
| **Steg** | <p>SSE (Azure Storage Service Encryption) for Data at Rest hjälper dig att skydda och skydda dina data för att uppfylla dina organisatoriska säkerhets- och efterlevnadsåtaganden. Med den här funktionen krypterar Azure Storage automatiskt dina data för beständig lagring och dekrypterar dem före hämtning. Kryptering, dekryptering och nyckelhantering är helt transparent för användarna. SSE gäller endast för att blockera blobbar, sidblobar och lägga till blobbar. Andra typer av data, inklusive tabeller, köer och filer, krypteras inte.</p><p>Arbetsflöde för kryptering och dekryptering:</p><ul><li>Kunden möjliggör kryptering på lagringskontot</li><li>När kunden skriver nya data (PUT Blob, PUT Block, PUT Page, etc.) till Blob-lagring; varje skrivning krypteras med 256-bitars AES-kryptering, en av de starkaste blockchiffer tillgängliga</li><li>När kunden behöver komma åt data (GET Blob, etc.) dekrypteras data automatiskt innan de återvänder till användaren</li><li>Om kryptering är inaktiverat krypteras inte längre nya skrivningar och befintliga krypterade data förblir krypterade tills de skrivs om av användaren. När kryptering är aktiverat krypteras skrivningar till Blob-lagring. Datatillståndet ändras inte när användaren växlar mellan att aktivera/inaktivera kryptering för lagringskontot</li><li>Alla krypteringsnycklar lagras, krypteras och hanteras av Microsoft</li></ul><p>Observera att för närvarande hanteras nycklarna som används för krypteringen av Microsoft. Microsoft genererar nycklarna ursprungligen och hanterar säker lagring av nycklarna samt den vanliga rotationen enligt definitionen i den interna Microsoft-policyn. I framtiden får kunderna möjlighet att hantera sina egna >krypteringsnycklar och tillhandahålla en migreringsväg från Microsoft-hanterade nycklar till kundhanterade nycklar.</p>| 

## <a name="use-client-side-encryption-to-store-sensitive-data-in-azure-storage"></a><a id="client-storage"></a>Använda kryptering på klientsidan för att lagra känsliga data i Azure Storage

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Storage | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Kryptering på klientsidan och Azure Key Vault för Microsoft Azure Storage](https://azure.microsoft.com/documentation/articles/storage-client-side-encryption/), [Självstudiekurs: Kryptera och dekryptera blobbar i Microsoft Azure Storage med Azure Key Vault](https://azure.microsoft.com/documentation/articles/storage-encrypt-decrypt-blobs-key-vault/), Lagra data säkert i Azure [Blob Storage med Azure-krypteringstillägg](https://blogs.msdn.microsoft.com/partnercatalystteam/2015/06/17/storing-data-securely-in-azure-blob-storage-with-azure-encryption-extensions/) |
| **Steg** | <p>Azure Storage Client Library for .NET Nuget-paketet stöder kryptering av data i klientprogram innan du överför till Azure Storage och dekrypterar data när du hämtar till klienten. Biblioteket stöder även integrering med Azure Key Vault för hantering av nycklar för lagringskonton. Här är en kort beskrivning av hur kryptering på klientsidan fungerar:</p><ul><li>Azure Storage-klienten SDK genererar en innehållskrypteringsnyckel (CEK), som är en symmetrisk nyckel för engångsbruk</li><li>Kunddata krypteras med denna CEK</li><li>CEK slås sedan in (krypterad) med hjälp av nyckelkrypteringsnyckeln (KEK). KEK identifieras av en nyckelidentifierare och kan vara ett asymmetriskt nyckelpar eller en symmetrisk nyckel och kan hanteras lokalt eller lagras i Azure Key Vault. Lagringsklienten själv har aldrig tillgång till KEK. Det åberopar bara nyckeln omslagsalgoritm som tillhandahålls av Key Vault. Kunder kan välja att använda anpassade leverantörer för nyckelförpackning/uppackning om de vill</li><li>De krypterade data överförs sedan till Azure Storage-tjänsten. Kontrollera länkarna i referensavsnittet för information om implementering på låg nivå.</li></ul>|

## <a name="encrypt-sensitive-or-pii-data-written-to-phones-local-storage"></a><a id="pii-phones"></a>Kryptera känsliga data eller PII-data som skrivits till telefoner lokal lagring

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Mobil klient | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Generisk, Xamarin  |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Hantera inställningar och funktioner på dina enheter med Microsoft Intune-principer](https://docs.microsoft.com/mem/intune/configuration/), [Nyckelringsjäljd](https://components.xamarin.com/view/square.valet) |
| **Steg** | <p>Om programmet skriver känslig information som användarens personuppgifter (e-post, telefonnummer, förnamn, efternamn, inställningar etc.) - på mobilens filsystem, då det bör krypteras innan du skriver till det lokala filsystemet. Om programmet är ett företagsprogram undersöker du möjligheten att publicera program med Windows Intune.</p>|

### <a name="example"></a>Exempel
Intune kan konfigureras med följande säkerhetsprinciper för att skydda känsliga data: 
```csharp
Require encryption on mobile device    
Require encryption on storage cards
Allow screen capture
```

### <a name="example"></a>Exempel
Om programmet inte är ett företagsprogram, använd sedan plattform som keystore, nyckelringar för att lagra krypteringsnycklar, med vilken kryptografisk åtgärd kan utföras på filsystemet. Följande kodavsnitt visar hur du kommer åt nyckelring från nyckelring med xamarin: 
```csharp
        protected static string EncryptionKey
        {
            get
            {
                if (String.IsNullOrEmpty(_Key))
                {
                    var query = new SecRecord(SecKind.GenericPassword);
                    query.Service = NSBundle.MainBundle.BundleIdentifier;
                    query.Account = "UniqueID";

                    NSData uniqueId = SecKeyChain.QueryAsData(query);
                    if (uniqueId == null)
                    {
                        query.ValueData = NSData.FromString(System.Guid.NewGuid().ToString());
                        var err = SecKeyChain.Add(query);
                        _Key = query.ValueData.ToString();
                    }
                    else
                    {
                        _Key = uniqueId.ToString();
                    }
                }

                return _Key;
            }
        }
```

## <a name="obfuscate-generated-binaries-before-distributing-to-end-users"></a><a id="binaries-end"></a>Fördunkla genererade binärfiler innan de distribueras till slutanvändare

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Mobil klient | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Crypto Förvirring för .Net](https://www.ssware.com/cryptoobfuscator/obfuscator-net.htm) |
| **Steg** | Genererade binärfiler (sammansättningar inom apk) bör fördunklas för att stoppa bakåtkompilering av sammansättningar. Verktyg `CryptoObfuscator` som kan användas för detta ändamål. |

## <a name="set-clientcredentialtype-to-certificate-or-windows"></a><a id="cert"></a>Ange clientCredentialType till Certifikat eller Windows

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | WCF | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | .NET-ramverk 3 |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Befästa](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_token) |
| **Steg** | Om du använder ett AnvändarnamnToken med ett lösenord med klartext över en okrypterad kanal exponeras lösenordet för angripare som kan sniffa SOAP-meddelandena. Tjänsteleverantörer som använder UsernameToken kan acceptera lösenord som skickas i klartext. Om du skickar lösenord med klartext över en okrypterad kanal kan autentiseringsuppgifterna exponeras för angripare som kan sniffa SOAP-meddelandet. | 

### <a name="example"></a>Exempel
Följande WCF-tjänstproviderkonfiguration använder UsernameToken: 
```
<security mode="Message"> 
<message clientCredentialType="UserName" />
``` 
Ange clientCredentialType till Certifikat eller Windows. 

## <a name="wcf-security-mode-is-not-enabled"></a><a id="security"></a>WCF-säkerhetsläget är inte aktiverat

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | WCF | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänt, .NET Framework 3 |
| **Attribut**              | Säkerhetsläge - Transport, säkerhetsläge - Meddelande |
| **Referenser**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify Kungariket](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference), [Grunderna i WCF Security CoDe Magazine](https://www.codemag.com/article/0611051) |
| **Steg** | Ingen transport- eller meddelandesäkerhet har definierats. Program som överför meddelanden utan transport- eller meddelandesäkerhet kan inte garantera meddelandenas integritet eller konfidentialitet. När en WCF-säkerhetsbindning är inställd på Ingen inaktiveras både transport- och meddelandesäkerhet. |

### <a name="example"></a>Exempel
Följande konfiguration ställer in säkerhetsläget till Ingen. 
```
<system.serviceModel> 
  <bindings> 
    <wsHttpBinding> 
      <binding name=""MyBinding""> 
        <security mode=""None""/> 
      </binding> 
  </bindings> 
</system.serviceModel> 
```

### <a name="example"></a>Exempel
Säkerhetsläge Över alla tjänstbindningar finns det fem möjliga säkerhetslägen: 
* Inga. Stänger av säkerheten. 
* Transport. Använder transportsäkerhet för ömsesidig autentisering och meddelandeskydd. 
* Meddelande. Använder meddelandesäkerhet för ömsesidig autentisering och meddelandeskydd. 
* Både. Gör att du kan ange inställningar för transport- och meddelandesäkerhet (endast MSMQ stöder detta). 
* TransportWithMessageCredential. Autentiseringsuppgifter skickas med meddelande- och meddelandeskyddet och serverautentiseringen tillhandahålls av transportlagret. 
* TransportCredentialOnly. Klientautentiseringsuppgifter skickas med transportlagret och inget meddelandeskydd tillämpas. Använd transport- och meddelandesäkerhet för att skydda integriteten och konfidentialiteten för meddelanden. Konfigurationen nedan talar om för tjänsten att använda transportsäkerhet med meddelandeautentiseringsuppgifter.
  ```
  <system.serviceModel>
  <bindings>
    <wsHttpBinding>
    <binding name=""MyBinding""> 
    <security mode=""TransportWithMessageCredential""/> 
    <message clientCredentialType=""Windows""/> 
    </binding> 
  </bindings> 
  </system.serviceModel> 
  ```
