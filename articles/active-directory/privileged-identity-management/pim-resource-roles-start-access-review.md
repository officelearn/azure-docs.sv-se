---
title: Skapa en åtkomstgranskning av Azure-resursroller i PIM - Azure AD | Microsoft-dokument
description: Lär dig hur du skapar en åtkomstgranskning av Azure-resursroller i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae70b8386b1dc3ebd570d2651cded3eda75dfc53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847077"
---
# <a name="create-an-access-review-of-azure-resource-roles-in-privileged-identity-management"></a>Skapa en åtkomstgranskning av Azure-resursroller i Privilegierad identitetshantering

Åtkomst till privilegierade Azure-resursroller för medarbetare ändras med tiden. Om du vill minska risken för inaktuella rolltilldelningar bör du regelbundet granska åtkomsten. Du kan använda Azure Active Directory (Azure AD) Privileged Identity Management (PIM) för att skapa åtkomstgranskningar för privilegierade Azure-resursroller. Du kan också konfigurera återkommande åtkomstgranskningar som sker automatiskt.

I den här artikeln beskrivs hur du skapar en eller flera åtkomstgranskningar för privilegierade Azure-resursroller.

## <a name="prerequisites"></a>Krav

[Administratör för privilegierad roll](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

## <a name="open-access-reviews"></a>Öppna åtkomstgranskningar

1. Logga in på [Azure-portalen](https://portal.azure.com/) med en användare som är medlem i rollen Administratör för privilegierad roll.

1. Öppna **Azure AD-privilegierad identitetshantering**.

1. Välj **Azure-resurser**på den vänstra menyn .

1. Välj den resurs som du vill hantera, till exempel en prenumeration eller hanteringsgrupp.

1. Under Hantera väljer du **Access-granskningar**.

    ![Azure-resurser – lista över Access-granskningar som visar status för alla granskningar](./media/pim-resource-roles-start-access-review/access-reviews.png)

[!INCLUDE [Privileged Identity Management access reviews](../../../includes/active-directory-privileged-identity-management-access-reviews.md)]

## <a name="start-the-access-review"></a>Starta åtkomstgranskningen

När du har angett inställningarna för en åtkomstgranskning klickar du på **Start**. Åtkomstgranskningen visas i listan med en indikator på dess status.

![Lista över accessgranskningar som visar status för den startade granskningen](./media/pim-resource-roles-start-access-review/access-reviews-list.png)

Som standard skickar Azure AD ett e-postmeddelande till granskare strax efter att granskningen har startat. Om du väljer att inte låta Azure AD skicka e-postmeddelandet måste du informera granskarna om att en åtkomstgranskning väntar på att de ska slutföras. Du kan visa dem instruktionerna för hur du [granskar åtkomsten till Azure-resursroller](pim-resource-roles-perform-access-review.md).

## <a name="manage-the-access-review"></a>Hantera åtkomstgranskningen

Du kan spåra förloppet när granskarna slutför sina granskningar på **sidan Översikt** för åtkomstgranskningen. Inga åtkomsträttigheter ändras i katalogen förrän [granskningen är klar](pim-resource-roles-complete-access-review.md).

![Översiktssida för Access recensioner som visar informationen om granskningen](./media/pim-resource-roles-start-access-review/access-review-overview.png)

Om detta är en engångsgranskning, sedan efter att åtkomstgranskningsperioden är över eller administratören stoppar åtkomstgranskningen, följer du stegen i [Slutför en åtkomstgranskning av Azure-resursroller](pim-resource-roles-complete-access-review.md) för att se och tillämpa resultaten.  

Om du vill hantera en serie åtkomstgranskningar navigerar du till åtkomstgranskningen och du hittar kommande förekomster i schemalagda granskningar och redigerar slutdatumet eller lägger till/tar bort granskare därefter.

Baserat på dina val i **Efter slutförandeinställningar**kommer automatisk tillsypning att utföras efter granskningens slutdatum eller när du stoppar granskningen manuellt. Statusen för granskningen ändras från **slutförda** till mellanliggande tillstånd som **Tillämpning** och slutligen till tillstånd **Tillämpad**. Du bör förvänta dig att se nekade användare, om någon, tas bort från roller i några minuter.

## <a name="next-steps"></a>Nästa steg

- [Granska åtkomst till Azure-resursroller](pim-resource-roles-perform-access-review.md)
- [Slutför en åtkomstgranskning av Azure-resursroller](pim-resource-roles-complete-access-review.md)
- [Skapa en åtkomstgranskning av Azure AD-roller](pim-how-to-start-security-review.md)
