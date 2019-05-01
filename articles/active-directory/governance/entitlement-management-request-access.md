---
title: Begär åtkomst till ett åtkomst-paket i Azure AD-rättigheten hantering (förhandsversion) – Azure Active Directory
description: Lär dig hur du använder portalen Mina åtkomst för att begära åtkomst till en åtkomst-paketet i Azure Active Directory rättigheten hantering (förhandsversion).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/19/2019
ms.author: rolyon
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39a50240b4360c5b4adcd6020c2b80b0f06315f7
ms.sourcegitcommit: 9ad75f83bbf0fc4623b7995794f33bbf823b31c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/26/2019
ms.locfileid: "64541562"
---
# <a name="request-access-to-an-access-package-in-azure-ad-entitlement-management-preview"></a>Begär åtkomst till ett åtkomst-paket i Azure AD rättigheten hantering (förhandsversion)

> [!IMPORTANT]
> Azure Active Directory (Azure AD) rättigheten management är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="sign-in-to-the-my-access-portal"></a>Logga in på portalen Mina åtkomst

Det första steget är att logga in på portalen Mina åtkomst där du kan begära åtkomst till en åtkomst-paketet.

**Nödvändiga roll:** Beställare

1. Leta efter ett e-postmeddelande eller ett meddelande från hanteraren för projektet eller företag som du arbetar med. E-postmeddelandet ska innehålla en länk till åtkomst-paketet som du behöver åtkomst till. Länken börjar med:

    `https://myaccess.microsoft.com`

1. Öppna länken.

1. Logga in på portalen Mina åtkomst.

    Var noga med att du använder ditt organisationskonto. Om du är osäker, kontrollera med chefen projekt eller ditt företag.

## <a name="request-an-access-package"></a>Begär ett åtkomst-paket

När du har hittat åtkomst-paketet i portalen Mina åtkomst, kan du skicka en begäran.

**Nödvändiga roll:** Beställare

1. Klicka på bockmarkeringen för att välja åtkomst-paket.

    ![Min åtkomstportalen - paket för åtkomst](./media/entitlement-management-shared/my-access-access-packages.png)

1. Klicka på **begär åtkomst** att öppna fönstret begäran om åtkomst.

1. Om den **motivering** visas, ange en motivering för att ha tillgång.

1. Om **begäran för specifika perioden?** är aktiva, markerar **Ja** eller **nr**.

1. Om det behövs kan du ange startdatum och slutdatum.

    ![Min åtkomst portal – begär åtkomst](./media/entitlement-management-shared/my-access-request-access.png)

1. När du är klar klickar du på **skicka** skicka din begäran.

1. Klicka på **Förfrågningshistorik** att se en lista över dina önskemål och status.

    Om det åtkomst krävs godkännande, finns begäran nu i tillståndet väntar på godkännande.

## <a name="cancel-a-request"></a>Avbryta en begäran

Om du skickar en förfrågan och begäran gäller fortfarande är i den **väntar på godkännande** tillstånd, kan du avbryta begäran.

**Nödvändiga roll:** Beställare

1. I portalen Mina åtkomst till vänster klickar du på **Förfrågningshistorik** att se en lista över dina önskemål och status.

1. Klicka på den **visa** länken för den begäran som du vill avbryta.

1. Om begäran är fortfarande i den **väntar på godkännande** tillstånd, kan du klicka på **avbrottsbegäran** att avbryta begäran.

    ![Min åtkomstportalen - begäran om att avbryta](./media/entitlement-management-request-access/my-access-cancel-request.png)

1. Klicka på **Förfrågningshistorik** att bekräfta att begäran har avbrutits.

## <a name="select-a-policy"></a>Välj en princip

Om du begär åtkomst till en åtkomst-paket som har flera principer som gäller, kan du bli ombedd att välja en princip. En åtkomst-Pakethanteraren kan till exempel konfigurera ett åtkomst-paket med två principer för två grupper av internt anställda. Den första principen kan tillåta åtkomst för 60 dagar och kräver godkännande. Den andra principen kan tillåta åtkomst till två dagar och inte kräver godkännande. Om du får det här scenariot måste du välja den princip du vill använda.

**Nödvändiga roll:** Beställare

## <a name="next-steps"></a>Nästa steg

- [Godkänn eller avvisa förfrågningar](entitlement-management-request-approve.md)
- [Begär processen och e-postaviseringar](entitlement-management-process.md)
