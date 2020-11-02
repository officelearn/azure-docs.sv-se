---
title: Registrera dig för Cloudyn i Azure med CSP-partnerinformation
description: Lär dig mer om registreringsprocessen som partner använder för att registrera sina kunder på Cloudyn-portalen.
author: bandersmsft
ms.author: banders
ms.date: 10/23/2020
ms.topic: quickstart
ms.custom: seodec18
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.reviewer: benshy
ROBOTS: NOINDEX
ms.openlocfilehash: 95d8a425314ad1c968915085b35ca89aa3f62711
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92543329"
---
# <a name="register-with-the-csp-partner-program-and-view-cost-data"></a>Registrera dig för CSP-partnerprogrammet och visa kostnadsdata

Som CSP-partner och registrerad Cloudyn-användare kan du visa och analysera dina molnutgifter i Cloudyn. [Azure Cost Management är internt tillgängligt för direkta partner](../costs/get-started-partners.md) som har upprättat ett Microsoft-kundavtal med sina kunder och har köpt en Azure-plan.

Registreringen ger dig åtkomst till Cloudyn-portalen. Den här snabbstarten beskriver registreringsprocessen som krävs för att skapa en utvärderingsprenumeration på Cloudyn och logga in på Cloudyn-portalen.

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="configure-indirect-csp-access-in-cloudyn"></a>Konfigurera indirekt CSP-åtkomst i Cloudyn

Som standard är Partner Center API endast tillgängligt för direkta CSP:er. En direkt CSP-provider kan dock konfigurera åtkomst för indirekta CSP-kunder eller partner med hjälp av entitetsgrupper i Cloudyn.

För att ge åtkomst till indirekta CSP-kunder eller -partner slutför du följande steg för att dela upp indirekta CSP-data med hjälp av entitetsgrupper i Cloudyn. Tilldela sedan lämpliga användarbehörigheter till entitetsgrupperna.

1. Skapa en entitetsgrupp med informationen i [Create entities](tutorial-user-access.md#create-and-manage-entities) (Skapa entiteter).
2. Följ stegen i [Tilldela prenumerationer till kostnadsentiteter](https://www.youtube.com/watch?v=d9uTWSdoQYo) (Tilldela prenumerationer till kostnadsentiteter). Associera den indirekta CSP-kundens konto och Azure-prenumerationer till den entitet som du skapade tidigare.
3. Följ stegen i [Create a user with admin access](tutorial-user-access.md#create-a-user-with-admin-access) (Skapa en användare med administratörsåtkomst) för att skapa ett användarkonto med administratörsåtkomst. Kontrollera sedan att användarkontot har administratörsåtkomst till de specifika entiteter som du skapade tidigare för det indirekta kontot.

Indirekta CSP-partner loggar in på Cloudyn-portalen med hjälp av de konton som du skapar åt dem.


[!INCLUDE [cost-management-create-account-view-data](../../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten använde du din CSP-information för att göra en registrering med Cloudyn. Du loggade också in på Cloudyn-portalen och började visa kostnadsdata. Om du vill veta mer om Cloudyn kan du fortsätta till självstudien för Cloudyn.

> [!div class="nextstepaction"]
> [Granska användning och kostnader](tutorial-review-usage.md)