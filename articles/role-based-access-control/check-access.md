---
title: Snabbstart – Visa roller som är tilldelade till en användare med hjälp av Azure-portalen | Microsoft Docs
description: Lär dig hur du visa behörigheter för rollbaserad åtkomstkontroll (RBAC) som tilldelats en användare, grupp, tjänstens huvudnamn eller hanterad identitet med hjälp av Azure-portalen.
services: role-based-access-control
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: quickstart
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 11/30/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: b755dd6223c21084cafea82a1c8857f9f54b03b5
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2018
ms.locfileid: "52641873"
---
# <a name="quickstart-view-roles-assigned-to-a-user-using-the-azure-portal"></a>Snabbstart: Visa roller som är tilldelade till en användare med hjälp av Azure-portalen

Du kan använda bladet **Åtkomstkontroll (IAM)** i [rollbaserad åtkomstkontroll (RBAC)](overview.md) för att visa rolltilldelningarna för flera användare, grupper, tjänsthuvudnamn och hanterade identiteter, men ibland behöver du bara snabbt visa rolltilldelningar för en enskild användare, grupp, tjänstens huvudnamn eller hanterad identitet. Det enklaste sättet att göra det är att använda funktionen **Kontrollera åtkomst** på Azure-portalen.

## <a name="view-role-assignments"></a>Visa rolltilldelningar

Följ dessa steg om du vill visa rolltilldelningar för en enskild användare, grupp, tjänstens huvudnamn eller hanterad identitet i prenumerationsomfånget.

1. I Azure-portalen klickar du på **Alla tjänster** och sedan **Prenumerationer**.

1. Klicka på din prenumeration.

1. Klicka på **Åtkomstkontroll (IAM)**.

1. Klicka på fliken **Kontrollera åtkomst**.

    ![Åtkomstkontroll – fliken Kontrollera åtkomst](./media/check-access/access-control-check-access.png)

1. I listan **Hitta** väljer du den typ av säkerhetsprincip som du vill kontrollera åtkomst för.

1. I sökrutan anger du en sträng för att söka i katalogen efter visningsnamn, e-postadresser eller objektidentifierare.

    ![Välj lista för Kontrollera åtkomst](./media/check-access/check-access-select.png)

1. Klicka på säkerhetsprincipen för att öppna fönsterrutan **tilldelningar**.

    ![fönsterrutan tilldelningar](./media/check-access/check-access-assignments.png)

    I den här fönsterrutan kan du se de roller som tilldelats till den valda säkerhetsprincipen och omfånget. Om det finns några nekande tilldelningar i det här omfånget eller som ärvts till det här omfånget visas de.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: Bevilja åtkomst för en användare med hjälp av RBAC och Azure-portalen](quickstart-assign-role-user-portal.md)
