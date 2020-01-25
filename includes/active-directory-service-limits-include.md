---
title: ta med fil
description: ta med fil
services: active-directory
author: curtand
ms.service: active-directory
ms.topic: include
ms.date: 01/22/2020
ms.author: curtand
ms.custom: include file
ms.openlocfilehash: 2e06a6c8dd7eb58769f504db9f96e0303c3e9f4c
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76748780"
---
Här följer användningsbegränsningarna och andra tjänstbegränsningar för Azure Active Directory-tjänsten (Azure AD).

| Kategori | Begränsningar |
| --- | --- |
| Kataloger | En enskild användare kan som mest tillhöra 500 Azure AD-kataloger som medlem eller gäst.<br/>En enskild användare kan som mest skapa 20 kataloger. |
| Domäner | Du kan som mest lägga till 900 hanterade domännamn. Om du konfigurerar alla dina domäner för federation lokalt med Active Directory kan du som mest lägga till 450 domännamn i varje katalog. |
|Resurser |<ul><li>Högst 50 000 Azure AD-resurser kan skapas i en enda katalog av användare av den kostnads fria versionen av Azure Active Directory som standard. Om du har minst en verifierad domän utökas standard katalog tjänst kvoten i Azure AD till 300 000 Azure AD-resurser. </li><li>En användare som inte är administratör kan inte skapa mer än 250 Azure AD-resurser. Både aktiva resurser och borttagna resurser som är tillgängliga för att återställas mot den här kvoten. Endast Azure AD-resurser som har tagits bort under 30 dagar sedan är tillgängliga för återställning. Tog bort Azure AD-resurser som inte längre är tillgängliga för återställning till den här kvoten till ett värde av ett kvartal i 30 dagar. Om du har utvecklare som troligen ofta överskrider den här kvoten under sina normala uppgifter, kan du [skapa och tilldela en anpassad roll](../articles/active-directory/users-groups-roles/roles-quickstart-app-registration-limits.md) med behörighet att skapa ett obegränsat antal registrerade appar.</li></ul> |
| Schematillägg |<ul><li>Tillägg av strängtyp får maximalt innehålla 256 tecken. </li><li>Tillägg av binär typ är begränsade till 256 byte.</li><li>Endast 100 tilläggs värden, för *alla* typer och *alla* program, kan skrivas till en enda Azure AD-resurs.</li><li>Endast entiteter av typen User, Group, TenantDetail, Device, Application och ServicePrincipal kan utökas med strängtypen eller enkelvärdesattribut av den binära typen.</li><li>Schematillägg är bara tillgängliga i förhandsversionen av Graph API-version 1.21. Programmet måste beviljas skrivbehörighet för att det ska vara möjligt att registrera ett tillägg.</li></ul> |
| Appar |Som mest kan 100 användare vara ägare av ett enda program. |
|Program manifest |Högst 1200 poster kan läggas till i applikations manifestet. |
| Grupper |<ul><li>En användare kan skapa högst 250 grupper i en Azure AD-organisation.</li><li>En Azure AD-organisation kan ha högst 5000 dynamiska grupper.<li>Som mest kan 100 användare vara ägare av en enda grupp.</li><li>Valfritt antal Azure AD-resurser kan vara medlemmar i en enda grupp.</li><li>En användare kan tillhöra ett valfritt antal grupper.</li><li>Antalet medlemmar i en grupp som du kan synkronisera från din lokala Active Directory till Azure Active Directory med Azure AD Connect är begränsat till 50 000 medlemmar.</li><li>Kapslade grupper i Azure AD stöds inte i alla scenarier</li></ul><br/> För närvarande är följande scenarier som stöds med kapslade grupper.<ul><li> En grupp kan läggas till som medlem i en annan grupp och du kan få grupp kapsling.</li><li> Anspråk för grupp medlemskap (när en app har kon figurer ATS för att ta emot grupp medlemskaps anspråk i token är kapslade grupper den inloggade användaren medlem i ingår)</li><li>Villkorlig åtkomst (när ett omfånget är en princip för villkorlig åtkomst till en grupp)</li><li>Begränsa åtkomsten till självbetjäning lösen ords återställning</li><li>Begränsa vilka användare som kan göra Azure AD-anslutning och enhets registrering</li></ul><br/>Följande scenarier stöder inte kapslade grupper:<ul><li> Roll tilldelning av appar (tilldela grupper till en app stöds, men grupper som är kapslade i den direkt tilldelade gruppen har inte åtkomst), både för åtkomst och för etablering</li><li>Gruppbaserad licensiering (tilldelar en licens automatiskt till alla medlemmar i en grupp)</li><li>Office 365-grupper.</li></ul> |
| Programproxy | <ul><li>Högst 500 transaktioner per sekund per app proxy-program</li><li>Högst 750 transaktioner per sekund för Azure AD-organisationen</li></ul><br/>En transaktion definieras som en enskild http-begäran och ett svar för en unik resurs. Vid begränsning får klienterna ett 429-svar (för många begär Anden). |
| Åtkomstpanel |<ul><li>Det finns ingen gräns för hur många program som kan visas på åtkomstpanelen per användare. Detta gäller för licenser som tilldelats av användare för Azure AD Premium eller Enterprise Mobility Suite.</li><li>Som mest kan 10 appaneler visas på åtkomstpanelen för varje användare. Den här gränsen gäller för användare som har tilldelats licenser för Azure AD Free licens plan. Exempel på appaneler är Box, Salesforce och Dropbox. Den här begränsningen gäller inte för administratörskonton.</li></ul> |
| Rapporter | Högst 1 000 rader kan visas eller hämtas i rapporter. Eventuella ytterligare data trunkeras. |
| Administrativa enheter | En Azure AD-resurs kan vara medlem i högst 30 administrativa enheter. |
| Administratörsroller och behörigheter | <ul><li>Det går inte att lägga till en grupp som [ägare](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context#object-ownership).</li><li>En grupp kan inte tilldelas till en [roll](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).</li><li>Användares möjlighet att läsa andra användares katalog information kan inte begränsas utanför Azure AD Organization-wide-växeln för att inaktivera alla användare som inte är administratörers åtkomst till all katalog information (rekommenderas inte). Mer information om standard behörigheter [här](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context#to-restrict-the-default-permissions-for-member-users).</li><li>Det kan ta upp till 15 minuter eller att logga ut/logga in innan tillägg för administratörs roll medlemskap och återkallning börjar gälla.</li></ul> |
