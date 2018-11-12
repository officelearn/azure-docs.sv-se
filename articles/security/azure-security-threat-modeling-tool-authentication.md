---
title: Autentisering – Microsoft Hotmodelleringsverktyg – Azure | Microsoft Docs
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
ms.openlocfilehash: b4b6e91f9b20166f63a4710f42726bf1d4090022
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51251567"
---
# <a name="security-frame-authentication--mitigations"></a>Security ram: Autentisering | Åtgärder 
| Produkt/tjänst | Artikel |
| --------------- | ------- |
| **Webbprogram**    | <ul><li>[Överväg att använda en standard autentiseringsmekanism för att autentisera till webbprogram](#standard-authn-web-app)</li><li>[Program måste hantera misslyckade autentiseringsscenarier på ett säkert sätt](#handle-failed-authn)</li><li>[Aktivera steg upp eller anpassad autentisering](#step-up-adaptive-authn)</li><li>[Se till att administrationsgränssnitt på rätt sätt är låsta](#admin-interface-lockdown)</li><li>[Implementera har glömt lösenord funktioner på ett säkert sätt](#forgot-pword-fxn)</li><li>[Se till att lösenord och principen tillämpas](#pword-account-policy)</li><li>[Implementera kontroller för att förhindra användarnamn uppräkning](#controls-username-enum)</li></ul> |
| **Databas** | <ul><li>[Använd om möjligt Windows-autentisering för att ansluta till SQL Server](#win-authn-sql)</li><li>[När det är möjligt använda Azure Active Directory-autentisering för anslutning till SQL-databas](#aad-authn-sql)</li><li>[När SQL-autentiseringsläge används, se till att kontot och lösenordet principer verkställs på SQLServer](#authn-account-pword)</li><li>[Använd inte SQL-autentisering i inneslutna databaser](#autn-contained-db)</li></ul> |
| **Azure-Händelsehubb** | <ul><li>[Använda per enhet autentiseringsuppgifter med hjälp av SaS-token](#authn-sas-tokens)</li></ul> |
| **Azure Förtroendegräns** | <ul><li>[Aktivera Azure Multi-Factor Authentication för Azure-administratörer](#multi-factor-azure-admin)</li></ul> |
| **Service Fabric-Förtroendegräns** | <ul><li>[Begränsa anonym åtkomst till Service Fabric-kluster](#anon-access-cluster)</li><li>[Se till att certifikatet för Service Fabric-klient-till-nod skiljer sig från nod till nod-certifikat](#fabric-cn-nn)</li><li>[Använda AAD för att autentisera klienter till service fabric-kluster](#aad-client-fabric)</li><li>[Se till att service fabric-certifikat hämtas från en godkänd certifikatutfärdaren (CA)](#fabric-cert-ca)</li></ul> |
| **Identitetsserver** | <ul><li>[Använd standard autentiseringsscenarier som stöds av Identity Server](#standard-authn-id)</li><li>[Åsidosätt standard Identity Server tokens cacheminne med ett skalbart alternativ](#override-token)</li></ul> |
| **Datorn Förtroendegräns** | <ul><li>[Se till att binärfilerna för distribuerade program är digitalt signerade](#binaries-signed)</li></ul> |
| **WCF** | <ul><li>[Aktivera autentisering när du ansluter till MSMQ-köer i WCF](#msmq-queues)</li><li>[WCF-gör Ange inte meddelande clientCredentialType till ingen](#message-none)</li><li>[WCF-gör inställd inte Transport clientCredentialType none](#transport-none)</li></ul> |
| **Webb-API** | <ul><li>[Se till att standard autentiseringen tekniker som används för att skydda webb-API: er](#authn-secure-api)</li></ul> |
| **Azure AD** | <ul><li>[Använd standard autentiseringsscenarier som stöds av Azure Active Directory](#authn-aad)</li><li>[Åsidosätt standard ADAL-tokencache med ett skalbart alternativ](#adal-scalable)</li><li>[Se till att TokenReplayCache används för att förhindra återuppspelning av ADAL-autentisering-token](#tokenreplaycache-adal)</li><li>[Använda ADAL-bibliotek för att hantera förfrågningar om säkerhetstoken från OAuth2-klienter till AAD (eller lokala AD)](#adal-oauth2)</li></ul> |
| **IoT-fält-Gateway** | <ul><li>[Autentisera enheter som ansluter till fält-Gateway](#authn-devices-field)</li></ul> |
| **IoT-molnet Gateway** | <ul><li>[Se till att enheter som ansluter till molngatewayen autentiseras](#authn-devices-cloud)</li><li>[Använd autentiseringsuppgifter för varje enhet](#authn-cred)</li></ul> |
| **Azure Storage** | <ul><li>[Se till att endast nödvändiga behållare och blobbar ges anonym läsbehörighet](#req-containers-anon)</li><li>[Bevilja begränsad åtkomst till objekt i Azure storage med SAS eller SAP](#limited-access-sas)</li></ul> |

## <a id="standard-authn-web-app"></a>Överväg att använda en standard autentiseringsmekanism för att autentisera till webbprogram

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| Information | <p>Autentisering är den process där en entitet bevisar sin identitet, vanligtvis via autentiseringsuppgifter, till exempel ett användarnamn och lösenord. Det finns flera autentiseringsprotokoll tillgängliga som kan betraktas. Vissa av dem anges nedan:</p><ul><li>Klientcertifikat</li><li>Windows-baserad</li><li>Formulärbaserad</li><li>Federation - AD FS</li><li>Federation - Azure AD</li><li>Federation - Identitetsserver</li></ul><p>Överväg att använda en standard autentiseringsmekanism för att identifiera Källprocessen</p>|

## <a id="handle-failed-authn"></a>Program måste hantera misslyckade autentiseringsscenarier på ett säkert sätt

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| Information | <p>Program som uttryckligen autentiserar användare måste hantera misslyckade autentiseringsscenarier på ett säkert sätt. Autentiseringsmekanismen måste:</p><ul><li>Neka åtkomst till Privilegierade resurser när autentisering misslyckas</li><li>Visa ett allmänt felmeddelande efter en misslyckad autentisering och åtkomst nekad inträffar</li></ul><p>Testa för:</p><ul><li>Skydd av privilegierad resurser efter misslyckade inloggningar</li><li>Ett allmänt felmeddelande visas på misslyckade autentiseringar och åtkomst nekad händelse(r)</li><li>Konton har inaktiverats efter ett orimligt antal misslyckade försök</li><ul>|

## <a id="step-up-adaptive-authn"></a>Aktivera steg upp eller anpassad autentisering

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| Information | <p>Kontrollera att programmet har ytterligare auktorisering (till exempel ta steget upp eller anpassad autentisering via autentisering med flera faktorer, till exempel skicka Engångslösenord i SMS, e-post o.s.v. eller fråga om återautentiseringen) så att användaren får innan de ges åtkomst till känslig information. Den här regeln gäller även för att göra viktiga ändringar till ett konto eller en åtgärd</p><p>Det innebär också att anpassningen av autentisering har implementeras på ett sådant sätt att programmet korrekt tillämpar sammanhangsberoende auktorisering så att inte obehöriga manipulering med hjälp av i exemplet parametern manipulering</p>|

## <a id="admin-interface-lockdown"></a>Se till att administrationsgränssnitt på rätt sätt är låsta

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| Information | Den första lösningen är att bevilja åtkomst från ett visst käll-IP-intervall till det administrativa gränssnittet. Om lösningen inte skulle vara möjligt än vad det är alltid rekommenderar vi för att upprätthålla en Step Up eller anpassningsbar authentication för att logga in i det administrativa gränssnittet |

## <a id="forgot-pword-fxn"></a>Implementera har glömt lösenord funktioner på ett säkert sätt

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| Information | <p>Första är att kontrollera att du har glömt lösenord och andra recovery sökvägar skickar en länk, inklusive en tidsbegränsad aktivering token i stället för själva lösenordet. Ytterligare autentisering baserat på mjuk-token (t.ex. SMS-token, inbyggda mobilappar, osv.) kan vara krävs också innan länken skickas över. Andra: du bör inte låsa ut användare även om processen för att få ett nytt lösenord pågår.</p><p>Detta kan leda till en DoS-angrepp när en angripare beslutar att avsiktligt låser ut användare med en automatiserad attack. Det tredje när den nya förfrågan lösenord ställdes in håller på att måste meddelandet du visa vara generaliserat, för att förhindra att användarnamn uppräkning. Fjärde, alltid Tillåt inte användning av gamla lösenord och implementera en princip för starkt lösenord.</p> |

## <a id="pword-account-policy"></a>Se till att lösenord och principen tillämpas

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| Information | <p>Princip för lösenord och efterlever organisationens policy och bästa praxis bör implementeras.</p><p>Att skydda mot brute force- och ordlista baserat gissa: stark lösenordsprincip måste implementeras för att säkerställa att användarna skapar komplext lösenord (t.ex. 12 tecken minimilängd, alfanumeriska tecken och specialtecken).</p><p>Principer för kontoutelåsning kan implementeras på följande sätt:</p><ul><li>**Mjuk Lås ut:** detta kan vara ett bra alternativ för att skydda användarna mot brute force-attacker. Till exempel när användaren anger fel lösenord kunde tre gånger programmet låsa kontot för en minut för att kunna långsammare processen med brute tvinga sitt lösenord, vilket gör det mindre lönsamma för angripare att fortsätta. Om du implementerar hårda motåtgärder för lås rapporteras för det här exemplet skulle du få en ”Dos” av permanent utelåsning konton. Du kan också program kan generera ett Engångslösenord (en gång lösenord) och skicka den out-of-band (via e-post, sms etc.) för användaren. En annan metod kan vara att implementera CAPTCHA när ett tröskelvärde för antal misslyckade försök har nåtts.</li><li>**Hårda Lås ut:** den här typen av kontoutelåsning som ska användas när du identifierar en användare som en attack ditt program och räknare honom med hjälp av permanent utelåsning kontot förrän en svarsgrupp haft tid att göra sina datautredning. Efter den här processen som du kan välja att ge användaren tillbaka sitt konto eller ytterligare vidta rättsliga åtgärder mot honom. Den här typen av metoden förhindrar att angriparen ytterligare leds genom ditt program och infrastruktur.</li></ul><p>Kontrollera att alla nycklar och lösenord är utbytbara och är för att skydda dig mot angrepp på standard- och förutsägbara konton, genererats eller ersatts efter tidpunkten för installationen.</p><p>Om programmet har att generera lösenord automatiskt, kontrollera att de genererade lösenord är slumpmässig och har hög entropi.</p>|

## <a id="controls-username-enum"></a>Implementera kontroller för att förhindra användarnamn uppräkning

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | Alla felmeddelanden måste vara generaliserat, för att förhindra att användarnamn uppräkning. Även undvika ibland du inte information läcker i funktioner, till exempel en registreringssida. Här behöver du använder hastighetsbegränsande metoder som CAPTCHA för att förhindra att en automatiserad attack av en angripare. |

## <a id="win-authn-sql"></a>Använd om möjligt Windows-autentisering för att ansluta till SQL Server

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | OnPrem |
| **Attribut**              | Version av SQL - alla |
| **Referenser**              | [SQLServer – Välj ett autentiseringsläge](https://msdn.microsoft.com/library/ms144284.aspx) |
| **Steg** | Windows-autentisering använder Kerberos-säkerhetsprotokoll, tillhandahåller tvingande principer lösenord avseende avancerad verifiering för starka lösenord, har stöd för kontoutelåsning och har stöd för Lösenordets giltighetstid.|

## <a id="aad-authn-sql"></a>När det är möjligt använda Azure Active Directory-autentisering för anslutning till SQL-databas

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | SQL Azure |
| **Attribut**              | SQL Version - V12 |
| **Referenser**              | [Ansluta till SQL Database med hjälp av Azure Active Directory-autentisering](https://azure.microsoft.com/documentation/articles/sql-database-aad-authentication/) |
| **Steg** | **Lägsta version:** Azure SQL Database V12 krävs för att tillåta Azure SQL Database för att använda AAD-autentisering mot Microsoft Directory |

## <a id="authn-account-pword"></a>När SQL-autentiseringsläge används, se till att kontot och lösenordet principer verkställs på SQLServer

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [SQL Server-lösenordsprincip](https://technet.microsoft.com/library/ms161959(v=sql.110).aspx) |
| **Steg** | När du använder SQL Server-autentisering, skapas inloggningar i SQL Server som inte är baserade på Windows-användarkonton. Både användarnamnet och lösenordet skapas med hjälp av SQL Server och lagras i SQL Server. SQL Server kan använda Windows lösenord princip metoder. Den kan tillämpa samma komplexiteten och principer för förfallodatum som används i Windows lösenord som används i SQL Server. |

## <a id="autn-contained-db"></a>Använd inte SQL-autentisering i inneslutna databaser

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | OnPrem, SQL Azure |
| **Attribut**              | SQL-Version - MSSQL2012, SQL-Version - V12 |
| **Referenser**              | [Rekommenderade säkerhetsmetoder med inneslutna databaser](https://msdn.microsoft.com/library/ff929055.aspx) |
| **Steg** | Avsaknad av en tvingande lösenordsprincip kan öka sannolikheten för en svag autentiseringsuppgift upprättas i en innesluten databas. Använda Windows-autentisering. |

## <a id="authn-sas-tokens"></a>Använda per enhet autentiseringsuppgifter med hjälp av SaS-token

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure händelsehubb | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Autentisering och säkerhet modellen översikt över Event Hubs](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Steg** | <p>Event Hubs-säkerhetsmodellen bygger på en kombination av token för signatur för delad åtkomst (SAS) och utgivare. Utgivarens namn representerar DeviceID som tar emot en token. Detta skulle hjälpa till att associera de token som genererats med respektive enheterna.</p><p>Alla meddelanden är märkta med avsändaren på tjänstsidan så att identifiering av in-nyttolast ursprung förfalskning försök. När du autentiserar enheter, generera en per enhet SaS-token som hör till en unik utgivare.</p>|

## <a id="multi-factor-azure-admin"></a>Aktivera Azure Multi-Factor Authentication för Azure-administratörer

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Förtroendegräns | 
| **SDL fas**               | Distribution |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Vad är Azure Multi-Factor Authentication?](https://azure.microsoft.com/documentation/articles/multi-factor-authentication/) |
| **Steg** | <p>Multifaktorautentisering (MFA) är en autentiseringsmetod som kräver mer än en verifieringsmetod och lägger till ett viktigt andra säkerhetslager till användarinloggningar och transaktioner. Det fungerar genom att kräva två eller flera av följande verifieringsmetoder:</p><ul><li>Något du vet (normalt ett lösenord)</li><li>Något du har (betrodda enheter som inte enkelt dupliceras, t.ex. en telefon)</li><li>Något du är (biometrik)</li><ul>|

## <a id="anon-access-cluster"></a>Begränsa anonym åtkomst till Service Fabric-kluster

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Service Fabric-Förtroendegräns | 
| **SDL fas**               | Distribution |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Miljö - Azure  |
| **Referenser**              | [Säkerhetsscenarier för Service Fabric-kluster](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security) |
| **Steg** | <p>Kluster bör alltid skyddas för att förhindra att obehöriga användare från att ansluta till ditt kluster, särskilt när den har produktionsarbetsbelastningar körs på den.</p><p>När du skapar ett service fabric-kluster, kontrollerar du att säkerhetsläget är inställd på ”säkra” och konfigurera krävs X.509-servercertifikatet. Skapar ett ”osäker” kluster tillåter anonyma användare att ansluta till den om den exponerar hanteringsslutpunkter till det offentliga Internet.</p>|

## <a id="fabric-cn-nn"></a>Se till att certifikatet för Service Fabric-klient-till-nod skiljer sig från nod till nod-certifikat

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Service Fabric-Förtroendegräns | 
| **SDL fas**               | Distribution |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Miljö - Azure, miljö - fristående |
| **Referenser**              | [Service Fabric klient-till-nod Certifikatsäkerhet](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/#_client-to-node-certificate-security), [Anslut till ett säkert kluster som använder klientcertifikat](https://azure.microsoft.com/documentation/articles/service-fabric-connect-to-secure-cluster/) |
| **Steg** | <p>Klient-till-nod Certifikatsäkerhet konfigureras när du skapade klustret antingen via Azure portal, Resource Manager-mallar eller en fristående JSON-mall genom att ange ett klientcertifikat för administratör och/eller ett klientcertifikat.</p><p>Administratören klient- och klientcertifikat du anger måste skilja sig från de primära och sekundära certifikat som du anger för nod till nod-säkerhet.</p>|

## <a id="aad-client-fabric"></a>Använda AAD för att autentisera klienter till service fabric-kluster

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Service Fabric-Förtroendegräns | 
| **SDL fas**               | Distribution |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Miljö - Azure |
| **Referenser**              | [Klustersäkerhetsscenarier - säkerhetsrekommendationer](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/#security-recommendations) |
| **Steg** | Kluster som körs på Azure kan även skydda åtkomst till management-slutpunkter med hjälp av Azure Active Directory (AAD), förutom de klientcertifikat. Azure-kluster kan rekommenderas det att du använder AAD-säkerhet för att autentisera klienter och certifikat för nod-till-nod-säkerhet.|

## <a id="fabric-cert-ca"></a>Se till att service fabric-certifikat hämtas från en godkänd certifikatutfärdaren (CA)

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Service Fabric-Förtroendegräns | 
| **SDL fas**               | Distribution |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Miljö - Azure |
| **Referenser**              | [X.509-certifikat och Service Fabric](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/#x509-certificates-and-service-fabric) |
| **Steg** | <p>Service Fabric använder X.509-servercertifikat för att autentisera noder och klienter.</p><p>Några viktiga saker att tänka på när du använder certifikat i service fabrics:</p><ul><li>Certifikat som används i kluster som kör produktionsarbetsbelastningar ska skapas med hjälp av en korrekt konfigurerad Certifikattjänsten för Windows Server eller hämtas från en godkänd certifikatutfärdaren (CA). CA: N kan vara en godkänd extern Certifikatutfärdare eller en korrekt hanterade interna Public Key Infrastructure (PKI)</li><li>Använd aldrig någon tillfälliga eller testa certifikat i produktion som har skapats med verktyg som MakeCert.exe</li><li>Du kan använda ett självsignerat certifikat, men bör bara göra det för testkluster och inte i produktion</li></ul>|

## <a id="standard-authn-id"></a>Använd standard autentiseringsscenarier som stöds av Identity Server

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Identitetsserver | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [IdentityServer3 - helheten](https://identityserver.github.io/Documentation/docsv2/overview/bigPicture.html) |
| **Steg** | <p>Nedan visas vanliga interaktioner som stöds av Identity Server:</p><ul><li>Webbläsare kommunicera med webbprogram</li><li>Webbprogram kommunicera med webb-API: er (ibland på egen hand, ibland för en användares räkning)</li><li>Webbläsarbaserade program kommunicera med web API: er</li><li>Interna program kommunicera med web API: er</li><li>Server-baserade program kommunicera med web API: er</li><li>Web API: erna kommunicera med webb-API: er (ibland på egen hand, ibland för en användares räkning)</li></ul>|

## <a id="override-token"></a>Åsidosätt standard Identity Server tokens cacheminne med ett skalbart alternativ

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Identitetsserver | 
| **SDL fas**               | Distribution |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Identity Server-distribution - cachelagring](https://identityserver.github.io/Documentation/docsv2/advanced/deployment.html) |
| **Steg** | <p>IdentityServer har en enkel inbyggd i cacheminnet. Även om det är bra för inbyggda appar i liten skala, skalar den inte för mid nivå och serverdelen av följande skäl:</p><ul><li>Dessa program som används av många användare samtidigt. Spara alla åtkomsttoken i samma lager skapas isolering problem och visar utmaningar när skalenligt: många användare med så många token som appen kommer åt å deras vägnar resurserna kan innebära enorma och väldigt dyr sökåtgärder</li><li>Dessa program distribueras vanligen på distribuerade topologier, där flera noder måste ha åtkomst till samma cache</li><li>Cachelagrade token måste klara återvinns och avaktiveringar</li><li>För alla ovanstående, rekommenderar samtidigt som implementerar webbappar, vi att åsidosätta standard Identity Server-tokencache med ett skalbart alternativ, till exempel Azure Redis cache</li></ul>|

## <a id="binaries-signed"></a>Se till att binärfilerna för distribuerade program är digitalt signerade

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Datorn Förtroendegräns | 
| **SDL fas**               | Distribution |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | Se till att binärfilerna för distribuerade program är digitalt signerade så att integriteten hos binärfilerna som kan verifieras|

## <a id="msmq-queues"></a>Aktivera autentisering när du ansluter till MSMQ-köer i WCF

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | WCF | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk NET Framework 3 |
| **Attribut**              | Gäller inte |
| **Referenser**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx) |
| **Steg** | Programmet inte kan aktivera autentisering när du ansluter till MSMQ-köer, en angripare kan anonymt skicka meddelanden till kön för bearbetning. Om autentisering inte används för att ansluta till en MSMQ-kö som används för att leverera ett meddelande till ett annat program, kan en angripare skicka en anonym meddelande som är skadliga.|

### <a name="example"></a>Exempel
Den `<netMsmqBinding/>` element i WCF-konfigurationsfilen nedan instruerar WCF att inaktivera autentisering när du ansluter till en MSMQ-kö för leverans av meddelanden.
```
<bindings>
    <netMsmqBinding>
        <binding>
            <security>
                <transport msmqAuthenticationMode=""None"" />
            </security>
        </binding>
    </netMsmqBinding>
</bindings>
```
Konfigurera MSMQ för att kräva autentisering för Windows-domän eller certifikat vid alla tidpunkter för inkommande eller utgående meddelanden.

### <a name="example"></a>Exempel
Den `<netMsmqBinding/>` element i WCF-konfigurationsfilen nedan instruerar WCF att aktivera autentisering när du ansluter till en MSMQ-kö. Klienten är autentiserad med X.509-certifikat. Klientcertifikatet måste finnas i certifikatarkivet för servern.
```
<bindings>
    <netMsmqBinding>
        <binding>
            <security>
                <transport msmqAuthenticationMode=""Certificate"" />
            </security>
        </binding>
    </netMsmqBinding>
</bindings>
```

## <a id="message-none"></a>WCF-gör Ange inte meddelande clientCredentialType till ingen

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | WCF | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | .NET framework 3 |
| **Attribut**              | Typ av autentiseringsuppgift för klient - ingen |
| **Referenser**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [spikning](https://vulncat.fortify.com/en/detail?id=desc.semantic.dotnet.wcf_misconfiguration_anonymous_message_client) |
| **Steg** | Avsaknad av autentisering innebär att alla har tillgång till den här tjänsten. En tjänst som dess klienter inte autentiserar tillåter åtkomst till alla användare. Konfigurera programmet att autentisera mot klientens autentiseringsuppgifter. Detta kan göras genom att ange meddelande clientCredentialType till Windows- eller certifikat. |

### <a name="example"></a>Exempel
```
<message clientCredentialType=""Certificate""/>
```

## <a id="transport-none"></a>WCF-gör inställd inte Transport clientCredentialType none

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | WCF | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Allmän och .NET Framework 3 |
| **Attribut**              | Typ av autentiseringsuppgift för klient - ingen |
| **Referenser**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [spikning](https://vulncat.hpefod.com/en/detail?id=desc.semantic.dotnet.wcf_misconfiguration_anonymous_transport_client) |
| **Steg** | Avsaknad av autentisering innebär att alla har tillgång till den här tjänsten. En tjänst som inte autentiserar dess klienter kan alla användare åtkomst till dess funktioner. Konfigurera programmet att autentisera mot klientens autentiseringsuppgifter. Detta kan göras genom att ange transport clientCredentialType till Windows- eller certifikat. |

### <a name="example"></a>Exempel
```
<transport clientCredentialType=""Certificate""/>
```

## <a id="authn-secure-api"></a>Se till att standard autentiseringen tekniker som används för att skydda webb-API: er

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webb-API | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Autentisering och auktorisering i ASP.NET Web API](http://www.asp.net/web-api/overview/security/authentication-and-authorization-in-aspnet-web-api), [extern autentiseringstjänster med ASP.NET webb-API (C#)](http://www.asp.net/web-api/overview/security/external-authentication-services) |
| **Steg** | <p>Autentisering är den process där en entitet bevisar sin identitet, vanligtvis via autentiseringsuppgifter, till exempel ett användarnamn och lösenord. Det finns flera autentiseringsprotokoll tillgängliga som kan betraktas. Vissa av dem anges nedan:</p><ul><li>Klientcertifikat</li><li>Windows-baserad</li><li>Formulärbaserad</li><li>Federation - AD FS</li><li>Federation - Azure AD</li><li>Federation - Identitetsserver</li></ul><p>Länkarna i referensavsnittet innehåller detaljer om hur var och en av autentiseringsscheman kan implementeras för att skydda ett webb-API.</p>|

## <a id="authn-aad"></a>Använd standard autentiseringsscenarier som stöds av Azure Active Directory

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure AD | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Autentiseringsscenarier för Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-authentication-scenarios/), [kodexempel för Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-code-samples/), [Utvecklarhandbok för Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-developers-guide/) |
| **Steg** | <p>Azure Active Directory (Azure AD) förenklar autentisering för utvecklare genom att tillhandahålla identitet som en tjänst, med stöd för branschstandardprotokoll som OAuth 2.0 och OpenID Connect. Här följer du de fem primära programmet scenarier som stöds av Azure AD:</p><ul><li>Webbläsaren till webbprogram: en användare måste logga in till ett webbprogram som skyddas av Azure AD</li><li>Sidan program (SPA): En användare måste logga in på en ensidesapplikation som skyddas av Azure AD</li><li>Internt program till webb-API: ett internt program som körs på en telefon, surfplatta eller dator måste autentisera en användare för att hämta resurser från ett webb-API som skyddas av Azure AD</li><li>Webbprogram till webb-API: ett program behöver få resurser från ett webb-API som skyddas av Azure AD</li><li>Daemon eller ett program till webb-API: en daemon-program eller ett serverprogram utan användargränssnitt för web måste hämta resurser från ett webb-API som skyddas av Azure AD</li></ul><p>Se länkarna i referensavsnittet för på låg nivå implementeringsdetaljer</p>|

## <a id="adal-scalable"></a>Åsidosätt standard ADAL-tokencache med ett skalbart alternativ

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure AD | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Modern autentisering med Azure Active Directory för webbprogram](https://blogs.msdn.microsoft.com/microsoft_press/2016/01/04/new-book-modern-authentication-with-azure-active-directory-for-web-applications/), [använda Redis som ADAL-tokencache](https://blogs.msdn.microsoft.com/mrochon/2016/09/19/using-redis-as-adal-token-cache/)  |
| **Steg** | <p>Standard-cache som använder ADAL (Active Directory Authentication Library) är en minnescache som förlitar sig på en statisk store, tillgängliga på hela processen. Även om det här fungerar för interna program, skalar den inte för mid nivå och backend-program av följande skäl:</p><ul><li>Dessa program som används av många användare samtidigt. Spara alla åtkomsttoken i samma lager skapas isolering problem och visar utmaningar när skalenligt: många användare med så många token som appen kommer åt å deras vägnar resurserna kan innebära enorma och väldigt dyr sökåtgärder</li><li>Dessa program distribueras vanligen på distribuerade topologier, där flera noder måste ha åtkomst till samma cache</li><li>Cachelagrade token måste klara återvinns och avaktiveringar</li></ul><p>För alla ovanstående, rekommenderar samtidigt som implementerar webbappar, vi att du åsidosätta standard ADAL-tokencache med ett skalbart alternativ, till exempel Azure Redis cache.</p>|

## <a id="tokenreplaycache-adal"></a>Se till att TokenReplayCache används för att förhindra återuppspelning av ADAL-autentisering-token

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure AD | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Modern autentisering med Azure Active Directory för webbprogram](https://blogs.msdn.microsoft.com/microsoft_press/2016/01/04/new-book-modern-authentication-with-azure-active-directory-for-web-applications/) |
| **Steg** | <p>Egenskapen TokenReplayCache kan utvecklare definiera en token repetitionsattacker cache, en butik som kan användas för att spara token för att kontrollera att ingen token kan användas flera gånger.</p><p>Det här är ett mått mot ett vanligt angrepp aptly kallas token repetitionsattacker angreppet: en angripare fångar upp den token som skickas vid inloggning kan försöka att skicka den till appen igen (”spela upp” den) för att upprätta en ny session. T.ex. I OIDC-beviljande via flow, efter lyckad användarautentisering, en begäran om att ”/ inloggning från oidc” slutpunkten för den förlitande parten görs med ”id_token”, ”code” och ”delstat” parametrar.</p><p>Den förlitande parten verifierar den här förfrågan och upprättar en ny session. Om en angripare samlar in den här förfrågan och spelar upp det, kan han/hon upprättar en lyckad session och förfalska användaren. Förekomst av nonce i OpenID Connect kan begränsa men inte helt eliminera i situationer där angreppet kan vara har trätt i kraft. För att skydda sina program, kan utvecklare ge en implementering av ITokenReplayCache och tilldela en instans till TokenReplayCache.</p>|

### <a name="example"></a>Exempel
```csharp
// ITokenReplayCache defined in ADAL
public interface ITokenReplayCache
{
bool TryAdd(string securityToken, DateTime expiresOn);
bool TryFind(string securityToken);
}
```

### <a name="example"></a>Exempel
Här är ett exempel på implementering av gränssnittet ITokenReplayCache. (Du anpassa och implementera din projekt cachelagring ramverk)
```csharp
public class TokenReplayCache : ITokenReplayCache
{
    private readonly ICacheProvider cache; // Your project-specific cache provider
    public TokenReplayCache(ICacheProvider cache)
    {
        this.cache = cache;
    }
    public bool TryAdd(string securityToken, DateTime expiresOn)
    {
        if (this.cache.Get<string>(securityToken) == null)
        {
            this.cache.Set(securityToken, securityToken);
            return true;
        }
        return false;
    }
    public bool TryFind(string securityToken)
    {
        return this.cache.Get<string>(securityToken) != null;
    }
}
```
Implementerad cacheminnet har refereras i OIDC alternativ via egenskapen ”TokenValidationParameters” på följande sätt.
```csharp
OpenIdConnectOptions openIdConnectOptions = new OpenIdConnectOptions
{
    AutomaticAuthenticate = true,
    ... // other configuration properties follow..
    TokenValidationParameters = new TokenValidationParameters
    {
        TokenReplayCache = new TokenReplayCache(/*Inject your cache provider*/);
    }
}
```

Tänk på att för att testa effektiviteten i den här konfigurationen måste logga in på ditt lokala OIDC-skyddade program och samla in begäran om att `"/signin-oidc"` slutpunkten i fiddler. När skyddet inte är på plats, kommer Spela upp den här begäran i fiddler in en ny sessionscookie. När begäran spelas när TokenReplayCache skydd har lagts till, genereras programmet ett undantagsfel på följande sätt: `SecurityTokenReplayDetectedException: IDX10228: The securityToken has previously been validated, securityToken: 'eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ1......`

## <a id="adal-oauth2"></a>Använda ADAL-bibliotek för att hantera förfrågningar om säkerhetstoken från OAuth2-klienter till AAD (eller lokala AD)

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure AD | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [ADAL](https://azure.microsoft.com/documentation/articles/active-directory-authentication-libraries/) |
| **Steg** | <p>Azure AD authentication Library (ADAL) gör det möjligt för utvecklare av klientprogram att autentisera enkelt användare till molnet eller lokala Active Directory (AD) och sedan hämta åtkomsttoken för att skydda API-anrop.</p><p>ADAL har många funktioner som gör autentiseringen enklare för utvecklare, till exempel asynkront stöd, en konfigurerbar token-cache som lagrar åtkomsttoken och uppdateringstoken, automatisk tokenuppdatering när en åtkomst-token upphör att gälla och en uppdateringstoken är tillgänglig, och Mer.</p><p>Genom att hantera de flesta av komplexiteten ADAL hjälpa en utvecklare fokusera på affärslogiken i sina program och enkelt skydda resurser utan att vara expert på säkerhet. Separata finns lagringsklientbibliotek för .NET, JavaScript (klient och Node.js), iOS, Android och Java.</p>|

## <a id="authn-devices-field"></a>Autentisera enheter som ansluter till fält-Gateway

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | IoT-fält-Gateway | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | Se till att varje enhet autentiseras av fält-Gateway innan du tar emot data från dem och innan du underlätta överordnade kommunikation med Molngatewayen. Kontrollera också att enheter ska ansluta med en per enhet så att enskilda enheter kan ha en unik identifiering av autentiseringsuppgifter.|

## <a id="authn-devices-cloud"></a>Se till att enheter som ansluter till molngatewayen autentiseras

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | IoT-molnet Gateway | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk, C#, Node.JS,  |
| **Attribut**              | Inte aktuellt, Gateway - valet i Azure IoT Hub |
| **Referenser**              | Inte aktuellt, [Azure IoT hub med .NET](https://azure.microsoft.com/documentation/articles/iot-hub-csharp-csharp-getstarted/), [komma igång med IoT hub och Node JS](https://azure.microsoft.com/documentation/articles/iot-hub-node-node-getstarted), [skydda IoT med SAS och certifikat](https://azure.microsoft.com/documentation/articles/iot-hub-sas-tokens/), [Git-lagringsplats](https://github.com/Azure/azure-iot-sdks/tree/master/node) |
| **Steg** | <ul><li>**Generisk:** autentisera enheten med Transport Layer Security (TLS) eller IPSec. Infrastruktur bör ha i förväg delad nyckel (PSK) på de enheter som inte kan hantera fullständiga asymmetrisk kryptering. Använda Azure AD, Oauth.</li><li>**C#:** när du skapar en DeviceClient-instans som standard, metoden Create skapar en DeviceClient-instans som använder AMQP-protokollet för att kommunicera med IoT Hub. Om du vill använda HTTPS-protokollet använder du åsidosättandet av Create-metoden som gör att du kan ange protokollet. Om du använder HTTPS-protokollet bör du också lägga till den `Microsoft.AspNet.WebApi.Client` NuGet-paket i ditt projekt i `System.Net.Http.Formatting` namnområde.</li></ul>|

### <a name="example"></a>Exempel
```csharp
static DeviceClient deviceClient;

static string deviceKey = "{device key}";
static string iotHubUri = "{iot hub hostname}";

var messageString = "{message in string format}";
var message = new Message(Encoding.ASCII.GetBytes(messageString));

deviceClient = DeviceClient.Create(iotHubUri, new DeviceAuthenticationWithRegistrySymmetricKey("myFirstDevice", deviceKey));

await deviceClient.SendEventAsync(message);
```

### <a name="example"></a>Exempel
**Node.JS: autentisering**
#### <a name="symmetric-key"></a>Symmetrisk nyckel
* Skapa en IoT-hubb på azure
* Skapa en post i enhetsidentitetsregistret
    ```javascript
    var device = new iothub.Device(null);
    device.deviceId = <DeviceId >
    registry.create(device, function(err, deviceInfo, res) {})
    ```
* Skapa en simulerad enhet
    ```javascript
    var clientFromConnectionString = require('azure-iot-device-amqp').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    var connectionString = 'HostName=<HostName>DeviceId=<DeviceId>SharedAccessKey=<SharedAccessKey>';
    var client = clientFromConnectionString(connectionString);
    ```
#### <a name="sas-token"></a>SAS-token
* Hämtar genereras internt när du använder symmetrisk nyckel, men vi kan skapa och använda det uttryckligen samt
* Definiera ett protokoll: `var Http = require('azure-iot-device-http').Http;`
* Skapa en sas-token:
    ```javascript
    resourceUri = encodeURIComponent(resourceUri.toLowerCase()).toLowerCase();
    var deviceName = "<deviceName >";
    var expires = (Date.now() / 1000) + expiresInMins * 60;
    var toSign = resourceUri + '\n' + expires;
    // using crypto
    var decodedPassword = new Buffer(signingKey, 'base64').toString('binary');
    const hmac = crypto.createHmac('sha256', decodedPassword);
    hmac.update(toSign);
    var base64signature = hmac.digest('base64');
    var base64UriEncoded = encodeURIComponent(base64signature);
    // construct authorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "%2fdevices%2f"+deviceName+"&sig="
    + base64UriEncoded + "&se=" + expires;
    if (policyName) token += "&skn="+policyName;
    return token;
    ```
* Anslut med sas-token: 
    ```javascript
    Client.fromSharedAccessSignature(sas, Http); 
    ```
#### <a name="certificates"></a>Certifikat
* Generera ett självsignerat signerade X509 certifikat med valfritt verktyg, till exempel OpenSSL för att generera en .cert och .key-filer för att lagra certifikatet och nyckeln för respektive
* Etablera en enhet som accepterar säker anslutning med hjälp av certifikat.
    ```javascript
    var connectionString = '<connectionString>';
    var registry = iothub.Registry.fromConnectionString(connectionString);
    var deviceJSON = {deviceId:"<deviceId>",
    authentication: {
        x509Thumbprint: {
        primaryThumbprint: "<PrimaryThumbprint>",
        secondaryThumbprint: "<SecondaryThumbprint>"
        }
    }}
    var device = deviceJSON;
    registry.create(device, function (err) {});
    ```
* Anslut en enhet med ett certifikat
    ```javascript
    var Protocol = require('azure-iot-device-http').Http;
    var Client = require('azure-iot-device').Client;
    var connectionString = 'HostName=<HostName>DeviceId=<DeviceId>x509=true';
    var client = Client.fromConnectionString(connectionString, Protocol);
    var options = {
        key: fs.readFileSync('./key.pem', 'utf8'),
        cert: fs.readFileSync('./server.crt', 'utf8')
    }; 
    // Calling setOptions with the x509 certificate and key (and optionally, passphrase) will configure the client //transport to use x509 when connecting to IoT Hub
    client.setOptions(options);
    //call fn to execute after the connection is set up
    client.open(fn);
    ```

## <a id="authn-cred"></a>Använd autentiseringsuppgifter för varje enhet

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | IoT-molnet Gateway  | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gateway - valet i Azure IoT Hub |
| **Referenser**              | [Azure IoT Hub säkerhetstoken](https://azure.microsoft.com/documentation/articles/iot-hub-sas-tokens/) |
| **Steg** | Använd per enhet autentiseringsuppgifter med hjälp av SaS-token baserat på enhetsnyckel eller klientcertifikat, i stället för IoT Hub-nivå delade åtkomstprinciper. Detta förhindrar återanvändning av Autentiseringstoken för en enhet eller fält-gateway av en annan |

## <a id="req-containers-anon"></a>Se till att endast nödvändiga behållare och blobbar ges anonym läsbehörighet

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Storage | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | StorageType - Blob |
| **Referenser**              | [Hantera anonym läsbehörighet till behållare och blobbar](https://azure.microsoft.com/documentation/articles/storage-manage-access-to-resources/), [signaturer för delad åtkomst, del 1: Förstå SAS-modellen](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/) |
| **Steg** | <p>Som standard kan en behållare och alla blobbar i den endast användas av ägaren av storage-konto. Om du vill ge anonyma användare som har läsbehörighet till en behållare och dess blobbar, uppsättning en behörigheter för behållaren att tillåta offentlig åtkomst. Anonyma användare kan läsa blobbar i en offentligt tillgänglig behållare utan att autentisera begäran.</p><p>Behållare ger följande alternativ för att hantera åtkomst till behållare:</p><ul><li>Fullständig offentlig läsbehörighet: behållare och blob-data kan läsas via anonyma begäran. Klienter kan räkna upp blobbar i behållaren via anonym begäran, men det går inte att räkna upp behållare i lagringskontot.</li><li>Offentlig läsbehörighet för blobar bara: Blob-data i den här behållaren kan läsas via anonym begäran, men behållardata är inte tillgänglig. Klienter kan inte räkna upp blobbar i behållaren via anonym begäran</li><li>Ingen offentlig läsbehörighet: behållare och blob-data kan läsas av ägare endast</li></ul><p>Anonym åtkomst är bäst för scenarier där vissa blobbar ska alltid finnas anonym läsåtkomst. För mer detaljerad kontroll skapa en signatur för delad åtkomst, vilket gör det möjligt att delegera begränsad åtkomst med olika behörigheter och under ett angivet tidsintervall. Se till att behållare och blobbar som kan innehålla potentiellt känsliga data, inte ges anonym åtkomst av misstag</p>|

## <a id="limited-access-sas"></a>Bevilja begränsad åtkomst till objekt i Azure storage med SAS eller SAP

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Storage | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte |
| **Referenser**              | [Signaturer för delad åtkomst, del 1: Förstå SAS-modellen](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/), [signaturer för delad åtkomst, del 2: skapa och använda en SAS med Blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-2/), [hur du delegerar åtkomst till objekt i ditt konto med delad Signaturer för åtkomst och lagrade åtkomstprinciper](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_how-to-delegate-access-to-objects-in-your-account-using-shared-access-signatures-and-stored-access-policies) |
| **Steg** | <p>Med hjälp av en signatur för delad åtkomst är (SAS) ett kraftfullt sätt att bevilja begränsad åtkomst till objekt i ett lagringskonto till andra klienter, utan att exponera åtkomstnyckel. Signaturen för delad åtkomst är en URI som omfattar i dess frågeparametrar all information som krävs för autentiserad åtkomst till en lagringsresurs. Om du vill få åtkomst till lagringsresurser med SAS, behöver klienten bara använda SAS till lämplig konstruktor nebo metodu.</p><p>Du kan använda en SAS när du vill ge åtkomst till resurser i ditt storage-konto till en klient som inte är betrott med kontonyckeln. Dina lagringskontonycklar omfattar både primära och sekundära nyckeln, som båda ha administrativ åtkomst till ditt konto och alla resurser i den. Exponera något av dina kontonycklar öppnas ditt konto så att risken för skadliga eller oaktsamhet. Signaturer för delad åtkomst är en säker alternativ som tillåter att andra användare att läsa, skriva och ta bort data i ditt lagringskonto enligt de behörigheter som du har beviljat och utan behov av kontonyckeln.</p><p>Om du har en logisk uppsättning parametrar som liknar varje gång, är Använd en lagras åtkomst princip (SAP) en bättre uppfattning. Eftersom en SAS som härletts från en lagrad åtkomstprincip ger dig möjlighet att återkalla den SAS direkt, är det rutin att alltid använda lagrade åtkomstprinciper när det är möjligt.</p>|