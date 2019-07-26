---
title: Stänga aktiva risk händelser i Azure Active Directory Identity Protection | Microsoft Docs
description: Lär dig mer om de alternativ som du har stängt aktiva risk händelser.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: f5e24c12b72852ee7009533c8dc24d231fe636f2
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2019
ms.locfileid: "68333995"
---
# <a name="how-to-close-active-risk-events"></a>Instruktioner: Stäng aktiva riskhändelser

Med [risk händelser](../reports-monitoring/concept-risk-events.md)identifierar Azure Active Directory indikatorer för potentiellt komprometterade användar konton. Som administratör vill du få alla risk händelser stängda, så att de berörda användarna inte längre är utsatta för risk.

Den här artikeln innehåller en översikt över de ytterligare alternativ som du måste stänga aktiva risk händelser.

## <a name="options-to-close-risk-events"></a>Alternativ för att stänga risk händelser 

Status för en risk händelse är antingen **aktiv** eller **stängd**. Alla aktiva risk händelser bidrar till beräkningen av ett värde som kallas användar risk nivå. Användar risk nivån är en indikator (låg, medel, hög) för sannolikheten att ett konto har komprometterats. 

För att stänga aktiva risk händelser har du följande alternativ:

- Kräv lösen ords återställning med en användar risk princip
- Manuell lösen ords återställning
- Ignorera alla risk händelser 
- Stäng enskilda risk händelser manuellt

## <a name="require-password-reset-with-a-user-risk-policy"></a>Kräv lösen ords återställning med en användar risk princip

Genom att konfigurera en [princip för villkorlig åtkomst för användar risker](howto-user-risk-policy.md)kan du kräva en lösen ords ändring om en angiven användar risk nivå har identifierats automatiskt. 

![Återställ lösenord](./media/howto-close-active-risk-events/13.png)

En lösen ords återställning stänger alla aktiva risk händelser för den relaterade användaren och tar tillbaka identiteten i ett säkert tillstånd. Att använda en användar risk princip är den bästa metoden för att stänga aktiva risk händelser eftersom den här metoden är automatiserad. Ingen interaktion krävs mellan den berörda användaren och supportavdelningen eller en administratör.

Att använda en användar risk princip är dock inte alltid tillämpligt. Detta gäller till exempel för att:

- Användare som inte har registrerats för Multi-Factor Authentication (MFA).
- Användare med aktiva risk händelser som har tagits bort.
- En undersökning som visar att en rapporterad risk händelse har utförts av den legitima användaren.

## <a name="manual-password-reset"></a>Manuell lösen ords återställning

Om det inte finns något alternativ för att återställa lösen ord med en användar risk princip, kan du få alla risk händelser för en användare stängd med manuell lösen ords återställning.

![Återställ lösenord](./media/howto-close-active-risk-events/04.png)

Den relaterade dialog rutan innehåller två olika metoder för att återställa ett lösen ord:

![Återställ lösenord](./media/howto-close-active-risk-events/05.png)

**Generera ett tillfälligt lösen ord** – genom att skapa ett tillfälligt lösen ord kan du omedelbart flytta tillbaka en identitet till ett säkert tillstånd. Den här metoden kräver att du interagerar med de berörda användarna eftersom de måste veta vad det tillfälliga lösen ordet är. Du kan till exempel skicka det nya tillfälliga lösen ordet till en annan e-postadress till användaren eller till användarens chef. Eftersom lösen ordet är tillfälligt uppmanas användaren att ändra lösen ordet vid nästa inloggning.

**Kräv att användaren vill återställa lösen ord** – som kräver att användarna återställer lösen ord aktiverar själv återställning utan att kontakta supportavdelningen eller en administratör. Precis som när det gäller en användar risk princip gäller den här metoden endast för användare som är registrerade för MFA. För användare som inte har registrerats för MFA än, är det här alternativet inte tillgängligt.

## <a name="dismiss-all-risk-events"></a>Ignorera alla risk händelser

Om ingen lösen ords återställning är ett alternativ kan du också ignorera alla risk händelser. 

![Återställ lösenord](./media/howto-close-active-risk-events/03.png)

När du klickar på **Ignorera alla händelser**stängs alla händelser och den berörda användaren är inte längre utsatt för risk. Men eftersom den här metoden inte påverkar det befintliga lösen ordet får den inte den relaterade identiteten tillbaka till ett säkert tillstånd. Det bästa användnings fallet för den här metoden är en borttagen användare med aktiva risk händelser. 

## <a name="close-individual-risk-events-manually"></a>Stäng enskilda risk händelser manuellt

Du kan stänga enskilda risk händelser manuellt. Genom att stänga risk händelser manuellt kan du sänka användar risk nivån. Vanligt vis stängs risk händelser manuellt som svar på en relaterad undersökning. Om du till exempel pratar med en användare visar att en aktiv risk händelse inte behövs längre. 
 
När du stänger risk händelser manuellt kan du välja att vidta någon av följande åtgärder för att ändra status för en risk händelse:

![Åtgärder](./media/howto-close-active-risk-events/06.png)

- **Lösning** – om du har undersökt en risk händelse vidtog du en lämplig åtgärds åtgärd utanför identitets skyddet och du tror att risk händelsen bör anses vara stängd, markerar händelsen som löst. Lösta händelser ställer in risk händelse statusen på stängd och risk händelsen kommer inte längre att bidra till användar risken.
- **Markera som falskt-positiv** – i vissa fall kan du undersöka en risk händelse och upptäcka att den är felaktigt flaggad som riskfylld. Du kan minska antalet sådana förekomster genom att markera risk händelsen som falskt-positiv. På så sätt kan Machine Learning-algoritmer förbättra klassificeringen av liknande händelser i framtiden. Status för falska positiva händelser är att stängas och de kommer inte längre att bidra till användar risken.
- **Ignore** – om du inte har vidtagit någon reparations åtgärd, men vill att risk händelsen ska tas bort från den aktiva listan, kan du markera en risk händelse ignorera och händelse status stängs. Ignorerade händelser bidrar inte till användar risken. Det här alternativet bör endast användas under ovanliga omständigheter.
- **Reaktivera** -risk händelser som stängdes manuellt (genom att välja lös, falskt positivt eller ignorera) kan återaktiveras, vilket återställer händelse statusen tillbaka till aktiv. Återaktiverade risk händelser bidrar till beräkning av användar risk nivå. Risk händelser som stängs genom reparation (till exempel en säker lösen ords återställning) kan inte återaktiveras.

## <a name="next-steps"></a>Nästa steg

För att få en översikt över Azure AD Identity Protection, se [Översikt över Azure AD Identity Protection](overview.md).
