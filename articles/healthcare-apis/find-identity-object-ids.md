---
title: Hitta identitet objekt-ID för autentisering – Azure API för FHIR
description: Den här artikeln förklarar hur du hittar identitetsobjekt-ID krävs för att konfigurera autentisering för Azure API för FHIR
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: b120b21a89a7105a25ba610402da99f9dce4b7e1
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55824301"
---
# <a name="find-identity-object-ids-for-authentication-configuration"></a>Hitta identitet objekt-ID: N för autentiseringskonfigurationen

I den här artikeln lär du dig att hitta objekt-ID krävs för att konfigurera listan över tillåtna identitetsobjekt-ID för Azure API för FHIR för identitet.

Fullständigt hanterad Azure-API för FHIR&reg; tjänsten är konfigurerad för att tillåta åtkomst endast en fördefinierad lista över identitet objekt-ID: N. När ett program eller en användare försöker komma åt FHIR-API, måste en ägartoken vara angiven. Den här ägartoken har vissa anspråk (fält). För att ge åtkomst till FHIR-API, token måste innehålla rätt utfärdaren (`iss`), målgrupp (`aud`), och objekt-ID (`oid`) från en lista över tillåtna objekt-ID: N. En identitet objekt-ID är antingen objekt-ID för en användare eller tjänstens huvudnamn i Azure Active Directory.

## <a name="configure-list-of-allowed-object-ids"></a>Konfigurera listan över tillåtna objekt-ID

När du skapar ett nytt Azure-API för FHIR-instans kan konfigurera du en lista över tillåtna objekt-ID:

![Konfigurera tillåtna objekt-ID](media/quickstart-paas-portal/configure-allowed-oids.png)

Dessa objekt-ID: N kan antingen vara ID: N för specifika användare eller tjänstens huvudnamn i Azure Active Directory.

## <a name="find-user-object-id-using-powershell"></a>Hitta objekt-ID för användaren med hjälp av PowerShell

Om du har en användare med användarnamnet `myuser@consoso.com`, du kan hitta de `ObjectId` med hjälp av följande PowerShell-kommando:

```azurepowershell-interactive
$(Get-AzureADUser -Filter "UserPrincipalName eq 'myuser@consoso.com'").ObjectId
```

Du kan också använda Azure CLI:

```azurecli-interactive
az ad user show --upn-or-object-id myuser@consoso.com | jq -r .objectId
```

## <a name="find-service-principal-object-id-using-powershell"></a>Hitta tjänstens huvudnamn objekt-ID med hjälp av PowerShell

Anta att du har registrerat en [tjänstklientapp](register-service-azure-ad-client-app.md) och du vill tillåta den här tjänsten åtkomst till Azure-API för FHIR, kan du hitta objekt-ID för klienten tjänstens huvudnamn med följande PowerShell-kommando:

```azurepowershell-interactive
$(Get-AzureADServicePrincipal -Filter "AppId eq 'XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX'").ObjectId
```

där `XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX` är tjänsten klienten program-ID. Du kan också använda den `DisplayName` av tjänstklienten:

```azurepowershell-interactive
$(Get-AzureADServicePrincipal -Filter "DisplayName eq 'testapp'").ObjectId
```

Om du använder Azure CLI kan använda du:

```azurecli-interactive
az ad sp show --id XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX | jq -r .objectId
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du hittar identitetsobjekt-ID krävs för att konfigurera identiteter som ska kunna få åtkomst till en Azure-API för FHIR-instans. Därefter distribuera en fullständigt hanterad Azure-API för FHIR:
 
>[!div class="nextstepaction"]
>[Distribuera öppen källkod FHIR-server](fhir-paas-portal-quickstart.md)