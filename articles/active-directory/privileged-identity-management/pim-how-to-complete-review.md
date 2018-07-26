---
title: Så här slutför du en åtkomstgranskning | Microsoft Docs
description: När du har startat en åtkomstgranskning i Azure AD Privileged Identity Management, lär du dig hur du slutför det och visa resultaten
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: protection
ms.date: 06/06/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: e8aacc95f73294420042549681aff5494dc63cce
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2018
ms.locfileid: "39257818"
---
# <a name="how-to-complete-an-access-review-in-azure-ad-privileged-identity-management"></a>Så här slutför du en åtkomstgranskning i Azure AD Privileged Identity Management
Privilegierade rolladministratörer kan granska privilegierad åtkomst när en [åtkomstgranskning har startats](pim-how-to-start-security-review.md). Azure AD Privileged Identity Management (PIM) skickas automatiskt ett e-postmeddelande som uppmanar användarna att granska åtkomsten. Om en användare inte fått ett e-postmeddelande, kan du skicka dem instruktionerna [så här utför du en åtkomstgranskning](pim-how-to-perform-security-review.md).

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
[!INCLUDE [active-directory-privileged-identity-management-toc](../../../includes/active-directory-privileged-identity-management-toc.md)]
