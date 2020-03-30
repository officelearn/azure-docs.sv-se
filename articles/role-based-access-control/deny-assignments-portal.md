---
title: Lista neka tilldelningar för Azure-resurser med Azure-portalen
description: Lär dig hur du listar användare, grupper, tjänsthuvudnamn och hanterade identiteter som har nekats åtkomst till specifika Azure-resursåtgärder vid vissa scope med hjälp av Azure-portalen.
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
ms.openlocfilehash: 4db76e5c6191457346ca1f95678cf73843334d3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77137426"
---
# <a name="list-deny-assignments-for-azure-resources-using-the-azure-portal"></a>Lista neka tilldelningar för Azure-resurser med Azure-portalen

[Neka tilldelningar](deny-assignments.md) blockera användare från att utföra specifika Azure-resursåtgärder även om en rolltilldelning ger dem åtkomst. I den här artikeln beskrivs hur du listar neka tilldelningar med Azure-portalen.

> [!NOTE]
> Du kan inte direkt skapa dina egna neka-tilldelningar. Information om hur neka tilldelningar skapas finns i [Neka tilldelningar](deny-assignments.md).

## <a name="prerequisites"></a>Krav

Om du vill ha information om en neka-tilldelning måste du ha:

- `Microsoft.Authorization/denyAssignments/read`behörighet, som ingår i de flesta [inbyggda roller för Azure-resurser](built-in-roles.md).

## <a name="list-deny-assignments"></a>Visa lista över nekade tilldelningar

Följ dessa steg för att lista neka tilldelningar i prenumerations- eller hanteringsgruppomfånget.

1. I Azure-portalen klickar du på **Alla tjänster** och sedan **hanteringsgrupper** eller **prenumerationer**.

1. Klicka på den hanteringsgrupp eller prenumeration som du vill lista.

1. Klicka på **Åtkomstkontroll (IAM)**.

1. Klicka på fliken **Neka tilldelningar** (eller klicka på knappen **Visa** på panelen Visa neka tilldelningar).

    Om det finns några nekande tilldelningar i det här omfånget eller som ärvts till det här omfånget visas de.

    ![Åtkomstkontroll - fliken Neka tilldelningar](./media/deny-assignments-portal/access-control-deny-assignments.png)

1. Om du vill visa ytterligare kolumner klickar du på **Redigera kolumner**.

    ![Neka tilldelningar - Kolumner](./media/deny-assignments-portal/deny-assignments-columns.png)

    |  |  |
    | --- | --- |
    | **Namn** | Namnet på neka tilldelning. |
    | **Typ av huvudkonto** | Användaren, gruppen, den systemdefinierade gruppen eller tjänstens huvudnamn. |
    | **Nekad**  | Namn på det säkerhetsobjekt som ingår i neka tilldelningen. |
    | **Id** | Unik identifierare för neka tilldelning. |
    | **Uteslutna huvudmän** | Om det finns säkerhetsobjekt som är undantagna från neka tilldelning. |
    | **Gäller inte barn** | Om neka tilldelning ärvs till underscope. |
    | **Systemskyddat** | Om neka-tilldelningen hanteras av Azure. För närvarande alltid Ja. |
    | **Omfång** | Hanteringsgrupp, prenumeration, resursgrupp eller resurs. |

1. Lägg till en bock i något av de aktiverade objekten och klicka sedan på **OK** för att visa de markerade kolumnerna.

## <a name="list-details-about-a-deny-assignment"></a>Lista information om en neka tilldelning

Följ dessa steg för att lista ytterligare information om en neka tilldelning.

1. Öppna fönstret **Neka tilldelningar** enligt beskrivningen i föregående avsnitt.

1. Klicka på namnet neka tilldelning för att öppna bladet **Användare.**

    ![Neka tilldelning - Användare](./media/deny-assignments-portal/deny-assignment-users.png)

    Bladet **Användare** innehåller följande två avsnitt.

    |  |  |
    | --- | --- |
    | **Neka tilldelning gäller för**  | Säkerhetsobjekt som neka tilldelningen gäller. |
    | **Neka tilldelning exkluderar** | Säkerhetsobjekt som är undantagna från neka tilldelning. |

    **Systemdefinierad huvudnamn** representerar alla användare, grupper, tjänsthuvudnamn och hanterade identiteter i en Azure AD-katalog.

1. Om du vill visa en lista över de behörigheter som nekas klickar du på **Nekade behörigheter**.

    ![Neka tilldelning - Nekade behörigheter](./media/deny-assignments-portal/deny-assignment-denied-permissions.png)

    | Åtgärdstyp | Beskrivning |
    | --- | --- |
    | **Åtgärder**  | Nekade ledningsåtgärder. |
    | **NotActions** | Hanteringsåtgärder som inte har uteslutits från den nekade hanteringsåtgärden. |
    | **DataÅtgärder**  | Nekade dataåtgärder. |
    | **NotDataActions** | Dataåtgärder som inte har åtgärdats från den nekade dataåtgärden. |

    I exemplet som visas i föregående skärmbild är följande de gällande behörigheterna:

    - Alla lagringsåtgärder på dataplanet nekas förutom beräkningsåtgärder.

1. Klicka på **Egenskaper**om du vill visa egenskaperna för en neka-tilldelning.

    ![Neka tilldelning - Egenskaper](./media/deny-assignments-portal/deny-assignment-properties.png)

    På bladet **Egenskaper** kan du se neka tilldelningsnamn, ID, beskrivning och omfattning. Växeln **Gäller inte för underordnade** anger om neka-tilldelningen ärvs till underscope. **Systemskyddade** växeln anger om den här neka tilldelningen hanteras av Azure. För närvarande är detta **Ja** i alla fall.

## <a name="next-steps"></a>Nästa steg

* [Förstå neka tilldelningar för Azure-resurser](deny-assignments.md)
* [Lista neka tilldelningar för Azure-resurser med Azure PowerShell](deny-assignments-powershell.md)
