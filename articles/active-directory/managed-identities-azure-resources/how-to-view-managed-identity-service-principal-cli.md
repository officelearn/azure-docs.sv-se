---
title: Visa tjänstens huvudnamn för en hanterad identitet - Azure CLI - Azure AD
description: Steg-för-steg-instruktioner för visning av tjänstens huvudnamn för en hanterad identitet med Azure CLI.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/29/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fedef003c6d4143d3ad1814081d55b61d5ee020
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79298707"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-azure-cli"></a>Visa tjänstens huvudnamn för en hanterad identitet med Azure CLI

Hanterade identiteter för Azure-resurser ger Azure-tjänster en automatiskt hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till alla tjänster som stöder Azure AD-autentisering utan att ha autentiseringsuppgifter i koden. 

I den här artikeln får du lära dig hur du visar tjänstens huvudnamn för en hanterad identitet med Azure CLI.

## <a name="prerequisites"></a>Krav

- Om du inte känner till hanterade identiteter för Azure-resurser kan du läsa [översiktsavsnittet](overview.md).
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/).
- Aktivera [systemtilldelade identitet på en virtuell dator](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) eller ett [virtuellt program](/azure/app-service/overview-managed-identity#add-a-system-assigned-identity).
- Om du vill köra CLI-skriptexemplen har du tre alternativ:
    - Använd [Azure Cloud Shell](../../cloud-shell/overview.md) från Azure-portalen (se nästa avsnitt).
    - Använd det inbäddade Azure Cloud Shell via knappen "Prova", som finns i det övre högra hörnet av varje kodblock.
    - [Installera den senaste versionen av Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) om du föredrar att använda en lokal CLI-konsol och logga in på Azure med`az login`
 
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="view-the-service-principal"></a>Visa tjänstens huvudnamn

Följande kommando visar hur du visar tjänstens huvudnamn för en virtuell dator eller ett program med hanterad identitet aktiverad. Ersätt `<VM or application name>` med dina egna värderingar. 

```azurecli-interactive
az ad sp list --display-name <VM or application name>
```

## <a name="next-steps"></a>Nästa steg

Mer information om hur du hanterar Azure AD-tjänsthuvudnamn med Azure CLI finns i [az ad sp](/cli/azure/ad/sp).


