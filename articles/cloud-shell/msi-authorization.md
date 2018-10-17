---
title: Använda hanterade identiteter för Azure-resurser i Azure Cloud Shell | Microsoft Docs
description: Autentisera kod med MSI i Azure Cloud Shell
services: azure
documentationcenter: ''
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/14/2018
ms.author: juluk
ms.openlocfilehash: fe77deeedc34bf769065e34ac2f81d631b0004d6
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2018
ms.locfileid: "49352959"
---
# <a name="use-managed-identities-for-azure-resources-in-azure-cloud-shell"></a>Använda hanterade identiteter för Azure-resurser i Azure Cloud Shell

Azure Cloud Shell har stöd för auktorisering med hanterade identiteter för Azure-resurser. Använd detta för att hämta åtkomsttoken för att kommunicera säkert med Azure-tjänster.

## <a name="about-managed-identities-for-azure-resources"></a>Om hanterade identiteter för Azure-resurser
En gemensam utmaning när är att skapa molnprogram på ett säkert sätt hantera autentiseringsuppgifterna som måste vara i din kod för att autentisera till molntjänster. I Cloud Shell kan du behöva autentisera hämtning från Key Vault för en autentiseringsuppgift som kan behöva ett skript.

Hanterade identiteter för Azure-resurser kan lösa problemet enklare genom att ge Azure-tjänster en automatiskt hanterad identitet i Azure Active Directory (AD Azure). Du kan använda den här identiteten för att autentisera till alla tjänster som stöder Azure AD-autentisering, inklusive Key Vault, utan att behöva ha några autentiseringsuppgifter i koden.

## <a name="acquire-access-token-in-cloud-shell"></a>Hämta åtkomsttoken i Cloud Shell

Kör följande kommandon för att ställa in din MSI-åtkomsttoken som en miljövariabel `access_token`.
```
response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The MSI access token is $access_token
```

## <a name="handling-token-expiration"></a>Hantera token upphör att gälla

Lokala MSI-undersystemet cachelagrar token. Därför kan anropa du den så ofta du tycker och ett på ledare anrop till Azure AD resultat bara om:
- en cachemiss inträffar på grund av ingen token i cacheminnet
- token har upphört att gälla

Om du Cachelagra token i koden, bör du vara beredd på att hantera scenarier där resursen indikerar att token har upphört att gälla.

Om du vill hantera token fel finns i [MSI-sidan om curling MSI-åtkomsttoken](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token#error-handling).

## <a name="next-steps"></a>Nästa steg
[Mer information om MSI](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview)  
[Hämta åtkomsttoken från MSI-datorer](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token)
