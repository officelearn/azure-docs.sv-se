---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/06/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 57368331e83111836e13b6c3796cacad9816e318
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521264"
---
Så här visar och kopierar du åtkomstnycklar för lagringskontot eller anslutningssträngen från Azure-portalen:

1. Navigera till [Azure-portalen](https://portal.azure.com).
2. Leta rätt på ditt lagringskonto.
3. Under **Inställningar**väljer du **Åtkomstnycklar**. Åtkomstnycklarna för kontot visas, samt den fullständiga anslutningssträngen för varje nyckel.
4. Sök efter värdet för **Key** (Nyckel) **key1** och klicka på knappen **Kopiera** för att kopiera kontonyckeln.
5. Alternativt kan du kopiera hela anslutningssträngen. Sök efter värdet för **Anslutningssträng** under **key1** och kopiera anslutningssträngen genom att klicka på **Kopiera**.

    ![Skärmbild som visar hur du visar åtkomstnycklar i Azure-portalen](media/storage-view-keys-include/portal-connection-string.png)

Du kan använda båda nycklarna för att komma åt Azure Storage, men i allmänhet är det en bra idé att använda den första nyckeln och reservera användningen av den andra nyckeln för när du roterar nycklar.

Om du vill visa eller läsa ett kontos åtkomstnycklar måste användaren antingen vara tjänstadministratör eller tilldelas en RBAC-roll som innehåller **Microsoft.Storage/storageAccounts/listkeys/action**. Några inbyggda RBAC-roller som innehåller den här åtgärden är rollerna **Ägare,** **Deltagare**och **Nyckeloperatörsroll för lagringskonto.** Mer information om rollen Tjänstadministratör finns i [Klassiska prenumerationsadministratörsroller, Azure RBAC-roller och Azure AD-roller](../articles/role-based-access-control/rbac-and-directory-admin-roles.md). Detaljerad information om inbyggda roller för Azure Storage finns i **avsnittet Lagring** [i Azure-inbyggda roller för Azure RBAC](../articles/role-based-access-control/built-in-roles.md#storage).
