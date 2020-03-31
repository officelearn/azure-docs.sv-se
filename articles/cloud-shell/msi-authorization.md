---
title: Använda hanterade identiteter för resurser i Azure Cloud Shell
description: Autentisera kod med MSI i Azure Cloud Shell
services: azure
author: maertendMSFT
ms.author: damaerte
tags: azure-resource-manager
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 04/14/2018
ms.openlocfilehash: a5d49a16324a5a97f4a0507f9abf47ea602ea072
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72328719"
---
# <a name="use-managed-identities-for-azure-resources-in-azure-cloud-shell"></a>Använda hanterade identiteter för Azure-resurser i Azure Cloud Shell

Azure Cloud Shell stöder auktorisering med hanterade identiteter för Azure-resurser. Använd detta för att hämta åtkomsttoken för att kommunicera säkert med Azure-tjänster.

## <a name="about-managed-identities-for-azure-resources"></a>Om hanterade identiteter för Azure-resurser
En vanlig utmaning när du skapar molnprogram är hur du på ett säkert sätt hanterar de autentiseringsuppgifter som måste finnas i din kod för att autentisera till molntjänster. I Cloud Shell kan du behöva autentisera hämtning från Key Vault för en autentiseringsuppgifter som ett skript kan behöva.

Hanterade identiteter för Azure-resurser gör det enklare att lösa problemet genom att ge Azure-tjänster en automatiskt hanterad identitet i Azure Active Directory (Azure AD). Du kan använda den här identiteten för att autentisera till alla tjänster som stöder Azure AD-autentisering, inklusive Key Vault, utan att behöva ha några autentiseringsuppgifter i koden.

## <a name="acquire-access-token-in-cloud-shell"></a>Hämta åtkomsttoken i Cloud Shell

Kör följande kommandon för att ange MSI-åtkomsttoken som en miljövariabel. `access_token`
```
response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The MSI access token is $access_token
```

## <a name="handling-token-expiration"></a>Hantering av tokenutgång

Det lokala MSI-undersystemet cachelagrar token. Därför kan du anropa det så ofta du vill, och ett on-the-wire-samtal till Azure AD-resultat endast om:
- en cachemissträffar på grund av att ingen token finns i cacheminnet
- token har upphört att gälla

Om du cachelagrar token i koden bör du vara beredd att hantera scenarier där resursen anger att token har upphört att gälla.

Om du vill hantera tokenfel besöker du [MSI-sidan om curling MSI-åtkomsttoken .](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token#error-handling)

## <a name="next-steps"></a>Nästa steg
[Läs mer om MSI](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview)  
[Skaffa åtkomsttoken från MSI-virtuella datorer](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token)
