---
title: Slutför åtkomstgranskning av Azure AD-roller i PIM - Azure AD | Microsoft-dokument
description: Lär dig hur du slutför en åtkomstgranskning av Azure AD-roller i Azure AD Privileged Identity Management (PIM) och visar resultaten
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe2d85d605b9ee418a5709ddcdb448c56be1d918
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022285"
---
# <a name="complete-an-access-review-of-azure-ad-roles-in-privileged-identity-management"></a>Slutför en åtkomstgranskning av Azure AD-roller i Privilegierad identitetshantering

Privilegierade rolladministratörer kan granska privilegierad åtkomst när en [åtkomstgranskning har startats](pim-how-to-start-security-review.md).  Pim (Privileged Identity Management) skickar automatiskt ett e-postmeddelande till användare i azure Active Directory (Azure AD) som uppmanar dem att granska deras åtkomst. Om en användare inte fick ett e-postmeddelande kan du skicka dem instruktionerna i [hur du utför en åtkomstgranskning](pim-how-to-perform-security-review.md).

När åtkomstgranskningsperioden är över, eller alla användare har avslutat sin självgranskning, följer du stegen i den här artikeln för att hantera granskningen och se resultaten.

## <a name="manage-access-reviews"></a>Hantera åtkomstgranskningar

1. Gå till [Azure-portalen](https://portal.azure.com/) och välj Azure **AD Privileged Identity Management-tjänsten** på instrumentpanelen.
1. Välj avsnittet **Access-granskningar** på instrumentpanelen.
1. Välj den åtkomstgranskning som du vill hantera.

På informationsbladet för åtkomstgranskning finns det ett antal alternativ för att hantera den granskningen.

![Knappar för åtkomstgranskning av privilegierad identitetshantering – skärmbild](./media/pim-how-to-complete-review/review-buttons.png)

### <a name="remind"></a>Påminna

Om en åtkomstgranskning har konfigurerats så att användarna granskar sig själva skickar knappen **Påminn** ett meddelande.

### <a name="stop"></a>Stoppa

Alla åtkomstgranskningar har ett slutdatum, men du kan använda **stoppknappen** för att slutföra det tidigt. Om några användare inte har granskats av den här gången, kommer de inte att kunna när du slutar översynen. Du kan inte starta om en granskning när den har stoppats.

### <a name="apply"></a>Använd

När en åtkomstgranskning har slutförts, antingen för att du har nått slutdatumet eller stoppat det manuellt, implementerar **knappen Använd** resultatet av granskningen. Om en användares åtkomst nekades i granskningen är det här steget som tar bort deras rolltilldelning.  

### <a name="export"></a>Exportera

Om du vill använda resultatet av åtkomstgranskningen manuellt kan du exportera granskningen. Knappen **Exportera** börjar hämta en CSV-fil. Du kan hantera resultaten i Excel eller andra program som öppnar CSV-filer.

### <a name="delete"></a>Ta bort

Om du inte är intresserad av granskningen ytterligare, ta bort den. Knappen **Ta bort** tar bort granskningen från tjänsten Privilegierad identitetshantering.

> [!IMPORTANT]
> Du behöver inte bekräfta den destruktiva ändringen, så kontrollera att du vill ta bort granskningen.

## <a name="next-steps"></a>Nästa steg

- [Starta en åtkomstgranskning för Azure AD-roller i Privilegierad identitetshantering](pim-how-to-start-security-review.md)
- [Utföra en åtkomstgranskning av mina Azure AD-roller i Privilegierad identitetshantering](pim-how-to-perform-security-review.md)
