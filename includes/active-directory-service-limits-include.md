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
ms.openlocfilehash: fff3cc176da155ab92514a126c43c33cbd21ad91
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2019
ms.locfileid: "56675440"
---
Här följer begränsningarna för användning och andra tjänstebegränsningar för tjänsten Azure Active Directory (AD Azure).

| Kategori | Begränsningar |
| --- | --- |
| Kataloger | En enskild användare kan höra till högst 500 Azure AD-kataloger som medlem eller gäst.<br/>En enskild användare kan skapa högst 20 kataloger. |
| Domäner | Du kan lägga till mer än 900 hanterade domännamn. Om du lägger upp alla domäner för federering med lokala Active Directory kan du lägga till fler än 450 domännamn i varje katalog. |
| Objekt |<ul><li>Högst 500 000 objekt kan skapas i en enskild katalog med användare i den kostnadsfria versionen av Azure Active Directory.</li><li>En icke-administratör kan skapa högst 250 objekt. Både aktiva objekt och borttagna objekt som är tillgängliga för återställning (bort färre än 30 dagar sedan) räknas in i kvoten. Borttagna objekt som inte längre går att återställa räknas in i kvoten på ett värde av 1/4 i 30 dagar. Överväg att [tilldela en administratörsroll](../articles/active-directory/users-groups-roles/directory-assign-admin-roles.md) till icke-administratörer som sannolikt upprepade gånger överskrider kvoten på grund av regelbundna tjänsten.</li></ul> |
| Schematillägg |<ul><li>Strängtypen tillägg kan innehålla maximalt 256 tecken. </li><li>Tillägg av typen binary är begränsade till 256 byte.</li><li>100 tilläggsvärden (över ALLA typer och ALLA program) kan skrivas till ett enskilt objekt.</li><li>Endast entiteter av typen ”User”, ”Group”, ”TenantDetail”, ”Device”, ”Application” och ”ServicePrincipal” kan utökas med typen ”String” eller enkelvärdesattribut av typen ”Binary”.</li><li>Schematillägg är bara tillgängliga i Graph API-version 1.21-preview. Programmet måste beviljas skrivbehörighet för att det ska vara möjligt att registrera ett tillägg.</li></ul> |
| Appar |Högst 100 användare kan vara ägare till ett enda program. |
| Grupper |<ul><li>Högst 100 användare kan vara ägare till en grupp.</li><li>Valfritt antal objekt kan vara medlemmar i en grupp.</li><li>En användare kan vara medlem i valfritt antal grupper.</li><li>Antalet medlemmar i en grupp som du kan synkronisera från din lokala Active Directory till Azure Active Directory med Azure AD Connect är begränsad till 50 K medlemmar.</li></ul> |
| Åtkomstpanel |<ul><li>Det finns ingen gräns för antalet program som kan visas i åtkomstpanelen per slutanvändaren för användare som har tilldelats licenser för Azure AD Premium eller Enterprise Mobility Suite.</li><li>Högst 10 appaneler (exempel: Box, Salesforce eller Dropbox) visas i åtkomstpanelen för varje slutanvändare för användare som har tilldelats licenser kostnadsfritt eller Azure AD Basic-utgåvor av Azure Active Directory. Den här begränsningen gäller inte för administratörskonton.</li></ul> |
| Rapporter | Högst 1 000 rader kan visas eller hämtas i rapporter. Eventuella ytterligare data trunkeras. |
| Administrativa enheter | Ett objekt kan ingå i högst 30 administrativa enheter. |
| Administratörsroller och behörigheter | <li>En grupp kan inte läggas till som ägare<li>En grupp kan inte tilldelas en roll<li>Det går inte att ändra standardinställningar för användarbehörigheter utöver klient växlar (användarinställningar i Azure AD) |
