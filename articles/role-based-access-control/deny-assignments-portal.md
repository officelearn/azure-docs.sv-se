---
title: Lista neka tilldelningar för Azure-resurser med Azure portal | Microsoft Docs
description: Lär dig mer om att lista användare, grupper, tjänstens huvudnamn och hanterade identiteter som har nekats åtkomst till specifika Azure-resurs åtgärder på viss scope med hjälp av Azure-portalen.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/10/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 21ffb1a2539a2e724a91dd3b2818270a5e573ef8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67127491"
---
# <a name="list-deny-assignments-for-azure-resources-using-the-azure-portal"></a>Lista neka tilldelningar för Azure-resurser med Azure portal

[Neka tilldelningar](deny-assignments.md) blockera användare från att utföra åtgärder för specifika Azure-resurs, även om en rolltilldelning ger dem åtkomst. Den här artikeln beskriver hur du listar neka tilldelningar med Azure portal.

> [!NOTE]
> Du kan inte direkt skapa en egen neka tilldelningar. Information om hur neka tilldelningar har skapats, finns i [neka tilldelningar](deny-assignments.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill ha information om en neka-tilldelning måste du ha:

- `Microsoft.Authorization/denyAssignments/read` behörigheter, som ingår i de flesta [inbyggda roller för Azure-resurser](built-in-roles.md).

## <a name="list-deny-assignments"></a>Lista neka tilldelningar

Följ dessa steg för att lista neka tilldelningar definitionsområdet prenumerations- eller grupp.

1. I Azure-portalen klickar du på **alla tjänster** och sedan **hanteringsgrupper** eller **prenumerationer**.

1. Klicka på hanteringsgruppen eller prenumerationen som du vill lista.

1. Klicka på **åtkomstkontroll (IAM)** .

1. Klicka på den **neka tilldelningar** fliken (eller klicka på den **visa** knappen i vyn neka tilldelningar panelen).

    Om det finns några nekande tilldelningar i det här omfånget eller som ärvts till det här omfånget visas de.

    ![Åtkomstkontroll – neka tilldelningar](./media/deny-assignments-portal/access-control-deny-assignments.png)

1. Om du vill visa fler kolumner, klickar du på **Redigera kolumner**.

    ![Neka tilldelningar - kolumner](./media/deny-assignments-portal/deny-assignments-columns.png)

    |  |  |
    | --- | --- |
    | **Namn** | Namnet på Neka tilldelningen. |
    | **Typ av huvudkonto** | Användare, grupp, systemdefinierade grupp eller tjänstens huvudnamn. |
    | **Nekad**  | Namnet på det säkerhetsobjekt som ingår i neka tilldelningen. |
    | **Id** | Unik identifierare för neka tilldelningen. |
    | **Exkluderade huvudnamn** | Om det finns säkerhetsobjekt som är undantagna från neka-tilldelning. |
    | **Gäller inte för barn** | Om neka tilldelningen ärvs till subscopes. |
    | **Systemet som skyddas** | Om tilldelningen neka hanteras av Azure. För närvarande alltid Ja. |
    | **Omfång** | Hanteringsgruppen, prenumeration, resursgrupp eller resurs. |

1. Lägg till någon av de Aktivera objekt och klicka sedan på **OK** att visa de markerade kolumnerna.

## <a name="list-details-about-a-deny-assignment"></a>Information om en neka-tilldelning

Följ dessa steg för att visa ytterligare information om en neka-tilldelning.

1. Öppna den **neka tilldelningar** fönstret enligt beskrivningen i föregående avsnitt.

1. Klicka på Neka tilldelningsnamn att öppna den **användare** bladet.

    ![Neka tilldelning - användare](./media/deny-assignments-portal/deny-assignment-users.png)

    Den **användare** bladet innehåller följande två avsnitt.

    |  |  |
    | --- | --- |
    | **Neka tilldelning gäller för**  | Säkerhetsobjekt som neka tilldelningen gäller för. |
    | **Neka tilldelning exkluderar** | Säkerhetsobjekt som är undantagna från neka-tilldelning. |

    **Systemdefinierade huvudnamn** representerar alla användare, grupper, tjänstens huvudnamn och hanterade identiteter i Azure AD-katalog.

1. Klicka för att visa en lista över de behörigheter som nekas **nekad behörighet**.

    ![Neka tilldelning - nekas behörigheter](./media/deny-assignments-portal/deny-assignment-denied-permissions.png)

    | Åtgärdstyp | Beskrivning |
    | --- | --- |
    | **Åtgärder**  | Nekade hanteringsåtgärder. |
    | **NotActions** | Hanteringsåtgärder som undantas från hanteringsaktiviteten nekade. |
    | **DataActions**  | Nekade dataåtgärder. |
    | **NotDataActions** | Dataåtgärder som undantas från nekas data igen. |

    För det här exemplet visas i föregående skärmbild följer gällande behörigheter:

    - All lagring åtgärder på dataplanet nekas undantag för beräkningar.

1. Klicka för att visa egenskaperna för en tilldelning av neka **egenskaper**.

    ![Neka tilldelning - egenskaper](./media/deny-assignments-portal/deny-assignment-properties.png)

    På den **egenskaper** bladet visas neka uppgiftens namn, ID, beskrivning och omfång. Den **gäller inte för underordnade** växeln anger om neka tilldelningen ärvs till subscopes. Den **systemet som skyddas** växeln anger om detta neka tilldelning hanteras av Azure. Detta är för närvarande **Ja** i samtliga fall.

## <a name="next-steps"></a>Nästa steg

* [Förstå neka tilldelningar för Azure-resurser](deny-assignments.md)
* [Lista neka tilldelningar för Azure-resurser med Azure PowerShell](deny-assignments-powershell.md)
