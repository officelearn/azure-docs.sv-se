---
title: Skapa en åtkomstgranskning i Azure-resursroller i PIM - Azure Active Directory | Microsoft Docs
description: Lär dig hur du skapar en åtkomstgranskning i Azure-resursroller i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 04/29/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 78e4de5bbc56f95c0e903b1dac4e8481373716f3
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65143528"
---
# <a name="create-an-access-review-of-azure-resource-roles-in-pim"></a>Skapa en åtkomstgranskning i Azure-resursroller i PIM

Åtkomst till Privilegierade Azure-resursroller för anställda ändras med tiden. För att minska riskerna med inaktuella rolltilldelningar så bör du regelbundet granska åtkomst. Du kan använda Azure Active Directory (Azure AD) Privileged Identity Management (PIM) för att skapa åtkomstgranskningar för privilegierade roller för Azure-resurs. Du kan också konfigurera återkommande åtkomstgranskningar som utförs automatiskt.

Den här artikeln beskriver hur du skapar en eller flera åtkomstgranskningar för privilegierade Azure-resursroller.

## <a name="prerequisites"></a>Nödvändiga komponenter

- [Privilegierad Rolladministratör](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

## <a name="open-access-reviews"></a>Öppna åtkomstgranskningar

1. Logga in på [Azure-portalen](https://portal.azure.com/) med en användare som är medlem i rollen privilegierad Rolladministratör.

1. Öppna **Azure AD Privileged Identity Management**.

1. I den vänstra menyn klickar du på **Azure-resurser**.

1. Klicka på resursen som du vill hantera, till exempel en prenumerations- eller grupp.

1. Klicka på under hantera, **Åtkomstgranskningar**.

    ![Azure-resurser – åtkomstgranskningar](./media/pim-resource-roles-start-access-review/access-reviews.png)


[!INCLUDE [Privileged Identity Management access reviews](../../../includes/active-directory-privileged-identity-management-access-reviews.md)]


## <a name="start-the-access-review"></a>Starta åtkomstgranskningen

När du har angett inställningarna för en åtkomstgranskning, klickar du på **starta**. Åtkomstgranskningen visas i listan med en indikator för dess status.

![Åtkomstgranskningar lista](./media/pim-resource-roles-start-access-review/access-reviews-list.png)

Som standard skickar Azure AD ett e-postmeddelande till granskare strax efter det att granskningen startar. Om du väljer att inte har Azure AD skickar e-postmeddelandet, måste du meddela granskarna att en åtkomstgranskning väntar dem att slutföra. Visa instruktioner för hur du [granska åtkomst till Azure-resursroller](pim-resource-roles-perform-access-review.md).

## <a name="manage-the-access-review"></a>Hantera åtkomstgranskningen

Du kan följa förloppet när granskarna har slutfört sina granskningar på den **översikt** sidan i åtkomstgranskningen. Ingen behörighet har ändrats i katalogen tills de [granskningen är klar](pim-resource-roles-complete-access-review.md).

![Åtkomstgranskningar pågår](./media/pim-resource-roles-start-access-review/access-review-overview.png)

Om det här är en enstaka granskning efter åtkomstgranskningsperiod är över eller administratören stoppar åtkomstgranskning, Följ stegen i [Slutför en åtkomstgranskning i Azure-resursroller](pim-resource-roles-complete-access-review.md) att se och tillämpa resultaten.  

För att hantera en serie med granskningar, gå till åtkomstgranskningen, och du ska hitta kommande förekomster i schemalagd granskningar och redigera slutdatumet eller Lägg till/ta bort granskare i enlighet med detta.

Baserat på dina val i **vid slutförande-inställningar**, Använd kommer automatiskt utförs efter slutet granskningsdatum eller när du manuellt stoppa granskningen. Status för granskningen kommer att ändras från **slutförd** via mellanliggande tillstånd som **tillämpar** och slutligen till tillstånd **kopplat**. Du kan förvänta att se nekade användare, om sådant finns, tas bort från roller på några få minuter.

## <a name="next-steps"></a>Nästa steg

- [Granska åtkomst till Azure-resursroller](pim-resource-roles-perform-access-review.md)
- [Slutför en åtkomstgranskning i Azure-resursroller](pim-resource-roles-complete-access-review.md)
- [Skapa en åtkomstgranskning i Azure AD-roller](pim-how-to-start-security-review.md)
