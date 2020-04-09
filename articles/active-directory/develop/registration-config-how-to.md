---
title: Konfigurera slutpunkter
description: Så här hittar du autentiseringsslutpunkterna för ett anpassat program som du utvecklar eller registrerar med Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 07/15/2019
ms.author: ryanwi
ms.openlocfilehash: 28d85736019a6fea6d977d813fdc1c9be6429748
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80883278"
---
# <a name="how-to-configure-endpoints"></a>Konfigurera slutpunkter

Du hittar autentiseringsslutpunkterna för ditt program i [Azure-portalen](https://portal.azure.com).

-   Navigera till [Azure-portalen](https://portal.azure.com).

-   Klicka på **Azure Active Directory**i det vänstra navigeringsfönstret .

-   Klicka på **Appregistreringar** och välj **Slutpunkter**.

-   Då öppnas sidan **Slutpunkter,** som listar alla autentiseringsslutpunkter för din klient.

-   Använd den slutpunkt som är specifik för det autentiseringsprotokoll du använder, tillsammans med program-ID:et för att skapa den autentiseringsbegäran som är specifik för ditt program.

## <a name="next-steps"></a>Nästa steg
[Utvecklarhandbok för Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)
