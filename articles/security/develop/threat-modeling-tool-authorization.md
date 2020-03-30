---
title: Auktorisering – Microsofts verktyg för hotmodellering – Azure | Microsoft-dokument
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
ms.openlocfilehash: 75bbce0f1e9787e55880ccac80dacb5457e1f2c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68728372"
---
# <a name="security-frame-authorization--mitigations"></a>Säkerhetsram: Auktorisering | Mitigations 
| Produkt/tjänst | Artikel |
| --------------- | ------- |
| **Gräns för datorförtroende** | <ul><li>[Se till att rätt åtkomstkontrollistor är konfigurerade för att begränsa obehörig åtkomst till data på enheten](#acl-restricted-access)</li><li>[Kontrollera att känsligt användarspecifikt programinnehåll lagras i användarprofilkatalogen](#sensitive-directory)</li><li>[Se till att de distribuerade programmen körs med minst behörighet](#deployed-privileges)</li></ul> |
| **Webbprogram** | <ul><li>[Framtvinga sekventiell stegorder vid bearbetning av affärslogikflöden](#sequential-logic)</li><li>[Mekanism för att införa hastighetsbegränsning för att förhindra uppräkning](#rate-enumeration)</li><li>[Se till att korrekt tillstånd finns på plats och principen om lägsta behörighet följs](#principle-least-privilege)</li><li>[Affärslogik- och resursåtkomstauktoriseringsbeslut bör inte baseras på parametrar för inkommande begäran](#logic-request-parameters)</li><li>[Se till att innehåll och resurser inte är uppräkningsbara eller tillgängliga via kraftfull surfning](#enumerable-browsing)</li></ul> |
| **Databas** | <ul><li>[Kontrollera att konton med minst privilegierade privilegier används för att ansluta till databasservern](#privileged-server)</li><li>[Implementera RLS för fjärrnivå för att förhindra att klienter kommer åt varandras data](#rls-tenants)</li><li>[Sysadmin-rollen bör endast ha giltiga nödvändiga användare](#sysadmin-users)</li></ul> |
| **IoT Cloud Gateway** | <ul><li>[Ansluta till Molngateway med token med lägsta behörighet](#cloud-least-privileged)</li></ul> |
| **Azure Event Hub** | <ul><li>[Använda en SAS-nyckel för sändningsbehörighet för att generera enhetstoken](#sendonly-sas)</li><li>[Använd inte åtkomsttoken som ger direkt åtkomst till händelsehubben](#access-tokens-hub)</li><li>[Ansluta till händelsehubben med SAS-nycklar som har de minsta behörigheter som krävs](#sas-minimum-permissions)</li></ul> |
| **Azure-dokument DB** | <ul><li>[Använd resurstoken för att ansluta till Azure Cosmos DB när det är möjligt](#resource-docdb)</li></ul> |
| **Azure-förtroendegräns** | <ul><li>[Aktivera hantering av finkornig åtkomst till Azure-prenumeration med RBAC](#grained-rbac)</li></ul> |
| **Förtroendegräns för serviceinfrastruktur** | <ul><li>[Begränsa klientens åtkomst till klusteråtgärder med hjälp av RBAC](#cluster-rbac)</li></ul> |
| **Dynamics CRM** | <ul><li>[Utför säkerhetsmodellering och använd fältnivåsäkerhet där det behövs](#modeling-field)</li></ul> |
| **Dynamics CRM-portal** | <ul><li>[Utför säkerhetsmodellering av portalkonton med tanke på att säkerhetsmodellen för portalen skiljer sig från resten av CRM](#portal-security)</li></ul> |
| **Azure-lagring** | <ul><li>[Bevilja finkornig behörighet för en rad entiteter i Azure Table Storage](#permission-entities)</li><li>[Aktivera RBAC (Role-Based Access Control) till Azure-lagringskonto med Azure Resource Manager](#rbac-azure-manager)</li></ul> |
| **Mobil klient** | <ul><li>[Implementera implicit fängelsebrytning eller böka upptäckt](#rooting-detection)</li></ul> |
| **WCF** | <ul><li>[Svag klassreferens i WCF](#weak-class-wcf)</li><li>[WCF-Implementera auktoriseringskontroll](#wcf-authz)</li></ul> |
| **Webb-API** | <ul><li>[Implementera rätt auktoriseringsmekanism i ASP.NET webb-API](#authz-aspnet)</li></ul> |
| **IoT-enhet** | <ul><li>[Utföra auktoriseringskontroller i enheten om den stöder olika åtgärder som kräver olika behörighetsnivåer](#device-permission)</li></ul> |
| **IoT-fältgateway** | <ul><li>[Utföra auktoriseringskontroller i Fältgatewayen om den stöder olika åtgärder som kräver olika behörighetsnivåer](#field-permission)</li></ul> |

## <a name="ensure-that-proper-acls-are-configured-to-restrict-unauthorized-access-to-data-on-the-device"></a><a id="acl-restricted-access"></a>Se till att rätt åtkomstkontrollistor är konfigurerade för att begränsa obehörig åtkomst till data på enheten

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Gräns för datorförtroende | 
| **SDL-fas**               | Distribution |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Se till att rätt åtkomstkontrollistor är konfigurerade för att begränsa obehörig åtkomst till data på enheten|

## <a name="ensure-that-sensitive-user-specific-application-content-is-stored-in-user-profile-directory"></a><a id="sensitive-directory"></a>Kontrollera att känsligt användarspecifikt programinnehåll lagras i användarprofilkatalogen

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Gräns för datorförtroende | 
| **SDL-fas**               | Distribution |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Kontrollera att känsligt användarspecifikt programinnehåll lagras i användarprofilkatalogen. Detta för att förhindra att flera användare av datorn kommer åt varandras data.|

## <a name="ensure-that-the-deployed-applications-are-run-with-least-privileges"></a><a id="deployed-privileges"></a>Se till att de distribuerade programmen körs med minst behörighet

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Gräns för datorförtroende | 
| **SDL-fas**               | Distribution |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Kontrollera att det distribuerade programmet körs med minst behörighet. |

## <a name="enforce-sequential-step-order-when-processing-business-logic-flows"></a><a id="sequential-logic"></a>Framtvinga sekventiell stegorder vid bearbetning av affärslogikflöden

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | För att verifiera att det här steget kördes igenom av en äkta användare vill du tillämpa programmet på att endast bearbeta affärslogikflöden i sekventiell stegordning, med alla steg som bearbetas i realistisk mänsklig tid och inte bearbetas i fel ordning, hoppas du på steg som hoppas över, och hoppade över steg , bearbetade steg från en annan användare eller för snabbt skickade transaktioner.|

## <a name="implement-rate-limiting-mechanism-to-prevent-enumeration"></a><a id="rate-enumeration"></a>Mekanism för att införa hastighetsbegränsning för att förhindra uppräkning

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Se till att känsliga identifierare är slumpmässiga. Implementera CAPTCHA-kontroll på anonyma sidor. Se till att fel och undantag inte avslöjar specifika data|

## <a name="ensure-that-proper-authorization-is-in-place-and-principle-of-least-privileges-is-followed"></a><a id="principle-least-privilege"></a>Se till att korrekt tillstånd finns på plats och principen om lägsta behörighet följs

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | <p>Principen innebär att endast de privilegier som är nödvändiga för användarna ska kunna få ett användarkonto. En säkerhetskopia behöver till exempel inte installera programvara: därför har säkerhetskopian endast behörighet att köra säkerhetskopierings- och säkerhetskopieringsrelaterade program. Alla andra privilegier, till exempel installera ny programvara, blockeras. Principen gäller även för en persondatoranvändare som vanligtvis arbetar i ett vanligt användarkonto, och öppnar ett privilegierat, lösenordsskyddat konto (det vill säga en superanvändare) endast när situationen absolut kräver det. </p><p>Den här principen kan också tillämpas på dina webbapplikationer. I stället för att enbart vara beroende av rollbaserade autentiseringsmetoder med hjälp av sessioner vill vi hellre tilldela privilegier till användare med hjälp av ett databasbaserat autentiseringssystem. Vi använder fortfarande sessioner för att identifiera om användaren var inloggad korrekt, först nu i stället för att tilldela användaren med en specifik roll som vi tilldelar honom med behörighet att verifiera vilka åtgärder han har behörighet att utföra på systemet. Också ett stort proffs av denna metod är, när en användare måste tilldelas färre privilegier dina ändringar kommer att tillämpas i farten eftersom tilldelningen inte beror på den session som annars var tvungen att löpa ut först.</p>|

## <a name="business-logic-and-resource-access-authorization-decisions-should-not-be-based-on-incoming-request-parameters"></a><a id="logic-request-parameters"></a>Affärslogik- och resursåtkomstauktoriseringsbeslut bör inte baseras på parametrar för inkommande begäran

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | När du kontrollerar om en användare är begränsad till att granska vissa data bör åtkomstbegränsningarna bearbetas på serversidan. UserID ska lagras inuti en sessionsvariabel vid inloggning och ska användas för att hämta användardata från databasen |

### <a name="example"></a>Exempel
```SQL
SELECT data 
FROM personaldata 
WHERE userID=:id < - session var 
```
Nu kan en möjlig angripare inte manipulera och ändra programåtgärden eftersom identifieraren för att hämta data hanteras på serversidan.

## <a name="ensure-that-content-and-resources-are-not-enumerable-or-accessible-via-forceful-browsing"></a><a id="enumerable-browsing"></a>Se till att innehåll och resurser inte är uppräkningsbara eller tillgängliga via kraftfull surfning

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | <p>Känsliga statiska filer och konfigurationsfiler bör inte förvaras i webbroten. För att innehåll inte ska vara offentligt bör antingen lämpliga åtkomstkontroller tillämpas eller borttagning av själva innehållet.</p><p>Dessutom är kraftfull surfning vanligtvis kombineras med Brute Force tekniker för att samla in information genom att försöka komma åt så många webbadresser som möjligt för att räkna upp kataloger och filer på en server. Angripare kan söka efter alla varianter av vanliga filer. En filsökning för lösenord skulle till exempel omfatta filer som psswd.txt, password.htm, password.dat och andra varianter.</p><p>För att minska detta bör funktioner för identifiering av brute force-försök inkluderas.</p>|

## <a name="ensure-that-least-privileged-accounts-are-used-to-connect-to-database-server"></a><a id="privileged-server"></a>Kontrollera att konton med minst privilegierade privilegier används för att ansluta till databasservern

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [SQL Database-behörighetshierarki](https://msdn.microsoft.com/library/ms191465), [SQL-databas säkerhetsbara](https://msdn.microsoft.com/library/ms190401) |
| **Steg** | Konton med minst privilegierade privilegier bör användas för att ansluta till databasen. Ansökan inloggning bör begränsas i databasen och bör endast köra valda lagrade procedurer. Ansökans inloggning bör inte ha någon direkt tabellåtkomst. |

## <a name="implement-row-level-security-rls-to-prevent-tenants-from-accessing-each-others-data"></a><a id="rls-tenants"></a>Implementera RLS för fjärrnivå för att förhindra att klienter kommer åt varandras data

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Sql Azure, OnPrem |
| **Attribut**              | SQL-version - V12, SQL-version - MsSQL2016 |
| **Referenser**              | [Säkerhet på SQL Server-radnivå (RLS)](https://msdn.microsoft.com/library/azure/dn765131.aspx) |
| **Steg** | <p>Säkerhet på radnivå ger kunder möjlighet att styra åtkomsten till rader i en databastabell baserat på egenskaperna för användaren som kör en fråga (t.ex. grupmedlemskap eller körningskontext).</p><p>RLS (Row-Level Security) förenklar utformningen och kodningen av säkerheten i ditt program. RLS låter dig implementera begränsningar för dataåtkomst för raden. Därmed får medarbetare endast tillgång till de datarader som är relevanta för deras avdelning, eller kunder får endast dataåtkomst till data som berör deras företag.</p><p>Logiken för åtkomstbegränsning finns på databasnivån i stället för bort från data på en annan programnivå. Databassystemet tillämpar åtkomstbegränsningarna varje gång dataåtkomsten görs från valfri nivå. Detta gör säkerhetssystemet mer tillförlitligt och robust genom att minska säkerhetssystemets yta.</p><p>|

Observera att RLS som en out-of-the-box databasfunktion endast gäller för SQL Server från och med 2016 och Azure SQL-databas. Om den färdiga RLS-funktionen inte implementeras bör det säkerställas att dataåtkomsten begränsas med hjälp av vyer och procedurer

## <a name="sysadmin-role-should-only-have-valid-necessary-users"></a><a id="sysadmin-users"></a>Sysadmin-rollen bör endast ha giltiga nödvändiga användare

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [SQL Database-behörighetshierarki](https://msdn.microsoft.com/library/ms191465), [SQL-databas säkerhetsbara](https://msdn.microsoft.com/library/ms190401) |
| **Steg** | Medlemmar i sysadmin-rollen för den fasta servern bör vara mycket begränsade och aldrig innehålla konton som används av program.  Läs listan över användare i rollen och ta bort onödiga konton|

## <a name="connect-to-cloud-gateway-using-least-privileged-tokens"></a><a id="cloud-least-privileged"></a>Ansluta till Molngateway med token med lägsta behörighet

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | IoT Cloud Gateway | 
| **SDL-fas**               | Distribution |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Gateway val - Azure IoT Hub |
| **Referenser**              | [Iot Hub-åtkomstkontroll](https://azure.microsoft.com/documentation/articles/iot-hub-devguide/#Security) |
| **Steg** | Ge minst behörighet till olika komponenter som ansluter till Cloud Gateway (IoT Hub). Typiskt exempel är – Enhetshantering/etableringskomponent använder registerläsning/skrivning, händelseprocessor (ASA) använder Service Connect. Enskilda enheter ansluter med autentiseringsuppgifter för enheten|

## <a name="use-a-send-only-permissions-sas-key-for-generating-device-tokens"></a><a id="sendonly-sas"></a>Använda en SAS-nyckel för sändningsbehörighet för att generera enhetstoken

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Event Hub | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Översikt över autentisering och säkerhetsmodell för eventhubbar](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Steg** | En SAS-nyckel används för att generera enskilda enhetstoken. Använda en SAS-nyckel för sändningsbehörigheter när du skapar enhetstoken för en viss utgivare|

## <a name="do-not-use-access-tokens-that-provide-direct-access-to-the-event-hub"></a><a id="access-tokens-hub"></a>Använd inte åtkomsttoken som ger direkt åtkomst till händelsehubben

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Event Hub | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Översikt över autentisering och säkerhetsmodell för eventhubbar](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Steg** | En token som ger direkt åtkomst till händelsehubben bör inte ges till enheten. Om du använder en minst privilegierad token för enheten som endast ger åtkomst till en utgivare kan du identifiera och svartlista den om den visar sig vara en otillåten eller komprometterade enhet.|

## <a name="connect-to-event-hub-using-sas-keys-that-have-the-minimum-permissions-required"></a><a id="sas-minimum-permissions"></a>Ansluta till händelsehubben med SAS-nycklar som har de minsta behörigheter som krävs

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Event Hub | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Översikt över autentisering och säkerhetsmodell för eventhubbar](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Steg** | Ge minst behörighet till olika backend-program som ansluter till händelsehubben. Generera separata SAS-nycklar för varje backend-program och ange endast de behörigheter som krävs - Skicka, ta emot eller hantera dem.|

## <a name="use-resource-tokens-to-connect-to-cosmos-db-whenever-possible"></a><a id="resource-docdb"></a>Använd resurstoken för att ansluta till Cosmos DB när det är möjligt

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure-dokument DB | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | En resurstoken associeras med en Azure Cosmos DB-behörighetsresurs och fångar relationen mellan användaren av en databas och den behörighet som användaren har för en specifik Azure Cosmos DB-programresurs (t.ex. samling, dokument). Använd alltid en resurstoken för att komma åt Azure Cosmos DB om klienten inte kan lita på att hantera huvud- eller skrivskyddade nycklar – som ett slutanvändarprogram som en mobil eller stationär klient. Använd huvudnyckeln eller skrivskyddade nycklar från backend-program som kan lagra dessa nycklar på ett säkert sätt.|

## <a name="enable-fine-grained-access-management-to-azure-subscription-using-rbac"></a><a id="grained-rbac"></a>Aktivera hantering av finkornig åtkomst till Azure-prenumeration med RBAC

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure-förtroendegräns | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Använda rolltilldelningar för att hantera åtkomsten till dina Azure-prenumerationsresurser](https://azure.microsoft.com/documentation/articles/role-based-access-control-configure/)  |
| **Steg** | Rollbaserad åtkomstkontroll (RBAC) i Azure ger tillgång till ingående åtkomsthantering för Azure. Med RBAC kan du bevilja exakt den åtkomstnivå som användarna behöver för att kunna utföra sitt arbete.|

## <a name="restrict-clients-access-to-cluster-operations-using-rbac"></a><a id="cluster-rbac"></a>Begränsa klientens åtkomst till klusteråtgärder med hjälp av RBAC

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Förtroendegräns för serviceinfrastruktur | 
| **SDL-fas**               | Distribution |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Miljö - Azure |
| **Referenser**              | [Rollbaserad åtkomstkontroll för Service Fabric-klienter](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security-roles/) |
| **Steg** | <p>Azure Service Fabric stöder två olika åtkomstkontrolltyper för klienter som är anslutna till ett Service Fabric-kluster: administratör och användare. Med åtkomstkontroll kan klusteradministratören begränsa åtkomsten till vissa klusteråtgärder för olika användargrupper, vilket gör klustret säkrare.</p><p>Administratörer har full tillgång till hanteringsfunktioner (inklusive läs-/skrivfunktioner). Användare har som standard bara läsbehörighet till hanteringsfunktioner (till exempel frågefunktioner) och möjligheten att lösa program och tjänster.</p><p>Du anger de två klientrollerna (administratör och klient) när klustret skapas genom att tillhandahålla separata certifikat för varje.</p>|

## <a name="perform-security-modeling-and-use-field-level-security-where-required"></a><a id="modeling-field"></a>Utför säkerhetsmodellering och använd fältnivåsäkerhet där det behövs

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Dynamics CRM | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Utför säkerhetsmodellering och använd fältnivåsäkerhet där det behövs|

## <a name="perform-security-modeling-of-portal-accounts-keeping-in-mind-that-the-security-model-for-the-portal-differs-from-the-rest-of-crm"></a><a id="portal-security"></a>Utför säkerhetsmodellering av portalkonton med tanke på att säkerhetsmodellen för portalen skiljer sig från resten av CRM

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Dynamics CRM-portal | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Utför säkerhetsmodellering av portalkonton med tanke på att säkerhetsmodellen för portalen skiljer sig från resten av CRM|

## <a name="grant-fine-grained-permission-on-a-range-of-entities-in-azure-table-storage"></a><a id="permission-entities"></a>Bevilja finkornig behörighet för en rad entiteter i Azure Table Storage

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Storage | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | StorageType - tabell |
| **Referenser**              | [Så här delegerar du åtkomst till objekt i ditt Azure-lagringskonto med SAS](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_data-plane-security) |
| **Steg** | I vissa affärsscenarier kan Azure Table Storage krävas för att lagra känsliga data som vänder sig till olika parter. T.ex. känsliga uppgifter om olika länder/regioner. I sådana fall kan SAS-signaturer konstrueras genom att ange partitions- och radnyckelintervall, så att en användare kan komma åt data som är specifika för ett visst land/en viss region.| 

## <a name="enable-role-based-access-control-rbac-to-azure-storage-account-using-azure-resource-manager"></a><a id="rbac-azure-manager"></a>Aktivera RBAC (Role-Based Access Control) till Azure-lagringskonto med Azure Resource Manager

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Storage | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Så här skyddar du ditt lagringskonto med rollbaserad åtkomstkontroll (RBAC)](https://azure.microsoft.com/documentation/articles/storage-security-guide/#management-plane-security) |
| **Steg** | <p>När du skapar ett nytt lagringskonto väljer du en distributionsmodell av Classic eller Azure Resource Manager. Den klassiska modellen för att skapa resurser i Azure tillåter bara allt eller inget åtkomst till prenumerationen och i sin tur lagringskontot.</p><p>Med Azure Resource Manager-modellen placerar du lagringskontot i en resursgrupp och styr åtkomsten till hanteringsplanet för det specifika lagringskontot med Azure Active Directory. Du kan till exempel ge specifika användare möjlighet att komma åt lagringskontonycklarna, medan andra användare kan visa information om lagringskontot, men inte komma åt lagringskontonycklarna.</p>|

## <a name="implement-implicit-jailbreak-or-rooting-detection"></a><a id="rooting-detection"></a>Implementera implicit fängelsebrytning eller böka upptäckt

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Mobil klient | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | <p>Ansökan bör skydda sin egen konfiguration och användardata i fall om telefonen är rotad eller fängelse bruten. Böka / fängelse bryta innebär obehörig åtkomst, vilket normala användare inte kommer att göra på sina egna telefoner. Därför ansökan bör ha implicit identifiering logik vid programstart, för att upptäcka om telefonen har rotats.</p><p>Identifieringslogiken kan helt enkelt komma åt filer som normalt bara root-användare kan komma åt, till exempel:</p><ul><li>/system/app/Superuser.apk</li><li>/sbin/su</li><li>/system/bin/su</li><li>/system/xbin/su</li><li>/data/lokal/xbin/su</li><li>/data/lokal/bin/su</li><li>/system/sd/xbin/su</li><li>/system/bin/felsäker/su</li><li>/data/lokal/su</li></ul><p>Om programmet kan komma åt någon av dessa filer, betecknas det att programmet körs som rotanvändare.</p>|

## <a name="weak-class-reference-in-wcf"></a><a id="weak-class-wcf"></a>Svag klassreferens i WCF

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | WCF | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänt, NET Framework 3 |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify Kungariket](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference) |
| **Steg** | <p>Systemet använder en svag klassreferens, vilket kan göra det möjligt för en angripare att köra obehörig kod. Programmet refererar till en användardefinierad klass som inte är unikt identifierad. När .NET läser in den här svagt identifierade klassen söker CLR-typhanteraren efter klassen på följande platser i angiven ordning:</p><ol><li>Om sammansättningen av typen är känd söker inläsaren igenom konfigurationsfilens omdirigeringsplatser, GAC, den aktuella sammansättningen med hjälp av konfigurationsinformation och programbaskatalogen</li><li>Om sammansättningen är okänd söker lastaren igenom den aktuella sammansättningen, mscorlib och platsen som returneras av TypeResolve-händelsehanteraren</li><li>Denna CLR-sökordning kan ändras med krokar som typbefordringsmekanismen och händelsen AppDomain.TypeResolve</li></ol><p>Om en angripare utnyttjar CLR-sökordningen genom att skapa en alternativ klass med samma namn och placera den på en alternativ plats som CLR kommer att läsa in först, kommer CLR oavsiktligt att köra den medföljande angriparen-koden</p>|

### <a name="example"></a>Exempel
Elementet `<behaviorExtensions/>` i WCF-konfigurationsfilen nedan instruerar WCF att lägga till en anpassad beteendeklass i ett visst WCF-tillägg.
```
<system.serviceModel>
    <extensions>
        <behaviorExtensions>
            <add name=""myBehavior"" type=""MyBehavior"" />
        </behaviorExtensions>
    </extensions>
</system.serviceModel>
```
Om du använder fullständigt kvalificerade (starka) namn identifierar du unikt en typ och ökar systemets säkerhet ytterligare. Använd fullständigt kvalificerade sammansättningsnamn när du registrerar typer i filerna machine.config och app.config.

### <a name="example"></a>Exempel
Elementet `<behaviorExtensions/>` i WCF-konfigurationsfilen nedan instruerar WCF att lägga till starkt refererad anpassad beteendeklass till ett visst WCF-tillägg.
```
<system.serviceModel>
    <extensions>
        <behaviorExtensions>
            <add name=""myBehavior"" type=""Microsoft.ServiceModel.Samples.MyBehaviorSection, MyBehavior,
            Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"" />
        </behaviorExtensions>
    </extensions>
</system.serviceModel>
```

## <a name="wcf-implement-authorization-control"></a><a id="wcf-authz"></a>WCF-Implementera auktoriseringskontroll

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | WCF | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänt, NET Framework 3 |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify Kungariket](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference) |
| **Steg** | <p>Den här tjänsten använder ingen auktoriseringskontroll. När en klient anropar en viss WCF-tjänst tillhandahåller WCF olika auktoriseringsscheman som verifierar att anroparen har behörighet att köra tjänstmetoden på servern. Om auktoriseringskontroller inte är aktiverade för WCF-tjänster kan en autentiserat användare uppnå eskalering av privilegier.</p>|

### <a name="example"></a>Exempel
Följande konfiguration instruerar WCF att inte kontrollera auktoriseringsnivån för klienten när tjänsten körs:
```
<behaviors>
    <serviceBehaviors>
        <behavior>
            ...
            <serviceAuthorization principalPermissionMode=""None"" />
        </behavior>
    </serviceBehaviors>
</behaviors>
```
Använd ett serviceauktoriseringsschema för att kontrollera att anroparen av servicemetoden har behörighet att göra det. WCF tillhandahåller två lägen och tillåter definitionen av ett anpassat auktoriseringsschema. I användningsfönstrets grupperingsläge används Windows-roller och användare och användningAspNetRoles-läget använder en ASP.NET rollprovider, till exempel SQL Server, för att autentisera.

### <a name="example"></a>Exempel
Följande konfiguration instruerar WCF att se till att klienten är en del av gruppen Administratörer innan du kör tjänsten Lägg till:
```
<behaviors>
    <serviceBehaviors>
        <behavior>
            ...
            <serviceAuthorization principalPermissionMode=""UseWindowsGroups"" />
        </behavior>
    </serviceBehaviors>
</behaviors>
```
Tjänsten deklareras sedan som följande:
```
[PrincipalPermission(SecurityAction.Demand,
Role = ""Builtin\\Administrators"")]
public double Add(double n1, double n2)
{
double result = n1 + n2;
return result;
}
```

## <a name="implement-proper-authorization-mechanism-in-aspnet-web-api"></a><a id="authz-aspnet"></a>Implementera rätt auktoriseringsmekanism i ASP.NET webb-API

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webb-API | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Generisk, MVC5 |
| **Attribut**              | Ej till, identitetsprovider – ADFS, identitetsprovider – Azure AD |
| **Referenser**              | [Autentisering och auktorisering i ASP.NET webb-API](https://www.asp.net/web-api/overview/security/authentication-and-authorization-in-aspnet-web-api) |
| **Steg** | <p>Rollinformation för programanvändarna kan härledas från Azure AD- eller ADFS-anspråk om programmet förlitar sig på dem som identitetsprovider eller själva programmet kan tillhandahålla det. I något av dessa fall bör den anpassade auktoriseringsimplementeringen validera användarrollinformationen.</p><p>Rollinformation för programanvändarna kan härledas från Azure AD- eller ADFS-anspråk om programmet förlitar sig på dem som identitetsprovider eller själva programmet kan tillhandahålla det. I något av dessa fall bör den anpassade auktoriseringsimplementeringen validera användarrollinformationen.</p>

### <a name="example"></a>Exempel
```csharp
[AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, Inherited = true, AllowMultiple = true)]
public class ApiAuthorizeAttribute : System.Web.Http.AuthorizeAttribute
{
        public async override Task OnAuthorizationAsync(HttpActionContext actionContext, CancellationToken cancellationToken)
        {
            if (actionContext == null)
            {
                throw new Exception();
            }

            if (!string.IsNullOrEmpty(base.Roles))
            {
                bool isAuthorized = ValidateRoles(actionContext);
                if (!isAuthorized)
                {
                    HandleUnauthorizedRequest(actionContext);
                }
            }

            base.OnAuthorization(actionContext);
        }

public bool ValidateRoles(actionContext)
{
   //Authorization logic here; returns true or false
}

}
```
Alla styrenheter och åtgärdsmetoder som behöver skyddas bör dekoreras med ovanstående attribut.
```csharp
[ApiAuthorize]
public class CustomController : ApiController
{
     //Application code goes here
}
```

## <a name="perform-authorization-checks-in-the-device-if-it-supports-various-actions-that-require-different-permission-levels"></a><a id="device-permission"></a>Utföra auktoriseringskontroller i enheten om den stöder olika åtgärder som kräver olika behörighetsnivåer

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | IoT-enhet | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | <p>Enheten bör ge den som ringer behörighet att kontrollera om den som ringer har de behörigheter som krävs för att utföra den begärda åtgärden. För t.ex. Låt oss säga att enheten är en Smart Door Lock som kan övervakas från molnet, plus att det ger funktioner som Fjärrlåsning dörren.</p><p>Smart Door Lock ger upplåsningsfunktion endast när någon fysiskt kommer nära dörren med ett kort. I det här fallet bör implementeringen av fjärrkommandot och kontrollen göras på ett sådant sätt att det inte ger några funktioner för att låsa upp dörren eftersom molngatewayen inte har behörighet att skicka ett kommando för att låsa upp dörren.</p>|

## <a name="perform-authorization-checks-in-the-field-gateway-if-it-supports-various-actions-that-require-different-permission-levels"></a><a id="field-permission"></a>Utföra auktoriseringskontroller i Fältgatewayen om den stöder olika åtgärder som kräver olika behörighetsnivåer

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | IoT-fältgateway | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Fältgatewayen bör ge anroparen behörighet att kontrollera om anroparen har de behörigheter som krävs för att utföra den begärda åtgärden. För t.ex. det bör finnas olika behörigheter för en admin användargränssnitt / API som används för att konfigurera ett fält gateway v / s-enheter som ansluter till den.|
