---
title: Skapa en åtkomst granskning av Azure AD-roller i PIM-Azure Active Directory | Microsoft Docs
description: Lär dig hur du skapar en åtkomst granskning av Azure AD-roller i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/22/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 72d9f0c7df12bd6746cd5cf0336e7bd58b6cc88d
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72809033"
---
# <a name="create-an-access-review-of-azure-ad-roles-in-privileged-identity-management"></a>Skapa en åtkomst granskning av Azure AD-roller i Privileged Identity Management

Krav för användare att få åtkomst till privilegierade Azure Active Directory (Azure AD) roller kan ändras med tiden. För att minska risken för inaktuella roll tilldelningar bör du regelbundet granska åtkomst. Du kan använda Azure AD Privileged Identity Management (PIM) för att skapa åtkomst granskningar för privilegierade Azure AD-roller. Du kan också konfigurera återkommande åtkomst granskningar som sker automatiskt.

Den här artikeln beskriver hur du skapar en eller flera åtkomst granskningar för privilegierade Azure AD-roller.

## <a name="prerequisites"></a>Krav

[Privilegie rad roll administratör](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

## <a name="open-access-reviews"></a>Öppna åtkomst granskningar

1. Logga in på [Azure Portal](https://portal.azure.com/) med en användare som är medlem i rollen privilegie rad roll administratör.

1. Öppna **Azure AD Privileged Identity Management**.

1. Välj **Azure AD-roller** på den vänstra menyn och välj sedan **åtkomst granskningar**.

1. Under hantera väljer du **åtkomst granskningar**.

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
