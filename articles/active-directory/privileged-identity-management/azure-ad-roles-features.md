---
title: Hantera Azure AD-roller i PIM (Privileged Identity Management) | Microsoft-dokument
description: Hantera Azure AD-roller för tilldelning privilegierad identitetshantering (PIM)
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: f5d63577cbdbdf18cb17618bdb5c9e3b5de0e44a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245985"
---
# <a name="management-capabilities-for-azure-ad-roles-in-privileged-identity-management"></a>Hanteringsfunktioner för Azure AD-roller i Privilegierad identitetshantering

Hanteringsupplevelsen för Azure AD-roller i Privilegierad identitetshantering har uppdaterats för att förena hur Azure AD-roller och Azure-resursroller hanteras. Tidigare har Privilegierad identitetshantering för Azure-resursroller haft ett par viktiga funktioner som inte var tillgängliga för Azure AD-roller.

Med uppdateringen som för närvarande distribueras, slår vi samman de två till en enda hanteringsupplevelse, och i den får du samma funktioner för Azure AD-roller som för Azure-resursroller. Den här artikeln informerar dig om de uppdaterade funktionerna och eventuella krav.


## <a name="time-bound-assignments"></a>Tidsbundna tilldelningar

Tidigare i Privilegierad identitetshantering för Azure AD-roller var du bekant med rolltilldelningar med två möjliga tillstånd – *kvalificerade* och *permanenta*. Nu kan du ange en start- och sluttid för varje typ av tilldelning. Detta tillägg ger dig fyra möjliga tillstånd där du kan placera en tilldelning:

- Stödberättigade permanent
- Aktiv permanent
- Stödberättigande, med angivna start-/slutdatum för tilldelning
- Aktiv, med angivna start-/slutdatum för tilldelning

I många fall, även om du inte vill att användare ska ha kvalificerade tilldelningar och aktivera roller varje gång, kan du fortfarande skydda din Azure AD-organisation genom att ange en förfallotid för tilldelningar. Om du till exempel har några tillfälliga användare som är kvalificerade kan du överväga att ange ett förfallodatum så att de tas bort automatiskt från rolltilldelningen när deras arbete är klart.

## <a name="new-role-settings"></a>Nya rollinställningar

Vi lägger också till nya inställningar för Azure AD-roller. Tidigare kunde du bara konfigurera aktiveringsinställningar per roll. Det vill än, aktiveringsinställningar som multifaktorautentiseringskrav och incident-/begäranningsbiljettkrav tillämpades på alla användare som är berättigade till en angiven roll. Nu kan du konfigurera om en enskild användare behöver utföra multifaktorautentisering innan de kan aktivera en roll. Du kan också ha avancerad kontroll över dina e-postmeddelanden om privilegierad identitetshantering som är relaterade till specifika roller.

## <a name="extend-and-renew-assignments"></a>Utöka och förnya tilldelningar

Så fort du räknar ut tidsbundna tilldelning, är den första frågan du kan ställa vad som händer om en roll har upphört att gälla? I den här nya versionen erbjuder vi två alternativ för det här scenariot:

- Utöka – När en rolltilldelning närmar sig utgångsdatum kan användaren använda Privilegierad identitetshantering för att begära ett tillägg för den rolltilldelningen
- Förnya – När en rolltilldelning har upphört att gälla kan användaren använda Privilegierad identitetshantering för att begära en förnyelse för den rolltilldelningen

Båda användarinitierade åtgärder kräver godkännande från en global administratör eller privilegierad rolladministratör. Administratörer behöver inte längre vara i branschen för att hantera dessa utgångsdatum. De behöver bara vänta på förlängnings- eller förnyelsebegäranden och godkänna dem om begäran är giltig.

## <a name="api-changes"></a>API-ändringar

När kunder har den uppdaterade versionen distribuerad till sin Azure AD-organisation slutar det befintliga graf-API:et att fungera. Du måste gå över för att använda [Graph API för Azure-resursroller](https://docs.microsoft.com/graph/api/resources/privilegedidentitymanagement-resources?view=graph-rest-beta). Om du vill hantera Azure `/azureResources` AD-roller med det API:et byter du med `/aadroles` i signaturen och använder katalog-ID:et `resourceId`för .

Vi har gjort vårt bästa för att nå ut till alla kunder som använder det tidigare API:et för att låta dem veta om denna förändring i förväg. Om din Azure AD-organisation flyttades vidare till den nya versionen och du pim_preview@microsoft.comfortfarande är beroende av det gamla API:et kontaktar du teamet på .

## <a name="powershell-change"></a>PowerShell-ändring

För kunder som använder PowerShell-modulen Privilegierad identitetshantering för Azure AD-roller slutar PowerShell att fungera med uppdateringen. I stället för föregående cmdlets måste du använda cmdlets för privilegierad identitetshantering i Azure AD Preview PowerShell-modulen. Installera Azure AD PowerShell-modulen från [PowerShell-galleriet](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17). Du kan nu [läsa dokumentationen och exemplen för PIM-åtgärder i den här PowerShell-modulen](powershell-for-azure-ad-roles.md).

## <a name="next-steps"></a>Nästa steg

- [Tilldela en anpassad Azure AD-roll](azure-ad-custom-roles-assign.md)
- [Ta bort eller uppdatera en anpassad Azure AD-rolltilldelning](azure-ad-custom-roles-update-remove.md)
- [Konfigurera en anpassad Azure AD-rolltilldelning](azure-ad-custom-roles-configure.md)
- [Rolldefinitioner i Azure AD](../users-groups-roles/directory-assign-admin-roles.md)
