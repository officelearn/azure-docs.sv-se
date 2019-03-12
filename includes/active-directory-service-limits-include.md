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
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554723"
---
Här följer begränsningarna för användning och andra tjänstebegränsningar för tjänsten Azure Active Directory (AD Azure).

| Kategori | Begränsningar |
| --- | --- |
| Kataloger | En enskild användare kan höra till högst 500 Azure AD-kataloger som medlem eller gäst.<br/>En enskild användare kan skapa högst 20 kataloger. |
| Domäner | Du kan lägga till mer än 900 hanterade domännamn. Om du har konfigurerat alla domäner för federering med lokala Active Directory kan du lägga till fler än 450 domännamn i varje katalog. |
| Objekt |<ul><li>Högst 500 000 objekt kan skapas i en enskild katalog med användare i den kostnadsfria versionen av Azure Active Directory.</li><li>En icke-administratör kan skapa högst 250 objekt. Både aktiva objekt och borttagna objekt som är tillgängliga för att återställa räknas in i kvoten. Endast borttagna objekt som har tagits bort är färre än 30 dagar sedan tillgängliga för återställning. Borttagna objekt som inte längre går att återställa räknas in i kvoten på ett värde på en fjärdedel i 30 dagar. Kanske [tilldela en administratörsroll](../articles/active-directory/users-groups-roles/directory-assign-admin-roles.md) till icke-administratörer som sannolikt upprepade gånger överskrider kvoten regelbundna tjänsten.</li></ul> |
| Schematillägg |<ul><li>Sträng-tillägg kan innehålla högst 256 tecken. </li><li>Binary-tillägg är begränsade till 256 byte.</li><li>Endast 100 tillägget värden över *alla* typer och *alla* program, kan skrivas till ett enskilt objekt.</li><li>Endast användare, grupp, TenantDetail, enhet, program och ServicePrincipal-enheter kan utökas med typen string eller binär typ enkelvärdesattribut anges.</li><li>Schematillägg är bara tillgängliga i Graph API-version 1.21-preview. Programmet måste beviljas skrivbehörighet för att det ska vara möjligt att registrera ett tillägg.</li></ul> |
| Appar |Högst 100 användare kan vara ägare till ett enda program. |
| Grupper |<ul><li>Högst 100 användare kan vara ägare till en grupp.</li><li>Valfritt antal objekt kan vara medlemmar i en grupp.</li><li>En användare kan vara medlem i valfritt antal grupper.</li><li>Antalet medlemmar i en grupp som du kan synkronisera från din lokala Active Directory till Azure Active Directory med hjälp av Azure AD Connect är begränsad till 50 000 medlemmar.</li></ul> |
| Åtkomstpanel |<ul><li>Det finns ingen gräns för hur många program som kan ses i åtkomstpanelen per användare. Detta gäller för användare som är tilldelade licenser för Azure AD Premium eller Enterprise Mobility Suite.</li><li>Högst 10 appaneler kan ses i åtkomstpanelen för varje användare. Den här gränsen gäller för användare som har tilldelats licenser kostnadsfritt eller Azure AD Basic-versionerna av Azure Active Directory. Exempel på appaneler är Box, Salesforce eller Dropbox. Den här gränsen gäller inte för administratörskonton.</li></ul> |
| Rapporter | Högst 1 000 rader kan visas eller hämtas i rapporter. Eventuella ytterligare data trunkeras. |
| Administrativa enheter | Ett objekt kan ingå i högst 30 administrativa enheter. |
| Administratörsroller och behörigheter | <li>En grupp kan inte läggas till som ägare.<li>En grupp kan inte tilldelas en roll.<li>Standardanvändare inte kan ändras behörigheter förutom klient växlar, som är användarinställningar i Azure AD. |
