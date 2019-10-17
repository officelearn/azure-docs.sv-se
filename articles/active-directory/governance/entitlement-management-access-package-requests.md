---
title: Visa och hantera begär Anden för ett Access-paket i Azure AD-hantering av rättigheter (för hands version) – Azure Active Directory
description: Lär dig hur du visar, ombearbeta och avbryter begär Anden för ett Access-paket i Azure Active Directory rättighets hantering (för hands version).
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: f7211fea4781904b9d97428ecf00b57970e4b57d
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72430278"
---
# <a name="view-and-manage-requests-for-an-access-package-in-azure-ad-entitlement-management-preview"></a>Visa och hantera begär Anden för ett Access-paket i Azure AD-hantering av rättigheter (för hands version)

> [!IMPORTANT]
> Azure Active Directory (Azure AD) rättighets hantering är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

I hantering av Azure AD-rättigheter kan du se vem som har begärt åtkomst paket, deras principer och status. Den här artikeln beskriver hur du visar, ombearbeta och avbryter begär Anden för ett åtkomst paket.

## <a name="view-requests"></a>Visa begär Anden

**Nödvändig roll:** Global administratör, användar administratör, katalog ägare eller åtkomst till paket hanteraren

1. Klicka på **Azure Active Directory** i Azure Portal och klicka sedan på **identitets styrning**.

1. I den vänstra menyn klickar du på **åtkomst paket** och öppnar sedan Access-paketet.

1. Klicka på **begär Anden**.

1. Klicka på en enskild begäran om du vill visa mer information.

    ![Lista över begär Anden för ett Access-paket](./media/entitlement-management-access-package-requests/requests-list.png)

## <a name="view-a-requests-delivery-errors"></a>Visa leverans fel för en begäran

**Nödvändig roll:** Global administratör, användar administratör, katalog ägare eller åtkomst till paket hanteraren

1. Klicka på **Azure Active Directory** i Azure Portal och klicka sedan på **identitets styrning**.

1. I den vänstra menyn klickar du på **åtkomst paket** och öppnar sedan Access-paketet.

1. Klicka på **begär Anden**.

1. Välj den begäran som du vill visa.

    Om begäran har några leverans fel kommer status för begäran att **levereras eller** **levereras delvis**.

    Om det finns några leverans fel i förfrågningens informations fönster, finns det ett antal leverans fel.

1. Klicka på antalet för att se alla begärans leverans fel.

## <a name="reprocess-a-request"></a>Ombearbeta en begäran

Om en begäran påträffar ett fel kan du bearbeta begäran igen för att försöka igen. Du kan bara ombearbeta en begäran som har statusen **levererad** eller **delvis levererad** och ett avslutat datum som är mindre än en vecka.

**Nödvändig roll:** Global administratör, användar administratör, katalog ägare eller åtkomst till paket hanteraren

1. Klicka på **Azure Active Directory** i Azure Portal och klicka sedan på **identitets styrning**.

1. I den vänstra menyn klickar du på **åtkomst paket** och öppnar sedan Access-paketet.

1. Klicka på **begär Anden**.

1. Klicka på den begäran som du vill bearbeta igen.

1. I informations fönstret för begäran klickar du på **rebearbeta begäran**.

    ![Ombearbeta en misslyckad begäran](./media/entitlement-management-access-package-requests/reprocess-request.png)

## <a name="cancel-a-pending-request"></a>Avbryt en väntande begäran

Du kan bara avbryta en väntande begäran som ännu inte har levererats eller vars leverans har misslyckats.

**Nödvändig roll:** Global administratör, användar administratör, katalog ägare eller åtkomst till paket hanteraren

1. Klicka på **Azure Active Directory** i Azure Portal och klicka sedan på **identitets styrning**.

1. I den vänstra menyn klickar du på **åtkomst paket** och öppnar sedan Access-paketet.

1. Klicka på **begär Anden**.

1. Klicka på den begäran som du vill avbryta.

1. Klicka på **Avbryt förfrågan**i informations fönstret för begäran.

## <a name="next-steps"></a>Nästa steg

- [Inställningar för ändring av begäran och godkännande för ett Access-paket](entitlement-management-access-package-request-policy.md)
- [Visa, lägga till och ta bort tilldelningar för ett Access-paket](entitlement-management-access-package-assignments.md)