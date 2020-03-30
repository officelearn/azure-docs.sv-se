---
title: Konfigurera din utvecklingsmiljö för Azure Red Hat OpenShift
description: Här är förutsättningarna för att arbeta med Microsoft Azure Red Hat OpenShift.
keywords: röd hatt openshift setup inrättas
author: jimzim
ms.author: jzim
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: container-service
ms.openlocfilehash: e7396ce9fbed46688d59b582f246e5454d063fb3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477042"
---
# <a name="set-up-your-azure-red-hat-openshift-dev-environment"></a>Konfigurera Azure Red Hat OpenShift-utvecklingsmiljön

Om du vill skapa och köra Microsoft Azure Red Hat OpenShift-program måste du:

* Installera version 2.0.65 (eller högre) av Azure CLI (eller använd Azure Cloud Shell).
* Registrera dig `AROGA` för funktionen och associerade resursleverantörer.
* Skapa en Azure Active Directory-klientorganisation (Azure AD).
* Skapa ett Azure AD-programobjekt.
* Skapa en Azure AD-användare.

Följande instruktioner kommer att gå igenom alla dessa förutsättningar.

## <a name="install-the-azure-cli"></a>Installera Azure CLI

Azure Red Hat OpenShift kräver version 2.0.65 eller senare av Azure CLI. Om du redan har installerat Azure CLI kan du kontrollera vilken version du har genom att köra:

```azurecli
az --version
```

Den första raden av utdata kommer `azure-cli (2.0.65)`att ha CLI-versionen, till exempel .

Här följer instruktioner för [hur du installerar Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) om du behöver en ny installation eller en uppgradering.

Alternativt kan du använda [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview). När du använder Azure Cloud Shell, se till att välja **Bash-miljön** om du planerar att följa med i serien [Skapa och hantera en Azure Red Hat OpenShift-klusterdjälvstudieserie.](tutorial-create-cluster.md)

## <a name="register-providers-and-features"></a>Registrera leverantörer och funktioner

Funktionen `Microsoft.ContainerService AROGA` , `Microsoft.Solutions` `Microsoft.Compute`, `Microsoft.Storage` `Microsoft.KeyVault` , `Microsoft.Network` och leverantörer måste registreras i din prenumeration manuellt innan du distribuerar ditt första Azure Red Hat OpenShift-kluster.

Om du vill registrera dessa leverantörer och funktioner manuellt använder du följande instruktioner från ett Bash-skal om du har installerat CLI- eller Azure Cloud Shell-sessionen (Bash) i din Azure-portal:

1. Om du har flera Azure-prenumerationer anger du relevant prenumerations-ID:

    ```azurecli
    az account set --subscription <SUBSCRIPTION ID>
    ```

1. Registrera funktionen Microsoft.ContainerService AROGA:

    ```azurecli
    az feature register --namespace Microsoft.ContainerService -n AROGA
    ```

1. Registrera Microsoft.Storage-providern:

    ```azurecli
    az provider register -n Microsoft.Storage --wait
    ```
    
1. Registrera Microsoft.Compute-providern:

    ```azurecli
    az provider register -n Microsoft.Compute --wait
    ```

1. Registrera Microsoft.Solutions-leverantören:

    ```azurecli
    az provider register -n Microsoft.Solutions --wait
    ```

1. Registrera Microsoft.Network-leverantören:

    ```azurecli
    az provider register -n Microsoft.Network --wait
    ```

1. Registrera Microsoft.KeyVault-providern:

    ```azurecli
    az provider register -n Microsoft.KeyVault --wait
    ```

1. Uppdatera registreringen av resursprovidern för Microsoft.ContainerService:

    ```azurecli
    az provider register -n Microsoft.ContainerService --wait
    ```

## <a name="create-an-azure-active-directory-azure-ad-tenant"></a>Skapa en Azure Active Directory-klientorganisation (Azure AD)

Azure Red Hat OpenShift-tjänsten kräver en associerad Azure Active Directory -klientorganisation (Azure AD) som representerar din organisation och dess relation till Microsoft. Med din Azure AD-klient kan du registrera, skapa och hantera appar samt använda andra Azure-tjänster.

Om du inte har en Azure AD att använda som klient för ditt Azure Red Hat OpenShift-kluster, eller om du vill skapa en klient för testning, följer du instruktionerna i [Skapa en Azure AD-klient för ditt Azure Red Hat OpenShift-kluster](howto-create-tenant.md) innan du fortsätter med den här guiden.

## <a name="create-an-azure-ad-user-security-group-and-application-object"></a>Skapa en Azure AD-användare, säkerhetsgrupp och programobjekt

Azure Red Hat OpenShift kräver behörigheter för att utföra uppgifter i klustret, till exempel konfigurera lagring. Dessa behörigheter representeras via ett [tjänsthuvudnamn](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object). Du vill också skapa en ny Active Directory-användare för att testa appar som körs på ditt Azure Red Hat OpenShift-kluster.

Följ instruktionerna i [Skapa ett Azure AD-appobjekt och användare](howto-aad-app-configuration.md) för att skapa ett huvudnamn för tjänsten, generera en klienthemlighet och autentiseringsåterrings-URL för din app och skapa en ny Azure AD-säkerhetsgrupp och användare för att komma åt klustret.

## <a name="next-steps"></a>Nästa steg

Du är nu redo att använda Azure Red Hat OpenShift!

Prova självstudien:
> [!div class="nextstepaction"]
> [Skapa ett Azure Red Hat OpenShift-kluster](tutorial-create-cluster.md)

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli
