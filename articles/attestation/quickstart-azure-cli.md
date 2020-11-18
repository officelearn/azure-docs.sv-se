---
title: Konfigurera Azure-attestering med Azure CLI
description: Konfigurera och konfigurera en attesterings leverantör med Azure CLI.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: quickstart
ms.date: 11/20/2020
ms.author: mbaldwin
ms.openlocfilehash: e5ffcd56be1ce7427697cd3a75bd7c4232cc3b8a
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94667622"
---
# <a name="quickstart-set-up-azure-attestation-with-azure-cli"></a>Snabb start: Konfigurera Azure-attestering med Azure CLI

Kom igång med Azure-attestering genom att använda Azure CLI för att konfigurera attestering.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="get-started"></a>Kom igång

1. Använd följande kommando för att logga in på Azure:

   ```azurecli
   az login
   ```

1. Om det behövs växlar du till prenumerationen för Azure-attestering:

   ```azurecli
   az account set --subscription 00000000-0000-0000-0000-000000000000
   ```

1. Registrera resurs leverantören Microsoft. attestering i prenumerationen med [AZ Provider register](/cli/azure/provider#az_provider_register) kommando:

   ```azurecli
   az provider register --name Microsoft.Attestation
   ```

   Mer information om Azure Resource providers och hur du konfigurerar och hanterar dem finns i [Azure Resource providers och-typer](../azure-resource-manager/management/resource-providers-and-types.md).

   > [!NOTE]
   > Du behöver bara registrera en resurs leverantör en gång för en prenumeration.

1. Skapa en resurs grupp för attesterings leverantören. Du kan lägga till andra Azure-resurser i samma resurs grupp, inklusive en virtuell dator med en klient program instans. Kör kommandot [AZ Group Create](/cli/azure/group#az_group_create) för att skapa en resurs grupp eller Använd en befintlig resurs grupp:

   ```azurecli
   az group create --name attestationrg --location uksouth
   ```

## <a name="create-and-manage-an-attestation-provider"></a>Skapa och hantera en attesterings leverantör

Följ den här proceduren om du vill skapa och hantera en attesterings leverantör.

1. Kör kommandot [AZ attestering Create](/cli/azure/ext/attestation/attestation#ext_attestation_az_attestation_create) för att skapa en attesterings-provider:

   ```azurecli
   az attestation create --resource-group attestationrg --name attestationProvider --location uksouth \
      --attestation-policy SgxDisableDebugMode --certs-input-path C:\test\policySignersCertificates.pem
   ```

   Parametern **--certifikats-Indataparametern** anger en uppsättning betrodda signerings nycklar. Om du anger ett fil namn för den här parametern, måste attesteringsservern endast konfigureras med principer i signerat JWT-format. Annars kan principen konfigureras i text eller i ett osignerat JWT-format. Information om JWT finns i [grundläggande begrepp](basic-concepts.md). För certifikat exempel, se [exempel på ett signerings certifikat för attesterings princip](policy-signer-examples.md).

1. Kör kommandot [AZ attestering show](/cli/azure/ext/attestation/attestation#ext_attestation_az_attestation_show) för att hämta egenskaper för attesterings leverantör, till exempel status och AttestURI:

   ```azurecli
   az attestation show --resource-group attestationrg --name attestationProvider
   ```

   Det här kommandot visar värden som följande utdata:

   ```output
   Id:/subscriptions/MySubscriptionID/resourceGroups/MyResourceGroup/providers/Microsoft.Attestation/attestationProviders/MyAttestationProvider
   Location: MyLocation
   ResourceGroupName: MyResourceGroup
   Name: MyAttestationProvider
   Status: Ready
   TrustModel: AAD
   AttestUri: https://MyAttestationProvider.us.attest.azure.net
   Tags:
   TagsTable:
   ```

Du kan ta bort en attesterings leverantör med kommandot [AZ attestering](/cli/azure/ext/attestation/attestation#ext_attestation_az_attestation_delete) :

```azurecli
az attestation delete --resource-group attestationrg --name attestationProvider
```

## <a name="policy-management"></a>Principhantering

För att hantera principer kräver en Azure AD-användare följande behörigheter för `Actions` :

- `Microsoft.Attestation/attestationProviders/attestation/read`
- `Microsoft.Attestation/attestationProviders/attestation/write`
- `Microsoft.Attestation/attestationProviders/attestation/delete`

Dessa behörigheter kan tilldelas till en Azure AD-användare via en roll som `Owner` (behörigheter för jokertecken), `Contributor` (behörigheter för jokertecken) eller `Attestation Contributor` (endast vissa behörigheter för Azure-attestering).  

För att kunna läsa principer kräver en Azure AD-användare följande behörighet för `Actions` :

- `Microsoft.Attestation/attestationProviders/attestation/read`

Den här behörigheten kan tilldelas till en Azure AD-användare via en roll som `Reader` (behörighet som jokertecken) eller `Attestation Reader` (endast vissa behörigheter för Azure-attestering).

Använd de kommandon som beskrivs här för att tillhandahålla princip hantering för en attesterings leverantör, en TEE i taget.

Kommandot [AZ attestering policy show](/cli/azure/ext/attestation/attestation/policy#ext_attestation_az_attestation_policy_show) returnerar den aktuella principen för den angivna tee:

```azurecli
az attestation policy show --resource-group attestationrg --name attestationProvider --tee SgxEnclave
```

> [!NOTE]
> Kommandot visar principen i både text-och JWT-format.

Följande typer av TEE stöds:

- `CyResComponent`
- `OpenEnclave`
- `SgxEnclave`
- `VSMEnclave`

Använd kommandot [AZ attestering policy set](/cli/azure/ext/attestation/attestation/policy#ext_attestation_az_attestation_policy_set) för att ange en ny princip för den angivna tee.

```azurecli
az attestation policy set --resource-group attestationrg --name attestationProvider --tee SgxEnclave \
   --new-attestation-policy newAttestationPolicyname
```

Attesterings principen i JWT-format måste innehålla ett anspråk med namnet `AttestationPolicy` . En signerad princip måste signeras med en nyckel som motsvarar något av de befintliga inloggnings certifikaten för principen.

Princip exempel finns i [exempel på en attesterings princip](policy-examples.md).

Kommandot [AZ policy för attestering av princip](/cli/azure/ext/attestation/attestation/policy#ext_attestation_az_attestation_policy_reset) anger en ny princip för den angivna tee.

```azurecli
az attestation policy reset --resource-group attestationrg --name attestationProvider --tee SgxEnclave \
   --policy-jws "eyJhbGciOiJub25lIn0.."
```

## <a name="policy-signer-certificates-management"></a>Hantering av princip inloggnings certifikat

Använd följande kommandon för att hantera princip signerings certifikat för en attesterings leverantör:

```azurecli
az attestation signer list --resource-group attestationrg --name attestationProvider

az attestation signer add --resource-group attestationrg --name attestationProvider \
   --signer "eyAiYWxnIjoiUlMyNTYiLCAie..."

az attestation signer remove --resource-group attestationrg --name attestationProvider \
   --signer "eyAiYWxnIjoiUlMyNTYiLCAie..."
```

Ett princip signerings certifikat är ett signerat JWT med ett anspråk med namnet `maa-policyCertificate` . Värdet för anspråket är ett JWK som innehåller den betrodda signerings nyckeln som ska läggas till. JWT måste vara signerat med en privat nyckel som motsvarar något av de befintliga inloggnings certifikaten för principen. Information om JWT och JWK finns i [grundläggande begrepp](basic-concepts.md).

All semantisk manipulering av principens registrerings certifikat måste göras utanför Azure CLI. Så långt som Azure CLI berörs är det en enkel sträng.

För certifikat exempel, se [exempel på ett signerings certifikat för attesterings princip](policy-signer-examples.md).

## <a name="next-steps"></a>Nästa steg

- [Så här skapar och signerar du en policy för attestering](author-sign-policy.md)
- [Implementera attestering med en SGX-enklaven med hjälp av kod exempel](/samples/browse/?expanded=azure&terms=attestation)
