---
title: Skapa en åtkomstgranskning av Azure AD-roller i PIM - Azure AD | Microsoft-dokument
description: Lär dig hur du skapar en åtkomstgranskning av Azure AD-roller i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/22/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f7e9ef503a9a3469ecbc835be8d9229fbd0167f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847116"
---
# <a name="create-an-access-review-of-azure-ad-roles-in-privileged-identity-management"></a>Skapa en åtkomstgranskning av Azure AD-roller i privilegierad identitetshantering

Om du vill minska risken för inaktuella rolltilldelningar bör du regelbundet granska åtkomsten. Du kan använda Azure AD Privileged Identity Management (PIM) för att skapa åtkomstgranskningar för privilegierade Azure AD-roller. Du kan också konfigurera återkommande åtkomstgranskningar som sker automatiskt.

I den här artikeln beskrivs hur du skapar en eller flera åtkomstgranskningar för privilegierade Azure AD-roller.

## <a name="prerequisites"></a>Krav

[Administratör för privilegierad roll](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

## <a name="open-access-reviews"></a>Öppna åtkomstgranskningar

1. Logga in på [Azure-portalen](https://portal.azure.com/) med en användare som är medlem i rollen Privilegierad rolladministratör.

1. Öppna **Azure AD-privilegierad identitetshantering**.

1. På den vänstra menyn väljer du **Azure AD-roller** och väljer sedan **Access-granskningar**.

1. Under Hantera väljer du **Access-granskningar**.

    ![Azure AD-roller - Lista över Access-granskningar som visar status för alla granskningar](./media/pim-how-to-start-security-review/access-reviews.png)

[!INCLUDE [Privileged Identity Management access reviews](../../../includes/active-directory-privileged-identity-management-access-reviews.md)]

## <a name="start-the-access-review"></a>Starta åtkomstgranskningen

När du har angett inställningarna för en åtkomstgranskning väljer du **Start**. Åtkomstgranskningen visas i listan med en indikator på dess status.

![Lista över Access-granskningar som visar status för startade granskningar](./media/pim-how-to-start-security-review/access-reviews-list.png)

Som standard skickar Azure AD ett e-postmeddelande till granskare strax efter att granskningen har startat. Om du väljer att inte låta Azure AD skicka e-postmeddelandet måste du informera granskarna om att en åtkomstgranskning väntar på att de ska slutföras. Du kan visa dem instruktionerna för hur du [granskar åtkomsten till Azure AD-roller](pim-how-to-perform-security-review.md).

## <a name="manage-the-access-review"></a>Hantera åtkomstgranskningen

Du kan spåra förloppet när granskarna slutför sina granskningar på **sidan Översikt** för åtkomstgranskningen. Inga åtkomsträttigheter ändras i katalogen förrän [granskningen är klar](pim-how-to-complete-review.md).

![Översiktssida för Access recensioner som visar informationen om granskningen](./media/pim-how-to-start-security-review/access-review-overview.png)

Om detta är en engångsgranskning, sedan efter att åtkomstgranskningsperioden är över eller administratören stoppar åtkomstgranskningen, följer du stegen i [Slutför en åtkomstgranskning av Azure AD-roller](pim-how-to-complete-review.md) för att se och tillämpa resultaten.  

Om du vill hantera en serie åtkomstgranskningar navigerar du till åtkomstgranskningen och du hittar kommande förekomster i schemalagda granskningar och redigerar slutdatumet eller lägger till/tar bort granskare därefter.

Baserat på dina val i **Efter slutförandeinställningar**kommer automatisk tillsypning att utföras efter granskningens slutdatum eller när du stoppar granskningen manuellt. Statusen för granskningen ändras från **slutförda** till mellanliggande tillstånd som **Tillämpning** och slutligen till tillstånd **Tillämpad**. Du bör förvänta dig att se nekade användare, om någon, tas bort från roller i några minuter.

## <a name="next-steps"></a>Nästa steg

- [Granska åtkomsten till Azure AD-roller](pim-how-to-perform-security-review.md)
- [Slutför en åtkomstgranskning av Azure AD-roller](pim-how-to-complete-review.md)
- [Skapa en åtkomstgranskning av Azure-resursroller](pim-resource-roles-start-access-review.md)
