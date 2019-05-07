---
title: Slutför en åtkomstgranskning i Azure AD-roller i PIM - Azure Active Directory | Microsoft Docs
description: Lär dig hur du slutför en åtkomstgranskning i Azure AD-roller i Azure AD Privileged Identity Management (PIM) och visa resultaten
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 06/06/2017
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: decba8f46432891a44a2b231890da4214bd5b1bb
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65140998"
---
# <a name="complete-an-access-review-of-azure-ad-roles-in-pim"></a>Slutför en åtkomstgranskning i Azure AD-roller i PIM
Privilegierade rolladministratörer kan granska privilegierad åtkomst när en [åtkomstgranskning har startats](pim-how-to-start-security-review.md). Azure Active Directory (Azure AD) Privileged Identity Management (PIM) skickas automatiskt ett e-postmeddelande som uppmanar användarna att granska åtkomsten. Om en användare inte fått ett e-postmeddelande, kan du skicka dem instruktionerna [så här utför du en åtkomstgranskning](pim-how-to-perform-security-review.md).

När åtkomstgranskningsperiod är över, eller alla användare är klar med sin egen granska, följer du stegen i den här artikeln för att hantera granskningen och se resultaten.

## <a name="manage-access-reviews"></a>Hantera åtkomstgranskningar
1. Gå till den [Azure-portalen](https://portal.azure.com/) och välj den **Azure AD Privileged Identity Management** på din instrumentpanel.
2. Välj den **Åtkomstgranskningar** på instrumentpanelen.
3. Välj åtkomstgranskning som du vill hantera.

På bladet för den åtkomstgranskning information om finns det ett antal alternativ för att hantera granskningen.

![PIM-knappar för granskning av åtkomst – skärmbild](./media/pim-how-to-complete-review/PIM_review_buttons.png)

### <a name="remind"></a>Påminn
Om en åtkomstgranskning har konfigurerats så att användarna granska själva den **Påminn** knappen skickar ut ett meddelande. 

### <a name="stop"></a>Stoppa
Alla åtkomstgranskningar har ett slutdatum, men du kan använda den **stoppa** för att slutföra den tidigt. Om användare inte har granskats av den här tiden, kommer inte de att kunna när du stoppa granskningen. Du kan inte starta om en granskning när den har stoppats.

### <a name="apply"></a>Använd
När en åtkomstgranskning är klar, antingen eftersom du har nått slutdatum eller stoppade den manuellt, den **tillämpa** knappen implementerar resultatet av granskningen. Om en användares åtkomst nekades i granskningen, är detta det steg som tar bort sina rolltilldelning.  

### <a name="export"></a>Exportera
Om du vill använda resultatet av åtkomstgranskningen manuellt kan du exportera granskningen. Den **exportera** knappen startar hämta en CSV-fil. Du kan hantera resultaten i Excel och andra program som öppna CSV-filer.

### <a name="delete"></a>Ta bort
Om du inte är intresserad av att granska ytterligare kan du ta bort den. Den **ta bort** knappen tar bort granskningen från PIM-programmet.

> [!IMPORTANT]
> Du kommer inte får en varning innan borttagningen sker, så var noga med att du vill ta bort granskningen. 

## <a name="next-steps"></a>Nästa steg

- [Starta en åtkomstgranskning för Azure AD-roller i PIM](pim-how-to-start-security-review.md)
- [Utför en åtkomstgranskning av Mina Azure AD-roller i PIM](pim-how-to-perform-security-review.md)
