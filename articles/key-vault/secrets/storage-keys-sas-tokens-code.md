---
title: Hämta token för signaturer för delad åtkomst i kod | Azure Key Vault
description: Funktionen hanterat lagrings konto ger en sömlös integrering mellan Azure Key Vault och ett Azure Storage-konto. I det här exemplet används Azure SDK för .NET för att hantera SAS-token.
ms.topic: tutorial
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: afe7d5ce3dd1756ddb9e33fe402fb2eb699ce8f7
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2020
ms.locfileid: "90007425"
---
# <a name="fetch-shared-access-signature-tokens-in-code"></a>Hämta token för signaturer för delad åtkomst i kod

Du kan hantera ditt lagrings konto med SAS-token (signatur för delad åtkomst) som lagras i ditt nyckel valv. Mer information finns i [bevilja begränsad åtkomst till Azure Storage-resurser med hjälp av SAS](../../storage/common/storage-sas-overview.md).

> [!NOTE]
> Vi rekommenderar att du använder [rollbaserad Access Control (RBAC)](../../storage/common/storage-auth-aad.md) för att skydda ditt lagrings konto för överlägsen säkerhet och enkel användning över autentisering med delad nyckel.

Den här artikeln innehåller exempel på .NET-kod som skapar en SAS-definition och hämtar SAS-token. Se vårt [ShareLink](https://docs.microsoft.com/samples/azure/azure-sdk-for-net/share-link/) -exempel för fullständig information, inklusive den genererade klienten för Key Vault hanterade lagrings konton. Information om hur du skapar och lagrar SAS-token finns i [Hantera lagrings konto nycklar med Key Vault och Azure CLI](overview-storage-keys.md) eller [Hantera lagrings konto nycklar med Key Vault och Azure PowerShell](overview-storage-keys-powershell.md).

## <a name="code-samples"></a>Kodexempel

I följande exempel skapar vi en SAS-mall:

:::code language="csharp" source="~/azure-sdk-for-net/sdk/keyvault/samples/sharelink/Program.cs" range="91-97":::

Med den här mallen kan vi skapa en SAS-definition med hjälp av 

:::code language="csharp" source="~/azure-sdk-for-net/sdk/keyvault/samples/sharelink/Program.cs" range="137-156":::

När SAS-definitionen har skapats kan du hämta SAS-token som hemligheter med hjälp av en `SecretClient` . Du måste skriva in det hemliga namnet med lagrings kontots namn följt av ett streck:

:::code language="csharp" source="~/azure-sdk-for-net/sdk/keyvault/samples/sharelink/Program.cs" range="52-58":::

Om signaturens token för delad åtkomst håller på att gå ut kan du hämta samma hemlighet igen för att skapa en ny.

## <a name="next-steps"></a>Nästa steg
- Lär dig hur du [ger begränsad åtkomst till Azure Storage-resurser med hjälp av SAS](../../storage/common/storage-sas-overview.md).
- Lär dig hur du [hanterar lagrings konto nycklar med Key Vault och Azure CLI](overview-storage-keys.md) eller [Azure PowerShell](overview-storage-keys-powershell.md).
- Se det fullständiga [ShareLink](https://docs.microsoft.com/samples/azure/azure-sdk-for-net/share-link/) -exemplet.
- Fler [Key Vaults exempel](https://docs.microsoft.com/samples/browse/?expanded=azure&products=azure-key-vault)
- Se [nyckel exempel för hanterade lagrings konton](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
