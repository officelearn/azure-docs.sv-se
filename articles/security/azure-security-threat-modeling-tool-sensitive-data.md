---
title: "Känsliga Data - verktyget Microsoft Threat modellering - Azure | Microsoft Docs"
description: "ändringar för hot som exponeras i verktyget Modeling hot"
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
ms.openlocfilehash: 8d7189ea4b01d43cea709e3300d8ed71d266f5c9
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2018
---
# <a name="security-frame-sensitive-data--mitigations"></a>Säkerhet ram: Känsliga Data | Åtgärder 
| Produkter eller tjänster | Artikel |
| --------------- | ------- |
| **Datorn Förtroendegräns** | <ul><li>[Se till att binärfilerna har dolts om de innehåller känslig information](#binaries-info)</li><li>[Överväg att använda krypterade filsystem (EFS) används för att skydda konfidentiella användarspecifika data](#efs-user)</li><li>[Se till att känsliga data som lagras av programmet på filsystemet är krypterad.](#filesystem)</li></ul> | 
| **Webbprogram** | <ul><li>[Se till att känsligt innehåll inte cachelagras i webbläsare](#cache-browser)</li><li>[Kryptera avsnitt i konfigurationsfilerna för Web App som innehåller känsliga data](#encrypt-data)</li><li>[Explicit inaktivera HTML-attributet för Komplettera automatiskt i känsliga formulär och indata](#autocomplete-input)</li><li>[Se till att känsliga data som visas på användarskärmen maskeras](#data-mask)</li></ul> | 
| **Databas** | <ul><li>[Implementera dynamisk datamaskering för att begränsa känsliga data exponering som ej Privilegierade användare](#dynamic-users)</li><li>[Kontrollera att lösenorden lagras i saltat hash-format](#salted-hash)</li><li>[Se till att känsliga data i databaskolumner krypteras](#db-encrypted)</li><li>[Se till att databasnivå-kryptering (TDE) är aktiverad](#tde-enabled)</li><li>[Se till att databassäkerhetskopiorna krypteras](#backup)</li></ul> | 
| **Webb-API** | <ul><li>[Se till att känsliga data som är relevanta för Web API inte lagras i webbläsarens lagring](#api-browser)</li></ul> | 
| Azure dokumentet DB | <ul><li>[Kryptera känsliga data som lagras i Azure Cosmos DB](#encrypt-docdb)</li></ul> | 
| **Azure IaaS-VM Förtroendegräns** | <ul><li>[Använda Azure Disk Encryption för att kryptera diskar som används av virtuella datorer](#disk-vm)</li></ul> | 
| **Service Fabric-Förtroendegräns** | <ul><li>[Kryptera hemligheter i Service Fabric-program](#fabric-apps)</li></ul> | 
| **Dynamics CRM** | <ul><li>[Utför säkerhet modellering och använder företagets enheter/team behov](#modeling-teams)</li><li>[Minimera åtkomst om du vill dela funktionen på kritiska entiteter](#entities)</li><li>[Utbilda användare på risker med Dynamics CRM resursen funktionen och säkerhetsprinciper](#good-practices)</li><li>[En regel för utveckling-standarder som proscribing med config information i hantering av undantag](#exception-mgmt)</li></ul> | 
| **Azure Storage** | <ul><li>[Använd Azure Storage Service-kryptering (SSE) för Data i vila (förhandsgranskning)](#sse-preview)</li><li>[Använd kryptering på klientsidan för att lagra känsliga data i Azure Storage](#client-storage)</li></ul> | 
| **Mobil klient** | <ul><li>[Kryptera känsliga eller PII-data som skrivs till telefoner lokal lagring](#pii-phones)</li><li>[Obfuscate genererade binärfiler innan du distribuerar till slutanvändare](#binaries-end)</li></ul> | 
| **WCF** | <ul><li>[Ange clientCredentialType certifikat eller Windows](#cert)</li><li>[WCF-säkerhetsläget har inte aktiverats](#security)</li></ul> | 

## <a id="binaries-info"></a>Se till att binärfilerna har dolts om de innehåller känslig information

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Datorn Förtroendegräns | 
| **SDL fas**               | Distribution |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | Se till att binärfilerna har dolts om de innehåller känslig information, till exempel affärshemligheter, känsliga affärslogik som bör inte ångras. Detta är att stoppa omvänd manipulation av sammansättningar. Verktyg som `CryptoObfuscator` kan användas för detta ändamål. |

## <a id="efs-user"></a>Överväg att använda krypterade filsystem (EFS) används för att skydda konfidentiella användarspecifika data

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Datorn Förtroendegräns | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | Överväg att använda krypterade filsystem (EFS) används för att skydda konfidentiella användarspecifika data från motståndare med fysisk åtkomst till datorn. |

## <a id="filesystem"></a>Se till att känsliga data som lagras av programmet på filsystemet är krypterad.

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Datorn Förtroendegräns | 
| **SDL fas**               | Distribution |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | Se till att känsliga data som lagras av programmet på filsystemet är krypterad (t.ex. med DPAPI) om EFS inte kan tillämpas |

## <a id="cache-browser"></a>Se till att känsligt innehåll inte cachelagras i webbläsare

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk Web Forms, MVC5, MVC6 |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | Webbläsare kan lagra information för tillämpning av cachelagring och historik. De här filerna lagras i en mapp som mappen Tillfälliga Internetfiler för Internet Explorer. När dessa sidor kallas igen, visar webbläsaren dem från sin cache. Om känslig information visas för användare (till exempel sina adress, kreditkortsinformation, personnummer eller användarnamn), kan den här informationen vara lagras i webbläsarens cacheminne och därför strängfält genom att undersöka webbläsarens cache eller genom att helt enkelt trycka på webbläsarens ”Bakåt”. Ange cache-control svar huvudvärde till ”Nej store” för alla sidor. |

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
Detta kan genomföras med ett filter. Följande exempel kan användas: 
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

## <a id="encrypt-data"></a>Kryptera avsnitt i konfigurationsfilerna för Web App som innehåller känsliga data

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Så här: Kryptera konfigurationsavsnitt i ASP.NET 2.0 med hjälp av DPAPI](https://msdn.microsoft.com/library/ff647398.aspx), [att ange en Konfigurationsprovider för skyddade](https://msdn.microsoft.com/library/68ze1hb2.aspx), [med hjälp av Azure Key Vault att skydda programmet hemligheter](https://azure.microsoft.com/documentation/articles/guidance-multitenant-identity-keyvault/) |
| **Steg** | Konfigurationsfiler exempel Web.config, appsettings.json ofta används för att hålla känslig information, inklusive användarnamn, lösenord, databasanslutningssträngar och krypteringsnycklar. Om du inte skyddar informationen är ditt program sårbar för attacker eller angripare erhålla känslig information, till exempel användarnamn och lösenord, databasnamn och servernamn. Baserat på typen av distribution (azure/lokalt), kryptera känsliga grupper i config-filer med hjälp av DPAPI eller tjänster som Azure Key Vault. |

## <a id="autocomplete-input"></a>Explicit inaktivera HTML-attributet för Komplettera automatiskt i känsliga formulär och indata

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [MSDN: autocomplete attributet](http://msdn.microsoft.com/library/ms533486(VS.85).aspx), [med Komplettera automatiskt i HTML](http://msdn.microsoft.com/library/ms533032.aspx), [HTML-rensning säkerhetsproblem](http://technet.microsoft.com/security/bulletin/MS10-071), [Autocomplete., igen?](http://blog.mindedsecurity.com/2011/10/autocompleteagain.html) |
| **Steg** | Autocomplete attributet anger om ett formulär ska ha autocomplete eller inaktivera. När autocomplete finns på, Slutför värden baserat på värden som användaren har angett innan automatiskt i webbläsaren. Till exempel när ett nytt namn och lösenord har angetts i ett formulär och skicka formuläret, så frågar webbläsaren om lösenord ska sparas. Därefter när formuläret visas namnet och lösenordet fylls i automatiskt eller har slutförts som det angivna namnet. En angripare med lokal åtkomst kan hämta lösenordet i klartext från webbläsarens cache. Autocomplete är aktiverat som standard och den måste uttryckligen inaktiveras. |

### <a name="example"></a>Exempel
```csharp
<form action="Login.aspx" method="post " autocomplete="off" >
      Social Security Number: <input type="text" name="ssn" />
      <input type="submit" value="Submit" />    
</form>
```

## <a id="data-mask"></a>Se till att känsliga data som visas på användarskärmen maskeras

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | Känsliga data, till exempel lösenord, kreditkortsnummer, SSN etc. ska maskeras när det visas på skärmen. Detta är att förhindra att obehöriga kommer åt data (t.ex. axel surfning lösenord, supporttekniker visa SSN antal användare). Se till att dessa dataelement visas inte i klartext och maskeras på lämpligt sätt. Detta måste vara åtgärdat vid accepterande av organisationens dem som indata (t.ex. input type = ”password”) samt visa tillbaka på skärmen (till exempel visa endast det sista 4 siffrorna i kreditkortsnumret). |

## <a id="dynamic-users"></a>Implementera dynamisk datamaskering för att begränsa känsliga data exponering som ej Privilegierade användare

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | SQL Azure bör OnPrem |
| **Attribut**              | SQL Version - V12, SQL Version - MsSQL2016 |
| **Referenser**              | [Dynamisk Datamaskning](https://msdn.microsoft.com/library/mt130841) |
| **Steg** | Syftet med dynamisk datamaskering är att begränsa exponering av känsliga data, förhindra att användare inte ska ha åtkomst till data från att visa den. Dynamisk datamaskering syftar inte till att förhindra användare från att ansluta direkt till databasen och köra fullständig frågor som exponerar delar av känsliga data. Dynamisk datamaskning är kompletterande till andra SQL Server-säkerhetsfunktioner (granskning, kryptering och säkerhet på radnivå...) och det rekommenderas att använda den här funktionen tillsammans med dem dessutom för att bättre skydda känsliga data i databasen. Observera att den här funktionen stöds bara av SQL Server från och med 2016 och Azure SQL Database. |

## <a id="salted-hash"></a>Kontrollera att lösenorden lagras i saltat hash-format

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Lösenord Hashing med .NET Crypto API: er](http://docs.asp.net/en/latest/security/data-protection/consumer-apis/password-hashing.html) |
| **Steg** | Lösenord bör inte lagras i arkivet för anpassade användardatabaser. Lösenordshashvärden bör lagras med salt värden i stället. Kontrollera att salt för användaren är alltid unikt och du tillämpa b-crypt, s crypt eller PBKDF2 innan du lagra lösenord med en minsta arbete faktor iterationsantal 150 000 slingor eliminera risken för brute tvinga.| 

## <a id="db-encrypted"></a>Se till att känsliga data i databaskolumner krypteras

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | SQL Version - All |
| **Referenser**              | [Kryptera känsliga data i SQLServer](https://technet.microsoft.com/library/ff848751(v=sql.105).aspx), [så här: kryptera en kolumn med Data i SQL Server](https://msdn.microsoft.com/library/ms179331), [kryptera av certifikat](https://msdn.microsoft.com/library/ms188061) |
| **Steg** | Känsliga data, till exempel kreditkortsnummer måste vara krypterade i databasen. Data kan krypteras med kryptering på kolumnnivå eller av ett program-funktionen med hjälp av funktioner för kryptering. |

## <a id="tde-enabled"></a>Se till att databasnivå-kryptering (TDE) är aktiverad

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Förstå Transparent datakryptering för SQL Server (TDE)](https://technet.microsoft.com/library/bb934049(v=sql.105).aspx) |
| **Steg** | Transparent Data kryptering (TDE)-funktionen i SQLServer på så sätt kan kryptera känsliga data i en databas och skydda nycklar som används för att kryptera data med ett certifikat. Detta förhindrar att någon utan nycklar med hjälp av data. TDE skyddar data ”i vila”, vilket innebär att data och loggfilen filer. Det ger dig möjlighet att vara kompatibel med många lagar, och riktlinjerna i olika branscher. |

## <a id="backup"></a>Se till att databassäkerhetskopiorna krypteras

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | SQL Azure, OnPrem |
| **Attribut**              | SQL Version - V12, SQL Version - MsSQL2014 |
| **Referenser**              | [Kryptering av säkerhetskopior i SQL-databas](https://msdn.microsoft.com/library/dn449489) |
| **Steg** | SQL Server har möjlighet att kryptera data när du skapar en säkerhetskopia. Genom att ange krypteringsalgoritmen och krypterare (ett certifikat eller en asymmetrisk nyckel) när du skapar en säkerhetskopia, går att skapa en krypterad säkerhetskopiefil. |

## <a id="api-browser"></a>Se till att känsliga data som är relevanta för Web API inte lagras i webbläsarens lagring

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webb-API | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | MVC 5, MVC 6 |
| **Attribut**              | Identitet Provider - ADFS, identitetsleverantör - Azure AD |
| **Referenser**              | Gäller inte  |
| **Steg** | <p>I vissa implementeringar lagras känsliga artefakter som är relevanta för Web API-autentisering i webbläsarens lokal lagring. Azure AD authentication artefakter som t.ex. adal.idtoken, adal.nonce.idtoken, adal.access.token.key, adal.token.keys, adal.state.login, adal.session.state, adal.expiration.key osv.</p><p>Alla dessa artefakter finns även efter att logga ut eller webbläsaren stängs. Om en angriparen får tillgång till dessa artefakter, kan han eller hon återanvända dem för att komma åt skyddade resurser (API). Se till att alla känsliga artefakter som rör Web API inte lagras i webbläsaren. I de fall där klientens lagring är oundvikligt (t.ex. enda sidan program (SPA) som utnyttjar Implicit OpenIdConnect/OAuth-flöden måste lagra åtkomsttoken lokalt), Använd lagringsalternativ med har inte beständig. till exempel vill SessionStorage LocalStorage.</p>| 

### <a name="example"></a>Exempel
Den nedan JavaScript fragment är från ett bibliotek för anpassad autentisering som lagrar autentiseringsartefakter i lokal lagring. Sådana implementeringar bör undvikas. 
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

## <a id="encrypt-docdb"></a>Kryptera känsliga data som lagras i Cosmos-DB

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure dokumentet DB | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | Kryptera känsliga data på programnivå innan de lagras i dokumentet DB eller lagra några känsliga data i andra lagringslösningar som Azure Storage eller Azure SQL| 

## <a id="disk-vm"></a>Använda Azure Disk Encryption för att kryptera diskar som används av virtuella datorer

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure IaaS-VM Förtroendegräns | 
| **SDL fas**               | Distribution |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Använda Azure Disk Encryption för att kryptera diskar som används av virtuella datorer](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines) |
| **Steg** | <p>Azure Disk Encryption är en ny funktion som används för närvarande under förhandsgranskning. Den här funktionen kan du kryptera OS-diskar och datadiskar som används av en virtuell IaaS-dator. För Windows krypteras enheterna med BitLocker-kryptering branschstandard. För Linux krypteras diskarna med DM-Crypt-teknik. Detta är integrerad med Azure Key Vault så att du kan styra och hantera krypteringsnycklar disk. Azure Disk Encryption-lösningen stöder följande tre kunden kryptering scenarier:</p><ul><li>Aktivera kryptering på den nya virtuella IaaS-datorer skapas från kund-krypterad VHD-filer och som tillhandahålls av kunden krypteringsnycklar som lagras i Azure Key Vault.</li><li>Aktivera kryptering på den nya virtuella IaaS-datorer skapas från Azure Marketplace.</li><li>Aktivera kryptering på befintliga virtuella IaaS-datorer körs i Azure.</li></ul>| 

## <a id="fabric-apps"></a>Kryptera hemligheter i Service Fabric-program

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Service Fabric-Förtroendegräns | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Miljö - Azure |
| **Referenser**              | [Hantera hemligheter i Service Fabric-program](https://azure.microsoft.com/documentation/articles/service-fabric-application-secret-management/) |
| **Steg** | Hemligheter kan vara känslig information, till exempel storage-anslutningssträngar, lösenord eller andra värden som inte ska hanteras i oformaterad text. Använda Azure Key Vault för att hantera nycklar och hemligheter i service fabric-program. |

## <a id="modeling-teams"></a>Utför säkerhet modellering och använder företagets enheter/team behov

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Dynamics CRM | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | Utför säkerhet modellering och använder företagets enheter/team behov |

## <a id="entities"></a>Minimera åtkomst om du vill dela funktionen på kritiska entiteter

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Dynamics CRM | 
| **SDL fas**               | Distribution |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | Minimera åtkomst om du vill dela funktionen på kritiska entiteter |

## <a id="good-practices"></a>Utbilda användare på risker med Dynamics CRM resursen funktionen och säkerhetsprinciper

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Dynamics CRM | 
| **SDL fas**               | Distribution |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | Utbilda användare på risker med Dynamics CRM resursen funktionen och säkerhetsprinciper |

## <a id="exception-mgmt"></a>En regel för utveckling-standarder som proscribing med config information i hantering av undantag

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Dynamics CRM | 
| **SDL fas**               | Distribution |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | Inkludera en utveckling standarder regel proscribing med config information i hantering av undantag utanför utveckling. Testa detta som en del av koden granskningar eller återkommande kontroll.|

## <a id="sse-preview"></a>Använd Azure Storage Service-kryptering (SSE) för Data i vila (förhandsgranskning)

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Storage | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | StorageType - Blob |
| **Referenser**              | [Azure Storage Service-kryptering för Data i vila (förhandsgranskning)](https://azure.microsoft.com/documentation/articles/storage-service-encryption/) |
| **Steg** | <p>Azure Storage Service kryptering (SSE) för Data i vila hjälper dig att skydda och skydda dina data för att uppfylla din organisations säkerhet och efterlevnad åtaganden. Med den här funktionen Azure Storage krypterar dina data innan beständighet till lagring och automatiskt dekrypterar före hämtning. Kryptering, dekryptering och hantering av nycklar är helt transparent för användarna. SSE gäller endast för blockblobbar, sidblobbar, och tilläggsblobar. Andra typer av data, inklusive tabeller, köer och filer, krypteras inte.</p><p>Kryptering och dekryptering arbetsflöde:</p><ul><li>Kunden aktiverar kryptering på storage-konto</li><li>När kunden skriver data (PLACERA Blob, PLACERA Block, PLACERA sidan o.s.v.) till Blob storage; varje skrivning har krypterats med 256-bitars AES-kryptering, något av de starkaste block chiffer som är tillgängliga</li><li>När kunden behöver åtkomst till data (hämta Blob, etc.), dekrypteras data automatiskt innan det returneras till användaren</li><li>Om kryptering har inaktiverats kan nya skrivningar krypteras inte längre och befintliga krypterade data förblir krypterade tills skrivas om av användaren. Även om kryptering har aktiverats, krypteras skrivningar till Blob storage. Tillståndet för data ändras inte med användaren växla mellan aktivering/inaktivering av kryptering för lagringskontot</li><li>Alla krypteringsnycklar lagras, krypteras och hanteras av Microsoft</li></ul><p>Observera att nycklar som används för kryptering hanteras av Microsoft för tillfället. Microsoft genererar nycklarna ursprungligen och hantera säker lagring av nycklar som vanlig rotationen som definieras av intern Microsoft-princip. I framtiden, kunderna får möjlighet att hantera sina egna > krypteringsnycklar och ange en migreringssökväg från Microsoft-hanterad nycklar till kundhanterad nycklar.</p>| 

## <a id="client-storage"></a>Använd kryptering på klientsidan för att lagra känsliga data i Azure Storage

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Storage | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Kryptering på klientsidan och Azure Key Vault för Microsoft Azure Storage](https://azure.microsoft.com/documentation/articles/storage-client-side-encryption/), [Självstudier: kryptera och dekryptera blobbar i Microsoft Azure Storage med hjälp av Azure Key Vault](https://azure.microsoft.com/documentation/articles/storage-encrypt-decrypt-blobs-key-vault/), [lagra Data på ett säkert sätt i Azure Blob Storage med tillägg för Azure-kryptering](https://blogs.msdn.microsoft.com/partnercatalystteam/2015/06/17/storing-data-securely-in-azure-blob-storage-with-azure-encryption-extensions/) |
| **Steg** | <p>Azure Storage-klientbiblioteket för .NET-Nuget-paketet har stöd för kryptering av data i klientprogram före överföringen till Azure Storage och dekryptera data under nedladdningen till klienten. Biblioteket stöder även integrering med Azure Key Vault för hantering av nycklar för lagringskonton. Här är en kort beskrivning av hur kryptering på serversidan för klienten fungerar:</p><ul><li>Azure Storage client SDK genererar en innehåll krypteringsnyckel (CEK), vilket är en gång Använd symmetrisk nyckel</li><li>Kundinformation krypteras med den här CEK</li><li>CEK kapslas sedan (krypterade) med hjälp av krypteringsnyckel (KEK). KEK kan identifieras med en nyckelidentifierare och vara ett asymmetriskt nyckelpar eller en symmetrisk nyckel och kan hanteras lokalt eller lagras i Azure Key Vault. Klientens lagring har aldrig åtkomst till KEK. Den anropar bara viktiga radbrytning algoritmen som tillhandahålls av Key Vault. Kunder kan du använda anpassade providers för nyckeln radbrytning/uppackning om de vill</li><li>Krypterade data överförs sedan till Azure Storage-tjänsten. Kontrollera länkarna i referensavsnittet för låg nivå implementeringsdetaljer.</li></ul>|

## <a id="pii-phones"></a>Kryptera känsliga eller PII-data som skrivs till telefoner lokal lagring

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Mobil klient | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generic, Xamarin  |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Hantera inställningar och funktioner på dina enheter med Microsoft Intune-principer](https://docs.microsoft.com/intune/deploy-use/manage-settings-and-features-on-your-devices-with-microsoft-intune-policies#create-a-configuration-policy), [nyckelringar Valet](https://components.xamarin.com/view/square.valet) |
| **Steg** | <p>Om programmet skriver känslig information, till exempel användarens personligt identifierbar information (e-post, telefonnummer, förnamn, efternamn, inställningar osv.) -på mobile filsystem, sedan den ska krypteras innan skrivningen till det lokala filsystemet. Om programmet är ett företagsprogram, utforska du möjligheten att publicera program med hjälp av Windows Intune.</p>|

### <a name="example"></a>Exempel
Intune kan konfigureras med följande säkerhetsprinciper för att skydda känsliga data: 
```csharp
Require encryption on mobile device    
Require encryption on storage cards
Allow screen capture
```

### <a name="example"></a>Exempel
Om programmet inte är ett företagsprogram, använda plattform keystore, nyckelringar att lagra krypteringsnycklarna med hjälp av vilken kryptografisk åtgärd kan utföras i filsystemet. Följande kodavsnitt visar hur åtkomst till nyckeln från nyckelringar med xamarin: 
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

## <a id="binaries-end"></a>Obfuscate genererade binärfiler innan du distribuerar till slutanvändare

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Mobil klient | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Kryptografi döljande för .net](http://www.ssware.com/cryptoobfuscator/obfuscator-net.htm) |
| **Steg** | Genererade binärfiler (sammansättningar i apk) ska vara dolt om du vill stoppa omvänd manipulation av sammansättningar. Verktyg som `CryptoObfuscator` kan användas för detta ändamål. |

## <a id="cert"></a>Ange clientCredentialType certifikat eller Windows

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | WCF | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | .NET Framework 3 |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Spikning](https://vulncat.fortify.com/en/vulncat/index.html) |
| **Steg** | Använda en UsernameToken med lösenord i klartext via en okrypterad kanal exponerar lösenordet för angripare som kan söka SOAP-meddelanden. Leverantörer som använder UsernameToken kan acceptera lösenord skickas i klartext. Skicka lösenord i klartext via en okrypterad kanal kan exponera autentiseringsuppgifter för angripare som kan söka SOAP-meddelandet. | 

### <a name="example"></a>Exempel
Följande providerkonfigurationen för WCF-tjänsten använder UsernameToken: 
```
<security mode="Message"> 
<message clientCredentialType="UserName" />
``` 
Ange clientCredentialType certifikat eller Windows. 

## <a id="security"></a>WCF-säkerhetsläget har inte aktiverats

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | WCF | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generic, .NET Framework 3 |
| **Attribut**              | Säkerhet-läge - Transport, säkerhetsläget - meddelande |
| **Referenser**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [spikning kungariket](https://vulncat.fortify.com/en/vulncat/index.html), [grunderna i säkerhet för WCF CoDe Magazine](http://www.codemag.com/article/0611051) |
| **Steg** | Ingen transport eller meddelandet säkerhet har definierats. Program som överför meddelanden utan transport eller meddelande säkerhet inte kan garantera integritet och sekretess för meddelanden. När en bindning för WCF-säkerheten är inställd på None, inaktiveras säkerhet på både transport och meddelandet. |

### <a name="example"></a>Exempel
Följande konfiguration anger säkerhetsläget till None. 
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
Säkerhetsläge över alla tjänstbindningar det finns fem säkerhetsrisker lägen: 
* Ingen. Inaktiverar säkerhet. 
* Transport. Använder transport säkerhet för ömsesidig autentisering och meddelandet skydd. 
* Meddelande. Använder meddelandesäkerhet för ömsesidig autentisering och meddelandet skydd. 
* Båda. Kan du ange inställningar för transport och meddelandenivå säkerhet (endast MSMQ stöder detta). 
* TransportWithMessageCredential. Autentiseringsuppgifter skickas med meddelandet och meddelandet skydd och serverautentisering som tillhandahålls av Transportskiktet. 
* TransportCredentialOnly. Klientens autentiseringsuppgifter skickas med Transportskiktet och inget meddelande skydd används. Använda säkerhet transport och meddelandet för att skydda integritet och sekretess för meddelanden. Konfigurationen nedan visar tjänsten att använda transportsäkerhet med autentiseringsuppgifter för meddelandet.
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
