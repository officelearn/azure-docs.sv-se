---
title: Resursskogskoncept för Azure AD-domäntjänster | Microsoft-dokument
description: Lär dig vad en resursskog är i Azure Active Directory Domain Services och hur de gynnar din organisation i hybridmiljö med begränsade alternativ för användarautentisering eller säkerhetsproblem.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: e0e5dde246dbcd5e5cb2e4ae923872a59a539d87
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80476401"
---
# <a name="resource-forest-concepts-and-features-for-azure-active-directory-domain-services"></a>Resursskogskoncept och funktioner för Azure Active Directory Domain Services

Azure Active Directory Domain Services (AD DS) ger en inloggningsupplevelse för äldre, lokala affärsprogram. Användare, grupper och lösenordsh hashar för lokala användare och molnanvändare synkroniseras med Azure AD DS-hanterade domänen. Dessa synkroniserade lösenordstecken är det som ger användarna en enda uppsättning autentiseringsuppgifter som de kan använda för den lokala AD DS, Office 365 och Azure Active Directory.

Även om det är säkert och ger ytterligare säkerhetsfördelar kan vissa organisationer inte synkronisera dessa användarlösenordshar till Azure AD eller Azure AD DS. Användare i en organisation kanske inte känner till sitt lösenord eftersom de bara använder smartkortsautentisering. Dessa begränsningar hindrar vissa organisationer från att använda Azure AD DS för att lyfta och flytta lokala klassiska program till Azure.

För att åtgärda dessa behov och begränsningar kan du skapa en Azure AD DS-hanterad domän som använder en resursskog. I den här begreppsmässiga artikeln beskrivs vilka skogar som är och hur de litar på andra resurser för att tillhandahålla en säker autentiseringsmetod. Azure AD DS-resursskogar är för närvarande i förhandsversion.

> [!IMPORTANT]
> Azure AD DS-resursskogar stöder för närvarande inte Azure HDInsight eller Azure Files. Standardskogarna för Azure AD DS-användare stöder båda dessa ytterligare tjänster.

## <a name="what-are-forests"></a>Vad är skogar?

En *skog* är en logisk konstruktion som används av AD DS (Active Directory Domain Services) för att gruppera en eller flera *domäner*. Domänerna lagrar sedan objekt för användare eller grupper och tillhandahåller autentiseringstjänster.

I Azure AD DS innehåller skogen bara en domän. Lokala AD DS-skogar innehåller ofta många domäner. I stora organisationer, särskilt efter sammanslagningar och förvärv, kan du sluta med flera lokala skogar som var och en sedan innehåller flera domäner.

Som standard skapas en Azure AD DS-hanterad domän som en *användarskog.* Den här typen av skog synkroniserar alla objekt från Azure AD, inklusive alla användarkonton som skapats i en lokal AD DS-miljö. Användarkonton kan autentisera direkt mot azure AD DS-hanterad domän, till exempel för att logga in på en domänansluten virtuell dator. En användarskog fungerar när lösenordshã¤net kan synkroniseras och användarna inte använder exklusiva inloggningsmetoder som smartkortsautentisering.

I en Azure AD *DS-resursskog* autentiserar användare över ett enkelriktat *skogsförtroende* från sin lokala AD DS. Med den här metoden synkroniseras inte användarobjekt och lösenordsharhare till Azure AD DS. The user objects and credentials only exist in the on-premises AD DS. Med den här metoden kan företag vara värd för resurser och programplattformar i Azure som är beroende av klassisk autentisering, till exempel LDAPS, Kerberos eller NTLM, men eventuella autentiseringsproblem eller problem tas bort. Azure AD DS-resursskogar är för närvarande i förhandsversion.

Resursskogar ger också möjlighet att lyfta och flytta dina program en komponent i taget. Många äldre lokala program är flera nivåer, ofta med hjälp av en webbserver eller klientdel och många databasrelaterade komponenter. Dessa nivåer gör det svårt att lyfta och flytta hela programmet till molnet i ett steg. Med resursskogar kan du lyfta ditt program till molnet i stegvis metod, vilket gör det enklare att flytta ditt program till Azure.

## <a name="what-are-trusts"></a>Vad är truster?

Organisationer som har mer än en domän behöver ofta användare för att komma åt delade resurser i en annan domän. Åtkomst till dessa delade resurser kräver att användare i en domän autentiserar till en annan domän. Om du vill tillhandahålla dessa autentiserings- och auktoriseringsfunktioner mellan klienter och servrar i olika domäner måste det finnas ett *förtroende* mellan de två domänerna.

Med domänförtroende litar autentiseringsmekanismerna för varje domän på de autentiseringar som kommer från den andra domänen. Förtroenden hjälper till att ge kontrollerad åtkomst till delade resurser i en resursdomän (den *betrodda* domänen) genom att verifiera att inkommande autentiseringsbegäranden kommer från en betrodd myndighet (den *betrodda* domänen). Förtroenden fungerar som bryggor som endast tillåter validerade autentiseringsbegäranden att resa mellan domäner.

Hur ett förtroende skickar autentiseringsbegäranden beror på hur det konfigureras. Förtroenden kan konfigureras på något av följande sätt:

* **Enkelriktad** – ger åtkomst från den betrodda domänen till resurser i den betrodda domänen.
* **Dubbelriktad** - ger åtkomst från varje domän till resurser i den andra domänen.

Förtroenden är också konfigurerade för att hantera ytterligare förtroenderelationer på något av följande sätt:

* **Icke-transitiv** - Förtroendet finns bara mellan de två förtroendepartnerdomänerna.
* **Transitiv** - Förtroende utökas automatiskt till alla andra domäner som någon av partnerna litar på.

I vissa fall upprättas förtroenderelationer automatiskt när domäner skapas. Andra gånger måste du välja en typ av förtroende och uttryckligen upprätta lämpliga relationer. Vilka typer av förtroenden som används och strukturen för dessa förtroenderelationer beror på hur Active Directory-katalogtjänsten är organiserad och om olika versioner av Windows samexisterar i nätverket.

## <a name="trusts-between-two-forests"></a>Förtroenden mellan två skogar

Du kan utöka domänförtroende inom en enskild skog till en annan skog genom att manuellt skapa ett enkelriktad eller dubbelriktad skogsförtroende. Ett skogsförtroende är ett transitivt förtroende som bara finns mellan en skogsrotdomän och en andra skogsrotdomän.

* Ett envägsskogsförtroende gör att alla användare i en skog kan lita på alla domäner i den andra skogen.
* Ett dubbelriktad skogsförtroende bildar en transitiv förtroenderelation mellan alla domäner i båda skogarna.

Skogsförtroendets transitivitet är begränsad till de två skogspartnerna. Skogsförtroendet sträcker sig inte till ytterligare skogar som någon av partnerna litar på.

![Diagram över skogsförtroende från Azure AD DS till lokalt AD DS](./media/concepts-resource-forest/resource-forest-trust-relationship.png)

Du kan skapa olika domän- och skogsförtroendekonfigurationer beroende på organisationens Active Directory-struktur. Azure AD DS stöder bara ett enkelriktat skogsförtroende. I den här konfigurationen kan resurser i Azure AD DS lita på alla domäner i en lokal skog.

## <a name="supporting-technology-for-trusts"></a>Stödteknik för truster

Förtroenden använder olika tjänster och funktioner, till exempel DNS för att hitta domänkontrollanter i partnerskogar. Förtroenden är också beroende av NTLM- och Kerberos-autentiseringsprotokoll och på Windows-baserade auktoriserings- och åtkomstkontrollmekanismer för att tillhandahålla en säker kommunikationsinfrastruktur över Active Directory-domäner och skogar. Följande tjänster och funktioner hjälper till att stödja framgångsrika förtroenderelationer.

### <a name="dns"></a>DNS

AD DS behöver DNS för domänkontrollantplats (DC) plats och namngivning. Följande stöd från DNS tillhandahålls för att AD DS ska fungera:

* En namnmatchningstjänst som gör att nätverksvärdar och tjänster kan hitta domänkontrollanter.
* En namngivningsstruktur som gör det möjligt för ett företag att återspegla dess organisationsstruktur i namnen på dess katalogtjänstdomäner.

Ett DNS-domännamnområde distribueras vanligtvis som speglar AD DS-domännamnsområdet. Om det finns ett befintligt DNS-namnområde före AD DS-distributionen partitioneras vanligtvis DNS-namnområdet för Active Directory och en DNS-underdomän och delegering för Active Directory-skogsroten skapas. Ytterligare DNS-domännamn läggs sedan till för varje underordnad Active Directory-domän.

DNS används också för att stödja platsen för Active Directory-domänkontrollanter. DNS-zonerna fylls i med DNS-resursposter som gör det möjligt för nätverksvärdar och tjänster att hitta Active Directory-domänkontrollanter.

### <a name="applications-and-net-logon"></a>Applikationer och Net Logon

Både program och Net Logon-tjänsten är komponenter i Windows distribuerade säkerhetskanalmodell. Program som är integrerade med Windows Server och Active Directory använder autentiseringsprotokoll för att kommunicera med tjänsten Net Logon så att en säker sökväg kan upprättas över vilken autentisering kan ske.

### <a name="authentication-protocols"></a>Autentiseringsprotokoll

Active Directory-domänkontrollanter autentiserar användare och program med något av följande protokoll:

* **Autentiseringsprotokoll för Kerberos version 5**
    * Kerberos version 5-protokollet är standardautentiseringsprotokollet som används av lokala datorer som kör Windows och stöder operativsystem från tredje part. Det här protokollet anges i RFC 1510 och är helt integrerat med Active Directory, servermeddelandeblock (SMB), HTTP och RPC (Remote Procedure Call), samt klient- och serverprogram som använder dessa protokoll.
    * När Kerberos-protokollet används behöver servern inte kontakta domänkontrollanten. I stället får klienten en biljett till en server genom att begära en från en domänkontrollant i serverkontodomänen. Servern validerar sedan biljetten utan att rådfråga någon annan myndighet.
    * Om någon dator som ingår i en transaktion inte stöder Kerberos version 5-protokollet används NTLM-protokollet.

* **NTLM-autentiseringsprotokoll**
    * NTLM-protokollet är ett klassiskt nätverksautentiseringsprotokoll som används av äldre operativsystem. Av kompatibilitetsskäl används den av Active Directory-domäner för att bearbeta nätverksautentiseringsbegäranden som kommer från program som utformats för tidigare Windows-baserade klienter och servrar och operativsystem från tredje part.
    * När NTLM-protokollet används mellan en klient och en server måste servern kontakta en domänautentiseringstjänst på en domänkontrollant för att verifiera klientautentiseringsuppgifterna. Servern autentiserar klienten genom att vidarebefordra klientautentiseringsuppgifterna till en domänkontrollant i klientkontodomänen.
    * När två Active Directory-domäner eller skogar är anslutna av ett förtroende kan autentiseringsbegäranden som görs med hjälp av dessa protokoll dirigeras för att ge åtkomst till resurser i båda skogarna.

## <a name="authorization-and-access-control"></a>Auktoriserings- och åtkomstkontroll

Auktoriserings- och förtroendetekniker arbetar tillsammans för att tillhandahålla en säker kommunikationsinfrastruktur över Active Directory-domäner eller skogar. Auktorisering avgör vilken åtkomstnivå en användare har till resurser i en domän. Förtroenden underlättar godkännande över flera domäner för användare genom att tillhandahålla en sökväg för att autentisera användare i andra domäner så att deras begäranden till delade resurser i dessa domäner kan auktoriseras.

När en autentiseringsbegäran som görs i en betrodd domän valideras av den betrodda domänen skickas den till målresursen. Målresursen avgör sedan om den specifika begäran som gjorts av användaren, tjänsten eller datorn i den betrodda domänen ska godkännas.

Förtroenden tillhandahåller den här mekanismen för att validera autentiseringsbegäranden som skickas till en betrodd domän. Åtkomstkontrollmekanismer på resursdatorn bestämmer den slutliga åtkomstnivån som beviljas beställaren i den betrodda domänen.

## <a name="next-steps"></a>Nästa steg

Mer information om förtroenden finns [i Hur fungerar skogsförtroende i Azure AD DS?][concepts-trust]

Information om hur du skapar en Azure AD DS-hanterad domän med en resursskog finns i [Skapa och konfigurera en Azure AD DS-hanterad domän][tutorial-create-advanced]. Du kan sedan [skapa ett utgående skogsförtroende till en lokal domän (förhandsversion).][create-forest-trust]

<!-- LINKS - INTERNAL -->
[concepts-trust]: concepts-forest-trust.md
[tutorial-create-advanced]: tutorial-create-instance-advanced.md
[create-forest-trust]: tutorial-create-forest-trust.md
