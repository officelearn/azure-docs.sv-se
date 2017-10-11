---
title: "Oväntat fel när du utför medgivande till ett program | Microsoft Docs"
description: "Beskriver fel som kan uppstå under processen att samtycka till ett program och vad du kan göra om dem."
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: fd500fdd4c8642bad96dcf71eebcf1fad461a35f
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2017
---
# <a name="unexpected-error-when-performing-consent-to-an-application"></a>Oväntat fel när du utför medgivande till ett program

Den här artikeln beskrivs fel som kan uppstå under processen att samtycka till ett program. Om du felsöka oväntat medgivande prompter som inte innehåller några felmeddelanden, se [Autentiseringsscenarier för Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-scenarios).

Många program som integreras med Azure Active Directory kräver behörighet att komma åt andra resurser för att fungera. När dessa resurser också är integrerade med Azure Active Directory, behörighet att komma åt dem begärs ofta använder vanliga medgivande framework. 

Detta resulterar i en fråga om medgivande visas, vilket sker vanligtvis första gången ett program som används men kan också inträffa om en efterföljande användning av programmet.

Vissa villkor måste vara true för en användare ska godkänna de behörigheter som krävs för ett program. Om dessa villkor inte uppfylls, kan det uppstå fel. Exempel på dessa är:

## <a name="requesting-not-authorized-permissions-error"></a>Begär inte auktoriserad Behörighetsfel
* **AADSTS90093:** &lt;clientAppDisplayName&gt; begär en eller flera behörigheter som du har inte behörighet för att bevilja. Kontakta en administratör som kan godkänna det här programmet för din räkning.

Det här felet uppstår när en användare som inte är en företagsadministratör försöker använda ett program som begär behörigheter som bara en administratör kan ge. Det här felet kan lösas av en administratör beviljas åtkomst till programmet för organisationen.

## <a name="policy-prevents-granting-permissions-error"></a>Princip förhindrar att bevilja behörigheter fel
* **AADSTS90093:** administratör för &lt;tenantDisplayName&gt; har angett en princip som hindrar dig från att bevilja &lt;namn på appen&gt; de behörigheter som begärs. Kontakta en administratör av &lt;tenantDisplayName&gt;, som kan ge behörigheter till den här appen å dina vägnar.

Felet uppstår när en företagsadministratör inaktiveras möjligheten för användare att samtycka till program och sedan en användare försöker använda ett program som kräver godkännande. Det här felet kan lösas av en administratör beviljas åtkomst till programmet för organisationen.

## <a name="intermittent-problem-error"></a>Återkommande problem fel
* **AADSTS90090:** det verkar som om vi påträffade ett tillfälligt problem spela in de behörigheter som du försökte att ge till &lt;clientAppDisplayName&gt;. Försök igen senare.

Det här felet indikerar att en återkommande på klientsidan problemet har uppstått. Det kan lösas genom att försöka samtycker till att programmet igen.

## <a name="resource-not-available-error"></a>Resursen inte tillgängliga fel
* **AADSTS65005:** appen &lt;clientAppDisplayName&gt; begärt behörighet att komma åt en resurs &lt;resourceAppDisplayName&gt; som inte är tillgängligt. 

Kontakta programutvecklaren.

##  <a name="resource-not-available-in-tenant-error"></a>Resursen är inte tillgängligt i klient-fel
* **AADSTS65005:** &lt;clientAppDisplayName&gt; begär åtkomst till en resurs &lt;resourceAppDisplayName&gt; som inte är tillgängligt i din organisation &lt;tenantDisplayName&gt;. 

Kontrollera att resursen är tillgänglig eller kontakta en administratör av &lt;tenantDisplayName&gt;.

## <a name="permissions-mismatch-error"></a>Matchningsfel för behörigheter
* **AADSTS65005:** appen begärt medgivande till åtkomst till resursen &lt;resourceAppDisplayName&gt;. Denna begäran misslyckades eftersom den inte matchar hur appen har redan konfigurerats under app registreringen. Kontakta app vendor.* *

Dessa fel alla inträffar när en användare försöker samtycker till att programmet begär behörighet att komma åt en resursprogram som inte kan hittas i organisationens katalog (klient). Detta kan inträffa av olika orsaker:

-   Klienten programutvecklaren har sina program felaktigt konfigurerad, orsakar det att begära åtkomst till en ogiltig resurs. I det här fallet måste programutvecklaren uppdatera konfigurationen av klientprogram att lösa problemet.

-   Ett huvudnamn för tjänsten som representerar målprogrammet för resursen finns inte i organisationen, eller har funnits i tidigare men har tagits bort. Lös problemet, måste ett huvudnamn för tjänsten för programmets resurs etableras i organisationen så att klientprogrammet kan begära åtkomst till den. Detta kan hända i ett antal olika sätt beroende på vilken typ av program, inklusive:

    -   Skaffa en prenumeration för resursprogram (Microsoft publicerade program)

    -   Principer för resursprogrammet

    -   Tillståndsbeviljande program via Azure Portal

    -   Att lägga till programmet från Azure AD Application Gallery

## <a name="next-steps"></a>Nästa steg 

[Appar, behörigheter och medgivande i Azure Active Directory (v1 slutpunkten)](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)<br>

[Scope, behörigheter och medgivande i Azure Active Directory (v2.0-slutpunkten)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)


