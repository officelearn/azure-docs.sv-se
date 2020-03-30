---
title: Slutför åtkomstgranskning av Azure-resursroller i PIM - Azure AD | Microsoft-dokument
description: Lär dig hur du slutför en åtkomstgranskning av Azure-resursroller Privilegierad identitetshantering i Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9e45249245aaab97070b7e774d4b6bab6827bdc9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74021987"
---
# <a name="complete-an-access-review-of-azure-resource-roles-in-privileged-identity-management"></a>Slutför en åtkomstgranskning av Azure-resursroller i Privilegierad identitetshantering

Privilegierade rolladministratörer kan granska privilegierad åtkomst när de [har påbörjat en åtkomstgranskning.](pim-resource-roles-start-access-review.md) Pim (Privileged Identity Management) i Azure Active Directory (Azure AD) skickar automatiskt ett e-postmeddelande som uppmanar användare att granska sin åtkomst. Om en användare inte får något e-postmeddelande kan du skicka dem instruktioner för [hur du utför en åtkomstgranskning](pim-resource-roles-perform-access-review.md).

När åtkomstgranskningsperioden är över, eller efter att alla användare har avslutat sin självgranskning, följer du stegen i den här artikeln för att hantera granskningen och se resultaten.

## <a name="manage-access-reviews"></a>Hantera åtkomstgranskningar

1. Gå till [Azure-portalen](https://portal.azure.com/). Välj **azure-resurstjänsten** på instrumentpanelen.

2. Välj din resurs.

3. Välj avsnittet **Access-granskningar** på instrumentpanelen.

    ![Azure-resurser – Lista över Access-granskningar som visar roll, ägare, startdatum, slutdatum och status](media/pim-resource-roles-complete-access-review/rbac-access-review-home-list.png)

4. Välj den åtkomstgranskning som du vill hantera.

På detaljsidan för åtkomstgranskningen finns det ett antal alternativ för att hantera den granskningen. Alternativen är följande:

![Alternativ för att hantera en recension - Stoppa, Återställ, Tillämpa, Ta bort](media/pim-resource-roles-complete-access-review/rbac-access-review-menu.png)

### <a name="stop"></a>Stoppa

Alla åtkomstgranskningar har ett slutdatum. Välj **Stoppa** för att avsluta det tidigt. Alla användare som inte har avslutat sin granskning vid den här tiden kommer inte att kunna slutföra den när du har avslutat granskningen. Du kan inte starta om en recension när den har stoppats.

### <a name="reset"></a>Återställ

Du kan återställa en åtkomstgranskning för att ta bort alla beslut som fattas på den. När du har återställt en åtkomstgranskning markeras alla användare som inte granskade igen.

### <a name="apply"></a>Använd

När en åtkomstgranskning har slutförts väljer du **Verkställ** för att implementera resultatet av granskningen. Om en användares åtkomst nekades i granskningen tar det här steget bort deras rolltilldelning.  

### <a name="delete"></a>Ta bort

Om du inte är intresserad av granskningen längre tar du bort den. Välj **Ta bort** yo ta bort granskningen från tjänsten Privilegierad identitetshantering.

## <a name="results"></a>Resultat

På **resultatsidan** kan du visa och hämta en lista över granskningsresultaten.

![Resultatsida med användare, resultat, orsak, granskad av, tillämpad av och tillämpa resultat](media/pim-resource-roles-complete-access-review/rbac-access-review-results.png)

## <a name="reviewers"></a>Granskare

Visa och lägg till granskare i din befintliga åtkomstgranskning. Påminn granskarna om att slutföra sina recensioner.

![Granskare sidlista namn och användarens huvudnamn](media/pim-resource-roles-complete-access-review/rbac-access-review-reviewers.png)

## <a name="next-steps"></a>Nästa steg

- [Starta en åtkomstgranskning för Azure-resursroller i Privilegierad identitetshantering](pim-resource-roles-start-access-review.md)
- [Utföra en åtkomstgranskning av mina Azure-resursroller i Privilegierad identitetshantering](pim-resource-roles-perform-access-review.md)
