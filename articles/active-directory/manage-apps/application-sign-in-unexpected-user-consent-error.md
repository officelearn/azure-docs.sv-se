---
title: Ett oväntat fel inträffade när du utför medgivande till ett program | Microsoft Docs
description: Beskriver fel som kan uppstå under processen att samtycka till ett program och vad du kan göra om dem.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: celested
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89c01202e0d7d7ca0f37b89d5473f96873c52606
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60292154"
---
# <a name="unexpected-error-when-performing-consent-to-an-application"></a>Ett oväntat fel inträffade när du utför medgivande till ett program

Den här artikeln beskriver fel som kan uppstå under processen att samtycka till ett program. Om du felsöker oväntat medgivande frågor som inte innehåller några felmeddelanden, se [Autentiseringsscenarier för Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-scenarios).

Många program som integreras med Azure Active Directory kräver behörighet att komma åt andra resurser för att kunna fungera. När dessa resurser är också integrerat med Azure Active Directory, behörighet att komma åt dem begärs ofta med hjälp av gemensamma ramverket för medgivande. Kommandotolken medgivande visas, vilket vanligtvis sker första gången ett program som används men kan också inträffa om en efterföljande användning av programmet.

Vissa villkor måste vara sant för en användare att godkänna de behörigheter som krävs för ett program. Om dessa villkor inte uppfylls, kan följande fel uppstå.

## <a name="requesting-not-authorized-permissions-error"></a>Begär inte auktoriserad Behörighetsfel
* **AADSTS90093:** &lt;clientAppDisplayName&gt; begär en eller flera behörigheter som du har inte behörighet för att bevilja. Kontakta en administratör som kan godkänna det här programmet för din räkning.

Det här felet uppstår när en användare som inte är en företagsadministratör försöker använda ett program som begär behörigheter som bara en administratör kan ge. Det här felet kan lösas av en administratör bevilja åtkomst till programmet för organisationen.

## <a name="policy-prevents-granting-permissions-error"></a>Princip förhindrar tillståndsbeviljande fel
* **AADSTS90093:** En administratör för &lt;tenantDisplayName&gt; har angett en princip som hindrar dig från att bevilja &lt;namn på app&gt; de behörigheter som begärs. Kontakta en administratör för &lt;tenantDisplayName&gt;, som kan ge behörigheter till den här appen å dina vägnar.

Det här felet uppstår när en företagsadministratör inaktiverar möjligheten för användare att samtycka till program och sedan en användare försöker använda ett program som kräver godkännande. Det här felet kan lösas av en administratör bevilja åtkomst till programmet för organisationen.

## <a name="intermittent-problem-error"></a>Problem med tillfälliga fel
* **AADSTS90090:** Det verkar som om inloggningsprocessen påträffade ett tillfälligt problem spela in de behörigheter som du har försökt att bevilja till &lt;clientAppDisplayName&gt;. Försök igen senare.

Det här felet indikerar att ett tillfälligt tjänsten på klientsidan problem har uppstått. Det kan lösas genom att försöka att ge samtycke för programmet igen.

## <a name="resource-not-available-error"></a>Resursen inte tillgänglig fel
* **AADSTS65005:** Appen &lt;clientAppDisplayName&gt; begärt behörighet att komma åt en resurs &lt;resourceAppDisplayName&gt; som inte är tillgängligt. 

Kontakta apputvecklaren.

##  <a name="resource-not-available-in-tenant-error"></a>Resursen är inte tillgänglig i klient-fel
* **AADSTS65005:** &lt;clientAppDisplayName&gt; begär åtkomst till en resurs &lt;resourceAppDisplayName&gt; som inte är tillgängligt i din organisation &lt;tenantDisplayName &gt;. 

Kontrollera att den här resursen är tillgänglig eller kontakta en administratör för &lt;tenantDisplayName&gt;.

## <a name="permissions-mismatch-error"></a>Matchningsfel för behörigheter
* **AADSTS65005:** Appen begärt medgivande till åtkomst till resursen &lt;resourceAppDisplayName&gt;. Den här begäran misslyckades eftersom det inte matchar hur appen var förkonfigurerade under registreringen. Kontakta app vendor.* *

De här felen som alla inträffar när en användare försöker att godkänna programmet begär behörighet att komma åt en resursprogram som inte kan hittas i organisationens katalog (klient). Den här situationen kan uppstå av flera skäl:

-   Klienten programutvecklaren har sitt program felaktigt konfigurerad, vilket gör att den att begära åtkomst till den ogiltiga resursen. I det här fallet måste programutvecklaren uppdatera konfigurationen av klientprogram för att lösa problemet.

-   Ett huvudnamn för tjänsten som representerar målprogrammet för resursen finns inte i organisationen, eller fanns tidigare men har tagits bort. För att lösa problemet måste tjänstens huvudnamn för resursprogrammet etableras i organisationen så att klientprogrammet kan begära behörighet till den. Tjänstens huvudnamn kan etableras på flera olika sätt, beroende på vilken typ av program, inklusive:

    -   Skaffa en prenumeration för resursprogrammet (Microsoft publicerade program)

    -   Medgivandedialogen resursprogrammet

    -   Bevilja programbehörigheter via Azure portal

    -   Att lägga till programmet från Azure AD-Programgalleriet

## <a name="next-steps"></a>Nästa steg 

[Appar, behörigheter och godkännande i Azure Active Directory (v1-slutpunkt)](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)<br>

[Omfattningar, behörigheter och godkännande i Azure Active Directory (v2.0-slutpunkt)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)


