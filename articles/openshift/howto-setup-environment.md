---
title: Konfigurera din utvecklingsmiljö för Azure Red Hat OpenShift | Microsoft Docs
description: Här är förutsättningarna för att arbeta med Microsoft Azure Red Hat OpenShift.
services: openshift
keywords: Red hat openshift ställa in
author: TylerMSFT
ms.author: twhitney
ms.date: 05/06/2019
ms.topic: conceptual
ms.service: openshift
manager: jeconnoc
ms.openlocfilehash: 3c265d6695af7ba1bc5833db59966a626cb29cb9
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65416084"
---
# <a name="set-up-your-azure-red-hat-openshift-dev-environment"></a>Konfigurera din utvecklingsmiljö för Azure Red Hat OpenShift

Om du vill skapa och köra Microsoft Azure Red Hat OpenShift program, måste du:

* Köpa Azure-dator som är reserverade instanser.
* Installera version 2.0.64 (eller högre) av Azure CLI (eller Använd Azure Cloud Shell).
* Registrera dig för den `openshiftmanagedcluster` funktionen och associerade resursprovidrar.
* Skapa en Azure Active Directory (Azure AD)-klient.
* Skapa ett objekt för Azure AD-program.
* Skapa en Azure AD-användare.

Följande instruktioner vägleder dig igenom alla dessa krav.

## <a name="purchase-azure-virtual-machine-reserved-instances"></a>Köpa Azure-dator som är reserverade instanser

Innan du kan använda Azure Red Hat OpenShift, behöver du köpa Azure-dator som är reserverade instanser.

Om du är Azure-kund härs så [köp Azure-dator reserverade instanser](https://aka.ms/openshift/buy). En reservation minskar dina utgifter per förväg betala för fullständigt hanterade Azure-tjänster. Referera till [ *vad är Azure reservationer* ](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations) mer information om reservationer och hur de spara pengar.

Om du inte är Azure-kund [försäljningskontakt](https://aka.ms/openshift/contact-sales) och Fyll i formuläret försäljning längst ned på sidan för att starta processen.

## <a name="install-the-azure-cli"></a>Installera Azure CLI

Azure Red Hat OpenShift kräver version 2.0.64 eller senare av Azure CLI. Om du redan har installerat Azure CLI kan du kontrollera vilken version du har genom att köra:

```bash
az --version
```

Den första raden i utdata har till exempel CLI-version `azure-cli (2.0.64)`.

Här följer instruktioner om [installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) om du behöver en ny installation eller en uppgradering.

Alternativt kan du använda den [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview). När du använder Azure Cloud Shell, måste du markera den **Bash** miljö om du vill följa med i [skapa och hantera ett kluster i Azure Red Hat OpenShift](tutorial-create-cluster.md) självstudieserien.

## <a name="register-providers-and-features"></a>Registrera providers och funktioner

Den `Microsoft.ContainerService openshiftmanagedcluster` funktion, `Microsoft.Solutions`, och `Microsoft.Network` måste vara registrerade providers för din prenumeration manuellt innan du distribuerar ditt första Azure Red Hat OpenShift-kluster.

Om du vill registrera dessa providers och funktioner manuellt, Använd följande instruktioner från ett Bash-gränssnitt om du har installerat CLI eller Azure Cloud Shell (Bash)-session i Azure portal:.
1. Om du har flera Azure-prenumerationer kan du ange relevant prenumerations-ID:

    ```bash
    az account set --subscription <SUBSCRIPTION ID>
    ```

2. Registrera funktionen Microsoft.ContainerService openshiftmanagedcluster:

    ```bash
    az feature register --namespace Microsoft.ContainerService -n openshiftmanagedcluster
    ```

3. Registrera providern Microsoft.Solutions:

    ```bash
    az provider register -n Microsoft.Solutions --wait
    ```

4. Registrera Microsoft.Network-providern:

    ```bash
    az provider register -n Microsoft.Network --wait
    ```

5. Registrera providern Microsoft.KeyVault:

    ```bash
    az provider register -n Microsoft.KeyVault --wait
    ```

6. Uppdatera registreringen av resursprovidern Microsoft.ContainerService:

    ```bash
    az provider register -n Microsoft.ContainerService --wait
    ```

## <a name="create-an-azure-active-directory-azure-ad-tenant"></a>Skapa en Azure Active Directory (Azure AD)-klient

Azure Red Hat OpenShift-tjänsten kräver en tillhörande Azure Active Directory (Azure AD)-klient som representerar din organisation och dess förhållande till Microsoft. Azure AD-klienten kan du registrera, skapa, och hantera appar, samt använda andra Azure-tjänster.

Om du inte har en Azure AD ska användas som klienten för ditt Azure Red Hat OpenShift-kluster, eller om du vill skapa en klient för att testa, följ instruktionerna i [skapa en Azure AD-klient för ditt kluster i Azure Red Hat OpenShift](howto-create-tenant.md) innan Du kan fortsätta med den här handboken.

## <a name="create-an-azure-ad-application-object-and-user"></a>Skapa en Azure AD-objekt för program och användare

Azure Red Hat OpenShift kräver behörighet att utföra uppgifter i ditt kluster, till exempel konfigurera lagring. Dessa behörigheter visas via en [tjänstens huvudnamn](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object) och skapas när du registrerar en Azure AD-program som representerar den arbetsbelastning som du tänker lägga på Azure Red Hat OpenShift. Du kommer också vill skapa en ny Active Directory-användare för att testa appar som körs på Azure Red Hat OpenShift klustret.

Följ instruktionerna i [skapa en Azure AD app-objekt och en användare](howto-aad-app-configuration.md) Generera en klient-hemlighet och autentisering Motringnings-URL för din app om du vill veta hur du skapar ett huvudnamn för tjänsten och skapa en ny Active Directory-användare för testning.

## <a name="next-steps"></a>Nästa steg

Nu är du redo att använda Azure Red Hat OpenShift!

Prova guiden:
> [!div class="nextstepaction"]
> [Skapa ett Azure Red Hat OpenShift-kluster](tutorial-create-cluster.md)

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli