---
title: Auktorisering-Microsoft Threat Modeling Tool – Azure | Microsoft Docs
description: begränsningar för hot som exponeras i Threat Modeling Tool
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
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68728372"
---
# <a name="security-frame-authorization--mitigations"></a>Säkerhets ram: Auktorisering | Åtgärder 
| Produkt/tjänst | Artikel |
| --------------- | ------- |
| **Datorns förtroende gränser** | <ul><li>[Kontrol lera att rätt ACL: er är konfigurerade för att begränsa obehörig åtkomst till data på enheten](#acl-restricted-access)</li><li>[Se till att känsligt användarspecifika program innehåll lagras i användar profil katalogen](#sensitive-directory)</li><li>[Kontrol lera att de distribuerade programmen körs med lägsta behörighet](#deployed-privileges)</li></ul> |
| **Webb program** | <ul><li>[Använd sekventiell steg ordning vid bearbetning av affärs logiks flöden](#sequential-logic)</li><li>[Implementera hastighets begränsning för att förhindra uppräkning](#rate-enumeration)</li><li>[Kontrol lera att rätt tillstånd är på plats och principen om lägsta behörigheten följs](#principle-least-privilege)</li><li>[Beslut om affärs logik och resurs åtkomst kan inte baseras på parametrar för inkommande begäran](#logic-request-parameters)</li><li>[Se till att innehållet och resurserna inte är enumerable eller tillgängliga via Tvingad bläddring](#enumerable-browsing)</li></ul> |
| **Databas** | <ul><li>[Se till att konton med lägst privilegier används för att ansluta till databas servern](#privileged-server)</li><li>[Implementera säkerhet på radnivå för att förhindra att klienter får åtkomst till var and ras data](#rls-tenants)</li><li>[Sysadmin-rollen bör bara ha giltiga användare](#sysadmin-users)</li></ul> |
| **IoT Cloud Gateway** | <ul><li>[Ansluta till en molnbaserad Gateway med hjälp av minst privilegierade token](#cloud-least-privileged)</li></ul> |
| **Azure Event Hub** | <ul><li>[Använd en SAS-nyckel för endast skicka-behörighet för att skapa enhets-token](#sendonly-sas)</li><li>[Använd inte åtkomsttoken som ger direkt åtkomst till Händelsehubben](#access-tokens-hub)</li><li>[Anslut till Händelsehubben med SAS-nycklar som har de lägsta behörigheter som krävs](#sas-minimum-permissions)</li></ul> |
| **Azure Document DB** | <ul><li>[Använd Resource tokens för att ansluta till Azure Cosmos DB närhelst det är möjligt](#resource-docdb)</li></ul> |
| **Azure Trust-gränser** | <ul><li>[Aktivera detaljerad åtkomst hantering för Azure-prenumeration med RBAC](#grained-rbac)</li></ul> |
| **Service Fabric förtroende gränser** | <ul><li>[Begränsa klientens åtkomst till kluster åtgärder med RBAC](#cluster-rbac)</li></ul> |
| **Dynamics CRM** | <ul><li>[Utför säkerhets modellering och Använd säkerhet på fält nivå där det behövs](#modeling-field)</li></ul> |
| **Dynamics CRM-Portal** | <ul><li>[Utför säkerhets modellering av Portal konton i åtanke att säkerhets modellen för portalen skiljer sig från resten av CRM](#portal-security)</li></ul> |
| **Azure Storage** | <ul><li>[Ge detaljerad behörighet för en serie entiteter i Azure Table Storage](#permission-entities)</li><li>[Aktivera rollbaserad Access Control (RBAC) till Azure Storage-konto med Azure Resource Manager](#rbac-azure-manager)</li></ul> |
| **Mobil klient** | <ul><li>[Implementera implicit upplåsning eller identifiering av rottips](#rooting-detection)</li></ul> |
| **WCF** | <ul><li>[Svag klass referens i WCF](#weak-class-wcf)</li><li>[WCF – implementera Authorization-kontroll](#wcf-authz)</li></ul> |
| **Webb-API** | <ul><li>[Implementera rätt mekanism för auktorisering i ASP.NET webb-API](#authz-aspnet)</li></ul> |
| **IoT-enhet** | <ul><li>[Utför verifierings kontroller på enheten om den har stöd för olika åtgärder som kräver olika behörighets nivåer](#device-permission)</li></ul> |
| **IoT-fält Gateway** | <ul><li>[Utföra verifierings kontroller i fältet Gateway om det stöder olika åtgärder som kräver olika behörighets nivåer](#field-permission)</li></ul> |

## <a id="acl-restricted-access"></a>Kontrol lera att rätt ACL: er är konfigurerade för att begränsa obehörig åtkomst till data på enheten

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Datorns förtroende gränser | 
| **SDL-fas**               | Distribution |  
| **Tillämpliga tekniker** | Generisk |
| **Dokumentattribut**              | Gäller inte  |
| **Reference**              | Gäller inte  |
| **Steg** | Kontrol lera att rätt ACL: er är konfigurerade för att begränsa obehörig åtkomst till data på enheten|

## <a id="sensitive-directory"></a>Se till att känsligt användarspecifika program innehåll lagras i användar profil katalogen

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Datorns förtroende gränser | 
| **SDL-fas**               | Distribution |  
| **Tillämpliga tekniker** | Generisk |
| **Dokumentattribut**              | Gäller inte  |
| **Reference**              | Gäller inte  |
| **Steg** | Se till att känsligt användarspecifika program innehåll lagras i användar profil katalogen. Detta är för att förhindra att flera användare av datorn får åtkomst till var and ras data.|

## <a id="deployed-privileges"></a>Kontrol lera att de distribuerade programmen körs med lägsta behörighet

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Datorns förtroende gränser | 
| **SDL-fas**               | Distribution |  
| **Tillämpliga tekniker** | Generisk |
| **Dokumentattribut**              | Gäller inte  |
| **Reference**              | Gäller inte  |
| **Steg** | Kontrol lera att det distribuerade programmet körs med minst behörighet. |

## <a id="sequential-logic"></a>Använd sekventiell steg ordning vid bearbetning av affärs logiks flöden

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Dokumentattribut**              | Gäller inte  |
| **Reference**              | Gäller inte  |
| **Steg** | För att kontrol lera att den här fasen kördes genom av en äkta användare vill du tvinga programmet att endast bearbeta affärs logiks flöden i sekventiell steg ordning, med alla steg som bearbetas i verklighetstrogen mänsklig tid och inte att bearbeta i ordning, hoppa över steg , bearbetade steg från en annan användare eller för snabbt skickade transaktioner.|

## <a id="rate-enumeration"></a>Implementera hastighets begränsning för att förhindra uppräkning

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Dokumentattribut**              | Gäller inte  |
| **Reference**              | Gäller inte  |
| **Steg** | Se till att känsliga identifierare är slumpmässiga. Implementera CAPTCHA-kontroll på anonyma sidor. Se till att fel och undantag inte ska avslöja vissa data|

## <a id="principle-least-privilege"></a>Kontrol lera att rätt tillstånd är på plats och principen om lägsta behörigheten följs

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Dokumentattribut**              | Gäller inte  |
| **Reference**              | Gäller inte  |
| **Steg** | <p>Principen innebär bara att ge ett användar konto behörighet som är nödvändig för att användarna ska fungera. En säkerhets kopierings användare behöver t. ex. inte installera program vara: säkerhets kopierings användaren har därför bara rättigheter att köra säkerhets kopierings-och säkerhets kopierings program. Alla andra behörigheter, till exempel installation av ny program vara, blockeras. Principen gäller också för en personlig dator användare som vanligt vis arbetar i ett normalt användar konto och öppnar ett skyddat, lösenordsskyddat konto (dvs. en superanvändare) endast när situationen absolut kräver det. </p><p>Den här principen kan också tillämpas på dina webb program. I stället för att bara beroende på rollbaserad autentiseringsmetoder med hjälp av sessioner, vill vi hellre tilldela användare behörighet med hjälp av ett databas-baserat autentiseringspaket. Vi använder fortfarande sessioner för att identifiera om användaren har loggat in på rätt sätt, bara nu i stället för att tilldela den användaren en speciell roll, tilldelar han han med behörighet för att verifiera vilka åtgärder han är behörig att utföra i systemet. Dessutom är det en stor Pro av den här metoden, när en användare måste tilldelas färre behörigheter kommer dina ändringar att tillämpas i farten eftersom tilldelningen inte är beroende av den session som annars var tvungen att upphöra att gälla först.</p>|

## <a id="logic-request-parameters"></a>Beslut om affärs logik och resurs åtkomst kan inte baseras på parametrar för inkommande begäran

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Dokumentattribut**              | Gäller inte  |
| **Reference**              | Gäller inte  |
| **Steg** | När du kontrollerar om en användare är begränsad till att granska vissa data, ska åtkomst begränsningarna bearbetas på Server sidan. UserID bör lagras i en sessionsvariabel vid inloggning och ska användas för att hämta användar data från databasen |

### <a name="example"></a>Exempel
```SQL
SELECT data 
FROM personaldata 
WHERE userID=:id < - session var 
```
Nu kan en angripare inte manipulera och ändra program åtgärden eftersom identifieraren för att hämta data hanteras på Server sidan.

## <a id="enumerable-browsing"></a>Se till att innehållet och resurserna inte är enumerable eller tillgängliga via Tvingad bläddring

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Dokumentattribut**              | Gäller inte  |
| **Reference**              | Gäller inte  |
| **Steg** | <p>Känsliga statiska filer och konfigurationsfiler bör inte behållas i webb roten. För att innehåll inte måste vara offentligt bör du använda lämpliga åtkomst kontroller eller ta bort själva innehållet.</p><p>Dessutom kombineras Tvingad bläddring vanligt vis med brute force-teknik för att samla in information genom att försöka få åtkomst till så många URL: er som möjligt för att räkna upp kataloger och filer på en server. Angripare kan kontrol lera alla variationer av vanliga filer. En lösen ords fils ökning skulle till exempel omfatta filer som psswd. txt, Password. htm, Password. dat och andra varianter.</p><p>För att minimera detta bör funktioner för identifiering av Brute Force-försök inkluderas.</p>|

## <a id="privileged-server"></a>Se till att konton med lägst privilegier används för att ansluta till databas servern

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL-fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Dokumentattribut**              | Gäller inte  |
| **Reference**              | Skydd bara objekt för [SQL Database behörigheter](https://msdn.microsoft.com/library/ms191465), [SQL Database-](https://msdn.microsoft.com/library/ms190401) |
| **Steg** | Konton med minst privilegier ska användas för att ansluta till databasen. Program inloggningen bör vara begränsad i databasen och bör endast köra valda lagrade procedurer. Programmets inloggning ska inte ha någon direkt tabell åtkomst. |

## <a id="rls-tenants"></a>Implementera säkerhet på radnivå för att förhindra att klienter får åtkomst till var and ras data

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL-fas**               | Utveckla |  
| **Tillämpliga tekniker** | Sql Azure, OnPrem |
| **Dokumentattribut**              | SQL-version – V12, SQL-version – MsSQL2016 |
| **Reference**              | [SQL Server säkerhet på radnivå (RLS)](https://msdn.microsoft.com/library/azure/dn765131.aspx) |
| **Steg** | <p>Säkerhet på radnivå ger kunder möjlighet att styra åtkomsten till rader i en databastabell baserat på egenskaperna för användaren som kör en fråga (t.ex. grupmedlemskap eller körningskontext).</p><p>Säkerhet på radnivå (RLS) fören klar utformningen och kodningen av säkerhet i ditt program. RLS låter dig implementera begränsningar för dataåtkomst för raden. Därmed får medarbetare endast tillgång till de datarader som är relevanta för deras avdelning, eller kunder får endast dataåtkomst till data som berör deras företag.</p><p>Logiken för åtkomst begränsning finns i databas nivån i stället för bort från data i en annan program nivå. Databas systemet tillämpar åtkomst begränsningar varje gång som data åtkomsten görs från vilken nivå som helst. Detta gör säkerhets systemet mer tillförlitligt och stabilt genom att minska säkerhets systemets Area.</p><p>|

Observera att RLS som en out-of-Box-databas-funktion bara kan användas för att SQL Server starta 2016 och Azure SQL Database. Om RLS-funktionen är inaktive ras, bör den säkerställa att data åtkomsten är begränsad med hjälp av vyer och procedurer

## <a id="sysadmin-users"></a>Sysadmin-rollen bör bara ha giltiga användare

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL-fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Dokumentattribut**              | Gäller inte  |
| **Reference**              | Skydd bara objekt för [SQL Database behörigheter](https://msdn.microsoft.com/library/ms191465), [SQL Database-](https://msdn.microsoft.com/library/ms190401) |
| **Steg** | Medlemmar i den fasta Server rollen SysAdmin bör vara mycket begränsade och innehåller aldrig konton som används av program.  Kontrol lera listan med användare i rollen och ta bort eventuella onödiga konton|

## <a id="cloud-least-privileged"></a>Ansluta till en molnbaserad Gateway med hjälp av minst privilegierade token

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | IoT Cloud Gateway | 
| **SDL-fas**               | Distribution |  
| **Tillämpliga tekniker** | Generisk |
| **Dokumentattribut**              | Gateway-val – Azure-IoT Hub |
| **Reference**              | [IoT Hub-Access Control](https://azure.microsoft.com/documentation/articles/iot-hub-devguide/#Security) |
| **Steg** | Ge minst behörighet till olika komponenter som ansluter till en molnbaserad Gateway (IoT Hub). Vanligt exempel är – enhets hantering/etablerings komponent använder registryread/Write, händelse processor (ASA) använder tjänst anslutning. Enskilda enheter ansluter med autentiseringsuppgifter för enhet|

## <a id="sendonly-sas"></a>Använd en SAS-nyckel för endast skicka-behörighet för att skapa enhets-token

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure händelsehubb | 
| **SDL-fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Dokumentattribut**              | Gäller inte  |
| **Reference**              | [Översikt över Event Hubs autentisering och säkerhets modell](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Steg** | En SAS-nyckel används för att generera enskilda enhets-token. Använd en SAS-nyckel för endast skicka-behörighet när du genererar enhets-token för en specifik utgivare|

## <a id="access-tokens-hub"></a>Använd inte åtkomsttoken som ger direkt åtkomst till Händelsehubben

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure händelsehubb | 
| **SDL-fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Dokumentattribut**              | Gäller inte  |
| **Reference**              | [Översikt över Event Hubs autentisering och säkerhets modell](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Steg** | En token som beviljar direkt åtkomst till händelsehubben ska inte ges till enheten. Om du använder en mindre privilegierad token för enheten som endast ger åtkomst till en utgivare, så skulle det underläta att identifiera och blockeringslista enheten om den skulle visa sig vara en otillåten eller komprometterad enhet.|

## <a id="sas-minimum-permissions"></a>Anslut till Händelsehubben med SAS-nycklar som har de lägsta behörigheter som krävs

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure händelsehubb | 
| **SDL-fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Dokumentattribut**              | Gäller inte  |
| **Reference**              | [Översikt över Event Hubs autentisering och säkerhets modell](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Steg** | Ge minsta behörighet till olika Server dels program som ansluter till Event Hub. Generera nycklar för separat SAS för varje backend-program och ge behörigheterna som krävs – endast skicka, ta emot eller hantera dem.|

## <a id="resource-docdb"></a>Använd Resource tokens för att ansluta till Cosmos DB närhelst det är möjligt

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Document DB | 
| **SDL-fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Dokumentattribut**              | Gäller inte  |
| **Reference**              | Gäller inte  |
| **Steg** | En resurs-token är kopplad till en Azure Cosmos DB behörighets resurs och fångar upp relationen mellan användaren av en databas och den behörighet som användaren har för en specifik Azure Cosmos DB program resurs (t. ex. samling, dokument). Använd alltid en Resource-token för att få åtkomst till Azure Cosmos DB om klienten inte är betrodd med hantering av Master-eller skrivskyddade nycklar – som ett program för slutanvändare som en mobil-eller Skriv bords klient. Använd huvud nyckel eller skrivskyddade nycklar från backend-program som kan lagra nycklarna på ett säkert sätt.|

## <a id="grained-rbac"></a>Aktivera detaljerad åtkomst hantering för Azure-prenumeration med RBAC

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Trust-gränser | 
| **SDL-fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Dokumentattribut**              | Gäller inte  |
| **Reference**              | [Använda rolltilldelningar för att hantera åtkomsten till dina Azure-prenumerationsresurser](https://azure.microsoft.com/documentation/articles/role-based-access-control-configure/)  |
| **Steg** | Rollbaserad åtkomstkontroll (RBAC) i Azure ger tillgång till ingående åtkomsthantering för Azure. Med RBAC kan du bevilja exakt den åtkomstnivå som användarna behöver för att kunna utföra sitt arbete.|

## <a id="cluster-rbac"></a>Begränsa klientens åtkomst till kluster åtgärder med RBAC

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Service Fabric förtroende gränser | 
| **SDL-fas**               | Distribution |  
| **Tillämpliga tekniker** | Generisk |
| **Dokumentattribut**              | Miljö – Azure |
| **Reference**              | [Rollbaserad åtkomst kontroll för Service Fabric klienter](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security-roles/) |
| **Steg** | <p>Azure Service Fabric har stöd för två olika åtkomst kontroll typer för klienter som är anslutna till ett Service Fabric-kluster: administratör och användare. Med åtkomst kontroll kan kluster administratören begränsa åtkomsten till vissa kluster åtgärder för olika grupper av användare, vilket gör klustret säkrare.</p><p>Administratörer har fullständig åtkomst till hanterings funktioner (inklusive Läs-och skriv funktioner). Användare har som standard endast Läs behörighet till hanterings funktioner (till exempel fråge funktioner) och möjligheten att lösa program och tjänster.</p><p>Du anger de två klient rollerna (administratör och klient) när klustret skapas genom att tillhandahålla separata certifikat för var och en.</p>|

## <a id="modeling-field"></a>Utför säkerhets modellering och Använd säkerhet på fält nivå där det behövs

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Dynamics CRM | 
| **SDL-fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Dokumentattribut**              | Gäller inte  |
| **Reference**              | Gäller inte  |
| **Steg** | Utför säkerhets modellering och Använd säkerhet på fält nivå där det behövs|

## <a id="portal-security"></a>Utför säkerhets modellering av Portal konton i åtanke att säkerhets modellen för portalen skiljer sig från resten av CRM

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Dynamics CRM-Portal | 
| **SDL-fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Dokumentattribut**              | Gäller inte  |
| **Reference**              | Gäller inte  |
| **Steg** | Utför säkerhets modellering av Portal konton i åtanke att säkerhets modellen för portalen skiljer sig från resten av CRM|

## <a id="permission-entities"></a>Ge detaljerad behörighet för en serie entiteter i Azure Table Storage

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Storage | 
| **SDL-fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Dokumentattribut**              | StorageType – tabell |
| **Reference**              | [Så här delegerar du åtkomst till objekt i ditt Azure Storage-konto med hjälp av SAS](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_data-plane-security) |
| **Steg** | I vissa affärs scenarier kan Azure Table Storage krävas för att lagra känsliga data som består till olika parter. T. ex. känsliga data som rör olika länder/regioner. I sådana fall kan SAS-signaturer skapas genom att ange nyckel intervall för partition och rad, så att en användare kan komma åt data som är specifika för ett visst land/en viss region.| 

## <a id="rbac-azure-manager"></a>Aktivera rollbaserad Access Control (RBAC) till Azure Storage-konto med Azure Resource Manager

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Storage | 
| **SDL-fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Dokumentattribut**              | Gäller inte  |
| **Reference**              | [Skydda ditt lagrings konto med rollbaserad Access Control (RBAC)](https://azure.microsoft.com/documentation/articles/storage-security-guide/#management-plane-security) |
| **Steg** | <p>När du skapar ett nytt lagrings konto väljer du en distributions modell av klassisk eller Azure Resource Manager. Den klassiska modellen för att skapa resurser i Azure tillåter bara all-eller-ingen åtkomst till prenumerationen, och i sin tur är lagrings kontot.</p><p>Med Azure Resource Manager-modellen ska du lagra lagrings kontot i en resurs grupp och kontrol lera åtkomsten till hanterings planet för det angivna lagrings kontot med hjälp av Azure Active Directory. Du kan till exempel ge vissa användare möjlighet att komma åt lagrings konto nycklarna, medan andra användare kan visa information om lagrings kontot, men inte åtkomst till lagrings konto nycklarna.</p>|

## <a id="rooting-detection"></a>Implementera implicit upplåsning eller identifiering av rottips

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Mobil klient | 
| **SDL-fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Dokumentattribut**              | Gäller inte  |
| **Reference**              | Gäller inte  |
| **Steg** | <p>Programmet bör skydda sina egna konfigurations-och användar data om telefonen är rotad eller jailbrokad bruten. Rot-/jailbrokad-brytning innebär otillåten åtkomst, vilket normala användare inte gör på sina egna telefoner. Därför bör programmet ha implicit identifierings logik vid program Start, för att identifiera om telefonen har rotats.</p><p>Identifierings logiken kan bara komma åt filer som vanligt vis bara rot användare kan komma åt, till exempel:</p><ul><li>/system/app/Superuser.apk</li><li>/sbin/su</li><li>/system/bin/su</li><li>/system/xbin/su</li><li>/data/local/xbin/su</li><li>/data/local/bin/su</li><li>/system/sd/xbin/su</li><li>/system/bin/failsafe/su</li><li>/data/local/su</li></ul><p>Om programmet kan komma åt någon av dessa filer anger det att programmet körs som rot användare.</p>|

## <a id="weak-class-wcf"></a>Svag klass referens i WCF

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | WCF | 
| **SDL-fas**               | Utveckla |  
| **Tillämpliga tekniker** | Allmänt, NET Framework 3 |
| **Dokumentattribut**              | Gäller inte  |
| **Reference**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [FORTIFY kungariket](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference) |
| **Steg** | <p>Systemet använder en svag klass referens, vilket kan göra det möjligt för en angripare att köra oauktoriserad kod. Programmet refererar till en användardefinierad klass som inte har identifierats unikt. När .NET läser in den svag identifierade klassen söker CLR-typ inläsaren efter klassen på följande platser i angiven ordning:</p><ol><li>Om sammansättningen av typen är känd söker inläsaren igenom konfigurations filens omdirigerings platser, GAC, den aktuella sammansättningen med hjälp av konfigurations information och programmets bas katalog</li><li>Om sammansättningen är okänd söker inläsaren efter den aktuella sammansättningen, mscorlib och den plats som returneras av händelse hanteraren för TypeResolve</li><li>Den här CLR-sökordningen kan ändras med krokar, till exempel mekanismen för typ vidarebefordran och händelsen AppDomain. TypeResolve</li></ol><p>Om en angripare utnyttjar CLR-sökordningen genom att skapa en alternativ klass med samma namn och placera den på en alternativ plats som CLR kommer att läsa in först, kommer CLR att oavsiktligt köra angriparen-kod som anges</p>|

### <a name="example"></a>Exempel
`<behaviorExtensions/>` Elementet i WCF-konfigurationsfilen nedan instruerar WCF att lägga till en anpassad beteende klass i ett visst WCF-tillägg.
```
<system.serviceModel>
    <extensions>
        <behaviorExtensions>
            <add name=""myBehavior"" type=""MyBehavior"" />
        </behaviorExtensions>
    </extensions>
</system.serviceModel>
```
Att använda fullständigt kvalificerade (starka) namn identifierar unikt en typ och ökar säkerheten i systemet ytterligare. Använd fullständigt kvalificerade sammansättnings namn när du registrerar typer i filerna Machine. config och app. config.

### <a name="example"></a>Exempel
`<behaviorExtensions/>` Elementet i WCF-konfigurationsfilen nedan instruerar WCF att lägga till en starkt refererad anpassad beteende klass till ett visst WCF-tillägg.
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

## <a id="wcf-authz"></a>WCF – implementera Authorization-kontroll

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | WCF | 
| **SDL-fas**               | Utveckla |  
| **Tillämpliga tekniker** | Allmänt, NET Framework 3 |
| **Dokumentattribut**              | Gäller inte  |
| **Reference**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [FORTIFY kungariket](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference) |
| **Steg** | <p>Den här tjänsten använder inte en Authorization-kontroll. När en klient anropar en viss WCF-tjänst tillhandahåller WCF olika auktoriserings scheman som kontrollerar att anroparen har behörighet att köra tjänst metoden på servern. Om verifierings kontroller inte är aktiverade för WCF-tjänster kan en autentiserad användare få behörighets eskalering.</p>|

### <a name="example"></a>Exempel
Följande konfiguration instruerar WCF att inte kontrol lera klientens autentiseringsnivå när tjänsten körs:
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
Använd ett schema för auktorisering av tjänsten för att kontrol lera att anroparen för tjänst metoden har behörighet att göra det. WCF tillhandahåller två lägen och tillåter definitionen av ett anpassat Authorization-schema. UseWindowsGroups-läget använder Windows-roller och användare och UseAspNetRoles-läget använder en ASP.NET-rollprovider, till exempel SQL Server, för att autentisera.

### <a name="example"></a>Exempel
Följande konfiguration instruerar WCF att kontrol lera att klienten är en del av gruppen Administratörer innan du kör Lägg till tjänsten:
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

## <a id="authz-aspnet"></a>Implementera rätt mekanism för auktorisering i ASP.NET webb-API

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webb-API | 
| **SDL-fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk, MVC5 |
| **Dokumentattribut**              | Ej tillämpligt, identitetsprovider-ADFS, identitetsprovider – Azure AD |
| **Reference**              | [Autentisering och auktorisering i ASP.NET webb-API](https://www.asp.net/web-api/overview/security/authentication-and-authorization-in-aspnet-web-api) |
| **Steg** | <p>Roll information för program användarna kan härledas från Azure AD eller ADFS-anspråk om programmet förlitar sig på dem som identitets leverantör eller själva programmet kan tillhandahålla det. I något av dessa fall ska implementeringen av anpassad auktorisering validera användar Rolls informationen.</p><p>Roll information för program användarna kan härledas från Azure AD eller ADFS-anspråk om programmet förlitar sig på dem som identitets leverantör eller själva programmet kan tillhandahålla det. I något av dessa fall ska implementeringen av anpassad auktorisering validera användar Rolls informationen.</p>

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
Alla styrenheter och åtgärds metoder som måste skyddas måste anges i ovanstående attribut.
```csharp
[ApiAuthorize]
public class CustomController : ApiController
{
     //Application code goes here
}
```

## <a id="device-permission"></a>Utför verifierings kontroller på enheten om den har stöd för olika åtgärder som kräver olika behörighets nivåer

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | IoT-enhet | 
| **SDL-fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Dokumentattribut**              | Gäller inte  |
| **Reference**              | Gäller inte  |
| **Steg** | <p>Enheten ska auktorisera anroparen för att kontrol lera om anroparen har de behörigheter som krävs för att utföra den begärda åtgärden. T. ex. kan du säga att enheten är ett smart dörr Lås som kan övervakas från molnet, samt att det ger funktioner som fjärrlåser dörren.</p><p>Smart dörr låset tillhandahåller en enda låsning av funktioner när någon fysiskt kommer nära dörren med ett kort. I det här fallet bör implementeringen av fjärrkommandot och kontrollen göras på ett sådant sätt att den inte tillhandahåller några funktioner för att låsa upp dörren eftersom moln-gatewayen inte har behörighet att skicka ett kommando för att låsa upp dörren.</p>|

## <a id="field-permission"></a>Utföra verifierings kontroller i fältet Gateway om det stöder olika åtgärder som kräver olika behörighets nivåer

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | IoT-fält Gateway | 
| **SDL-fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Dokumentattribut**              | Gäller inte  |
| **Reference**              | Gäller inte  |
| **Steg** | Fält-gatewayen ska auktorisera anroparen för att kontrol lera om anroparen har de behörigheter som krävs för att utföra den begärda åtgärden. Till exempel bör det finnas olika behörigheter för ett administratörs användar gränssnitt/API som används för att konfigurera en fält-Gateway v/s-enheter som ansluter till den.|
