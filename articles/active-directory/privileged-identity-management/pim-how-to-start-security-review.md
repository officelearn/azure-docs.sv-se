---
title: Skapa en åtkomst granskning av Azure AD-roller i PIM – Azure AD | Microsoft Docs
description: Lär dig hur du skapar en åtkomst granskning av Azure AD-roller i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 10/22/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b3070a1296b368ea2c56038ec696942416a6fe2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84743889"
---
# <a name="create-an-access-review-of-azure-ad-roles-in-privileged-identity-management"></a>Skapa en åtkomst granskning av Azure AD-roller i Privileged Identity Management

För att minska risken för inaktuella roll tilldelningar bör du regelbundet granska åtkomst. Du kan använda Azure AD Privileged Identity Management (PIM) för att skapa åtkomst granskningar för privilegierade Azure AD-roller. Du kan också konfigurera återkommande åtkomst granskningar som sker automatiskt.

Den här artikeln beskriver hur du skapar en eller flera åtkomst granskningar för privilegierade Azure AD-roller.

## <a name="prerequisites"></a>Krav

[Privilegie rad roll administratör](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

## <a name="open-access-reviews"></a>Öppna åtkomst granskningar

1. Logga in på [Azure Portal](https://portal.azure.com/) med en användare som är medlem i rollen privilegie rad roll administratör.

1. Öppna **Azure AD Privileged Identity Management**.

1. Välj **Azure AD-roller**.

1. Under hantera, Välj **åtkomst granskningar**och välj sedan **ny**.

    ![Azure AD-roller – åtkomst gransknings listan visar status för alla granskningar](./media/pim-how-to-start-security-review/access-reviews.png)

[!INCLUDE [Privileged Identity Management access reviews](../../../includes/active-directory-privileged-identity-management-access-reviews.md)]

## <a name="start-the-access-review"></a>Starta åtkomst granskningen

När du har angett inställningarna för åtkomst granskning väljer du **Starta**. Åtkomst granskningen visas i listan med en indikator för dess status.

![Listan åtkomst granskningar som visar statusen för startade granskningar](./media/pim-how-to-start-security-review/access-reviews-list.png)

Som standard skickar Azure AD ett e-postmeddelande till granskare strax efter att granskningen startar. Om du väljer att inte låta Azure AD skicka e-postmeddelandet måste du meddela granskarna att en åtkomst granskning väntar på att de ska slutföras. Du kan visa dem i anvisningarna för hur du [granskar åtkomst till Azure AD-roller](pim-how-to-perform-security-review.md).

## <a name="manage-the-access-review"></a>Hantera åtkomst granskningen

Du kan följa förloppet när granskarna har slutfört sina granskningar på **översikts** sidan för åtkomst granskningen. Ingen åtkomst behörighet har ändrats i katalogen förrän [granskningen har slutförts](pim-how-to-complete-review.md).

![Översikts sidan åtkomst granskningar som visar information om granskningen](./media/pim-how-to-start-security-review/access-review-overview.png)

Om det här är en engångs granskning kan du efter att åtkomst gransknings perioden är över eller administratören stoppar åtkomst granskningen genom att följa stegen i [slutföra en åtkomst granskning av Azure AD-roller](pim-how-to-complete-review.md) för att se och tillämpa resultaten.  

Om du vill hantera en serie åtkomst granskningar navigerar du till åtkomst granskningen och du hittar kommande förekomster i schemalagda granskningar och redigerar slutdatumet eller lägger till/tar bort granskare i enlighet med detta.

Baserat på dina val i **när inställningarna för slut för ande slutförs**, utförs automatiskt tillämpande efter slutdatum för granskningen eller när du stoppar granskningen manuellt. Status för granskningen ändras från **slutförd** till mellanliggande tillstånd som att **tillämpa** och slutligen tillämpa **tillstånd.** Du bör förvänta dig att se till att nekade användare, om de finns, tas bort från roller på några minuter.

## <a name="next-steps"></a>Nästa steg

- [Granska åtkomst till Azure AD-roller](pim-how-to-perform-security-review.md)
- [Slutför en åtkomst granskning av Azure AD-roller](pim-how-to-complete-review.md)
- [Skapa en åtkomst granskning av Azures resurs roller](pim-resource-roles-start-access-review.md)
