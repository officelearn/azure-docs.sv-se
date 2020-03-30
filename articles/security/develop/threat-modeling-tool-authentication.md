---
title: Autentisering – Microsofts verktyg för hotmodellering – Azure | Microsoft-dokument
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
ms.openlocfilehash: 1bef73e6be4bdbe8828e1d20ea6e684759984627
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72244639"
---
# <a name="security-frame-authentication--mitigations"></a>Säkerhetsram: Autentisering | Mitigations 

| Produkt/tjänst | Artikel |
| --------------- | ------- |
| **Webbprogram**    | <ul><li>[Överväg att använda en standardautentiseringsmekanism för att autentisera till webbprogram](#standard-authn-web-app)</li><li>[Program måste hantera misslyckade autentiseringsscenarier på ett säkert sätt](#handle-failed-authn)</li><li>[Aktivera stegupp- eller adaptiv autentisering](#step-up-adaptive-authn)</li><li>[Se till att de administrativa gränssnitten är låsta på rätt sätt](#admin-interface-lockdown)</li><li>[Implementera funktioner för glömt lösenord på ett säkert sätt](#forgot-pword-fxn)</li><li>[Se till att lösenords- och kontoprincipen implementeras](#pword-account-policy)</li><li>[Implementera kontroller för att förhindra uppräkning av användarnamn](#controls-username-enum)</li></ul> |
| **Databas** | <ul><li>[Använd om möjligt Windows-autentisering för anslutning till SQL Server](#win-authn-sql)</li><li>[När det är möjligt används Azure Active Directory-autentisering för anslutning till SQL Database](#aad-authn-sql)</li><li>[När SQL-autentiseringsläge används kontrollerar du att konto- och lösenordsprincipen tillämpas på SQL-servern](#authn-account-pword)</li><li>[Använd inte SQL-autentisering i inneslutna databaser](#autn-contained-db)</li></ul> |
| **Azure Event Hub** | <ul><li>[Använda autentiseringsuppgifter per enhet med SaS-token](#authn-sas-tokens)</li></ul> |
| **Azure-förtroendegräns** | <ul><li>[Aktivera Azure Multi Factor-autentisering för Azure-administratörer](#multi-factor-azure-admin)</li></ul> |
| **Förtroendegräns för serviceinfrastruktur** | <ul><li>[Begränsa anonym åtkomst till Service Fabric Cluster](#anon-access-cluster)</li><li>[Kontrollera att Service Fabric-klient-till-nod-certifikat skiljer sig från nod-till-nodcertifikat](#fabric-cn-nn)</li><li>[Använda AAD för att autentisera klienter för att betjäna kluster](#aad-client-fabric)</li><li>[Kontrollera att serviceinfrastrukturcertifikat erhålls från en godkänd certifikatutfärdare](#fabric-cert-ca)</li></ul> |
| **Identitetsserver** | <ul><li>[Använda standardautentiseringsscenarier som stöds av Identity Server](#standard-authn-id)</li><li>[Åsidosätt standardminnet för Identity Server-token med ett skalbart alternativ](#override-token)</li></ul> |
| **Gräns för datorförtroende** | <ul><li>[Kontrollera att det distribuerade programmets binärfiler är digitalt signerade](#binaries-signed)</li></ul> |
| **WCF** | <ul><li>[Aktivera autentisering vid anslutning till MSMQ-köer i WCF](#msmq-queues)</li><li>[WCF-Ange inte Message clientCredentialType till ingen](#message-none)</li><li>[WCF-Ange inte Transport clientCredentialType till ingen](#transport-none)</li></ul> |
| **Webb-API** | <ul><li>[Kontrollera att standardautentiseringstekniker används för att skydda webb-API:er](#authn-secure-api)</li></ul> |
| **Azure AD** | <ul><li>[Använda standardautentiseringsscenarier som stöds av Azure Active Directory](#authn-aad)</li><li>[Åsidosätt standardcatoettcachen för ADAL med ett skalbart alternativ](#adal-scalable)</li><li>[Se till att TokenReplayCache används för att förhindra att ADAL-autentiseringstoken spelas upp](#tokenreplaycache-adal)</li><li>[Använda ADAL-bibliotek för att hantera tokenbegäranden från OAuth2-klienter till AAD (eller lokal AD)](#adal-oauth2)</li></ul> |
| **IoT-fältgateway** | <ul><li>[Autentisera enheter som ansluter till fältgatewayen](#authn-devices-field)</li></ul> |
| **IoT Cloud Gateway** | <ul><li>[Se till att enheter som ansluter till Cloud gateway autentiseras](#authn-devices-cloud)</li><li>[Använda autentiseringsuppgifter per enhet](#authn-cred)</li></ul> |
| **Azure-lagring** | <ul><li>[Se till att endast de behållare och blobbar som krävs får anonym läsåtkomst](#req-containers-anon)</li><li>[Bevilja begränsad åtkomst till objekt i Azure-lagring med SAS eller SAP](#limited-access-sas)</li></ul> |

## <a name="consider-using-a-standard-authentication-mechanism-to-authenticate-to-web-application"></a><a id="standard-authn-web-app"></a>Överväg att använda en standardautentiseringsmekanism för att autentisera till webbprogram

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| Information | <p>Autentisering är den process där en entitet bevisar sin identitet, vanligtvis genom autentiseringsuppgifter, till exempel ett användarnamn och lösenord. Det finns flera tillgängliga autentiseringsprotokoll som kan övervägas. Några av dem är listade nedan:</p><ul><li>Klientcertifikat</li><li>Windows-baserade</li><li>Formulär baserade</li><li>Federation - ADFS</li><li>Federation - Azure AD</li><li>Federation - Identitetsserver</li></ul><p>Överväg att använda en standardautentiseringsmekanism för att identifiera källprocessen</p>|

## <a name="applications-must-handle-failed-authentication-scenarios-securely"></a><a id="handle-failed-authn"></a>Program måste hantera misslyckade autentiseringsscenarier på ett säkert sätt

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| Information | <p>Program som uttryckligen autentiserar användare måste hantera misslyckade autentiseringsscenarier på ett säkert sätt. Autentiseringsmekanismen måste</p><ul><li>Neka åtkomst till privilegierade resurser när autentiseringen misslyckas</li><li>Visa ett allmänt felmeddelande efter misslyckad autentisering och nekad åtkomst</li></ul><p>Test för:</p><ul><li>Skydd av privilegierade resurser efter misslyckade inloggningar</li><li>Ett allmänt felmeddelande visas vid misslyckad autentisering och åtkomst till nekade händelser</li><li>Konton inaktiveras efter ett alltför stort antal misslyckade försök</li><ul>|

## <a name="enable-step-up-or-adaptive-authentication"></a><a id="step-up-adaptive-authn"></a>Aktivera stegupp- eller adaptiv autentisering

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| Information | <p>Kontrollera att programmet har ytterligare auktorisering (t.ex. steg-upp eller adaptiv autentisering, via multifaktorautentisering som att skicka OTP i SMS, e-post etc. eller uppmanas att återautentisering) så att användaren utmanas innan han beviljas tillgång till känslig information. Den här regeln gäller även för att göra viktiga ändringar i ett konto eller en åtgärd</p><p>Detta innebär också att anpassningen av autentiseringen måste genomföras på ett sådant sätt att programmet korrekt tillämpar kontextkänslig auktorisering för att inte tillåta obehörig manipulering genom exempelvis parametermanipulation</p>|

## <a name="ensure-that-administrative-interfaces-are-appropriately-locked-down"></a><a id="admin-interface-lockdown"></a>Se till att de administrativa gränssnitten är låsta på rätt sätt

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| Information | Den första lösningen är att bevilja åtkomst endast från ett visst käll-IP-intervall till det administrativa gränssnittet. Om den lösningen inte skulle vara möjlig än det rekommenderas alltid att genomdriva en step-up eller adaptiv autentisering för att logga in i det administrativa gränssnittet |

## <a name="implement-forgot-password-functionalities-securely"></a><a id="forgot-pword-fxn"></a>Implementera funktioner för glömt lösenord på ett säkert sätt

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| Information | <p>Det första är att kontrollera att glömt lösenord och andra återställningsvägar skickar en länk inklusive en tidsbegränsad aktiveringstoken i stället för själva lösenordet. Ytterligare autentisering baserad på mjuka tokens (t.ex. SMS-token, inbyggda mobila applikationer, etc.) kan krävas också innan länken skickas över. För det andra bör du inte låsa ut användarkontot medan processen för att få ett nytt lösenord pågår.</p><p>Detta kan leda till en denial of service-attack när en angripare bestämmer sig för att avsiktligt låsa ute användarna med en automatiserad attack. För det tredje, när den nya lösenordsbegäran har angetts pågående, bör meddelandet du visar generaliseras för att förhindra uppräkning av användarnamn. För det fjärde, alltid inte tillåta användning av gamla lösenord och genomföra en stark lösenordsprincip.</p> |

## <a name="ensure-that-password-and-account-policy-are-implemented"></a><a id="pword-account-policy"></a>Se till att lösenords- och kontoprincipen implementeras

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| Information | <p>Lösenords- och kontoprincip i enlighet med organisationspolicy och bästa praxis bör implementeras.</p><p>För att försvara sig mot brute-force och ordliste baserad gissa: Stark lösenordsprincip måste genomföras för att säkerställa att användarna skapar komplexa lösenord (t.ex. 12 tecken minsta längd, alfanumeriska och specialtecken).</p><p>Principer för kontoutelåsning kan implementeras på följande sätt:</p><ul><li>**Mjuk lock-out:** Detta kan vara ett bra alternativ för att skydda dina användare mot brute force-attacker. Till exempel, när användaren anger ett felaktigt lösenord tre gånger ansökan kan låsa kontot i en minut för att bromsa processen för brute tvinga sitt lösenord gör det mindre lönsamt för angriparen att fortsätta. Om du skulle genomföra hårda lock-out motåtgärder för detta exempel skulle du uppnå en "DoS" genom att permanent låsa ut konton. Alternativt kan programmet generera en OTP (One Time Password) och skicka den out-of-band (via e-post, sms etc.) till användaren. Ett annat tillvägagångssätt kan vara att genomföra CAPTCHA efter det att ett tröskelvärde för antal misslyckade försök har nåtts.</li><li>**Hård lockout:** Den här typen av utelåsning bör tillämpas när du upptäcker en användare som attackerar ditt program och motverkar dem genom att permanent låsa ut sitt konto tills en insatsgrupp hann göra sin kriminalteknik. Efter denna process kan du välja att ge användaren tillbaka sitt konto eller vidta ytterligare rättsliga åtgärder mot dem. Den här typen av metod hindrar angriparen från att ytterligare penetrera ditt program och infrastruktur.</li></ul><p>Om du vill skydda dig mot attacker på standardkonton och förutsägbara konton kontrollerar du att alla nycklar och lösenord är utbytbara och genereras eller ersätts efter installationstiden.</p><p>Om programmet måste automatiskt generera lösenord, se till att de genererade lösenorden är slumpmässiga och har hög entropy.</p>|

## <a name="implement-controls-to-prevent-username-enumeration"></a><a id="controls-username-enum"></a>Implementera kontroller för att förhindra uppräkning av användarnamn

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Alla felmeddelanden bör generaliseras för att förhindra uppräkning av användarnamn. Ibland kan du inte heller undvika att information läcker i funktioner som en registreringssida. Här måste du använda hastighetsbegränsande metoder som CAPTCHA för att förhindra en automatiserad attack av en angripare. |

## <a name="when-possible-use-windows-authentication-for-connecting-to-sql-server"></a><a id="win-authn-sql"></a>Använd om möjligt Windows-autentisering för anslutning till SQL Server

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | OnPrem (onprem) |
| **Attribut**              | SQL-version - Alla |
| **Referenser**              | [SQL Server - Välj ett autentiseringsläge](https://msdn.microsoft.com/library/ms144284.aspx) |
| **Steg** | Windows-autentisering använder Kerberos säkerhetsprotokoll, ger tvingande lösenordsprincip när det gäller komplexitetsvalidering för starka lösenord, ger stöd för kontoutelåsning och stöder lösenordsförfallodatum.|

## <a name="when-possible-use-azure-active-directory-authentication-for-connecting-to-sql-database"></a><a id="aad-authn-sql"></a>När det är möjligt används Azure Active Directory-autentisering för anslutning till SQL Database

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | SQL Azure |
| **Attribut**              | SQL-version - V12 |
| **Referenser**              | [Ansluta till SQL Database med hjälp av Azure Active Directory-autentisering](https://azure.microsoft.com/documentation/articles/sql-database-aad-authentication/) |
| **Steg** | **Minsta version:** Azure SQL Database V12 krävs för att Azure SQL Database ska kunna använda AAD-autentisering mot Microsoft Directory |

## <a name="when-sql-authentication-mode-is-used-ensure-that-account-and-password-policy-are-enforced-on-sql-server"></a><a id="authn-account-pword"></a>När SQL-autentiseringsläge används kontrollerar du att konto- och lösenordsprincipen tillämpas på SQL-servern

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Lösenordsprincip för SQL Server](https://technet.microsoft.com/library/ms161959(v=sql.110).aspx) |
| **Steg** | När DU använder SQL Server-autentisering skapas inloggningar i SQL Server som inte baseras på Windows-användarkonton. Både användarnamnet och lösenordet skapas med hjälp av SQL Server och lagras i SQL Server. SQL Server kan använda Windows lösenordsprincipmekanismer. Den kan tillämpa samma komplexitets- och förfalloprinciper som används i Windows på lösenord som används i SQL Server. |

## <a name="do-not-use-sql-authentication-in-contained-databases"></a><a id="autn-contained-db"></a>Använd inte SQL-autentisering i inneslutna databaser

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | OnPrem, SQL Azure |
| **Attribut**              | SQL-version - MSSQL2012, SQL-version - V12 |
| **Referenser**              | [Metodtips för säkerhet med inneslutna databaser](https://msdn.microsoft.com/library/ff929055.aspx) |
| **Steg** | Frånvaron av en tvingande lösenordsprincip kan öka sannolikheten för att en svag autentiseringsdokument upprättas i en innesluten databas. Utnyttja Windows-autentisering. |

## <a name="use-per-device-authentication-credentials-using-sas-tokens"></a><a id="authn-sas-tokens"></a>Använda autentiseringsuppgifter per enhet med SaS-token

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Event Hub | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Översikt över autentisering och säkerhetsmodell för eventhubbar](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Steg** | <p>Säkerhetsmodellen Event Hubs baseras på en kombination av SAS-token (Shared Access Signature) och händelseutgivare. Utgivarens namn representerar DeviceID som tar emot token. Detta skulle hjälpa associera token som genereras med respektive enheter.</p><p>Alla meddelanden är märkta med upphovsmannen på tjänstsidan som gör det möjligt att upptäcka förfalskningsförsök i nyttolast. När du autentiserar enheter genererar du en SaS-token per enhet som omfattas av en unik utgivare.</p>|

## <a name="enable-azure-multi-factor-authentication-for-azure-administrators"></a><a id="multi-factor-azure-admin"></a>Aktivera Azure Multi Factor-autentisering för Azure-administratörer

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure-förtroendegräns | 
| **SDL-fas**               | Distribution |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Vad är Azure Multi-Factor Authentication?](https://azure.microsoft.com/documentation/articles/multi-factor-authentication/) |
| **Steg** | <p>MFA (Multifaktor authentication) är en autentiseringsmetod som kräver mer än en verifieringsmetod och lägger till ett kritiskt andra säkerhetslager i användarloggningar och transaktioner. Det fungerar genom att kräva två eller flera av följande verifieringsmetoder:</p><ul><li>Något du vet (vanligtvis ett lösenord)</li><li>Något du har (en betrodd enhet som inte är lätt att duplicera, som en telefon)</li><li>Något du är (biometri)</li><ul>|

## <a name="restrict-anonymous-access-to-service-fabric-cluster"></a><a id="anon-access-cluster"></a>Begränsa anonym åtkomst till Service Fabric Cluster

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Förtroendegräns för serviceinfrastruktur | 
| **SDL-fas**               | Distribution |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Miljö - Azure  |
| **Referenser**              | [Säkerhetsscenarier för service fabric-kluster](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security) |
| **Steg** | <p>Kluster bör alltid skyddas för att förhindra att obehöriga användare ansluter till klustret, särskilt när det körs produktionsarbetsbelastningar på det.</p><p>När du skapar ett service fabric-kluster kontrollerar du att säkerhetsläget är inställt på "säker" och konfigurerar det X.509-servercertifikat som krävs. Om du skapar ett "osäkert" kluster kan alla anonyma användare ansluta till det om hanteringsslutpunkter exponeras för det offentliga Internet.</p>|

## <a name="ensure-that-service-fabric-client-to-node-certificate-is-different-from-node-to-node-certificate"></a><a id="fabric-cn-nn"></a>Kontrollera att Service Fabric-klient-till-nod-certifikat skiljer sig från nod-till-nodcertifikat

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Förtroendegräns för serviceinfrastruktur | 
| **SDL-fas**               | Distribution |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Miljö - Azure, Miljö - Fristående |
| **Referenser**              | [Service Fabric-certifikatsäkerhet för klient till nod](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/#_client-to-node-certificate-security), [Anslut till ett säkert kluster med klientcertifikat](https://azure.microsoft.com/documentation/articles/service-fabric-connect-to-secure-cluster/) |
| **Steg** | <p>Klient-till-nodcertifikatsäkerhet konfigureras när klustret skapas antingen via Azure-portalen, Resource Manager-mallar eller en fristående JSON-mall genom att ange ett administratörsklientcertifikat och/eller ett användarklientcertifikat.</p><p>De administratörsklient- och användarklientcertifikat som du anger bör skilja sig från de primära och sekundära certifikat som du anger för nod-till-nodsäkerhet.</p>|

## <a name="use-aad-to-authenticate-clients-to-service-fabric-clusters"></a><a id="aad-client-fabric"></a>Använda AAD för att autentisera klienter för att betjäna kluster

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Förtroendegräns för serviceinfrastruktur | 
| **SDL-fas**               | Distribution |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Miljö - Azure |
| **Referenser**              | [Klustersäkerhetsscenarier – säkerhetsrekommendationer](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/#security-recommendations) |
| **Steg** | Kluster som körs på Azure kan också skydda åtkomsten till hanteringsslutpunkterna med Azure Active Directory (AAD), förutom klientcertifikat. För Azure-kluster rekommenderar vi att du använder AAD-säkerhet för att autentisera klienter och certifikat för nod-till-nodsäkerhet.|

## <a name="ensure-that-service-fabric-certificates-are-obtained-from-an-approved-certificate-authority-ca"></a><a id="fabric-cert-ca"></a>Kontrollera att serviceinfrastrukturcertifikat erhålls från en godkänd certifikatutfärdare

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Förtroendegräns för serviceinfrastruktur | 
| **SDL-fas**               | Distribution |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Miljö - Azure |
| **Referenser**              | [X.509 certifikat och serviceinfrastruktur](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/#x509-certificates-and-service-fabric) |
| **Steg** | <p>Service Fabric använder X.509-servercertifikat för att autentisera noder och klienter.</p><p>Några viktiga saker att tänka på när du använder certifikat i servicetyger:</p><ul><li>Certifikat som används i kluster som kör produktionsarbetsbelastningar bör skapas med hjälp av en korrekt konfigurerad Windows Server-certifikattjänst eller som hämtats från en godkänd certifikatutfärdare. Certifikatutfärdaren kan vara en godkänd extern certifikatutfärdar eller en korrekt hanterad intern infrastruktur för offentliga nycklar (PKI)</li><li>Använd aldrig tillfälliga certifikat eller testcertifikat i produktion som skapas med verktyg som MakeCert.exe</li><li>Du kan använda ett självsignerat certifikat, men bör bara göra det för testkluster och inte i produktion</li></ul>|

## <a name="use-standard-authentication-scenarios-supported-by-identity-server"></a><a id="standard-authn-id"></a>Använda standardautentiseringsscenarier som stöds av Identity Server

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Identitetsserver | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [IdentityServer3 - Den stora bilden](https://identityserver.github.io/Documentation/docsv2/overview/bigPicture.html) |
| **Steg** | <p>Nedan visas de typiska interaktioner som stöds av Identity Server:</p><ul><li>Webbläsare kommunicerar med webbapplikationer</li><li>Webbprogram kommunicerar med webb-API:er (ibland på egen hand, ibland för en användares räkning)</li><li>Webbläsarbaserade program kommunicerar med webb-API:er</li><li>Inbyggda program kommunicerar med webb-API:er</li><li>Serverbaserade program kommunicerar med webb-API:er</li><li>Webb-API:er kommunicerar med webb-API:er (ibland på egen hand, ibland för en användares räkning)</li></ul>|

## <a name="override-the-default-identity-server-token-cache-with-a-scalable-alternative"></a><a id="override-token"></a>Åsidosätt standardminnet för Identity Server-token med ett skalbart alternativ

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Identitetsserver | 
| **SDL-fas**               | Distribution |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Identitetsserverdistribution - Cachelagring](https://identityserver.github.io/Documentation/docsv2/advanced/deployment.html) |
| **Steg** | <p>IdentityServer har en enkel inbyggd cache i minnet. Även om detta är bra för småskaliga inbyggda appar skalas det inte för mellannivå- och backend-program av följande skäl:</p><ul><li>Dessa program nås av många användare samtidigt. Spara alla åtkomsttoken i samma butik skapar isoleringsproblem och innebär utmaningar när de arbetar i stor skala: många användare, var och en med lika många tokens som de resurser som appen har åtkomst till för deras räkning, kan innebära stort antal och mycket dyra uppslagsåtgärder</li><li>Dessa program distribueras vanligtvis på distribuerade topologier, där flera noder måste ha åtkomst till samma cache</li><li>Cachelagrade token måste överleva processåtervinningar och avaktiveringar</li><li>Av alla ovanstående skäl, när du implementerar webbappar, rekommenderas att åsidosätta standardidentitetsserverns tokencache med ett skalbart alternativ som Azure Cache för Redis</li></ul>|

## <a name="ensure-that-deployed-applications-binaries-are-digitally-signed"></a><a id="binaries-signed"></a>Kontrollera att det distribuerade programmets binärfiler är digitalt signerade

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Gräns för datorförtroende | 
| **SDL-fas**               | Distribution |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Kontrollera att det distribuerade programmets binärfiler är digitalt signerade så att binärornas integritet kan verifieras|

## <a name="enable-authentication-when-connecting-to-msmq-queues-in-wcf"></a><a id="msmq-queues"></a>Aktivera autentisering vid anslutning till MSMQ-köer i WCF

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | WCF | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänt, NET Framework 3 |
| **Attribut**              | Ej tillämpligt |
| **Referenser**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx) |
| **Steg** | Programmet misslyckas med att aktivera autentisering vid anslutning till MSMQ-köer, en angripare kan anonymt skicka meddelanden till kön för bearbetning. Om autentisering inte används för att ansluta till en MSMQ-kö som används för att leverera ett meddelande till ett annat program kan en angripare skicka ett anonymt meddelande som är skadligt.|

### <a name="example"></a>Exempel
Elementet `<netMsmqBinding/>` i WCF-konfigurationsfilen nedan instruerar WCF att inaktivera autentisering när du ansluter till en MSMQ-kö för meddelandeleverans.
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
Konfigurera MSMQ så att den alltid kräver Windows-domän- eller certifikatautentisering för inkommande eller utgående meddelanden.

### <a name="example"></a>Exempel
Elementet `<netMsmqBinding/>` i WCF-konfigurationsfilen nedan instruerar WCF att aktivera certifikatautentisering vid anslutning till en MSMQ-kö. Klienten autentiseras med X.509-certifikat. Klientcertifikatet måste finnas i serverns certifikatarkiv.
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

## <a name="wcf-do-not-set-message-clientcredentialtype-to-none"></a><a id="message-none"></a>WCF-Ange inte Message clientCredentialType till ingen

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | WCF | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | .NET-ramverk 3 |
| **Attribut**              | Klientautentiseringstyp - Ingen |
| **Referenser**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Stärka](https://community.microfocus.com/t5/UFT-Discussions/UFT-API-Test-with-WCF-wsHttpBinding/m-p/600927) |
| **Steg** | Frånvaron av autentisering innebär att alla kan komma åt den här tjänsten. En tjänst som inte autentiserar sina klienter ger åtkomst till alla användare. Konfigurera programmet så att det autentiseras mot klientautentiseringsuppgifter. Detta kan göras genom att ange meddelandeklientcredentialType till Windows eller Certifikat. |

### <a name="example"></a>Exempel
```
<message clientCredentialType=""Certificate""/>
```

## <a name="wcf-do-not-set-transport-clientcredentialtype-to-none"></a><a id="transport-none"></a>WCF-Ange inte Transport clientCredentialType till ingen

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | WCF | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänt, .NET Framework 3 |
| **Attribut**              | Klientautentiseringstyp - Ingen |
| **Referenser**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Stärka](https://community.microfocus.com/t5/UFT-Discussions/UFT-API-Test-with-WCF-wsHttpBinding/m-p/600927) |
| **Steg** | Frånvaron av autentisering innebär att alla kan komma åt den här tjänsten. En tjänst som inte autentiserar sina klienter gör det möjligt för alla användare att komma åt dess funktioner. Konfigurera programmet så att det autentiseras mot klientautentiseringsuppgifter. Detta kan göras genom att ställa in transportklientenCredentialType till Windows eller Certifikat. |

### <a name="example"></a>Exempel
```
<transport clientCredentialType=""Certificate""/>
```

## <a name="ensure-that-standard-authentication-techniques-are-used-to-secure-web-apis"></a><a id="authn-secure-api"></a>Kontrollera att standardautentiseringstekniker används för att skydda webb-API:er

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webb-API | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Autentisering och auktorisering i ASP.NET webb-API](https://www.asp.net/web-api/overview/security/authentication-and-authorization-in-aspnet-web-api), [externa autentiseringstjänster med ASP.NET webb-API (C#)](https://www.asp.net/web-api/overview/security/external-authentication-services) |
| **Steg** | <p>Autentisering är den process där en entitet bevisar sin identitet, vanligtvis genom autentiseringsuppgifter, till exempel ett användarnamn och lösenord. Det finns flera tillgängliga autentiseringsprotokoll som kan övervägas. Några av dem är listade nedan:</p><ul><li>Klientcertifikat</li><li>Windows-baserade</li><li>Formulär baserade</li><li>Federation - ADFS</li><li>Federation - Azure AD</li><li>Federation - Identitetsserver</li></ul><p>Länkar i referensavsnittet innehåller information på låg nivå om hur vart och ett av autentiseringsschemana kan implementeras för att skydda ett webb-API.</p>|

## <a name="use-standard-authentication-scenarios-supported-by-azure-active-directory"></a><a id="authn-aad"></a>Använda standardautentiseringsscenarier som stöds av Azure Active Directory

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure AD | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Autentiseringsscenarier för Azure AD,](https://azure.microsoft.com/documentation/articles/active-directory-authentication-scenarios/) [Azure Active Directory-kodexempel,](https://azure.microsoft.com/documentation/articles/active-directory-code-samples/) [Azure Active Directory-utvecklarguide](https://azure.microsoft.com/documentation/articles/active-directory-developers-guide/) |
| **Steg** | <p>Azure Active Directory (Azure AD) förenklar autentiseringen för utvecklare genom att tillhandahålla identitet som en tjänst, med stöd för branschstandardprotokoll som OAuth 2.0 och OpenID Connect. Nedan följer de fem primära programscenarierna som stöds av Azure AD:</p><ul><li>Webbläsare till webbprogram: En användare måste logga in på ett webbprogram som skyddas av Azure AD</li><li>SPA (Single Page Application): En användare måste logga in på ett program med en sida som skyddas av Azure AD</li><li>Native Application to Web API: Ett inbyggt program som körs på en telefon, surfplatta eller dator måste autentisera en användare för att hämta resurser från ett webb-API som skyddas av Azure AD</li><li>Webbprogram till webb-API: Ett webbprogram måste hämta resurser från ett webb-API som skyddas av Azure AD</li><li>Daemon eller Serverprogram till webb-API: Ett daemonprogram eller ett serverprogram utan webbanvändargränssnitt behöver hämta resurser från ett webb-API som skyddas av Azure AD</li></ul><p>Se länkarna i referensavsnittet för information om implementering på låg nivå</p>|

## <a name="override-the-default-adal-token-cache-with-a-scalable-alternative"></a><a id="adal-scalable"></a>Åsidosätt standardcatoettcachen för ADAL med ett skalbart alternativ

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure AD | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Modern autentisering med Azure Active Directory för webbprogram](https://blogs.msdn.microsoft.com/microsoft_press/2016/01/04/new-book-modern-authentication-with-azure-active-directory-for-web-applications/), [Använda Redis som ADAL tokencache](https://blogs.msdn.microsoft.com/mrochon/2016/09/19/using-redis-as-adal-token-cache/)  |
| **Steg** | <p>Standardcachen som ADAL (Active Directory Authentication Library) använder är en minnesminnescache som är beroende av en statisk lagring, tillgänglig processomfattande. Även om detta fungerar för inbyggda program skalas det inte för mellannivå- och backend-program av följande skäl:</p><ul><li>Dessa program nås av många användare samtidigt. Spara alla åtkomsttoken i samma butik skapar isoleringsproblem och innebär utmaningar när de arbetar i stor skala: många användare, var och en med lika många tokens som de resurser som appen har åtkomst till för deras räkning, kan innebära stort antal och mycket dyra uppslagsåtgärder</li><li>Dessa program distribueras vanligtvis på distribuerade topologier, där flera noder måste ha åtkomst till samma cache</li><li>Cachelagrade token måste överleva processåtervinningar och avaktiveringar</li></ul><p>Av alla ovanstående skäl, när du implementerar webbappar, rekommenderas att åsidosätta standardcatoaken ADAL-tokencachen med ett skalbart alternativ som Azure Cache för Redis.</p>|

## <a name="ensure-that-tokenreplaycache-is-used-to-prevent-the-replay-of-adal-authentication-tokens"></a><a id="tokenreplaycache-adal"></a>Se till att TokenReplayCache används för att förhindra att ADAL-autentiseringstoken spelas upp

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure AD | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Modern autentisering med Azure Active Directory för webbprogram](https://blogs.msdn.microsoft.com/microsoft_press/2016/01/04/new-book-modern-authentication-with-azure-active-directory-for-web-applications/) |
| **Steg** | <p>Egenskapen TokenReplayCache tillåter utvecklare att definiera en token repris cache, en butik som kan användas för att spara token för att kontrollera att ingen token kan användas mer än en gång.</p><p>Detta är en åtgärd mot en gemensam attack, den passande kallas token repris attack: en angripare fånga token skickas vid inloggning kan försöka skicka den till appen igen ("repris" det) för att upprätta en ny session. T.ex. i OIDC-kod-bidrag flöde, efter lyckad användarautentisering, en begäran till "/signin-oidc" slutpunkt för den förlitande parten görs med "id_token", "kod" och "tillstånd" parametrar.</p><p>Den förlitande parten validerar den här begäran och upprättar en ny session. Om en motståndare fångar denna begäran och spelar upp den igen, kan han/hon upprätta en lyckad session och förfalska användaren. Förekomsten av nonce i OpenID Connect kan begränsa men inte helt eliminera de omständigheter under vilka attacken kan antas. För att skydda sina program kan utvecklare tillhandahålla en implementering av ITokenReplayCache och tilldela en instans till TokenReplayCache.</p>|

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
Här är ett exempel på implementering av ITokenReplayCache-gränssnittet. (Anpassa och implementera ditt projektspecifika cachelagringsramverk)
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
Den implementerade cachen måste refereras i OIDC-alternativ via egenskapen "TokenValidationParameters" enligt följande.
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

Observera att för att testa effektiviteten av denna konfiguration, logga in på din lokala `"/signin-oidc"` OIDC-skyddade program och fånga begäran till slutpunkten i spelman. När skyddet inte är på plats, kommer att spela upp denna begäran i spelman en ny sessionscookie. När begäran spelas upp igen efter att TokenReplayCache-skyddet har lagts till, kommer programmet att utlösa ett undantag enligt följande:`SecurityTokenReplayDetectedException: IDX10228: The securityToken has previously been validated, securityToken: 'eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ1......`

## <a name="use-adal-libraries-to-manage-token-requests-from-oauth2-clients-to-aad-or-on-premises-ad"></a><a id="adal-oauth2"></a>Använda ADAL-bibliotek för att hantera tokenbegäranden från OAuth2-klienter till AAD (eller lokal AD)

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure AD | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [ADAL](https://azure.microsoft.com/documentation/articles/active-directory-authentication-libraries/) |
| **Steg** | <p>Azure AD-autentiseringsbiblioteket (ADAL) gör det möjligt för klientprogramutvecklare att enkelt autentisera användare till molnet eller lokala Active Directory (AD) och sedan hämta åtkomsttoken för att skydda API-anrop.</p><p>ADAL har många funktioner som gör autentiseringen enklare för utvecklare, till exempel asynkront support, en konfigurerbar tokencache som lagrar åtkomsttoken och uppdaterar token, automatisk tokenuppdatering när en åtkomsttoken upphör att gälla och en uppdateringstoken är tillgänglig, och Mer.</p><p>Genom att hantera det mesta av komplexiteten kan ADAL hjälpa en utvecklare att fokusera på affärslogik i sitt program och enkelt säkra resurser utan att vara expert på säkerhet. Separata bibliotek är tillgängliga för .NET, JavaScript (klient och node.js), Python, iOS, Android och Java.</p>|

## <a name="authenticate-devices-connecting-to-the-field-gateway"></a><a id="authn-devices-field"></a>Autentisera enheter som ansluter till fältgatewayen

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | IoT-fältgateway | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Se till att varje enhet autentiseras av Fältgatewayen innan du accepterar data från dem och innan du underlättar kommunikation uppströms med Cloud Gateway. Se också till att enheter ansluter till en autentiseringsuppgifter per enhet så att enskilda enheter kan identifieras unikt.|

## <a name="ensure-that-devices-connecting-to-cloud-gateway-are-authenticated"></a><a id="authn-devices-cloud"></a>Se till att enheter som ansluter till Cloud gateway autentiseras

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | IoT Cloud Gateway | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Generisk, C#, Node.JS,  |
| **Attribut**              | N/A, gateway-val - Azure IoT Hub |
| **Referenser**              | Saknas/A, [Azure IoT-hubb med .NET](https://azure.microsoft.com/documentation/articles/iot-hub-csharp-csharp-getstarted/), [Komma igång med IoT-hubb och nod JS](https://azure.microsoft.com/documentation/articles/iot-hub-node-node-getstarted), [Skydda IoT med SAS och certifikat](https://azure.microsoft.com/documentation/articles/iot-hub-sas-tokens/), [Git-databas](https://github.com/Azure/azure-iot-sdks/tree/master/node) |
| **Steg** | <ul><li>**Generisk:** Autentisera enheten med Hjälp av Transport Layer Security (TLS) eller IPSec. Infrastruktur bör stödja användning av fördelad nyckel (PSK) på de enheter som inte kan hantera fullständig asymmetrisk kryptografi. Utnyttja Azure AD, Oauth.</li><li>**C#:** När du skapar en DeviceClient-instans skapas som standard en DeviceClient-instans som använder AMQP-protokollet för att kommunicera med IoT Hub. Om du vill använda HTTPS-protokollet använder du åsidosättningen av Create-metoden som gör att du kan ange protokollet. Om du använder HTTPS-protokollet bör `Microsoft.AspNet.WebApi.Client` du också lägga till NuGet-paketet i projektet för att inkludera `System.Net.Http.Formatting` namnområdet.</li></ul>|

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
**Nod.JS: Autentisering**
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
* Blir internt genereras när du använder symmetrisk nyckel men vi kan generera och använda den uttryckligen samt
* Definiera ett protokoll:`var Http = require('azure-iot-device-http').Http;`
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
* Generera ett självsignerat X509-certifikat med ett verktyg som OpenSSL för att generera en .cert- och .key-filer för att lagra certifikatet respektive nyckeln
* Etablera en enhet som accepterar säker anslutning med certifikat.
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
* Ansluta en enhet med ett certifikat
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

## <a name="use-per-device-authentication-credentials"></a><a id="authn-cred"></a>Använda autentiseringsuppgifter per enhet

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | IoT Cloud Gateway  | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Gateway val - Azure IoT Hub |
| **Referenser**              | [Azure IoT Hub-säkerhetstoken](https://azure.microsoft.com/documentation/articles/iot-hub-sas-tokens/) |
| **Steg** | Använd autentiseringsuppgifter per enhet med SaS-token baserat på enhetsnyckel eller klientcertifikat, i stället för principer för delad åtkomst på IoT-hubbnivå. Detta förhindrar återanvändning av autentiseringstoken för en enhet eller fältgateway av en annan |

## <a name="ensure-that-only-the-required-containers-and-blobs-are-given-anonymous-read-access"></a><a id="req-containers-anon"></a>Se till att endast de behållare och blobbar som krävs får anonym läsåtkomst

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Storage | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | StorageType - Blob |
| **Referenser**              | [Hantera anonym läsåtkomst till behållare och blobbar,](https://azure.microsoft.com/documentation/articles/storage-manage-access-to-resources/) [Signaturer för delad åtkomst, del 1: Förstå SAS-modellen](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/) |
| **Steg** | <p>Som standard kan en behållare och eventuella blobbar i den endast nås av ägaren till lagringskontot. Om du vill ge anonyma användare läsbehörighet till en behållare och dess blobbar kan man ange behållarbehörigheter för att tillåta offentlig åtkomst. Anonyma användare kan läsa blobbar i en allmänt tillgänglig behållare utan att autentisera begäran.</p><p>Behållare innehåller följande alternativ för hantering av containeråtkomst:</p><ul><li>Fullständig offentlig läsåtkomst: Behållar- och blob-data kan läsas via anonym begäran. Klienter kan räkna upp blobbar i behållaren via anonym begäran, men kan inte räkna upp behållare i lagringskontot.</li><li>Offentlig läsåtkomst endast för blobbar: Blob-data i den här behållaren kan läsas via anonym begäran, men behållardata är inte tillgängliga. Klienter kan inte räkna upp blobbar i behållaren via anonym begäran</li><li>Ingen offentlig läsåtkomst: Behållar- och blob-data kan endast läsas av kontoägaren</li></ul><p>Anonym åtkomst är bäst för scenarier där vissa blobbar alltid ska vara tillgängliga för anonym läsåtkomst. För finarekornig kontroll kan man skapa en signatur för delad åtkomst, som gör det möjligt att delegera begränsad åtkomst med hjälp av olika behörigheter och över ett angivet tidsintervall. Se till att behållare och blobbar, som eventuellt kan innehålla känsliga data, inte får anonym åtkomst av misstag</p>|

## <a name="grant-limited-access-to-objects-in-azure-storage-using-sas-or-sap"></a><a id="limited-access-sas"></a>Bevilja begränsad åtkomst till objekt i Azure-lagring med SAS eller SAP

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Storage | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt |
| **Referenser**              | [Signaturer för delad åtkomst, del 1: Förstå SAS-modellen,](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/) [Signaturer för delad åtkomst, del 2: Skapa och använda en SAS med Blob-lagring,](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-2/) [Så här delegerar du åtkomst till objekt i ditt konto med signaturer för delad åtkomst och principer för lagrad åtkomst](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_how-to-delegate-access-to-objects-in-your-account-using-shared-access-signatures-and-stored-access-policies) |
| **Steg** | <p>Att använda en SAS (Shared Access Signature) är ett kraftfullt sätt att bevilja begränsad åtkomst till objekt i ett lagringskonto till andra klienter, utan att behöva exponera kontoåtkomstnyckeln. SAS är en URI som i sina frågeparametrar omfattar all information som krävs för autentiserat åtkomst till en lagringsresurs. För att komma åt lagringsresurser med SAS behöver klienten bara skicka i SAS till lämplig konstruktor eller metod.</p><p>Du kan använda en SAS när du vill ge åtkomst till resurser i ditt lagringskonto till en klient som inte kan lita på kontonyckeln. Dina lagringskontonycklar innehåller både en primär och sekundär nyckel, som båda ger administrativ åtkomst till ditt konto och alla resurser i det. Om du exponerar någon av dina kontonycklar öppnas ditt konto för risken för skadlig eller vårdslös användning. Signaturer för delad åtkomst är ett säkert alternativ som gör att andra klienter kan läsa, skriva och ta bort data i ditt lagringskonto enligt de behörigheter du har beviljat och utan att behöva för kontonyckeln.</p><p>Om du har en logisk uppsättning parametrar som liknar varandra varje gång är det bättre att använda en SAP (Stored Access Policy). Eftersom användning av en SAS som härleds från en princip för lagrad åtkomst ger dig möjlighet att återkalla det SAS omedelbart, är det den rekommenderade bästa metoden att alltid använda principer för lagrad åtkomst när det är möjligt.</p>|
