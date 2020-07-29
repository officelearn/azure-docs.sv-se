---
title: Visa tjänstens huvud namn för en hanterad identitet – Azure CLI – Azure AD
description: Stegvisa instruktioner för att Visa tjänstens huvud namn för en hanterad identitet med Azure CLI.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/29/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 767a0189ad9f55c615dc4bbd30ddfd90522f5761
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85608406"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-azure-cli"></a>Visa tjänstens huvud namn för en hanterad identitet med Azure CLI

Hanterade identiteter för Azure-resurser tillhandahåller Azure-tjänster med en automatiskt hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till en tjänst som stöder Azure AD-autentisering utan att ha autentiseringsuppgifter i din kod. 

I den här artikeln får du lära dig hur du visar tjänstens huvud namn för en hanterad identitet med hjälp av Azure CLI.

## <a name="prerequisites"></a>Krav

- Om du inte känner till hanterade identiteter för Azure-resurser kan du läsa [avsnittet Översikt](overview.md).
- Om du inte redan har ett Azure-konto kan du [Registrera dig för ett kostnads fritt konto](https://azure.microsoft.com/free/).
- Aktivera [systemtilldelad identitet på en virtuell dator eller ett](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) [program](/azure/app-service/overview-managed-identity#add-a-system-assigned-identity).
- Det finns tre alternativ för att köra CLI-skript exempel:
    - Använd [Azure Cloud Shell](../../cloud-shell/overview.md) från Azure Portal (se nästa avsnitt).
    - Använd den inbäddade Azure Cloud Shell via knappen "prova", som finns i det övre högra hörnet av varje kodblock.
    - [Installera den senaste versionen av Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) om du föredrar att använda en lokal CLI-konsol och logga in på Azure med`az login`
 
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="view-the-service-principal"></a>Visa tjänstens huvud namn

Följande kommando visar hur du visar tjänstens huvud namn för en virtuell dator eller ett program med hanterad identitet aktive rad. Ersätt `<VM or application name>` med dina egna värden. 

```azurecli-interactive
az ad sp list --display-name <VM or application name>
```

## <a name="next-steps"></a>Nästa steg

Mer information om hur du hanterar Azure AD-tjänstens huvud namn med Azure CLI finns i [AZ AD SP](/cli/azure/ad/sp).


