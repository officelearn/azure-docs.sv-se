---
title: Åtkomst från behållarinstanser
description: Lär dig hur du ger åtkomst till avbildningar i ditt privata behållarregister från Azure Container Instances med hjälp av ett huvudnamn för Tjänsten Azure Active Directory.
ms.topic: article
ms.date: 04/23/2018
ms.openlocfilehash: b1bc8119c495dea99c6bdc4923db198d041a1e9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74456515"
---
# <a name="authenticate-with-azure-container-registry-from-azure-container-instances"></a>Autentisera med Azure Container Registry från Azure Container Instances

Du kan använda ett Azure Active Directory-tjänsthuvudnamn (Azure AD) för att ge åtkomst till dina privata behållarregister i Azure Container Registry.

I den här artikeln lär du dig att *pull* skapa och konfigurera ett Azure AD-tjänsthuvudnamn med pull-behörigheter till registret. Sedan startar du en behållare i Azure Container Instances (ACI) som hämtar avbildningen från ditt privata register med hjälp av tjänstens huvudnamn för autentisering.

## <a name="when-to-use-a-service-principal"></a>När ska ett tjänsthuvudnamn användas

Du bör använda ett tjänsthuvudnamn för autentisering från ACI i **huvudlösa scenarier,** till exempel i program eller tjänster som skapar behållarinstanser på ett automatiserat eller på annat sätt obevakat sätt.

Om du till exempel har ett automatiserat skript som körs varje kväll och skapar en [uppgiftsbaserad behållarförekomst](../container-instances/container-instances-restart-policy.md) för att bearbeta vissa data, kan det använda ett tjänsthuvudnamn med pull-only-behörigheter för att autentisera till registret. Du kan sedan rotera tjänsthuvudhuvudets autentiseringsuppgifter eller återkalla dess åtkomst helt utan att påverka andra tjänster och program.

Tjänsthuvudnamn bör också användas när [registeradministratörsanvändaren](container-registry-authentication.md#admin-account) är inaktiverad.

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

## <a name="authenticate-using-the-service-principal"></a>Autentisera med tjänstens huvudnamn

Om du vill starta en behållare i Azure Container Instances med hjälp av ett huvudnamn för tjänsten anger du dess ID för `--registry-username`och dess lösenord för `--registry-password`.

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

Du hittar föregående exempelskript för Azure CLI på GitHub, samt versioner för Azure PowerShell:

* [Azure CLI][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="next-steps"></a>Nästa steg

Följande artiklar innehåller ytterligare information om hur du arbetar med tjänstens huvudnamn och ACR:

* [Azure Container Registry autentisering med tjänsthuvudnamn](container-registry-auth-service-principal.md)
* [Autentisera med Azure Container Registry från Azure Kubernetes Service (AKS)](../aks/cluster-container-registry-integration.md)

<!-- IMAGES -->

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
