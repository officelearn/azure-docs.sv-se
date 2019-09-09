---
title: Slutför en åtkomst granskning av Azure AD-roller i PIM-Azure Active Directory | Microsoft Docs
description: Lär dig att slutföra en åtkomst granskning av Azure AD-roller i Azure AD Privileged Identity Management (PIM) och visa resultatet
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 06/06/2017
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e50ccc208219896e89bcc80f40c846f69c759f9b
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804412"
---
# <a name="complete-an-access-review-of-azure-ad-roles-in-pim"></a>Slutför en åtkomst granskning av Azure AD-roller i PIM
Privilegierade roll administratörer kan granska privilegie rad åtkomst när en [åtkomst granskning har startats](pim-how-to-start-security-review.md). Azure Active Directory (Azure AD) Privileged Identity Management (PIM) skickar automatiskt ett e-postmeddelande som efterfrågar användare att granska deras åtkomst. Om en användare inte fick något e-postmeddelande kan du skicka dem till anvisningar i [så här gör du en åtkomst granskning](pim-how-to-perform-security-review.md).

När åtkomst gransknings perioden är över, eller om alla användare har avslutat sin egen granskning, följer du stegen i den här artikeln för att hantera granskningen och se resultatet.

## <a name="manage-access-reviews"></a>Hantera åtkomst granskningar
1. Gå till [Azure Portal](https://portal.azure.com/) och välj **Azure AD Privileged Identity Management** programmet på instrument panelen.
2. Välj avsnittet **åtkomst granskningar** på instrument panelen.
3. Välj den åtkomst granskning som du vill hantera.

På bladet detalj gransknings information finns det ett antal alternativ för att hantera den här granskningen.

![PIM åtkomst gransknings knappar-skärm bild](./media/pim-how-to-complete-review/review-buttons.png)

### <a name="remind"></a>Påminn
Om en åtkomst granskning har kon figurer ATS så att användarna kan granska dem, skickar knappen **Påminn** ut ett meddelande. 

### <a name="stop"></a>Stoppa
Alla åtkomst granskningar har ett slutdatum, men du kan använda **stopp** -knappen för att avsluta den tidigt. Om några användare inte har granskats av den här gången kan de inte göra det efter att du har stoppat granskningen. Du kan inte starta om en granskning när den har stoppats.

### <a name="apply"></a>Använd
När en åtkomst granskning har slutförts måste du, antingen på grund av att du nått slutdatumet eller stoppa den manuellt, **använda knappen Verkställ** för att implementera resultatet av granskningen. Om en användares åtkomst nekades i granskningen är detta det steg som tar bort roll tilldelningen.  

### <a name="export"></a>Exportera
Om du vill tillämpa resultatet av åtkomst granskningen manuellt kan du exportera granskningen. Knappen **Exportera** kommer att börja ladda ned en CSV-fil. Du kan hantera resultatet i Excel eller andra program som öppnar CSV-filer.

### <a name="delete"></a>Ta bort
Om du inte är intresse rad av granskningen tar du bort den. Med knappen **ta bort** tas granskningen bort från PIM-programmet.

> [!IMPORTANT]
> Du får ingen varning innan borttagningen sker, så se till att du vill ta bort den granskningen. 

## <a name="next-steps"></a>Nästa steg

- [Starta en åtkomst granskning för Azure AD-roller i PIM](pim-how-to-start-security-review.md)
- [Utföra en åtkomst granskning av mina Azure AD-roller i PIM](pim-how-to-perform-security-review.md)
