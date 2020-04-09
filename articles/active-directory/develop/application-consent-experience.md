---
title: Azure AD-app medgivande upplevelser
titleSuffix: Microsoft identity platform
description: Läs mer om Azure AD-medgivandet för att se hur du kan använda den när du hanterar och utvecklar program på Azure AD
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: ryanwi
ms.reviewer: zachowd
ms.openlocfilehash: e96442be50a075ebf2cd81bf1b6fb0f58f883bad
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885590"
---
# <a name="understanding-azure-ad-application-consent-experiences"></a>Förstå medgivande i Azure AD-program

Läs mer om användarupplevelsen för Azure Active Directory (Azure AD) -program medgivande. Så du kan på ett intelligent sätt hantera program för din organisation och/eller utveckla program med en mer sömlös samtyckesupplevelse.

## <a name="consent-and-permissions"></a>Samtycke och behörigheter

Samtycke är processen för en användare som beviljar tillstånd till ett program för att komma åt skyddade resurser för deras räkning. En administratör eller användare kan bli ombedd att ge sitt samtycke för att ge åtkomst till sina organisations-/enskilda data.

Den faktiska användarupplevelsen av att bevilja medgivande varierar beroende på principer som angetts för användarens klientorganisation, användarens behörighetsområde (eller roll) och vilken typ av behörigheter som [begärs](https://docs.microsoft.com/azure/active-directory/azuread-dev/v1-permissions-consent) av klientprogrammet. Det innebär att programutvecklare och klientadministratörer har viss kontroll över medgivandeupplevelsen. Administratörer har flexibiliteten att ange och inaktivera principer för en klient eller app för att styra medgivandeupplevelsen i sin klientorganisation. Programutvecklare kan diktera vilka typer av behörigheter som begärs och om de vill vägleda användare via användarens medgivandeflöde eller administratörssamtyckeflödet.

- **Användarens medgivandeflöde** är när en programutvecklare dirigerar användare till auktoriseringsslutpunkten med avsikt att registrera medgivande för endast den aktuella användaren.
- **Administratörsmedgivandeflödet** är när en programutvecklare dirigerar användare till slutpunkten för administratörsmedgivande med avsikt att registrera medgivande för hela klienten. För att säkerställa att administratörsmedgivandeflödet fungerar korrekt `RequiredResourceAccess` måste programutvecklare lista alla behörigheter i egenskapen i programmanifestet. Mer information finns i [Programmanifest](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest).

## <a name="building-blocks-of-the-consent-prompt"></a>Byggstenar i samtyckesprompten

Samtyckesprompten är utformad för att säkerställa att användarna har tillräckligt med information för att avgöra om de litar på klientprogrammet för att komma åt skyddade resurser för deras räkning. Att förstå byggstenarna hjälper användare som beviljar samtycke att fatta mer välgrundade beslut och det kommer att hjälpa utvecklare att skapa bättre användarupplevelser.

Följande diagram och tabell innehåller information om byggstenarna i samtyckesprompten.

![Byggstenar i samtyckesprompten](./media/application-consent-experience/consent_prompt.png)

| # | Komponent | Syfte |
| ----- | ----- | ----- |
| 1 | Användaridentifierare | Den här identifieraren representerar den användare som klientprogrammet begär åtkomst till skyddade resurser för. |
| 2 | Titel | Titeln ändras baserat på om användarna går igenom användarens eller administratörssamtyckeflödet. I användarens medgivande flöde, titeln kommer att vara "Behörigheter begärs" medan i admin samtycke flödet titeln kommer att ha en ytterligare rad "Acceptera för din organisation". |
| 3 | Applogotyp | Den här bilden bör hjälpa användarna att få en visuell referens om huruvida den här appen är den app de är avsedd att komma åt. Den här avbildningen tillhandahålls av programutvecklare och ägarskapet av den här avbildningen valideras inte. |
| 4 | Appnamn | Det här värdet bör informera användarna om vilket program som begär åtkomst till deras data. Observera att det här namnet tillhandahålls av utvecklarna och äganderätten till det här appnamnet har inte validerats. |
| 5 | Utgivardomän | Det här värdet bör ge användarna en domän som de kanske kan utvärdera för tillförlitlighet. Den här domänen tillhandahålls av utvecklarna och ägarskapet för den här utgivardomänen valideras. |
| 6 | Behörigheter | Den här listan innehåller de behörigheter som begärs av klientprogrammet. Användare bör alltid utvärdera vilka typer av behörigheter som begärs för att förstå vilka data klientprogrammet har behörighet att komma åt för deras räkning om de accepterar. Som programutvecklare är det bäst att begära åtkomst, till de behörigheter som har minst behörighet. |
| 7 | Beskrivning av behörighet | Det här värdet tillhandahålls av tjänsten som exponerar behörigheterna. Om du vill se behörighetsbeskrivningarna måste du växla sparren bredvid behörigheten. |
| 8 | Appvillkor | Dessa villkor innehåller länkar till användarvillkoren och sekretesspolicyn för programmet. Utgivaren ansvarar för att beskriva sina regler i sina användarvillkor. Dessutom är utgivaren ansvarig för att avslöja hur de använder och dela användardata i sin sekretesspolicy. Om utgivaren inte tillhandahåller länkar till dessa värden för program med flera innehavare visas en fet varning om medgivandeprompten. |
| 9 | https://myapps.microsoft.com | Det här är länken där användare kan granska och ta bort program som inte kommer från Microsoft och som för närvarande har åtkomst till deras data. |

## <a name="common-consent-scenarios"></a>Scenarier för gemensamt samtycke

Här är de medgivandeupplevelser som en användare kan se i scenarierna för gemensamt samtycke:

1. Personer som använder en app som leder dem till användarens medgivandeflöde samtidigt som de kräver en behörighetsgrupp som ligger inom deras behörighetsområde.
    
    1. Administratörer kommer att se en ytterligare kontroll på den traditionella samtyckesprompten som ger dem samtycke på uppdrag av hela klienten. Kontrollen kommer att vara avstängd som standard, så endast när administratörer uttryckligen markerar rutan kommer samtycke beviljas för hela klientens räkning. Från och med idag visas den här kryssrutan endast för rollen Global administratör, så cloud admin och appadministratör kommer inte att se den här kryssrutan.

        ![Fråga om samtycke för scenario 1a](./media/application-consent-experience/consent_prompt_1a.png)
    
    2. Användarna ser den traditionella samtyckesprompten.

        ![Fråga om samtycke för scenario 1b](./media/application-consent-experience/consent_prompt_1b.png)

2. Personer som använder en app som kräver minst en behörighet som ligger utanför deras behörighetsområde.
    1. Administratörer kommer att se samma fråga som 1.i visas ovan.
    2. Användare kommer att blockeras från att bevilja samtycke till programmet, och de kommer att bli tillsagda att be sin administratör om åtkomst till appen. 
                
        ![Fråga om samtycke för scenario 1b](./media/application-consent-experience/consent_prompt_2b.png)

3. Personer som navigerar eller dirigeras till administratörens medgivandeflöde.
    1. Administratörsanvändare ser uppmaningen om administratörsgodkännande. Titeln och behörighetsbeskrivningarna som ändrats för den här prompten, ändringarna belyser det faktum att acceptera denna fråga kommer att ge appen åtkomst till begärda data på uppdrag av hela klienten.
        
        ![Fråga om samtycke för scenario 1b](./media/application-consent-experience/consent_prompt_3a.png)
        
    1. Icke-admin användare kommer att se samma skärm som 2.ii visas ovan.

## <a name="next-steps"></a>Nästa steg
- Få en stegvis översikt över [hur Azure AD-medgivanderamverket implementerar samtycke](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).
- Mer information finns i [hur ett program med flera innehavare kan använda medgivanderamverket](active-directory-devhowto-multi-tenant-overview.md) för att implementera "användar- och administratörstillstånd", vilket stöder mer avancerade programmönster på flera nivåer.
- Läs om hur du [konfigurerar appens utgivardomän](howto-configure-publisher-domain.md).
