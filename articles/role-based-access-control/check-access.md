---
title: Snabbstart – Visa den åtkomst som en användare har till Azure-resurser | Microsoft Docs
description: Lär dig hur du visar den åtkomst som en användare eller ett annat säkerhetsobjekt har till Azure-resurser med hjälp av rollbaserad åtkomstkontroll (RBAC) och Azure-portalen.
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
ms.openlocfilehash: f388215b2829066906ee7faf41abb17307bf3fff
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2019
ms.locfileid: "56337953"
---
# <a name="quickstart-view-the-access-a-user-has-to-azure-resources"></a>Snabbstart: Visa den åtkomst som en användare har till Azure-resurser

Du kan använda bladet **Åtkomstkontroll (IAM)** i [rollbaserad åtkomstkontroll (RBAC)](overview.md) för att visa den åtkomst som en användare eller ett annat säkerhetsobjekt har till Azure-resurser. Ibland behöver du dock bara snabbt visa åtkomsten för en enskild användare eller ett annat säkerhetsobjekt. Det enklaste sättet att göra det är att använda funktionen **Kontrollera åtkomst** på Azure-portalen.

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
> [Självstudier: Bevilja åtkomst för en användare till Azure-resurser med hjälp av RBAC och Azure-portalen](quickstart-assign-role-user-portal.md)
