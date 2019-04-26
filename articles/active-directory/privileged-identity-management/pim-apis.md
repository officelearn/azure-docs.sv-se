---
title: 'Microsoft Graph API: er för PIM (förhandsversion) – Azure Active Directory | Microsoft Docs'
description: 'Innehåller information om hur du använder Microsoft Graph API: er för Azure AD Privileged Identity Management (PIM) (förhandsversion).'
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: pim
ms.topic: overview
ms.date: 11/13/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e54ec4049b2b0cd67c148d881a64a40efff438a2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60440474"
---
# <a name="microsoft-graph-apis-for-pim-preview"></a>Microsoft Graph-API:er för PIM (förhandsversion)

För de flesta av de uppgifter du kan utföra i Azure Active Directory (Azure AD) Privileged Identity Management (PIM) med Azure-portalen, kan du också utföra med hjälp av den [Microsoft Graph API: er](https://developer.microsoft.com/graph/docs/concepts/overview). Den här artikeln beskriver några viktiga begrepp vid användning av Microsoft Graph-API:er för PIM. Mer information om Microsoft Graph-API:er finns i [referensen för Azure AD Privileged Identity Management API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/privilegedidentitymanagement_root).

> [!IMPORTANT]
> API:er under /betaversionen i Microsoft Graph är i förhandsversion och kan komma att ändras. Användning av dessa API:er i produktionsprogram stöds inte.

## <a name="required-permissions"></a>Nödvändiga behörigheter

För att anropa Microsoft Graph API:er för PIM måste du ha **en eller flera** av följande behörigheter:

- `Directory.AccessAsUser.All`
- `Directory.Read.All`
- `Directory.ReadWrite.All`
- `PrivilegedAccess.ReadWrite.AzureAD`

### <a name="set-permissions"></a>Ställa in behörigheter

För att program ska kunna anropa Microsoft Graph-API:er för PIM måste de ha nödvändiga behörigheter. Det enklaste sättet att ange nödvändiga behörigheter är att använda [Azure AD-ramverket för medgivande](../develop/consent-framework.md).

### <a name="set-permissions-in-graph-explorer"></a>Ställa in behörigheter i Graph Explorer

Om du använder Graph Explorer för att testa anrop kan du ange behörigheterna i verktyget.

1. Logga in på [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) som global administratör.

1. Klicka på **ändra behörigheter**.

    ![Graph Explorer – ändra behörigheter](./media/pim-apis/graph-explorer.png)

1. Lägg till bockar intill de behörigheter som du vill inkludera. `PrivilegedAccess.ReadWrite.AzureAD` är ännu inte tillgänglig i Graph Explorer.

    ![Graph Explorer – ändra behörigheter](./media/pim-apis/graph-explorer-modify-permissions.png)

1. Klicka på **Ändra behörigheter** för att tillämpa behörighetsändringarna.

## <a name="next-steps"></a>Nästa steg

- [Referens för Azure AD Privileged Identity Management API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/privilegedidentitymanagement_root)
