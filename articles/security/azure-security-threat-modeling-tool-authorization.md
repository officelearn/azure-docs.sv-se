---
title: Auktoriserings - verktyget Microsoft Threat modellering - Azure | Microsoft Docs
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
ms.openlocfilehash: b9ad3ceeb77a4adc2c47b262aa40a48c14423198
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2018
---
# <a name="security-frame-authorization--mitigations"></a>Säkerhet ram: Auktorisering | Åtgärder 
| Produkter eller tjänster | Artikel |
| --------------- | ------- |
| **Datorn Förtroendegräns** | <ul><li>[Kontrollera att rätt ACL: er har konfigurerats för att begränsa obehörig åtkomst till data på enheten](#acl-restricted-access)</li><li>[Se till att känslig användarspecifika programinnehållet lagras i användarprofil katalog](#sensitive-directory)</li><li>[Se till att distribuerade program körs med de lägsta privilegierna](#deployed-privileges)</li></ul> |
| **Webbprogram** | <ul><li>[Tillämpa sekventiella steg ordning vid bearbetning av business logic flöden](#sequential-logic)</li><li>[Implementera hastighet begränsa mekanism för att förhindra att uppräkningen](#rate-enumeration)</li><li>[Se till att rätt behörighet är på plats och principen lägsta privilegier följs](#principle-least-privilege)</li><li>[Företag logik och resursen åtkomst auktoriseringsbeslut ska inte baseras på parametrar för inkommande begäran](#logic-request-parameters)</li><li>[Kontrollera innehållet och resurser är inte enumerable eller är tillgängliga via kraftfullt surfning](#enumerable-browsing)</li></ul> |
| **Databas** | <ul><li>[Se till att minst Privilegierade konton som används för att ansluta till databasservern](#privileged-server)</li><li>[Implementera raden nivå säkerhet RLS att hindra klienter från att komma åt varandras data](#rls-tenants)</li><li>[Rollen sysadmin får endast ha giltiga nödvändiga användare](#sysadmin-users)</li></ul> |
| **Gateway för IoT-moln** | <ul><li>[Ansluta till Gateway moln som använder lägsta behörighet token](#cloud-least-privileged)</li></ul> |
| **Azure Event Hub** | <ul><li>[Använda en skicka-behörigheter endast SAS-nyckel för att skapa enhetstoken](#sendonly-sas)</li><li>[Använd inte åtkomsttoken som ger direktåtkomst till Händelsehubben](#access-tokens-hub)</li><li>[Ansluta till en Händelsehubb med hjälp av SAS-nycklar som har den lägsta behörigheten som krävs](#sas-minimum-permissions)</li></ul> |
| **Azure dokumentet DB** | <ul><li>[Använd resource token för att ansluta till Azure Cosmos DB när det är möjligt](#resource-docdb)</li></ul> |
| **Azure Förtroendegräns** | <ul><li>[Aktivera detaljerad åtkomsthantering för Azure-prenumeration med RBAC](#grained-rbac)</li></ul> |
| **Service Fabric-Förtroendegräns** | <ul><li>[Begränsa åtkomsten för klientens till klusteråtgärder med RBAC](#cluster-rbac)</li></ul> |
| **Dynamics CRM** | <ul><li>[Utföra security modellering och använder fältet Level Security behov](#modeling-field)</li></ul> |
| **Dynamics CRM-portalen** | <ul><li>[Utföra modellering säkerhet i åtanke säkerhetsmodell för portalen skiljer sig från resten av CRM-portal konton](#portal-security)</li></ul> |
| **Azure Storage** | <ul><li>[Ger detaljerad behörighet på ett intervall med enheter i Azure-tabellagring](#permission-entities)</li><li>[Aktivera rollbaserad åtkomstkontroll (RBAC) till Azure storage-konto med Azure Resource Manager](#rbac-azure-manager)</li></ul> |
| **Mobil klient** | <ul><li>[Implementera implicit jailbreak eller rota identifiering](#rooting-detection)</li></ul> |
| **WCF** | <ul><li>[Svag klassreferens i WCF](#weak-class-wcf)</li><li>[Implementera WCF-auktorisering kontroll](#wcf-authz)</li></ul> |
| **Webb-API** | <ul><li>[Implementera rätt mekanism i ASP.NET Web API](#authz-aspnet)</li></ul> |
| **IoT-enhet** | <ul><li>[Utför auktoriseringskontroller i enheten om den har stöd för olika åtgärder som kräver olika behörighetsnivåer](#device-permission)</li></ul> |
| **Fältet för IoT-Gateway** | <ul><li>[Utföra auktoriseringskontroller i fältet Gateway om det har stöd för olika åtgärder som kräver olika behörighetsnivåer](#field-permission)</li></ul> |

## <a id="acl-restricted-access"></a>Kontrollera att rätt ACL: er har konfigurerats för att begränsa obehörig åtkomst till data på enheten

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Datorn Förtroendegräns | 
| **SDL fas**               | Distribution |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | Kontrollera att rätt ACL: er har konfigurerats för att begränsa obehörig åtkomst till data på enheten|

## <a id="sensitive-directory"></a>Se till att känslig användarspecifika programinnehållet lagras i användarprofil katalog

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Datorn Förtroendegräns | 
| **SDL fas**               | Distribution |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | Se till att känslig användarspecifika programinnehåll som lagras i användarprofil katalogen. Detta är att förhindra att flera användare på datorn kommer åt varandras data.|

## <a id="deployed-privileges"></a>Se till att distribuerade program körs med de lägsta privilegierna

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Datorn Förtroendegräns | 
| **SDL fas**               | Distribution |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | Se till att det distribuerade programmet körs med de lägsta privilegierna. |

## <a id="sequential-logic"></a>Tillämpa sekventiella steg ordning vid bearbetning av business logic flöden

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | Hoppas över steg, bearbetade steg från en annan användare eller för snabbt skickade transaktioner för att verifiera att det här steget körs av en äkta användare som du vill aktivera programmet att endast bearbeta business logic flöden i sekventiella steg ordning med alla steg som bearbetas i realistiska mänsklig tid och inte bearbeta fel ordning.|

## <a id="rate-enumeration"></a>Implementera hastighet begränsa mekanism för att förhindra att uppräkningen

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | Kontrollera att känslig identifierare är slumpmässig. Implementera CAPTCHA-kontroll på anonym sidor. Se till att fel och undantag inte bör avslöjar specifika data|

## <a id="principle-least-privilege"></a>Se till att rätt behörighet är på plats och principen lägsta privilegier följs

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | <p>Principen innebär att ge ett användarkonto behörighet som är nödvändiga för att användare fungerar. Till exempel en säkerhetskopiering användare behöver inte installera program: därför säkerhetskopiering användaren har behörighet för endast för att köra program för säkerhetskopiering och säkerhetskopiering. Privilegier, till exempel installera ny programvara har blockerats. Principen gäller också för en datoranvändare vanligtvis fungerar i ett vanligt användarkonto och öppnar ett konto med privilegier, lösenordsskyddat (det vill säga en superanvändare) endast när situationen absolut begär den. </p><p>Denna princip kan tillämpas till ditt webbprogram. I stället för enbart utifrån rollbaserade autentiseringsmetoder som använder sessioner och, vi i stället vill tilldela behörigheter till användare med hjälp av ett system för databas-baserad autentisering. Vi kan fortfarande använda sessioner för att identifiera om användaren har loggats i korrekt endast nu i stället för att tilldela användaren med en viss roll som vi tilldela honom med behörighet för att kontrollera vilka åtgärder som han är behörig för att utföra på systemet. En stor pro för den här metoden är också, när en användare som har tilldelas färre privilegier ändringarna tillämpas direkt eftersom tilldelningen inte är beroende sessionen som annars hade ut först.</p>|

## <a id="logic-request-parameters"></a>Företag logik och resursen åtkomst auktoriseringsbeslut ska inte baseras på parametrar för inkommande begäran

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | När du kontrollerar om en användare är begränsad till granska vissa data ska åtkomstbegränsningarna bearbetade serversidan. Det användar-ID ska lagras i en sessionsvariabel vid inloggningen och ska användas för att hämta användardata från databasen |

### <a name="example"></a>Exempel
```SQL
SELECT data 
FROM personaldata 
WHERE userID=:id < - session var 
```
Nu en möjliga angripare inte kan säkerhetsförsluten och ändra hanteras program-åtgärden eftersom identifierare för att hämta data serversidan.

## <a id="enumerable-browsing"></a>Kontrollera innehållet och resurser är inte enumerable eller är tillgängliga via kraftfullt surfning

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | <p>Känsliga lagras statiska och konfigurationen inte i web-roten. För innehåll som inte krävs för att vara offentlig, antingen åtkomstbegränsningar tillämpas eller borttagning av själva innehållet.</p><p>Dessutom kombineras kraftfullt surfning vanligtvis med Brute Force-metoder för att samla in information som försöker få åtkomst till alla URL: er som möjligt att räkna upp kataloger och filer på en server. Angripare kan söka efter alla variationer av ofta befintliga filer. Ett lösenord för sökning skulle till exempel omfattar filer inklusive psswd.txt, password.htm, password.dat och andra ändringar.</p><p>För att åtgärda det ska funktioner för att identifiera brute force försök inkluderas.</p>|

## <a id="privileged-server"></a>Se till att minst Privilegierade konton som används för att ansluta till databasservern

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [SQL-databas behörigheter hierarkin](https://msdn.microsoft.com/library/ms191465), [objekt för SQL-databas](https://msdn.microsoft.com/library/ms190401) |
| **Steg** | Lägsta behörighet konton bör användas för att ansluta till databasen. Inloggning som programmet ska begränsas i databasen och bör endast köra valda lagrade procedurer. Programmets inloggningen bör ha inte direkt tabellåtkomst. |

## <a id="rls-tenants"></a>Implementera raden nivå säkerhet RLS att hindra klienter från att komma åt varandras data

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | SQL Azure bör OnPrem |
| **Attribut**              | SQL Version - V12, SQL Version - MsSQL2016 |
| **Referenser**              | [SQL Server säkerheten på radnivå (RLS)](https://msdn.microsoft.com/library/azure/dn765131.aspx) |
| **Steg** | <p>Säkerhet på radnivå ger kunder möjlighet att styra åtkomsten till rader i en databastabell baserat på egenskaperna för användaren som kör en fråga (t.ex. grupmedlemskap eller körningskontext).</p><p>Säkerhet på radnivå (RLS) förenklar design och kodning av säkerhet i ditt program. RLS låter dig implementera begränsningar för dataåtkomst för raden. Därmed får medarbetare endast tillgång till de datarader som är relevanta för deras avdelning, eller kunder får endast dataåtkomst till data som berör deras företag.</p><p>Logik för åtkomst-begränsning är finns i databasnivån i stället för avstånd från data i ett annat program skikt. Databasen tillämpas åtkomstbegränsningarna varje gång ett försök gjordes att dataåtkomst från alla skikt. Detta gör säkerhetssystemet mer tillförlitlig och stabil genom att minska ytan på systemets säkerhet.</p><p>|

Observera att RLS som en out box databasfunktion gäller bara för SQL Server från 2016 och Azure SQL-databas. Om funktionen out box RLS inte har implementerats, ska den säkerställas att dataåtkomst begränsade använda vyer och procedurer

## <a id="sysadmin-users"></a>Rollen sysadmin får endast ha giltiga nödvändiga användare

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [SQL-databas behörigheter hierarkin](https://msdn.microsoft.com/library/ms191465), [objekt för SQL-databas](https://msdn.microsoft.com/library/ms190401) |
| **Steg** | Medlemmar i den fasta serverrollen SysAdmin bör vara mycket begränsad och innehåller aldrig konton som används av program.  Granska listan över användare i rollen och ta bort alla onödiga konton|

## <a id="cloud-least-privileged"></a>Ansluta till Gateway moln som använder lägsta behörighet token

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | IoT Cloud Gateway | 
| **SDL fas**               | Distribution |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Val av gateway - Azure IoT-hubb |
| **Referenser**              | [Åtkomstkontroll för IOT-hubb](https://azure.microsoft.com/documentation/articles/iot-hub-devguide/#Security) |
| **Steg** | Ange behörighet behörigheter till olika komponenter som ansluter till molntjänster Gateway (IoT-hubb). Vanliga exempel är – enheten/etablering komponenten använder registryread/skrivning, händelse Processor (ASA) använder tjänsten ansluta. Enskilda enheter ansluter med autentiseringsuppgifter för enhet|

## <a id="sendonly-sas"></a>Använda en skicka-behörigheter endast SAS-nyckel för att skapa enhetstoken

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Event Hub | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Autentisering och säkerhet modellen översikt över Event Hubs](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Steg** | En SAS-nyckel används för att generera enskilda enhetstoken. Använda en behörigheter endast skicka SAS-nyckel när enhetens token för den angivna utgivaren|

## <a id="access-tokens-hub"></a>Använd inte åtkomsttoken som ger direktåtkomst till Händelsehubben

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Event Hub | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Autentisering och säkerhet modellen översikt över Event Hubs](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Steg** | En token som ger direktåtkomst till händelsehubben bör inte ges till enheten. Med en mindre privilegierad token för den enhet som ger tillgång endast till en utgivare skulle att identifiera och svartlista det om att hitta en falsk eller komprometterats enhet.|

## <a id="sas-minimum-permissions"></a>Ansluta till en Händelsehubb med hjälp av SAS-nycklar som har den lägsta behörigheten som krävs

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Event Hub | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Autentisering och säkerhet modellen översikt över Event Hubs](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Steg** | Ange minsta privilegium behörigheter för olika backend-program som ansluter till Händelsehubben. Generera nycklar för separat SAS för varje backend-program och ge behörigheterna som krävs – endast skicka, ta emot eller hantera dem.|

## <a id="resource-docdb"></a>Använd resource token för att ansluta till Cosmos-DB när det är möjligt

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure dokumentet DB | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | En resurs-token är associerad med en Azure Cosmos DB behörighet resurs och samlar in relationen mellan användaren av en databas och behörigheten att användaren har för en viss Azure Cosmos DB programmet resurs (t.ex. samling och dokument). Använd alltid en resurs-token kan komma åt Azure Cosmos DB om klienten inte är betrott med hantering av master eller skrivskyddad nycklar – som ett program för slutanvändare som en mobil- eller datorprogram klient. Använd huvudnyckeln eller skrivskyddade nycklar från backend-program som kan lagra dessa nycklar på ett säkert sätt.|

## <a id="grained-rbac"></a>Aktivera detaljerad åtkomsthantering för Azure-prenumeration med RBAC

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Förtroendegräns | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Använda rolltilldelningar för att hantera åtkomsten till dina Azure-prenumerationsresurser](https://azure.microsoft.com/documentation/articles/role-based-access-control-configure/)  |
| **Steg** | Rollbaserad åtkomstkontroll (RBAC) i Azure ger tillgång till ingående åtkomsthantering för Azure. Med RBAC kan du bevilja exakt den åtkomstnivå som användarna behöver för att kunna utföra sitt arbete.|

## <a id="cluster-rbac"></a>Begränsa åtkomsten för klientens till klusteråtgärder med RBAC

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Service Fabric-Förtroendegräns | 
| **SDL fas**               | Distribution |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Miljö - Azure |
| **Referenser**              | [Rollbaserad åtkomstkontroll för Service Fabric-klienter](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security-roles/) |
| **Steg** | <p>Azure Service Fabric stöder två typer av olika åtkomstkontroll för klienter som är anslutna till ett Service Fabric-kluster: administratörs- och. Åtkomstkontroll kan Klusteradministratören att begränsa åtkomsten till vissa klusteråtgärder för olika grupper av användare, vilket gör att klustret säkrare.</p><p>Administratörer har fullständig åtkomst till funktioner för hantering (inklusive funktioner för läsning och skrivning). Användare, har som standard bara läsbehörighet till funktioner för hantering (till exempel frågefunktioner) och möjligheten att lösa program och tjänster.</p><p>Du kan ange två klienten roller (administratör och klient) när klustret skapas genom att tillhandahålla olika certifikat för varje.</p>|

## <a id="modeling-field"></a>Utföra security modellering och använder fältet Level Security behov

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Dynamics CRM | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | Utföra security modellering och använder fältet Level Security behov|

## <a id="portal-security"></a>Utföra modellering säkerhet i åtanke säkerhetsmodell för portalen skiljer sig från resten av CRM-portal konton

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Dynamics CRM-portalen | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | Utföra modellering säkerhet i åtanke säkerhetsmodell för portalen skiljer sig från resten av CRM-portal konton|

## <a id="permission-entities"></a>Ger detaljerad behörighet på ett intervall med enheter i Azure-tabellagring

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Storage | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | StorageType - tabell |
| **Referenser**              | [Hur du delegerar åtkomst till objekt i ditt Azure storage-konto med hjälp av SAS](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_data-plane-security) |
| **Steg** | I vissa affärsscenarier kan Azure Table Storage behöva lagra känsliga data som caters till olika parter. Till exempel känsliga data som hör till olika länder. I sådana fall kan SAS signaturer konstrueras genom att ange nyckelintervall partition och raden så att en användare kan komma åt data som är specifika för ett visst land.| 

## <a id="rbac-azure-manager"></a>Aktivera rollbaserad åtkomstkontroll (RBAC) till Azure storage-konto med Azure Resource Manager

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Storage | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Hur du skyddar ditt lagringskonto med rollbaserad åtkomstkontroll (RBAC)](https://azure.microsoft.com/documentation/articles/storage-security-guide/#management-plane-security) |
| **Steg** | <p>När du skapar ett nytt lagringskonto, Välj en distributionsmodell för klassisk eller Azure Resource Manager. Den klassiska modellen för att skapa resurser i Azure kan endast fullständiga åtkomst till prenumerationen och i sin tur storage-konto.</p><p>Med Azure Resource Manager-modellen placera storage-konto i en grupp och kontrollera åtkomst till företagsresurser med den specifika storage-konto med Azure Active Directory management plan. Du kan till exempel ge särskilda användare möjlighet att komma åt lagringskontonycklar, medan andra användare kan visa information om lagringskontot, men det går inte att komma åt lagringskontonycklarna.</p>|

## <a id="rooting-detection"></a>Implementera implicit jailbreak eller rota identifiering

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Mobil klient | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | <p>Programmet ska skydda sina egna konfigurations- och data i fallet om telefon är rotad eller jailbroken. Rota/jailbreakad bryter innebär obehörig åtkomst, vilka normal användare inte på sina egna telefoner. Programmet ska därför ha implicit identifieringslogik för start av program, att identifiera om telefonen har blivit rotad.</p><p>Identifieringslogik kan bara komma åt filer som normalt endast rotanvändare kan komma åt, till exempel:</p><ul><li>/system/app/Superuser.apk</li><li>/ sbin/su</li><li>/system/bin/su</li><li>/system/xbin/su</li><li>/data/Local/xbin/su</li><li>/data/Local/bin/su</li><li>/system/SD/xbin/su</li><li>/system/bin/failsafe/su</li><li>/data/Local/su</li></ul><p>Om programmet kan komma åt någon av dessa filer, anger att programmet körs som rotanvändare.</p>|

## <a id="weak-class-wcf"></a>Svag klassreferens i WCF

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | WCF | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generic, NET Framework 3 |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [spikning kungariket](https://vulncat.fortify.com/en/vulncat/index.html) |
| **Steg** | <p>En svag klassreferens som gör att en angripare att köra kod används. Programmet refererar till en användardefinierad klass som inte identifieras unikt. När .NET läses in svagt identifierade klassen söker inläsaren för CLR-typen för klassen på följande platser i angiven ordning:</p><ol><li>Om sammansättningen av typen är känt söker inläsaren konfigurationsfilen omdirigering platser, GAC, aktuella sammansättningen med information om konfiguration och programmets baskatalog</li><li>Om sammansättningen är okänt söker inläsaren aktuella sammansättning, mscorlib och den plats som returneras av händelsehanteraren TypeResolve</li><li>Den här CLR-sökordningen kan ändras med hook som mekanismen för vidarebefordran av typen och händelsen AppDomain.TypeResolve</li></ol><p>Om en angripare utnyttjar sökordningen CLR genom att skapa en annan klass med samma namn och att den placeras på en annan plats att CLR läser in först CLR kommer oavsiktligt köra koden angripare angavs</p>|

### <a name="example"></a>Exempel
Den `<behaviorExtensions/>` element av WCF-konfigurationsfilen nedan instruerar WCF att lägga till en anpassad beteende klass i ett visst WCF-tillägg.
```
<system.serviceModel>
    <extensions>
        <behaviorExtensions>
            <add name=""myBehavior"" type=""MyBehavior"" />
        </behaviorExtensions>
    </extensions>
</system.serviceModel>
```
Med hjälp av fullständiga (stark) namn unikt identifierar en typ och ökar ytterligare för systemets säkerhet. Använd fullständigt kvalificerat sammansättningsnamn när du registrerar typer i machine.config och app.config.

### <a name="example"></a>Exempel
Den `<behaviorExtensions/>` element av WCF-konfigurationsfilen nedan instruerar WCF att lägga till starkt refereras beteende klass i ett visst WCF-tillägg.
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

## <a id="wcf-authz"></a>Implementera WCF-auktorisering kontroll

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | WCF | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generic, NET Framework 3 |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [spikning kungariket](https://vulncat.fortify.com/en/vulncat/index.html) |
| **Steg** | <p>Den här tjänsten används inte av en kontroll för auktorisering. När en klient anropar en viss WCF-tjänst, ger WCF olika för auktorisering som verifierar att anroparen har behörighet att köra metoden tjänsten på servern. Om tillståndet kontroller inte är aktiverade för WCF-tjänster, kan en autentiserad användare få eskalering.</p>|

### <a name="example"></a>Exempel
Följande konfiguration instruerar WCF inte Kontrollera åtkomstnivån för klienten när du kör tjänsten:
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
Använda ett schema för auktorisering av tjänsten för att kontrollera att anroparen av Servicemetoden har behörighet att göra det. WCF tillhandahåller två lägen och gör att definitionen av ett schema för anpassad auktorisering. UseWindowsGroups läge använder Windows-roller och användare och UseAspNetRoles läge använder en rollprovider för ASP.NET, till exempel SQL Server för att autentisera.

### <a name="example"></a>Exempel
Följande konfiguration instruerar WCF se till att klienten tillhör gruppen Administratörer innan du kör Add-tjänsten:
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
Tjänsten har sedan deklarerats som följande:
```
[PrincipalPermission(SecurityAction.Demand,
Role = ""Builtin\\Administrators"")]
public double Add(double n1, double n2)
{
double result = n1 + n2;
return result;
}
```

## <a id="authz-aspnet"></a>Implementera rätt mekanism i ASP.NET Web API

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webb-API | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generic, MVC5 |
| **Attribut**              | Ingen, Provider - ADFS, identitet identitetsleverantör - Azure AD |
| **Referenser**              | [Autentisering och auktorisering i ASP.NET webb-API](http://www.asp.net/web-api/overview/security/authentication-and-authorization-in-aspnet-web-api) |
| **Steg** | <p>Rollinformation om för programanvändare kan härledas från Azure AD eller AD FS-anspråk om programmet använder dem som identitetsleverantör eller själva programmet under förutsättning att den. I alla dessa fall kan ska implementering för anpassad auktorisering verifiera användaren rollen.</p><p>Rollinformation om för programanvändare kan härledas från Azure AD eller AD FS-anspråk om programmet använder dem som identitetsleverantör eller själva programmet under förutsättning att den. I alla dessa fall kan ska implementering för anpassad auktorisering verifiera användaren rollen.</p>

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
Alla domänkontrollanter och åtgärdsmetoder som måste skyddas måste vara dekorerad med ovan attribut.
```csharp
[ApiAuthorize]
public class CustomController : ApiController
{
     //Application code goes here
}
```

## <a id="device-permission"></a>Utför auktoriseringskontroller i enheten om den har stöd för olika åtgärder som kräver olika behörighetsnivåer

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | IoT-enhet | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | <p>Enheten ska tillåta anroparen att kontrollera om anroparen har behörighet att utföra den begärda åtgärden. För t.ex. kan enheten är säga en Smart Lock dörren som kan övervakas från molnet, plus det ger funktioner som att låsa dörren via fjärranslutning.</p><p>Smart dörren Lock innehåller upplåsning funktion endast när någon fysiskt medföljer nära dörren ett kort. I det här fallet bör du göra implementeringen av fjärrkommandot och kontroll så att den inte innehåller några funktioner för att låsa upp dörren som molgatewayen inte har behörighet att skicka ett kommando för att låsa upp dörren.</p>|

## <a id="field-permission"></a>Utföra auktoriseringskontroller i fältet Gateway om det har stöd för olika åtgärder som kräver olika behörighetsnivåer

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Fältet för IoT-Gateway | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | Fältet Gateway ska tillåta anroparen att kontrollera om anroparen har behörighet att utföra den begärda åtgärden. För t.ex. Det måste vara olika behörigheter för en administratör användaren gränssnittet/API används för att konfigurera en fältet gateway v/s-enheter som ansluter till den.|
