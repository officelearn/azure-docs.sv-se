---
title: Auktorisering – Microsoft Hotmodelleringsverktyg – Azure | Microsoft Docs
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
ms.openlocfilehash: 3c078f7246140ee966f1d202d2248758dde49059
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57888476"
---
# <a name="security-frame-authorization--mitigations"></a>Security ram: Auktorisering | Åtgärder 
| Produkt/tjänst | Artikel |
| --------------- | ------- |
| **Datorn Förtroendegräns** | <ul><li>[Kontrollera att rätt ACL: er är konfigurerade för att begränsa obehörig åtkomst till data på enheten](#acl-restricted-access)</li><li>[Se till att känsliga användarspecifika programinnehållet lagras i användarprofilens katalog](#sensitive-directory)</li><li>[Se till att distribuerade program körs med de lägsta privilegierna](#deployed-privileges)</li></ul> |
| **Webbprogram** | <ul><li>[Framtvinga sekventiella steg ordning vid bearbetning av business logic-flöden](#sequential-logic)</li><li>[Implementera begränsar mekanism för att förhindra uppräkning frekvensbegränsningen](#rate-enumeration)</li><li>[Kontrollera att rätt behörighet är på plats och principen lägsta privilegier följs](#principle-least-privilege)</li><li>[Logik och resurs åtkomst auktorisering affärsbeslut får inte baseras på den inkommande begäranparametrar](#logic-request-parameters)</li><li>[Kontrollera innehållet och resurser är inte uppräkningsbara eller tillgängliga öppet via kraftfullt surfning](#enumerable-browsing)</li></ul> |
| **Databas** | <ul><li>[Se till att minst Privilegierade konton som används för att ansluta till databasservern](#privileged-server)</li><li>[Implementera säkerhet på radnivå RLS att hindra klienter från åtkomst till varandras data](#rls-tenants)</li><li>[Sysadmin-rollen bör bara ha giltiga nödvändiga användare](#sysadmin-users)</li></ul> |
| **IoT Cloud Gateway** | <ul><li>[Ansluta till Molngatewayen med lägsta behörighet token](#cloud-least-privileged)</li></ul> |
| **Azure Event Hub** | <ul><li>[Använda en skicka behörighet för endast SAS-nyckel för att skapa enhetstoken](#sendonly-sas)</li><li>[Använd inte åtkomsttokens som ger direktåtkomst till Händelsehubben](#access-tokens-hub)</li><li>[Ansluta till Event Hub med SAS-nycklar som har den lägsta behörigheten som krävs](#sas-minimum-permissions)</li></ul> |
| **Azure Document DB** | <ul><li>[Använd resurstokens för att ansluta till Azure Cosmos DB när det är möjligt](#resource-docdb)</li></ul> |
| **Azure Förtroendegräns** | <ul><li>[Aktivera detaljerad åtkomsthantering för Azure-prenumeration med RBAC](#grained-rbac)</li></ul> |
| **Service Fabric-Förtroendegräns** | <ul><li>[Begränsa åtkomsten för klientens till klusteråtgärder med RBAC](#cluster-rbac)</li></ul> |
| **Dynamics CRM** | <ul><li>[Utföra security modellering och använda på fältnivå om det behövs](#modeling-field)</li></ul> |
| **Dynamics CRM Portal** | <ul><li>[Utföra security modellering av portalen konton med tanke på att säkerhetsmodell för portalen skiljer sig från resten av CRM](#portal-security)</li></ul> |
| **Azure Storage** | <ul><li>[Ge detaljerad behörighet på ett intervall med enheter i Azure Table Storage](#permission-entities)</li><li>[Aktivera rollbaserad åtkomstkontroll (RBAC) till Azure storage-konto med Azure Resource Manager](#rbac-azure-manager)</li></ul> |
| **Mobila klienten** | <ul><li>[Implementera implicit uppbrytning eller rota identifiering](#rooting-detection)</li></ul> |
| **WCF** | <ul><li>[Svag Class Reference i WCF](#weak-class-wcf)</li><li>[WCF-implementera auktorisering kontroll](#wcf-authz)</li></ul> |
| **Webb-API** | <ul><li>[Implementera rätt auktoriseringsmekanism i ASP.NET Web API](#authz-aspnet)</li></ul> |
| **IoT-enheter** | <ul><li>[Utför auktoriseringskontroller i enheten om den har stöd för olika åtgärder som kräver olika behörighetsnivåer](#device-permission)</li></ul> |
| **IoT Field Gateway** | <ul><li>[Utför auktoriseringskontroller i fält-Gateway om det har stöd för olika åtgärder som kräver olika behörighetsnivåer](#field-permission)</li></ul> |

## <a id="acl-restricted-access"></a>Kontrollera att rätt ACL: er är konfigurerade för att begränsa obehörig åtkomst till data på enheten

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Datorn Förtroendegräns | 
| **SDL fas**               | Distribution |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | Kontrollera att rätt ACL: er är konfigurerade för att begränsa obehörig åtkomst till data på enheten|

## <a id="sensitive-directory"></a>Se till att känsliga användarspecifika programinnehållet lagras i användarprofilens katalog

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Datorn Förtroendegräns | 
| **SDL fas**               | Distribution |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | Se till att känsliga användarspecifika programinnehållet lagras i användarprofilens katalog. Det här är att förhindra att flera användare för datorn åtkomst till varandras data.|

## <a id="deployed-privileges"></a>Se till att distribuerade program körs med de lägsta privilegierna

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Datorn Förtroendegräns | 
| **SDL fas**               | Distribution |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | Se till att det distribuerade programmet körs med de lägsta privilegierna. |

## <a id="sequential-logic"></a>Framtvinga sekventiella steg ordning vid bearbetning av business logic-flöden

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | Hoppades över stegen för att kontrollera att det här skedet kördes av en äkta användare som du vill framtvinga att programmet endast processen business logic-flöden i sekventiella steg ordning, med alla steg som bearbetas i realistisk mänskliga tid och inte bearbeta oordnade, , bearbetas steg från en annan användare eller för snabbt skickats transaktioner.|

## <a id="rate-enumeration"></a>Implementera begränsar mekanism för att förhindra uppräkning frekvensbegränsningen

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | Kontrollera att känsliga identifierare är slumpmässig. Implementera CAPTCHA-kontroll på anonym sidor. Se till att fel och undantag inte bör avslöjar specifika data|

## <a id="principle-least-privilege"></a>Kontrollera att rätt behörighet är på plats och principen lägsta privilegier följs

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | <p>Principen innebär och ger ett användarkonto behörighet som är nödvändiga för att användare fungerar. Till exempel en säkerhetskopiering användare behöver inte installera programvara: därför säkerhetskopiering användaren har bara för att köra program för säkerhetskopiering och säkerhetskopiering. Privilegier, till exempel installera ny programvara, blockeras. Principen gäller även för en personlig dator-användare som vanligtvis fungerar i ett vanligt användarkonto och öppnar ett konto med privilegier, lösenordsskyddade (det vill säga en superanvändare) endast när situationen absolut begär den. </p><p>Den här principen kan tillämpas på dina webbprogram. I stället för enbart utifrån rollbaserade autentiseringsmetoder med sessioner, vi i stället vill tilldela behörigheter till användare med hjälp av ett system för databas-baserad autentisering. Vi kan fortfarande använda sessioner för att identifiera om användaren har loggat in korrekt, endast nu i stället för att tilldela användaren med en viss roll som vi tilldela honom med behörighet för att kontrollera vilka åtgärder som han är behörig för att utföra på systemet. En stor pro för den här metoden är också, när en användare som har tilldelas färre privilegier ändringarna tillämpas direkt eftersom den tilldela inte beroende av den session som annars hade att gälla först.</p>|

## <a id="logic-request-parameters"></a>Logik och resurs åtkomst auktorisering affärsbeslut får inte baseras på den inkommande begäranparametrar

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | När du kontrollerar om en användare är begränsade till att granska vissa data, ska åtkomstbegränsningarna vara bearbetade serversidan. Användar-ID ska lagras i en variabel för sessions på inloggning och bör användas för att hämta användardata från databasen |

### <a name="example"></a>Exempel
```SQL
SELECT data 
FROM personaldata 
WHERE userID=:id < - session var 
```
Nu en möjlig angripare inte kan ändra och ändra programmet igen eftersom identifieraren för att hämta data som hanteras från serversidan.

## <a id="enumerable-browsing"></a>Kontrollera innehållet och resurser är inte uppräkningsbara eller tillgängliga öppet via kraftfullt surfning

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | <p>Känsliga statiskt och konfigurationsfiler ska inte finnas i web-roten. För innehåll som inte krävs för att vara offentlig, antingen korrekta åtkomstkontroller ska användas eller borttagning av själva innehållet.</p><p>Dessutom kombineras kraftfullt surfning vanligtvis med Brute Force-tekniker för att samla in information genom att försöka komma åt så många webbadresser som möjligt att räkna upp kataloger och filer på en server. Angripare kan söka efter alla varianter av ofta befintliga filer. Ett lösenord filsökning skulle till exempel omfatta filer inklusive psswd.txt, password.htm, password.dat eller andra ändringar.</p><p>För att åtgärda det ska funktioner för identifiering av råstyrkeattacker som inkluderas.</p>|

## <a id="privileged-server"></a>Se till att minst Privilegierade konton som används för att ansluta till databasservern

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [SQL Database behörigheter hierarkin](https://msdn.microsoft.com/library/ms191465), [skyddbara objekt SQL-databas](https://msdn.microsoft.com/library/ms190401) |
| **Steg** | Lägsta behörighet konton bör användas för att ansluta till databasen. Inloggning för program bör begränsas i databasen och bör endast kör valda lagrade procedurer. Programmets inloggningen ska inte ha direkt tabellåtkomst. |

## <a id="rls-tenants"></a>Implementera säkerhet på radnivå RLS att hindra klienter från åtkomst till varandras data

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Sql Azure, OnPrem |
| **Attribut**              | SQL-Version - V12, SQL-Version - MsSQL2016 |
| **Referenser**              | [Säkerhet för SQL Server på radnivå (RLS)](https://msdn.microsoft.com/library/azure/dn765131.aspx) |
| **Steg** | <p>Säkerhet på radnivå ger kunder möjlighet att styra åtkomsten till rader i en databastabell baserat på egenskaperna för användaren som kör en fråga (t.ex. grupmedlemskap eller körningskontext).</p><p>Säkerhet på radnivå (RLS) förenklar design och kodning av säkerheten i ditt program. RLS låter dig implementera begränsningar för dataåtkomst för raden. Därmed får medarbetare endast tillgång till de datarader som är relevanta för deras avdelning, eller kunder får endast dataåtkomst till data som berör deras företag.</p><p>Logiken för begränsning av är placerad på databasnivån i stället för in från data i en annan programnivå. Databasen tillämpas åtkomstbegränsningarna varje gång den dataåtkomsten görs från valfri nivå. Detta gör säkerhetssystemet mer tillförlitlig och stabil genom att minska ytan på säkerhetssystemet.</p><p>|

Observera att RLS som en databasfunktion för out-of the box-är gäller endast för SQL Server från och med 2016 och Azure SQL-databas. Om out-of the box RLS-funktionen inte har implementerats, viktigt att se till att dataåtkomsten är begränsad använda vyer och procedurer

## <a id="sysadmin-users"></a>Sysadmin-rollen bör bara ha giltiga nödvändiga användare

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [SQL Database behörigheter hierarkin](https://msdn.microsoft.com/library/ms191465), [skyddbara objekt SQL-databas](https://msdn.microsoft.com/library/ms190401) |
| **Steg** | Medlemmar i den fasta serverrollen SysAdmin bör vara mycket begränsad och aldrig har konton som används av program.  Granska listan över användare i rollen och ta bort eventuella onödiga konton|

## <a id="cloud-least-privileged"></a>Ansluta till Molngatewayen med lägsta behörighet token

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | IoT Cloud Gateway | 
| **SDL fas**               | Distribution |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gateway - valet i Azure IoT Hub |
| **Referenser**              | [Åtkomstkontroll för IOT Hub](https://azure.microsoft.com/documentation/articles/iot-hub-devguide/#Security) |
| **Steg** | Ange den minsta möjliga behörighet behörighet till olika komponenter som ansluter till Molngatewayen (IoT Hub). Vanliga exempel är – enheten/driftsättning komponent använder registryread/skrivning, Event Processor (ASA) använder tjänsten ansluta. Enskilda enheter ansluts via enhetens autentiseringsuppgifter|

## <a id="sendonly-sas"></a>Använda en skicka behörighet för endast SAS-nyckel för att skapa enhetstoken

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure händelsehubb | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Autentisering och säkerhet modellen översikt över Event Hubs](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Steg** | En SAS-nyckel används för att generera enskilda enhetstoken. Använda en behörighet för endast skicka SAS-nyckel när du genererar enhetstoken för en viss utgivare|

## <a id="access-tokens-hub"></a>Använd inte åtkomsttokens som ger direktåtkomst till Händelsehubben

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure händelsehubb | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Autentisering och säkerhet modellen översikt över Event Hubs](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Steg** | En token som ger direktåtkomst till event hub bör inte ges till enheten. Om du använder en mindre privilegierad token för enheten som endast ger åtkomst till en utgivare, så skulle det underläta att identifiera och blockeringslista enheten om den skulle visa sig vara en otillåten eller komprometterad enhet.|

## <a id="sas-minimum-permissions"></a>Ansluta till Event Hub med SAS-nycklar som har den lägsta behörigheten som krävs

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure händelsehubb | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Autentisering och säkerhet modellen översikt över Event Hubs](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Steg** | Ange den minsta möjliga behörighet behörighet till olika serverdels-program som ansluter till Händelsehubben. Generera nycklar för separat SAS för varje backend-program och ge behörigheterna som krävs – endast skicka, ta emot eller hantera dem.|

## <a id="resource-docdb"></a>Använd resurstokens för att ansluta till Cosmos DB när det är möjligt

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Document DB | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | En Resurstoken är associerat med en resurs för Azure Cosmos DB-behörighet och samlar in relationen mellan användaren av en databas och behörigheten som användaren har för en specifik Azure Cosmos DB programmet resurs (t.ex. samling och dokument). Använd alltid en Resurstoken åtkomst till Azure Cosmos DB om klienten inte är betrott med hantering av huvudnyckeln eller den skrivskyddade nycklar – som ett program för slutanvändare som en klient för mobil eller dator. Använda huvudnyckeln eller skrivskyddade nycklar från backend-program som kan lagra nycklarna på ett säkert sätt.|

## <a id="grained-rbac"></a>Aktivera detaljerad åtkomsthantering för Azure-prenumeration med RBAC

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Förtroendegräns | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Använda rolltilldelningar för att hantera åtkomsten till dina Azure-prenumerationsresurser](https://azure.microsoft.com/documentation/articles/role-based-access-control-configure/)  |
| **Steg** | Rollbaserad åtkomstkontroll (RBAC) i Azure ger tillgång till ingående åtkomsthantering för Azure. Med RBAC kan du bevilja exakt den åtkomstnivå som användarna behöver för att kunna utföra sitt arbete.|

## <a id="cluster-rbac"></a>Begränsa åtkomsten för klientens till klusteråtgärder med RBAC

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Service Fabric-Förtroendegräns | 
| **SDL fas**               | Distribution |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Environment - Azure |
| **Referenser**              | [Rollbaserad åtkomstkontroll för Service Fabric-klienter](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security-roles/) |
| **Steg** | <p>Azure Service Fabric stöder två typer av olika åtkomstkontroll för klienter som är anslutna till ett Service Fabric-kluster: administratör och användare. Åtkomstkontroll kan du Klusteradministratören att begränsa åtkomsten till vissa klusteråtgärder för olika grupper av användare, vilket gör att klustret säkrare.</p><p>Administratörer har fullständig åtkomst till funktioner för hantering (inklusive Läs-och skrivbehörighet). Användare, har som standard bara läsbehörighet till funktioner för hantering (till exempel frågefunktioner) och möjligheten att lösa program och tjänster.</p><p>Du kan ange två klientroller (administratör och klienten) vid tidpunkten för klustret har skapats genom att tillhandahålla separata certifikat för varje.</p>|

## <a id="modeling-field"></a>Utföra security modellering och använda på fältnivå om det behövs

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Dynamics CRM | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | Utföra security modellering och använda på fältnivå om det behövs|

## <a id="portal-security"></a>Utföra security modellering av portalen konton med tanke på att säkerhetsmodell för portalen skiljer sig från resten av CRM

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Dynamics CRM Portal | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | Utföra security modellering av portalen konton med tanke på att säkerhetsmodell för portalen skiljer sig från resten av CRM|

## <a id="permission-entities"></a>Ge detaljerad behörighet på ett intervall med enheter i Azure Table Storage

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Storage | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | StorageType - tabell |
| **Referenser**              | [Hur du delegerar åtkomst till objekt i ditt Azure storage-konto med hjälp av SAS](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_data-plane-security) |
| **Steg** | I vissa affärsscenarier kan Azure Table Storage krävas för att lagra känsliga data som caters till olika parter. T.ex. känsliga data som hör till olika länder. I sådana fall kan SAS signaturer skapas genom att ange nyckelintervall partitions- och radnycklar så att en användare kan komma åt data som är specifika för ett visst land.| 

## <a id="rbac-azure-manager"></a>Aktivera rollbaserad åtkomstkontroll (RBAC) till Azure storage-konto med Azure Resource Manager

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Storage | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Hur du skyddar ditt lagringskonto med rollbaserad åtkomstkontroll (RBAC)](https://azure.microsoft.com/documentation/articles/storage-security-guide/#management-plane-security) |
| **Steg** | <p>När du skapar ett nytt lagringskonto kan välja du en distributionsmodell för klassisk eller Azure Resource Manager. Den klassiska modellen för att skapa resurser i Azure tillåter endast fullständiga åtkomst till prenumerationen och i sin tur, storage-konto.</p><p>Med Azure Resource Manager-modellen kan placera du storage-konto i en resurs och kontrollerar åtkomst till Hanteringsplanet för den specifika storage-konto med Azure Active Directory. Du kan till exempel ge särskilda användare möjlighet att få åtkomst till lagringskontonycklarna, medan andra användare kan visa information om storage-konto, men det går inte att få åtkomst till lagringskontonycklarna.</p>|

## <a id="rooting-detection"></a>Implementera implicit uppbrytning eller rota identifiering

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Mobile Client | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | <p>Programmet bör skydda egna data i konfigurationen och användardata i fallet om telefon är rotad eller jailbroken. Rota/upplåsta större innebär obehörig åtkomst, vilka normala användarna inte utföra på sina egna telefoner. Programmet bör därför ha implicita identifieringslogik vid programstart av, att identifiera om telefonen har blivit rotad.</p><p>Identifieringslogiken kan vara helt enkelt få åtkomst till filer som normalt endast rotanvändare kan komma åt, till exempel:</p><ul><li>/system/app/Superuser.apk</li><li>/sbin/su</li><li>/system/bin/su</li><li>/system/xbin/su</li><li>/data/Local/xbin/su</li><li>/data/Local/bin/su</li><li>/system/sd/xbin/su</li><li>/system/bin/Failsafe/su</li><li>/data/Local/su</li></ul><p>Om programmet kan komma åt någon av dessa filer, anger att programmet körs som rotanvändare.</p>|

## <a id="weak-class-wcf"></a>Svag Class Reference i WCF

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | WCF | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generic, NET Framework 3 |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [spikning Nordirland](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference) |
| **Steg** | <p>Systemet använder en svag klassreferens, vilket gör att en angripare att köra kod. Programmet refererar till en användardefinierad klass som inte identifieras unikt. När .NET har lästs in den här svagt identifierade klassen söker inläsaren för CLR-typ för klassen på följande platser i angiven ordning:</p><ol><li>Om sammanställningen av typen är känd söker inläsaren den konfigurationsfilen omdirigerings-platser, GAC, aktuella sammansättningen med konfigurationsinformation och programmets baskatalog</li><li>Om sammansättningen är okänt söker inläsaren den aktuella sammansättningen, mscorlib och den plats som returneras av händelsehanteraren TypeResolve</li><li>Den här CLR-sökordningen kan ändras med krokar som mekanismen för vidarebefordran av typen och händelsen AppDomain.TypeResolve</li></ol><p>Om en angripare utnyttjar sökordningen CLR genom att skapa en annan klass med samma namn och placera den på en annan plats att CLR laddas först CLR kommer oavsiktligt köra angripare anger-kod</p>|

### <a name="example"></a>Exempel
Den `<behaviorExtensions/>` element i WCF-konfigurationsfilen nedan instruerar WCF att lägga till en anpassad beteende klass i ett visst WCF-tillägg.
```
<system.serviceModel>
    <extensions>
        <behaviorExtensions>
            <add name=""myBehavior"" type=""MyBehavior"" />
        </behaviorExtensions>
    </extensions>
</system.serviceModel>
```
Med hjälp av fullständiga (stark) namn unikt identifierar en typ och ytterligare ökar säkerheten för ditt system. Använd fullständigt kvalificerade sammansättningsnamn när du registrerar typer i machine.config och app.config.

### <a name="example"></a>Exempel
Den `<behaviorExtensions/>` element i WCF-konfigurationsfilen nedan instruerar WCF att lägga till starkt refereras beteende klass i ett visst WCF-tillägg.
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

## <a id="wcf-authz"></a>WCF-implementera auktorisering kontroll

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | WCF | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generic, NET Framework 3 |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [spikning Nordirland](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference) |
| **Steg** | <p>Den här tjänsten använder inte en kontroll för auktorisering. När en klient anropar en viss WCF-tjänst, ger WCF olika för auktorisering som verifierar att anroparen har behörighet att köra metoden tjänsten på servern. Om auktoriseringskontroller inte har aktiverats för WCF-tjänster, kan en autentiserad användare få eskalering.</p>|

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
Använda ett schema för auktorisering av tjänsten för att verifiera att den som anroparen Servicemetoden har behörighet att göra detta. WCF har två lägen och tillåter definitionen av ett schema för anpassad auktorisering. UseWindowsGroups läge använder Windows-roller och användare och UseAspNetRoles läge använder du en provider för ASP.NET-rollen, till exempel SQL Server för att autentisera.

### <a name="example"></a>Exempel
Följande konfiguration instruerar WCF att se till att klienten är medlem i gruppen Administratörer innan du kör Add-tjänsten:
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

## <a id="authz-aspnet"></a>Implementera rätt auktoriseringsmekanism i ASP.NET Web API

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webb-API | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generic, MVC5 |
| **Attribut**              | Inte aktuellt, identitets-Provider – ADFS, identitetsprovider – Azure AD |
| **Referenser**              | [Autentisering och auktorisering i ASP.NET webb-API](https://www.asp.net/web-api/overview/security/authentication-and-authorization-in-aspnet-web-api) |
| **Steg** | <p>Rollinformation för vilka programanvändare kan härledas från Azure AD eller AD FS anspråk om programmet är beroende av dem som identitetsprovider eller själva programmet kanske har angett en sådan. I någon av dessa fall, bör anpassad auktorisering implementeringen verifiera rollen användarinformation.</p><p>Rollinformation för vilka programanvändare kan härledas från Azure AD eller AD FS anspråk om programmet är beroende av dem som identitetsprovider eller själva programmet kanske har angett en sådan. I någon av dessa fall, bör anpassad auktorisering implementeringen verifiera rollen användarinformation.</p>

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
Alla domänkontrollanter och åtgärdsmetoder som måste skyddas bör vara dekorerad med ovan attribut.
```csharp
[ApiAuthorize]
public class CustomController : ApiController
{
     //Application code goes here
}
```

## <a id="device-permission"></a>Utför auktoriseringskontroller i enheten om den har stöd för olika åtgärder som kräver olika behörighetsnivåer

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | IoT-enheter | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | <p>Enheten bör tillåta anroparen att kontrollera om anroparen har behörighet att utföra den begärda åtgärden. För t.ex. kan säga att enheten är en Smart Lock dörren som kan övervakas från molnet, samt den innehåller funktioner som t.ex. via fjärranslutning låsa dörren.</p><p>Smart dörren Lock innehåller funktioner för att låsa upp endast när någon fysiskt levereras nära dörren med ett kort. I det här fallet bör du göra implementeringen av remote kommando och kontroll så att den inte innehåller några funktioner för att låsa upp dörren som molgatewayen inte har behörighet att skicka ett kommando för att låsa upp dörren.</p>|

## <a id="field-permission"></a>Utför auktoriseringskontroller i fält-Gateway om det har stöd för olika åtgärder som kräver olika behörighetsnivåer

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | IoT Field Gateway | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | Fält-Gateway ska tillåta anroparen att kontrollera om anroparen har behörighet att utföra den begärda åtgärden. För t.ex. Det bör finnas olika behörigheter för en administratör användaren gränssnittet/API används för att konfigurera en fält-gateway v/s-enheter som ansluter till den.|
