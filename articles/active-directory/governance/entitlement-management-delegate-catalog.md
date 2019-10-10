---
title: Delegera åtkomst styrning till katalog skapare i Azure AD-hantering av rättigheter (för hands version) – Azure Active Directory
description: Lär dig hur du delegerar åtkomst styrning från IT-administratörer till katalog skapare och projektledare så att de kan hantera åtkomst själva.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/07/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: da6183599f2dacd1a98d8fe359edda741b903b50
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170768"
---
# <a name="delegate-access-governance-to-catalog-creators-in-azure-ad-entitlement-management-preview"></a>Delegera åtkomst styrning till katalog skapare i Azure AD-hantering av rättigheter (för hands version)

> [!IMPORTANT]
> Azure Active Directory (Azure AD) rättighets hantering är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Om du vill delegera till användare som inte är administratörer, så att de kan skapa sina egna kataloger, kan du lägga till dessa användare i rollen Azure AD-rättighets hantering-definierad katalog skapare. Du kan lägga till enskilda användare eller lägga till en grupp, vars medlemmar sedan kan skapa kataloger.

## <a name="as-an-it-administrator-delegate-to-a-catalog-creator"></a>Delegera till en katalog skapare som IT-administratör

Följ dessa steg om du vill tilldela en användare rollen som katalog skapare.

**Nödvändig roll:** Global administratör eller användar administratör

1. Klicka på **Azure Active Directory** i Azure Portal och klicka sedan på **identitets styrning**.

1. På den vänstra menyn i avsnittet **rättighets hantering** klickar du på **Inställningar**.

1. Klicka på **Redigera**.

    ![Inställningar för att lägga till katalog skapare](./media/entitlement-management-delegate/settings-delegate.png)

1. I avsnittet **delegera rättighets hantering** klickar du på **Lägg till katalog skapare** för att välja de användare eller grupper som du vill delegera den här rättighets hanterings rollen till.

1. Klicka på **Välj**.

1. Klicka på **Save** (Spara).

## <a name="next-steps"></a>Nästa steg

- [Skapa och hantera en katalog med resurser](entitlement-management-catalog-create.md)
- [Delegera åtkomst styrning för att komma åt paket hanterare](entitlement-management-delegate-managers.md)

