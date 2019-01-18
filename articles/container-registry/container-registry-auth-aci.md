---
title: Autentisera med Azure Container Registry från Azure Container Instances
description: Lär dig hur du ger tillgång till avbildningar i ditt privata behållarregister med hjälp av en Azure Active Directory-tjänstobjekt från Azure Container Instances.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 04/23/2018
ms.author: danlep
ms.openlocfilehash: 8a2d19a09233e510055e147fa1cf95dd4471768b
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2019
ms.locfileid: "54390667"
---
# <a name="authenticate-with-azure-container-registry-from-azure-container-instances"></a>Autentisera med Azure Container Registry från Azure Container Instances

Du kan använda ett huvudnamn för tjänsten Azure Active Directory (Azure AD) för att ge åtkomst till din privata behållarregister i Azure Container Registry.

I den här artikeln får du lära dig att skapa och konfigurera en Azure AD-tjänstobjekt med *pull* behörigheter till registret. Sedan kan starta du en behållare i Azure Container Instances (ACI) som hämtar dess avbildning från ditt privata register med tjänstens huvudnamn för autentisering.

## <a name="when-to-use-a-service-principal"></a>När du ska använda ett huvudnamn för tjänsten

Du bör använda ett huvudnamn för tjänsten för autentisering från ACI i **fjärradministrerad scenarier**, till exempel program eller tjänster som skapar behållarinstanser i automatiserade eller på annat sätt oövervakat.

Exempel: Om du har ett automatiserat skript som körs varje natt och skapar en [uppgiftsbaserade behållarinstans](../container-instances/container-instances-restart-policy.md) för att bearbeta vissa data kan användas för ett huvudnamn för tjänsten med envägsreplikering behörighet för att autentisera till registret. Du kan rotera autentiseringsuppgifter för tjänstens huvudnamn eller återkalla åtkomsten helt utan att påverka andra tjänster och program.

Tjänstens huvudnamn bör också vara används när registret [administratörsanvändare](container-registry-authentication.md#admin-account) är inaktiverad.

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

## <a name="authenticate-using-the-service-principal"></a>Autentisera med tjänstens huvudnamn

Om du vill starta en behållare i Azure Container Instances via ett huvudnamn för tjänsten, anger du dess ID för `--registry-username`, och dess lösenord för `--registry-password`.

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mycontainerregistry.azurecr.io/myimage:v1 \
    --registry-login-server mycontainerregistry.azurecr.io \
    --registry-username <service-principal-ID> \
    --registry-password <service-principal-password>
```

## <a name="sample-scripts"></a>Exempelskript

Du hittar de föregående exempelskript för Azure CLI på GitHub som och versioner för Azure PowerShell:

* [Azure CLI][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="next-steps"></a>Nästa steg

I följande artiklar innehåller ytterligare information om hur du arbetar med tjänstens huvudnamn och ACR:

* [Azure Container Registry-autentisering med tjänstens huvudnamn](container-registry-auth-service-principal.md)
* [Autentisera med Azure Container Registry från Azure Kubernetes Service (AKS)](container-registry-auth-aks.md)

<!-- IMAGES -->

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
