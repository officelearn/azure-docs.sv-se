---
title: Konfigurera din Azure Red Hat-utvecklings miljö | Microsoft Docs
description: Här är kraven för att arbeta med Microsoft Azure Red Hat OpenShift.
services: openshift
keywords: Red Hat OpenShift-installation konfigureras
author: jimzim
ms.author: jzim
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: container-service
manager: jeconnoc
ms.openlocfilehash: fa09ed90258a62d37dafeea5f4760e1fabdc210b
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73581605"
---
# <a name="set-up-your-azure-red-hat-openshift-dev-environment"></a>Konfigurera Azure Red Hat OpenShift-utvecklingsmiljön

Om du vill skapa och köra Microsoft Azure Red Hat OpenShift-program måste du:

* Installera version 2.0.65 (eller högre) av Azure CLI (eller Använd Azure Cloud Shell).
* Registrera dig för `AROGA` funktionen och tillhör ande resurs leverantörer.
* Skapa en Azure Active Directory-klient (Azure AD).
* Skapa ett Azure AD-programobjekt.
* Skapa en Azure AD-användare.

I följande anvisningar får du stegvisa instruktioner om alla dessa krav.

## <a name="install-the-azure-cli"></a>Installera Azure CLI

Det krävs version 2.0.65 eller senare av Azure CLI i Azure Red Hat OpenShift. Om du redan har installerat Azure CLI kan du kontrol lera vilken version du har genom att köra:

```bash
az --version
```

Den första raden med utdata kommer att ha CLI-versionen, till exempel `azure-cli (2.0.65)`.

Här följer instruktioner för hur du [installerar Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) om du behöver en ny installation eller en uppgradering.

Alternativt kan du använda [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview). När du använder Azure Cloud Shell bör du välja **bash** -miljön om du planerar att följa tillsammans med själv studie serien [skapa och hantera en Azure Red Hat OpenShift-kluster](tutorial-create-cluster.md) .

## <a name="register-providers-and-features"></a>Registrera leverantörer och funktioner

`Microsoft.ContainerService AROGA` funktionen `Microsoft.Solutions`, `Microsoft.Compute`, `Microsoft.Storage`, `Microsoft.KeyVault` och `Microsoft.Network` måste registreras för din prenumeration manuellt innan du distribuerar ditt första Azure Red Hat OpenShift-kluster.

Om du vill registrera dessa providers och funktioner manuellt kan du använda följande instruktioner från ett bash-gränssnitt om du har installerat CLI eller från Azure Cloud Shell-sessionen (bash) i Azure Portal:

1. Om du har flera Azure-prenumerationer anger du det relevanta prenumerations-ID:

    ```bash
    az account set --subscription <SUBSCRIPTION ID>
    ```

1. Registrera funktionen Microsoft. container service AROGA:

    ```bash
    az feature register --namespace Microsoft.ContainerService -n AROGA
    ```

1. Registrera Microsoft. Storage-providern:

    ```bash
    az provider register -n Microsoft.Storage --wait
    ```
    
1. Registrera Microsoft. Compute-providern:

    ```bash
    az provider register -n Microsoft.Compute --wait
    ```

1. Registrera Microsoft. Solutions-providern:

    ```bash
    az provider register -n Microsoft.Solutions --wait
    ```

1. Registrera Microsoft. Network-providern:

    ```bash
    az provider register -n Microsoft.Network --wait
    ```

1. Registrera Microsoft. nyckel valv-providern:

    ```bash
    az provider register -n Microsoft.KeyVault --wait
    ```

1. Uppdatera registreringen av Microsoft. container service-resurs leverantören:

    ```bash
    az provider register -n Microsoft.ContainerService --wait
    ```

## <a name="create-an-azure-active-directory-azure-ad-tenant"></a>Skapa en Azure Active Directory-klient (Azure AD)

Tjänsten Azure Red Hat OpenShift kräver en associerad Azure Active Directory (Azure AD)-klient som representerar din organisation och dess relation till Microsoft. Med din Azure AD-klient kan du registrera, bygga och hantera appar, samt använda andra Azure-tjänster.

Om du inte har en Azure AD att använda som klient för ditt Azure Red Hat OpenShift-kluster, eller om du vill skapa en klient för testning, följer du anvisningarna i [skapa en Azure AD-klient för ditt Azure Red Hat OpenShift-kluster](howto-create-tenant.md) innan du fortsätter med den här guiden .

## <a name="create-an-azure-ad-user-security-group-and-application-object"></a>Skapa en Azure AD-användare, en säkerhets grupp och ett program objekt

För att kunna utföra uppgifter i klustret, till exempel att konfigurera lagring, måste du använda OpenShift i Azure Red Hat. Dessa behörigheter visas via ett [huvud namn för tjänsten](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object). Du vill också skapa en ny Active Directory användare för att testa appar som körs på ditt Azure Red Hat OpenShift-kluster.

Följ instruktionerna i [skapa ett Azure AD-App-objekt och-användare](howto-aad-app-configuration.md) för att skapa ett huvud namn för tjänsten, generera en URL för klient hemlighet och autentiserings-motanrop för din app och skapa en ny Azure AD-säkerhetsgrupp och-användare för att få åtkomst till klustret.

## <a name="next-steps"></a>Nästa steg

Nu är du redo att använda Azure Red Hat OpenShift!

Prova själv studie kursen:
> [!div class="nextstepaction"]
> [Skapa ett Azure Red Hat OpenShift-kluster](tutorial-create-cluster.md)

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli
