---
title: Behörigheter i Azure AD | Microsoft Docs
description: Lär dig mer om omfång och behörigheter i Azure Active Directory och hur du använder dem
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 6c0dc122-2cd8-4d70-be5a-3943459d308e
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 04/20/2017
ms.author: celested
ms.reviewer: justhu
ms.custom: aaddev
ms.openlocfilehash: 749253d6a082bcdc2b80c5984f20c4b8c4039ad0
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2018
---
# <a name="permissions-in-azure-ad"></a>Behörigheter i Azure AD
Azure Active Directory (Azure AD) använder sig av behörigheter för både OAuth-flöden och OIDC-flöden (OpenID Connect). När appen tar emot en åtkomsttoken från Azure AD, innehåller den anspråk som beskriver de behörigheter (även kallat omfång) som appen har för en viss resurs. Detta gör auktoriseringen enkel för resursen, eftersom den endast behöver kontrollera att din token innehåller lämplig behörighet för den API som du anropar. 

## <a name="types-of-permissions"></a>Typer av behörigheter
Azure AD definierar två typer av behörigheter: 
* **Delegerade behörigheter** – Används av appar som har en inloggad användare. För dessa appar godkänner antingen användaren eller administratören de behörigheter som appen begär, och appen delegeras behörighet att agera som den inloggade användaren när en API anropas. Beroende på API kan användaren kanske inte godkänna API:n direkt. Det kan i stället [krävas att en administratör ger sitt ”administratörsmedgivande”.](/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent)
* **Programbehörigheter** – Används av appar som körs utan någon inloggad användare, t.ex, appar som körs som en bakgrundstjänst eller daemon. Programbehörigheter kan bara [godkännas av en administratör](/azure/active-directory/develop/active-directory-v2-scopes#requesting-consent-for-an-entire-tenant) eftersom de vanligtvis är mycket kraftfulla och ger åtkomst till data över användarens gränser, eller data som annars skulle vara begränsad till administratörer. 

Gällande behörigheter är de behörigheter som din app har vid begäranden till en API. 

* För delegerade behörigheter blir de behörigheter som gäller för din app lägsta möjliga skärningspunkt för de delegerade behörigheter som appen har beviljats (via godkännande) och de behörigheter som den för tillfället inloggade användaren har. Din app kan aldrig ha fler behörigheter än den inloggade användaren. Inom organisationer kan behörigheter för den inloggade användaren fastställas med en princip eller av medlemskap i en eller flera administratörsroller. Mer information om administratörsroller finns i [Tilldela administratörsroller i Azure AD](/azure/active-directory/active-directory-assign-admin-roles-azure-portal.md).
    Anta exempelvis att din app har beviljats den delegerade behörigheten `User.ReadWrite.All` i Microsoft Graph. Den här behörigheten ger i princip din app behörighet att läsa och uppdatera profilen för alla användare i en organisation. Om den inloggade användaren är en global administratör, kommer din app att kunna uppdatera profilen för alla användare i organisationen. Men om den inloggade användaren inte har någon administratörsroll, kommer appen endast kunna uppdatera profilen för den inloggade användaren. Den kommer inte att kunna uppdatera profilerna för andra användare i organisationen, eftersom den användare som den har behörighet att agera på uppdrag åt inte har den behörigheten.
* För programbehörigheter är de gällande behörigheterna för din app den fullständiga behörighetsnivå som behörigheten ger. En app som exempelvis har programbehörigheten `User.ReadWrite.All` kan uppdatera profilen för alla användare i organisationen. 

## <a name="permission-attributes"></a>Behörighetsattribut
Behörigheter i Azure AD har ett antal egenskaper som hjälper användare, administratörer eller apputvecklare att fatta välgrundade beslut om vad behörigheten ger åtkomst till. 

> [!NOTE]
> Du kan se de behörigheter som ett Azure AD-program eller ett huvudnamn för tjänsten har med Azure Portal eller PowerShell. Testa det här skriptet för att se de behörigheter som görs tillgängliga av Microsoft Graph.
> ```powershell
> Connect-AzureAD
> 
> # Get OAuth2 Permissions/delegated permissions
> (Get-AzureADServicePrincipal -filter "DisplayName eq 'Microsoft Graph'").OAuth2Permissions
> 
> # Get App roles/application permissions
> (Get-AzureADServicePrincipal -filter "DisplayName eq 'Microsoft Graph'").AppRoles
> ```

| Egenskapsnamn | Beskrivning | Exempel | 
| --- | --- | --- |
| ID | Detta är ett GUID-värde som ger en unik identifiering av behörigheten. | 570282fd-fa5c-430d-a7fd-fc8dc98a9dca | 
| IsEnabled | Anger om det här omfånget är tillgängligt för användning. | true | 
| Typ | Anger om den här behörigheten kräver ett godkännande av användaren eller administratören. | Användare | 
| AdminConsentDescription | Detta är en beskrivning som visas för administratörer vid administratörsmedgivanden | Tillåter att appen läser e-post i användarnas postlådor. | 
| AdminConsentDisplayName | Detta är ett eget namn som visas för administratörer vid administratörsmedgivanden. | Läsa användarnas e-post | 
| UserConsentDescription | Detta är en beskrivning som visas för användarna vid ett användargodkännande. |  Tillåter att appen läser e-post i din postlåda. | 
| UserConsentDisplayName | Detta är ett eget namn som visas för användare vid ett användargodkännande. | Läsa din e-post | 
| Värde | Det här är den sträng som används för att identifiera behörigheten vid OAuth 2.0-auktorisering av flöden. Detta kan även kombineras med app-ID:ts URI-sträng för att bilda ett fullständigt behörighetsnamn. | `Mail.Read` | 

## <a name="types-of-consent"></a>Typer av godkännande
Program i Azure AD förlitar sig på godkännanden för att få åtkomst till resurser eller API:er som krävs. Det finns ett antal typer av godkännanden som din app kan behöva ha för att fungera. Om du definierar behörigheter behöver du också känna till hur användarna får tillgång till din app eller API.

* **Statiskt användargodkännande** – Sker automatiskt under [OAuth 2.0-auktoriseringsflödet](/azure/active-directory/develop/active-directory-protocols-oauth-code.md#request-an-authorization-code) när du anger den resurs som din app vill interagera med. För ett statiskt användargodkännande måste din app redan ha angett alla behörigheter som behövs i appens konfiguration i Azure Portal. Om användaren (eller administratören efter behov) inte har gett sitt godkännande för den här appen, kommer Azure AD be användaren om detta nu. 

    Läs mer om hur du registrerar en Azure AD-app som begär åtkomst till en statisk uppsättning API:er.
* **Dynamiskt användargodkännande** – Är en funktion i v2 Azure AD-appmodellen. I det här scenariot begär appen en uppsättning omfång som krävs i [OAuth 2.0-auktoriseringen av flödet för v2-appar](/azure/active-directory/develop/active-directory-v2-scopes#requesting-individual-user-consent). Om användarna inte har godkänt detta, uppmanas de att godkänna nu. [Mer information om dynamiskt godkännande](/azure/active-directory/develop/active-directory-v2-compare#incremental-and-dynamic-consent).

    > [!NOTE]
    > Ett dynamiskt godkännande kan vara praktiskt, men det är en utmaning för behörigheter som kräver administratörsmedgivande eftersom man inte känner till dessa behörigheter vid godkännandet. Om du behöver omfång med administratörsbehörighet, måste din app registrera dem i Azure Portal.
  
* **Administratörsmedgivande** – Krävs när din app behöver ha åtkomst till vissa högpriviligierade behörigheter. Detta säkerställer att administratörer har vissa ytterligare kontroller innan de godkänner appar, eller att användare får åtkomst till högprivilegierade data från organisationen. [Mer information om hur du ger ett administratörsmedgivande](/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint).

## <a name="best-practices"></a>Bästa praxis

### <a name="resource-best-practices"></a>Metodtips för resurser
Resurser som använder API:er bör definiera behörigheter som är mycket specifika för de data eller de åtgärder som de skyddar. Detta säkerställer att klienterna inte får behörighet att komma åt data som de inte behöver och att användarna är väl informerade om vilka data de godkänner.

Resurserna ska tydligt definiera behörigheterna `Read` och `ReadWrite` separat. 

Resurserna ska markera alla behörigheter som ger åtkomst till data över användarens gränser som `Admin`-behörigheter. 

Resurserna bör följa följande namngivningsmönster `Subject.Permission[.Modifier]` där `Subject` motsvarar den typ av data som är tillgänglig, `Permission` motsvarar den åtgärd som en användare kan vidta för dessa data och `Modifier` används vid behov till att beskriva specialiseringar av någon annan behörighet. Till exempel: 
* Mail.Read – Tillåter att användarna läser e-post. 
* Mail.ReadWrite – Tillåter att användarna läser eller skriver e-post.
* Mail.ReadWrite.All – Ger en administratör eller användare åtkomst till all e-post i organisationen.

### <a name="client-best-practices"></a>Metodtips för klienter
Begär endast behörighet för de omfång som din app behöver. Appar med för många behörigheter riskerar att exponera användardata om de komprometteras.

Klienter bör inte begära programbehörigheter och delegerade behörigheter från samma app. Detta kan leda till en rättighetsökning och ge användare åtkomst till data som deras egna behörighet inte tillåter. 




