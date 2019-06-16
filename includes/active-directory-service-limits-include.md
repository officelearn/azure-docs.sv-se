---
title: ta med fil
description: ta med fil
services: active-directory
author: curtand
ms.service: active-directory
ms.topic: include
ms.date: 05/22/2019
ms.author: curtand
ms.custom: include file
ms.openlocfilehash: 067280cdad85f59106bce5ff214e2fa9eddf3b71
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133419"
---
Här följer användningsbegränsningarna och andra tjänstbegränsningar för Azure Active Directory-tjänsten (Azure AD).

| Category | Begränsningar |
| --- | --- |
| Kataloger | En enskild användare kan som mest tillhöra 500 Azure AD-kataloger som medlem eller gäst.<br/>En enskild användare kan som mest skapa 20 kataloger. |
| Domäner | Du kan som mest lägga till 900 hanterade domännamn. Om du konfigurerar alla dina domäner för federation lokalt med Active Directory kan du som mest lägga till 450 domännamn i varje katalog. |
| Objekt |<ul><li>Som mest kan 50 000 objekt skapas i en enda katalog av användare med den kostnadsfria versionen av Azure Active Directory som standard. Om du har minst en verifierad domän kan standardkvoten för katalogtjänsten i Azure AD utökas till 300 000 objekt. </li><li>En icke-administratör kan skapa högst 250 objekt. Både aktiva objekt och borttagna objekt som är tillgängliga för återställning räknas mot den här kvoten. Endast borttagna objekt som har tagits bort för mindre än 30 dagar sedan är tillgängliga för återställning. Borttagna objekt som inte längre går att återställa räknas i 30 dagar mot kvoten till en fjärdedels värde. Du kan [tilldela en administratörsroll](../articles/active-directory/users-groups-roles/directory-assign-admin-roles.md) till icke-administratörer som du tror kommer att överskrida kvoten upprepade gånger i sitt dagliga arbete.</li></ul> |
| Schematillägg |<ul><li>Tillägg av strängtyp får maximalt innehålla 256 tecken. </li><li>Tillägg av binär typ är begränsade till 256 byte.</li><li>Endast 100 tilläggsvärden över *alla* typer och *alla* program kan skrivas till ett enskilt objekt.</li><li>Endast entiteter av typen User, Group, TenantDetail, Device, Application och ServicePrincipal kan utökas med strängtypen eller enkelvärdesattribut av den binära typen.</li><li>Schematillägg är bara tillgängliga i förhandsversionen av Graph API-version 1.21. Programmet måste beviljas skrivbehörighet för att det ska vara möjligt att registrera ett tillägg.</li></ul> |
| Program |Som mest kan 100 användare vara ägare av ett enda program. |
| Grupper |<ul><li>Som mest kan 100 användare vara ägare av en enda grupp.</li><li>Ett valfritt antal objekt kan vara medlemmar i en enskild grupp.</li><li>En användare kan tillhöra ett valfritt antal grupper.</li><li>Antalet medlemmar i en grupp som du kan synkronisera från din lokala Active Directory till Azure Active Directory med Azure AD Connect är begränsat till 50 000 medlemmar.</li></ul> |
| Programproxy | <ul><li>Högst 500 transaktioner per sekund per App Proxy-program</li><li>Högst 750 transaktioner per sekund för klienten</li></ul><br/>En transaktion har definierats som en enda http-begäran och svar för en unik resurs. När prestandan för får klienter svar 429 (för många begäranden). |
| Åtkomstpanel |<ul><li>Det finns ingen gräns för hur många program som kan visas på åtkomstpanelen per användare. Detta gäller för licenser som tilldelats av användare för Azure AD Premium eller Enterprise Mobility Suite.</li><li>Som mest kan 10 appaneler visas på åtkomstpanelen för varje användare. Den här gränsen gäller för användare som har tilldelats licenser för en kostnadsfri version eller Azure AD Basic-versionen av Azure Active Directory. Exempel på appaneler är Box, Salesforce och Dropbox. Den här begränsningen gäller inte för administratörskonton.</li></ul> |
| Rapporter | Högst 1 000 rader kan visas eller hämtas i rapporter. Eventuella ytterligare data trunkeras. |
| Administrativa enheter | Ett objekt kan ingå i högst 30 administrativa enheter. |
| Administratörsroller och behörigheter | <ul><li>En grupp kan inte läggas till som en [ägare](https://docs.microsoft.com/en-us/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context#object-ownership).</li><li>En grupp kan inte tilldelas en [rollen](https://docs.microsoft.com/en-us/azure/active-directory/users-groups-roles/directory-assign-admin-roles).</li><li>Användarnas möjlighet att läsa kataloginformation för andra användare kan inte begränsas utanför växeln klienttäckande att inaktivera alla icke-administratörer användares åtkomst till all kataloginformation (rekommenderas inte). Mer information om standardbehörigheterna [här](https://docs.microsoft.com/en-us/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context#to-restrict-the-default-permissions-for-member-users).</li><li>Det kan ta upp till 15 minuter eller logga ut/logga in innan admin rollen medlemskap tillägg och återkallelse av träder i kraft.</li></ul> |
