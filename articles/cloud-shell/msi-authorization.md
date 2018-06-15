---
title: Använd MSI i Azure-molnet Shell | Microsoft Docs
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
ms.openlocfilehash: 99577faf7328dc773a9da5f7c1227aa63600aa0a
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
ms.locfileid: "31517445"
---
# <a name="use-msi-in-azure-cloud-shell"></a>Använd MSI i Azure-molnet Shell

Azure stödjer Cloud auktorisering med hanterade tjänsten identiteter (MSI). Använda detta för att hämta åtkomsttoken för säker kommunikation med Azure-tjänster.

## <a name="about-managed-service-identity-msi"></a>Om hanterade tjänstidentiteten (MSI)
En gemensam utmaning när skapa molnprogram är på ett säkert sätt hantera autentiseringsuppgifterna som måste vara i koden för att autentisera till molntjänster. Du kan behöva autentisera hämtning från Nyckelvalvet för en autentiseringsuppgift som ett skript kan behöva i molnet Shell.

Hanterad Service identitet (MSI) gör att lösa problemet enklare genom att ge en automatiskt hanterade identitet i Azure-tjänster i Azure Active Directory (AD Azure). Du kan använda den här identiteten för att autentisera till alla tjänster som stöder Azure AD-autentisering, inklusive Key Vault utan några autentiseringsuppgifter i koden.

## <a name="acquire-access-token-in-cloud-shell"></a>Skaffa åtkomst-token i molnet Shell

Kör följande kommandon för att ange ditt MSI-åtkomsttoken som en miljövariabel `access_token`.
```
response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The MSI access token is $access_token
```

## <a name="handling-token-expiration"></a>Hantera token upphör att gälla

Lokala MSI-undersystemet cachelagrar token. Därför kan anropa du den så ofta du vill och ett under överföring anrop till Azure AD resultatet bara om:
- en cache-miss inträffar på grund av att inga token i cacheminnet
- token har upphört att gälla

Om du Cachelagra token i koden, bör du vara beredd på att hantera scenarier där resursen anger att token har upphört att gälla.

Om du vill hantera token fel finns i [MSI sida om curling MSI åtkomsttoken](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token#error-handling).

## <a name="next-steps"></a>Nästa steg
[Mer information om MSI](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview)  
[Hämta åtkomsttoken från MSI virtuella datorer](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token)
