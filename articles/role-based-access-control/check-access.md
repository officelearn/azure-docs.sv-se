---
title: Snabb start – Visa en användares åtkomst till Azure-resurser – Azure RBAC
description: I den här snabb starten får du lära dig hur du visar åtkomsten till en användare eller ett annat säkerhets objekt som har till gång till Azure-resurser med hjälp av Azure Portal och rollbaserad åtkomst kontroll i Azure (Azure RBAC).
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
ms.openlocfilehash: 9be53aa964e75bab0b90495640537fe927a5af0e
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/03/2020
ms.locfileid: "82734169"
---
# <a name="quickstart-view-the-access-a-user-has-to-azure-resources"></a>Snabb start: Visa en användares åtkomst till Azure-resurser

Du kan använda bladet **åtkomst kontroll (IAM)** i [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](overview.md) om du vill visa åtkomsten till en användare eller ett annat säkerhets objekt som har till gång till Azure-resurser. Ibland behöver du dock bara snabbt visa åtkomsten för en enskild användare eller ett annat säkerhetsobjekt. Det enklaste sättet att göra det är att använda funktionen **Kontrollera åtkomst** på Azure-portalen.

## <a name="view-role-assignments"></a>Visa rolltilldelningar

 Du visar åtkomsten för en användare genom att lista användarens rolltilldelningar. Följ dessa steg om du vill visa rolltilldelningar för en enskild användare, grupp, tjänstens huvudnamn eller hanterad identitet i prenumerationsomfånget.

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
> [Självstudie: ge en användare åtkomst till Azure-resurser med hjälp av Azure Portal](quickstart-assign-role-user-portal.md)
