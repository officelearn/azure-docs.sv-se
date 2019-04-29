---
title: ta med fil
description: ta med fil
services: active-directory
author: curtand
ms.service: active-directory
ms.topic: include
ms.date: 02/21/2019
ms.author: curtand
ms.custom: include file
ms.openlocfilehash: 38f2dd301ddc2a5f8d28322856b2011bd2034c30
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60472229"
---
Här följer användningsbegränsningarna och andra tjänstbegränsningar för Azure Active Directory-tjänsten (Azure AD).

| Kategori | Begränsningar |
| --- | --- |
| Kataloger | En enskild användare kan som mest tillhöra 500 Azure AD-kataloger som medlem eller gäst.<br/>En enskild användare kan som mest skapa 20 kataloger. |
| Domäner | Du kan som mest lägga till 900 hanterade domännamn. Om du konfigurerar alla dina domäner för federation lokalt med Active Directory kan du som mest lägga till 450 domännamn i varje katalog. |
| Objekt |<ul><li>Som mest kan 500 000 objekt skapas i en enda katalog av användare med den kostnadsfria versionen av Azure Active Directory.</li><li>En icke-administratör kan skapa högst 250 objekt. Både aktiva objekt och borttagna objekt som är tillgängliga för återställning räknas mot den här kvoten. Endast borttagna objekt som har tagits bort för mindre än 30 dagar sedan är tillgängliga för återställning. Borttagna objekt som inte längre går att återställa räknas i 30 dagar mot kvoten till en fjärdedels värde. Du kan [tilldela en administratörsroll](../articles/active-directory/users-groups-roles/directory-assign-admin-roles.md) till icke-administratörer som du tror kommer att överskrida kvoten upprepade gånger i sitt dagliga arbete.</li></ul> |
| Schematillägg |<ul><li>Tillägg av strängtyp får maximalt innehålla 256 tecken. </li><li>Tillägg av binär typ är begränsade till 256 byte.</li><li>Endast 100 tilläggsvärden över *alla* typer och *alla* program kan skrivas till ett enskilt objekt.</li><li>Endast entiteter av typen User, Group, TenantDetail, Device, Application och ServicePrincipal kan utökas med strängtypen eller enkelvärdesattribut av den binära typen.</li><li>Schematillägg är bara tillgängliga i förhandsversionen av Graph API-version 1.21. Programmet måste beviljas skrivbehörighet för att det ska vara möjligt att registrera ett tillägg.</li></ul> |
| Program |Som mest kan 100 användare vara ägare av ett enda program. |
| Grupper |<ul><li>Som mest kan 100 användare vara ägare av en enda grupp.</li><li>Ett valfritt antal objekt kan vara medlemmar i en enskild grupp.</li><li>En användare kan tillhöra ett valfritt antal grupper.</li><li>Antalet medlemmar i en grupp som du kan synkronisera från din lokala Active Directory till Azure Active Directory med Azure AD Connect är begränsat till 50 000 medlemmar.</li></ul> |
| Åtkomstpanel |<ul><li>Det finns ingen gräns för hur många program som kan visas på åtkomstpanelen per användare. Detta gäller för licenser som tilldelats av användare för Azure AD Premium eller Enterprise Mobility Suite.</li><li>Som mest kan 10 appaneler visas på åtkomstpanelen för varje användare. Den här gränsen gäller för användare som har tilldelats licenser för en kostnadsfri version eller Azure AD Basic-versionen av Azure Active Directory. Exempel på appaneler är Box, Salesforce och Dropbox. Den här begränsningen gäller inte för administratörskonton.</li></ul> |
| Rapporter | Högst 1 000 rader kan visas eller hämtas i rapporter. Eventuella ytterligare data trunkeras. |
| Administrativa enheter | Ett objekt kan ingå i högst 30 administrativa enheter. |
| Administratörsroller och behörigheter | <li>En grupp kan inte läggas till som ägare.<li>En grupp kan inte tilldelas en roll.<li>Behörigheter för standardanvändare kan inte ändras förutom för byte av klientorganisation, som är användarinställningar i Azure AD. |
