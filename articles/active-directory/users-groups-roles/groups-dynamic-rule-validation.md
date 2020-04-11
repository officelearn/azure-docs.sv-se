---
title: Validera regler för dynamiskt gruppmedlemskap (förhandsversion) – Azure AD | Microsoft-dokument
description: Så här testar du medlemmar mot en medlemsregel för en dynamisk grupp i Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/10/2020
ms.author: curtand
ms.reviewer: yukarppa
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84e081f62949f4cf3f7ae375b2c60ee5b032d974
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115526"
---
# <a name="validate-a-dynamic-group-membership-rule-preview-in-azure-active-directory"></a>Validera en dynamisk gruppmedlemskapsregel (förhandsversion) i Azure Active Directory

Azure Active Directory (Azure AD) ger nu möjlighet att validera dynamiska gruppregler (i offentlig förhandsversion). På fliken **Validera regler** kan du validera den dynamiska regeln mot exempelgruppmedlemmar för att bekräfta att regeln fungerar som förväntat. När du skapar eller uppdaterar dynamiska gruppregler vill administratörer veta om en användare eller en enhet ska vara medlem i gruppen. Detta hjälper till att utvärdera om användare eller enhet uppfyller regelkriterierna och hjälp med felsökning när medlemskap inte förväntas.

## <a name="step-by-step-walk-through"></a>Steg-för-steg genomgång

Kom igång genom att gå till **Azure Active Directory** > **Groups**. Välj en befintlig dynamisk grupp eller skapa en ny dynamisk grupp och klicka på Dynamiska medlemskapsregler. Du kan sedan se fliken **Validera regler.**

![Leta reda på fliken Validera regler och börja med en befintlig regel](./media/groups-dynamic-rule-validation/validate-tab.png)

På fliken **Validera regler** kan du välja användare för att validera deras medlemskap. 20 användare eller enheter kan väljas samtidigt.

![Lägga till användare för att validera den befintliga regeln mot](./media/groups-dynamic-rule-validation/validate-tab-add-users.png)

När du har valt användare eller enheter från väljaren och **Välj**visas valideringen automatiskt och valideringsresultaten visas.

![Visa resultatet av regelvalideringen](./media/groups-dynamic-rule-validation/validate-tab-results.png)

Resultaten visar om en användare är medlem i gruppen eller inte. Om regeln inte är giltig eller om det finns ett nätverksproblem visas resultatet som **Okänt**. I händelse av **Okänd**kommer det detaljerade felmeddelandet att beskriva problemet och de åtgärder som behövs.

![Visa information om resultatet av regelvalideringen](./media/groups-dynamic-rule-validation/validate-tab-view-details.png)

Du kan ändra regeln och validering av medlemskap kommer att utlösas. För att se varför användaren inte är medlem i gruppen, klicka på "Visa information" och verifieringsinformation kommer att visa resultatet av varje uttryck som utgör regeln. Klicka på **OK** för att avsluta.

## <a name="next-steps"></a>Nästa steg

- [Dynamiska medlemskapsregler för grupper](groups-dynamic-membership.md)
