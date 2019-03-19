---
title: Känsliga Data – Microsoft Hotmodelleringsverktyg – Azure | Microsoft Docs
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
ms.openlocfilehash: 27028903daeaf62a25584300944538341a861c80
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57905230"
---
# <a name="security-frame-sensitive-data--mitigations"></a>Security ram: Känsliga Data | Åtgärder 
| Produkt/tjänst | Artikel |
| --------------- | ------- |
| **Datorn Förtroendegräns** | <ul><li>[Se till att binärfilerna har dolts om de innehåller känslig information](#binaries-info)</li><li>[Överväg att använda krypterade filsystem (EFS) används för att skydda konfidentiella användarspecifika data](#efs-user)</li><li>[Se till att känsliga data som lagras av programmet i filsystemet är krypterad](#filesystem)</li></ul> | 
| **Webbprogram** | <ul><li>[Se till att känsligt innehåll inte cachelagras i webbläsare](#cache-browser)</li><li>[Kryptera avsnitt för Web App configuration-filer som innehåller känslig information](#encrypt-data)</li><li>[Inaktivera automatisk komplettering HTML-attribut i känsliga formulär och indata](#autocomplete-input)</li><li>[Se till att känsliga data som visas på användarskärmen maskeras](#data-mask)</li></ul> | 
| **Databas** | <ul><li>[Implementera dynamisk datamaskning för att begränsa exponering icke privilegierad användare för känsliga data](#dynamic-users)</li><li>[Se till att lösenord lagras i saltat hash-format](#salted-hash)</li><li>[Se till att känsliga data i databaskolumner är krypterad](#db-encrypted)</li><li>[Kontrollera att databasnivå-kryptering (TDE) är aktiverat](#tde-enabled)</li><li>[Se till att databassäkerhetskopiorna är krypterad](#backup)</li></ul> | 
| **Webb-API** | <ul><li>[Se till att känsliga data som är relevanta för webb-API inte lagras i webbläsarens storage](#api-browser)</li></ul> | 
| Azure Document DB | <ul><li>[Kryptera känsliga data som lagras i Azure Cosmos DB](#encrypt-docdb)</li></ul> | 
| **Azure IaaS VM-Förtroendegräns** | <ul><li>[Använda Azure Disk Encryption för att kryptera diskar som används av virtuella datorer](#disk-vm)</li></ul> | 
| **Service Fabric-Förtroendegräns** | <ul><li>[Kryptera hemligheter i Service Fabric-program](#fabric-apps)</li></ul> | 
| **Dynamics CRM** | <ul><li>[Utföra security modellering och använda enheter/Verksamhetsteamen om det behövs](#modeling-teams)</li><li>[Minimera åtkomst om du vill dela funktionen på kritiska entiteter](#entities)</li><li>[Utbilda användare på riskerna med funktionen för Dynamics CRM-resurs och säkerhetsprinciper](#good-practices)</li><li>[Inkludera en regel för utveckling-standarder som proscribing med config information i hantering av undantag](#exception-mgmt)</li></ul> | 
| **Azure Storage** | <ul><li>[Använd Azure Storage Service Encryption (SSE) för Data i vila (förhandsversion)](#sse-preview)</li><li>[Använda Client Side Encryption för att lagra känsliga data i Azure Storage](#client-storage)</li></ul> | 
| **Mobila klienten** | <ul><li>[Kryptera känsliga eller PII-data som skrivs till lokal lagring för telefoner](#pii-phones)</li><li>[Förvräng genererade binärfiler innan du distribuerar till slutanvändare](#binaries-end)</li></ul> | 
| **WCF** | <ul><li>[Ange clientCredentialType till certifikat eller Windows](#cert)</li><li>[WCF-Security-läge är inte aktiverat](#security)</li></ul> | 

## <a id="binaries-info"></a>Se till att binärfilerna har dolts om de innehåller känslig information

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Datorn Förtroendegräns | 
| **SDL fas**               | Distribution |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | Se till att binärfilerna har dolts om de innehåller känslig information, till exempel affärshemligheter, känsliga affärslogik som bör inte omvänds. Det här är att stoppa bakåtkompilering av sammansättningar. Verktyg som `CryptoObfuscator` kan användas för detta ändamål. |

## <a id="efs-user"></a>Överväg att använda krypterade filsystem (EFS) används för att skydda konfidentiella användarspecifika data

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Datorn Förtroendegräns | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | Överväg att använda krypterade filsystem (EFS) används för att skydda konfidentiella användarspecifika data från angripare med tillgång till datorn. |

## <a id="filesystem"></a>Se till att känsliga data som lagras av programmet i filsystemet är krypterad

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Datorn Förtroendegräns | 
| **SDL fas**               | Distribution |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | Se till att känsliga data som lagras av programmet i filsystemet är krypterad (t.ex. med DPAPI), om EFS inte kan tillämpas |

## <a id="cache-browser"></a>Se till att känsligt innehåll inte cachelagras i webbläsare

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generic, Web Forms, MVC5, MVC6 |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | Webbläsare kan lagra information om cachelagring och historik. Dessa cachelagrade filer lagras i en mapp som mappen Tillfälliga Internetfiler för Internet Explorer. När dessa sidor kallas igen, visar webbläsaren dem från sitt cacheminne. Om känslig information visas för användaren (till exempel deras, kreditkort, personnummer eller användarnamn), kan sedan den här informationen vara lagrad i webbläsarens cacheminne och därför hämtningsbara via undersöka webbläsarens cache eller bara att trycka på knappen ”Bakåt” i webbläsaren. Ange cache-control svar huvudvärde till ”no-store” för alla sidor. |

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
Detta kan implementeras genom ett filter. Följande exempel kan användas: 
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

## <a id="encrypt-data"></a>Kryptera avsnitt för Web App configuration-filer som innehåller känslig information

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Anvisningar: Kryptera konfigurationsavsnitt i ASP.NET 2.0 med hjälp av DPAPI](https://msdn.microsoft.com/library/ff647398.aspx), [att ange en Konfigurationsprovider för skyddade](https://msdn.microsoft.com/library/68ze1hb2.aspx), [med hjälp av Azure Key Vault för att skydda programhemligheter](https://azure.microsoft.com/documentation/articles/guidance-multitenant-identity-keyvault/) |
| **Steg** | Konfigurationsfiler, till exempel Web.config, appsettings.json ofta används för att lagra känslig information, inklusive användarnamn, lösenord, anslutningssträngar för databasen och krypteringsnycklar. Om du inte skyddar den här informationen kan är ditt program sårbar för attacker eller illvilliga användare att känslig information som användarnamn och lösenord, databasnamn och servernamn. Baserat på typen av distribution (azure/on-premises), kryptera känsliga grupper i konfigurationsfiler med hjälp av DPAPI eller tjänster som Azure Key Vault. |

## <a id="autocomplete-input"></a>Inaktivera automatisk komplettering HTML-attribut i känsliga formulär och indata

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [MSDN: autocomplete attributet](https://msdn.microsoft.com/library/ms533486(VS.85).aspx), [med automatisk komplettering i HTML](https://msdn.microsoft.com/library/ms533032.aspx), [HTML gemensamt säkerhetsproblem](https://technet.microsoft.com/security/bulletin/MS10-071), [automatisk komplettering., igen?](https://blog.mindedsecurity.com/2011/10/autocompleteagain.html) |
| **Steg** | Automatisk komplettering attributet anger om ett formulär ska ha automatisk komplettering eller inaktivera. Om automatisk komplettering finns på måste slutföra värden baserat på värden som användaren har angett innan automatiskt i webbläsaren. Till exempel när ett nytt namn och lösenord har angetts i ett formulär och formuläret har skickats, så frågar webbläsaren om lösenordet ska sparas. Därefter när formuläret visas, namn och lösenord fylls i automatiskt eller har slutförts eftersom namnet har angetts. En angripare med lokal åtkomst kan hämta lösenordet i klartext från webbläsarens cache. Komplettera automatiskt är aktiverad som standard och det måste uttryckligen inaktiveras. |

### <a name="example"></a>Exempel
```csharp
<form action="Login.aspx" method="post " autocomplete="off" >
      Social Security Number: <input type="text" name="ssn" />
      <input type="submit" value="Submit" />    
</form>
```

## <a id="data-mask"></a>Se till att känsliga data som visas på användarskärmen maskeras

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | Känsliga data, till exempel lösenord, kreditkortsnummer, SSN etc. ska vara dold när visas på skärmen. Det här är att förhindra att obehöriga personer från att komma åt data (t.ex. axel surfning lösenord, support om du behöver visa SSN antal användare). Kontrollera att dessa dataelement är inte synliga i oformaterad text och maskeras på rätt sätt. Den måste vara åtgärdat vid accepterande av organisationens dem som indata (till exempel. Ange typ = ”password”) samt visa tillbaka på skärmen (t.ex. Visa endast de sista 4 siffrorna i kreditkortsnumret). |

## <a id="dynamic-users"></a>Implementera dynamisk datamaskning för att begränsa exponering icke privilegierad användare för känsliga data

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Sql Azure, OnPrem |
| **Attribut**              | SQL-Version - V12, SQL-Version - MsSQL2016 |
| **Referenser**              | [Dynamisk Datamaskning](https://msdn.microsoft.com/library/mt130841) |
| **Steg** | Syftet med dynamisk datamaskning är att begränsa exponering av känsliga data, hindra användare som inte ska ha åtkomst till data från att visa den. Dynamisk datamaskning syftar inte till att förhindra användare från att ansluta direkt till databasen och köra fullständig frågor som exponerar delar av känsliga data. Dynamisk datamaskning är kompletterar andra SQL Server-säkerhetsfunktioner (granskning, kryptering och säkerhet på radnivå...) och det rekommenderas att använda den här funktionen tillsammans med dem också för att bättre skydda känsliga data i den -databasen. Observera att den här funktionen stöds endast av SQL Server från och med 2016 och Azure SQL Database. |

## <a id="salted-hash"></a>Se till att lösenord lagras i saltat hash-format

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Lösenord Hashing med hjälp av .NET Crypto API: er](https://docs.asp.net/en/latest/security/data-protection/consumer-apis/password-hashing.html) |
| **Steg** | Lösenord ska lagras inte i anpassade store användardatabaser. Hashvärden för lösenord ska lagras med salt värden i stället. Kontrollera att salt för användaren är alltid unikt och du tillämpa b-crypt, s-crypt eller PBKDF2 innan du lagrar lösenordet, och det minsta work faktor iterationsantal 150 000 slingor till att undanröja möjligheterna för råstyrkeattacker.| 

## <a id="db-encrypted"></a>Se till att känsliga data i databaskolumner är krypterad

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Version av SQL - alla |
| **Referenser**              | [Kryptera känsliga data i SQLServer](https://technet.microsoft.com/library/ff848751(v=sql.105).aspx), [så här: Kryptera en kolumn med Data i SQLServer](https://msdn.microsoft.com/library/ms179331), [kryptera med certifikat](https://msdn.microsoft.com/library/ms188061) |
| **Steg** | Känsliga data, till exempel kreditkortsnummer måste vara krypterade i databasen. Data kan krypteras med kryptering på enhetsnivå kolumnen eller av en program-funktion med hjälp av funktioner för kryptering. |

## <a id="tde-enabled"></a>Kontrollera att databasnivå-kryptering (TDE) är aktiverat

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Förstå SQL Server Transparent datakryptering (TDE)](https://technet.microsoft.com/library/bb934049(v=sql.105).aspx) |
| **Steg** | Transparent datakryptering (TDE)-funktionen i SQLServer hjälper i kryptera känsliga data i en databas och skydda nycklar som används för att kryptera data med ett certifikat. Detta förhindrar att någon utan nycklar med hjälp av data. TDE skyddar data ”i vila”, vilket innebär att data-och log. Tjänsten ger möjlighet att följa många lagar, bestämmelser och riktlinjerna i olika branscher. |

## <a id="backup"></a>Se till att databassäkerhetskopiorna är krypterad

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | SQL Azure, OnPrem |
| **Attribut**              | SQL-Version - V12, SQL-Version - MsSQL2014 |
| **Referenser**              | [SQL database backup-kryptering](https://msdn.microsoft.com/library/dn449489) |
| **Steg** | SQL Server har möjlighet att kryptera data när du skapar en säkerhetskopia. Genom att ange krypteringsalgoritmen och encryptor (ett certifikat eller en asymmetrisk nyckel) när du skapar en säkerhetskopia är en kan skapa en krypterad säkerhetskopiefil. |

## <a id="api-browser"></a>Se till att känsliga data som är relevanta för webb-API inte lagras i webbläsarens storage

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webb-API | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | MVC 5, MVC 6 |
| **Attribut**              | Identitetsprovider för Identity - Provider – ADFS, Azure AD |
| **Referenser**              | Gäller inte  |
| **Steg** | <p>I vissa implementeringar lagras känsliga artefakter som är relevanta för webb-API-autentisering i webbläsarens lokal lagring. Azure AD authentication artefakter som t.ex. adal.idtoken, adal.nonce.idtoken, adal.access.token.key, adal.token.keys, adal.state.login, adal.session.state, adal.expiration.key osv.</p><p>Alla dessa artefakter är tillgängliga även efter logga ut eller webbläsaren är stängd. Om en angripare får åtkomst till dessa artefakter kan kan han/hon återanvända dem för att komma åt skyddade resurser-API: er. Se till att alla känsliga artefakter relaterade till webb-API inte lagras i webbläsarens lagring. I fall där klientsidan lagring krävs (t.ex. enkel sidan program (SPA) som utnyttjar Implicit OpenIdConnect/OAuth-flöden måste lagra åtkomsttoken lokalt), Använd lagringsalternativ med inte har persistence. t.ex. vill SessionStorage LocalStorage.</p>| 

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

## <a id="encrypt-docdb"></a>Kryptera känsliga data som lagras i Cosmos DB

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Document DB | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | Kryptera känsliga data på programnivå innan de lagras i dokumentet DB eller lagra känsliga data i andra lagringslösningar som Azure Storage eller Azure SQL| 

## <a id="disk-vm"></a>Använda Azure Disk Encryption för att kryptera diskar som används av virtuella datorer

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure IaaS VM-Förtroendegräns | 
| **SDL fas**               | Distribution |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Med Azure Disk Encryption för att kryptera diskar som används av dina virtuella datorer](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines) |
| **Steg** | <p>Azure Disk Encryption är en ny funktion som är en förhandsversion. Den här funktionen kan du kryptera OS-diskar och datadiskar som används av en IaaS-dator. För Windows krypteras enheterna som med BitLocker-kryptering branschstandard. För Linux krypteras diskar med hjälp av teknik för DM-Crypt. Detta är integrerat med Azure Key Vault så att du kan styra och hantera krypteringsnycklar för disken. Azure Disk Encryption-lösningen stöder följande tre kunden krypteringsscenarier:</p><ul><li>Aktivera kryptering på den nya virtuella IaaS-datorer skapas från kund-krypterad VHD-filer och som tillhandahålls av kunden krypteringsnycklar, som är lagrade i Azure Key Vault.</li><li>Aktivera kryptering på den nya virtuella IaaS-datorer skapas från Azure Marketplace.</li><li>Aktivera kryptering på befintliga virtuella IaaS-datorer som redan körs i Azure.</li></ul>| 

## <a id="fabric-apps"></a>Kryptera hemligheter i Service Fabric-program

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Service Fabric-Förtroendegräns | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Environment - Azure |
| **Referenser**              | [Hantera hemligheter i Service Fabric-program](https://azure.microsoft.com/documentation/articles/service-fabric-application-secret-management/) |
| **Steg** | Hemligheter kan vara känslig information, till exempel storage-anslutningssträngar, lösenord och andra värden som inte ska hanteras i oformaterad text. Använda Azure Key Vault för att hantera nycklar och hemligheter i service fabric-program. |

## <a id="modeling-teams"></a>Utföra security modellering och använda enheter/Verksamhetsteamen om det behövs

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Dynamics CRM | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | Utföra security modellering och använda enheter/Verksamhetsteamen om det behövs |

## <a id="entities"></a>Minimera åtkomst om du vill dela funktionen på kritiska entiteter

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Dynamics CRM | 
| **SDL fas**               | Distribution |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | Minimera åtkomst om du vill dela funktionen på kritiska entiteter |

## <a id="good-practices"></a>Utbilda användare på riskerna med funktionen för Dynamics CRM-resurs och säkerhetsprinciper

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Dynamics CRM | 
| **SDL fas**               | Distribution |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | Utbilda användare på riskerna med funktionen för Dynamics CRM-resurs och säkerhetsprinciper |

## <a id="exception-mgmt"></a>Inkludera en regel för utveckling-standarder som proscribing med config information i hantering av undantag

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Dynamics CRM | 
| **SDL fas**               | Distribution |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | Inkludera en regel för utveckling-standarder som proscribing med config information i hantering av undantag utanför utveckling. Testa för detta som en del av kodgranskningar eller återkommande kontroll.|

## <a id="sse-preview"></a>Använd Azure Storage Service Encryption (SSE) för Data i vila (förhandsversion)

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Storage | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | StorageType - Blob |
| **Referenser**              | [Azure Storage Service Encryption för Data i vila (förhandsversion)](https://azure.microsoft.com/documentation/articles/storage-service-encryption/) |
| **Steg** | <p>Azure Storage Service Encryption (SSE) för vilande Data hjälper dig att skydda och skydda dina data för att uppfylla organisationens säkerhets- och efterlevnadsbestämmelser. Med den här funktionen krypterar Azure Storage automatiskt dina data för beständig lagring och dekrypterar dem före hämtning. Kryptering, dekryptering och nyckelhantering är helt transparent för användarna. SSE gäller endast för blockblobbar, sidblobbar och tilläggsblobbar. Andra typer av data, inklusive tabeller, köer och filer, krypteras inte.</p><p>Kryptering och dekryptering arbetsflöde:</p><ul><li>Kunden aktiverar kryptering på lagringskontot</li><li>När kunden skriver nya data (PLACERA Blob, PLACERA Block, PLACERA sidan osv.) till Blob-lagring. varje skrivning krypteras med 256-bitars AES-kryptering, en av de starkaste blockchiffer som är tillgängliga</li><li>När kunden behöver åtkomst till data (hämta Blob, osv.), dekrypteras data automatiskt innan det returneras till användaren</li><li>Om kryptering har inaktiverats kan nya skrivningar krypteras inte längre och befintliga krypterade data förblir krypterade tills har skrivits av användaren. Även om kryptering är aktiverat krypteras skrivningar till Blob storage. Tillståndet för data ändras inte av användaren växla mellan aktivering/inaktivering av kryptering för storage-konto</li><li>Alla krypteringsnycklar lagras, krypterade och hanteras av Microsoft</li></ul><p>Observera att nycklarna som används för kryptering för närvarande hanteras av Microsoft. Microsoft genererar nycklarna ursprungligen och hantera säker lagring av nycklar samt vanliga rotationen enligt definitionen i interna Microsoft-princip. I framtiden kommer kunderna får möjlighet att hantera sina egna > krypteringsnycklar, och ange en migreringsvägen från Microsoft-hanterade nycklar till Kundhanterade nycklar.</p>| 

## <a id="client-storage"></a>Använda Client Side Encryption för att lagra känsliga data i Azure Storage

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Storage | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Client Side Encryption och Azure Key Vault för Microsoft Azure Storage](https://azure.microsoft.com/documentation/articles/storage-client-side-encryption/), [självstudien: Kryptera och dekryptera blobbar i Microsoft Azure Storage med Azure Key Vault](https://azure.microsoft.com/documentation/articles/storage-encrypt-decrypt-blobs-key-vault/), [lagra Data säkert i Azure Blob Storage med Azure-tillägg för kryptering](https://blogs.msdn.microsoft.com/partnercatalystteam/2015/06/17/storing-data-securely-in-azure-blob-storage-with-azure-encryption-extensions/) |
| **Steg** | <p>Azure Storage-klientbiblioteket för .NET Nuget-paket har stöd för kryptering av data i klientprogram före överföringen till Azure Storage och dekryptering av data under nedladdningen till klienten. Biblioteket stöder även integrering med Azure Key Vault för hantering av nycklar för lagringskonton. Här är en kort beskrivning av hur kryptering fungerar:</p><ul><li>SDK för Azure Storage-klient genererar en content krypteringsnyckel (CEK), vilket är en symmetrisk nyckel för en gång användning</li><li>Kundernas data krypteras med den här CEK</li><li>CEK kapslas sedan (krypterad) med klartextnyckel-nyckel (KEK). KEK kan identifieras med en nyckelidentifierare och vara ett asymmetriskt nyckelpar eller en symmetrisk nyckel och kan hanteras lokalt eller i Azure Key Vault. Storage-klienten sig själv har aldrig åtkomst till KEK. Den anropar bara viktiga radbrytning algoritmen som tillhandahålls av Key Vault. Kunderna kan välja att använda anpassade providers för nyckeln radbrytning/uppackning om de vill</li><li>Krypterade data överförs sedan till Azure Storage-tjänsten. Kontrollera länkarna i referensavsnittet för på låg nivå implementeringsdetaljer.</li></ul>|

## <a id="pii-phones"></a>Kryptera känsliga eller PII-data som skrivs till lokal lagring för telefoner

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Mobile Client | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generic, Xamarin  |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Hantera inställningar och funktioner på dina enheter med Microsoft Intune-principer](https://docs.microsoft.com/intune/deploy-use/manage-settings-and-features-on-your-devices-with-microsoft-intune-policies), [nyckelring Valet](https://components.xamarin.com/view/square.valet) |
| **Steg** | <p>Om programmet skriver känslig information, t.ex. användarens personligt identifierbar information (e-post, telefonnummer, förnamn, efternamn, inställningar osv.) -på Mobiles filsystem, sedan den ska krypteras innan du skriver till det lokala filsystemet. Om programmet är ett företagsprogram, utforska du möjligheten att publicera program med hjälp av Windows Intune.</p>|

### <a name="example"></a>Exempel
Intune kan konfigureras med följande säkerhetsprinciper som skyddar känsliga data: 
```csharp
Require encryption on mobile device    
Require encryption on storage cards
Allow screen capture
```

### <a name="example"></a>Exempel
Om programmet inte är ett företagsprogram, använda plattform tillhandahålls keystore, nyckelringar krypteringsnycklar, med hjälp av vilken kryptografisk åtgärd kan utföras i filsystemet. Följande kodavsnitt visar hur du kommer åt nyckeln från nyckelringen med xamarin: 
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

## <a id="binaries-end"></a>Förvräng genererade binärfiler innan du distribuerar till slutanvändare

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Mobile Client | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Krypto döljande för .net](https://www.ssware.com/cryptoobfuscator/obfuscator-net.htm) |
| **Steg** | Genererade binärfiler (sammansättningar i apk) ska vara dold för att stoppa bakåtkompilering av sammansättningar. Verktyg som `CryptoObfuscator` kan användas för detta ändamål. |

## <a id="cert"></a>Ange clientCredentialType till certifikat eller Windows

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | WCF | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | .NET Framework 3 |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Spikning](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_token) |
| **Steg** | Med hjälp av en UsernameToken med lösenord i klartext via en okrypterad kanal exponerar lösenordet för att angripare som kan söka SOAP-meddelanden. Leverantörer av tjänster som använder UsernameToken kan acceptera lösenord skickas i klartext. Skicka lösenord i klartext via en okrypterad kanal kan exponera autentiseringsuppgifterna för attacker som kan söka SOAP-meddelandet. | 

### <a name="example"></a>Exempel
Följande Providerkonfiguration för WCF-tjänsten använder UsernameToken: 
```
<security mode="Message"> 
<message clientCredentialType="UserName" />
``` 
Ange clientCredentialType till certifikat eller Windows. 

## <a id="security"></a>WCF-Security-läge är inte aktiverat

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | WCF | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generic, .NET Framework 3 |
| **Attribut**              | Läget - Transport, säkerhetsläget - säkerhetsmeddelande |
| **Referenser**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [spikning kungariket](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference), [grunderna i säkerhet för WCF CoDe Magazine](https://www.codemag.com/article/0611051) |
| **Steg** | Ingen transport eller meddelandet säkerhet har definierats. Program som överför meddelanden utan transport eller meddelandet säkerhet inte kan garantera integritet eller konfidentialiteten för meddelanden. När en bindning för WCF-säkerhet är inställd på Ingen, både transport- och meddelandet säkerhet är inaktiverade. |

### <a name="example"></a>Exempel
Följande konfiguration ställer in säkerhetsläget till ingen. 
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
* Transport. Använder transport security för ömsesidig autentisering och meddelandet. 
* meddelande. Använder meddelandesäkerhet för ömsesidig autentisering och meddelandet. 
* Båda. Kan du ange inställningar för transport och meddelandet på radnivå (endast MSMQ stöder detta). 
* TransportWithMessageCredential. Autentiseringsuppgifter skickas med meddelandet och meddelandet skydd och serverautentisering tillhandahålls av transportlagret. 
* TransportCredentialOnly. Klientens autentiseringsuppgifter skickas med transportlagret och inget meddelande skydd används. Använd transport och meddelandet security för att skydda integriteten och sekretessen för meddelanden från. Av konfigurationen nedan talar om för tjänsten att använda transportsäkerhet med autentiseringsuppgifter för meddelandet.
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
