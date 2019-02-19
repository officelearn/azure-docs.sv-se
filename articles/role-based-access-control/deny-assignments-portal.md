---
title: Visa neka tilldelningar för Azure-resurser med Azure portal | Microsoft Docs
description: Lär dig hur du visar användare, grupper, tjänstens huvudnamn och hanterade identiteter som har nekats åtkomst till specifika Azure-resurs åtgärder i visst omfång med Azure portal.
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
ms.date: 11/30/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: ec5e3daf1d4d799aab043f241548a3b4177f567c
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2019
ms.locfileid: "56343246"
---
# <a name="view-deny-assignments-for-azure-resources-using-the-azure-portal"></a>Visa neka tilldelningar för Azure-resurser med Azure portal

[Neka tilldelningar](deny-assignments.md) blockera användare från att utföra åtgärder för specifika Azure-resurs, även om en rolltilldelning ger dem åtkomst. Även om det går inte att skapa egna neka tilldelningar, du behöver för att kunna visa neka tilldelningar eftersom de kan påverka din övergripande behörigheter. Om du vill ha information om en neka-tilldelning måste du ha den `Microsoft.Authorization/denyAssignments/read` behörigheter, som ingår i de flesta [inbyggda roller för Azure-resurser](built-in-roles.md).

Den här artikeln beskriver hur du använder Azure-portalen för att visa neka tilldelningar.

> [!NOTE]
> Neka tilldelningar är skrivskyddade och kan bara ställas in av Azure för tillfället.

## <a name="view-deny-assignments"></a>Visa neka tilldelningar

Följ anvisningarna nedan om du vill visa neka tilldelningar definitionsområdet prenumerations- eller grupp.

1. I Azure-portalen klickar du på **alla tjänster** och sedan **hanteringsgrupper** eller **prenumerationer**.

1. Klicka på hanteringsgruppen eller prenumerationen som du vill visa.

1. Klicka på **Åtkomstkontroll (IAM)**.

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

## <a name="view-details-about-a-deny-assignment"></a>Visa information om en neka-tilldelning

Följ dessa steg om du vill visa mer information om en neka-tilldelning.

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
* [Lista neka tilldelningar för Azure-resurser med hjälp av REST-API](deny-assignments-rest.md)
