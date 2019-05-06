---
title: Självstudie – skapa ett kluster i Azure Red Hat OpenShift | Microsoft Docs
description: Lär dig hur du skapar ett Microsoft Azure Red Hat OpenShift-kluster med Azure CLI
services: container-service
author: TylerMSFT
ms.author: twhitney
manager: jeconnoc
ms.topic: tutorial
ms.service: openshift
ms.date: 05/06/2019
ms.openlocfilehash: 5bc71a2d0f29fed163fb5c93ebd27df7f66a1325
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65080760"
---
# <a name="tutorial-create-an-azure-red-hat-openshift-cluster"></a>Självstudier: Skapa ett Azure Red Hat OpenShift-kluster

Den här självstudien ingår i en serie. Du lär dig att skapa ett Microsoft Azure Red Hat OpenShift-kluster med Azure CLI, skalning och sedan ta bort den för att rensa resurser.

I del ett av serien får du lära dig hur du:

> [!div class="checklist"]
> * Skapa ett Azure Red Hat OpenShift-kluster

I den här självstudieserien får du lära du dig att:
> [!div class="checklist"]
> * Skapa ett Azure Red Hat OpenShift-kluster
> * [Skala ett Azure Red Hat OpenShift-kluster](tutorial-scale-cluster.md)
> * [Ta bort ett kluster i Azure Red Hat OpenShift](tutorial-delete-cluster.md)

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du börjar den här självstudien:

Se till att du har [ställa in din utvecklingsmiljö](howto-setup-environment.md), vilket inkluderar:
- Installera den senaste versionen av CLI
- Skapa en klient
- Skapa ett programobjekt i Azure
- Skapa en Active Directory-användare som används för att logga in på appar som körs i klustret.

## <a name="step-1-sign-in-to-azure"></a>Steg 1: Logga in på Azure

Om du använder Azure CLI lokalt kan du öppna ett Bash-kommando skal och kör `az login` att logga in på Azure.

```bash
az login
```

 Om du har åtkomst till flera prenumerationer kan köra `az account set -s {subscription ID}` ersätter `{subscription ID}` med den prenumeration som du vill använda.

## <a name="step-2-create-an-azure-red-hat-openshift-cluster"></a>Steg 2: Skapa ett Azure Red Hat OpenShift-kluster

I ett Bash-kommando ställer in följande variabler:

> [!IMPORTANT]
> Namnet på klustret måste skrivas med små bokstäver eller skapa ett kluster kommer att misslyckas.

```bash
CLUSTER_NAME=<cluster name in lowercase>
```

 Använd samma namn för det kluster som du valde i steg 6 i [Skapa ny appregistrering](howto-aad-app-configuration.md#create-a-new-app-registration).

```bash
LOCATION=<location>
```

Välj en plats för att skapa klustret. En lista över azure-regioner som har stöd för OpenShift på Azure finns i [stöds regioner](supported-resources.md#azure-regions). Till exempel: `LOCATION=eastus`.

Ange `FQDN` till det fullständigt kvalificerade namnet på klustret. Det här namnet består av klustrets namn, plats, och `.cloudapp.azure.com` läggas till i slutet. Det här är samma som inloggnings-URL som du skapade i steg 6 i [Skapa ny appregistrering](howto-aad-app-configuration.md#create-a-new-app-registration). Exempel:  

```bash
FQDN=$CLUSTER_NAME.$LOCATION.cloudapp.azure.com
```

Ange `APPID` till det värde som du sparade i steg 9 av [skapa en ny appregistrering](howto-aad-app-configuration.md#create-a-new-app-registration).  

```bash
APPID=<app ID value>
```

Ange `SECRET` till det värde som du sparade i steg 6 i [skapa en klienthemlighet](howto-aad-app-configuration.md#create-a-client-secret).  

```bash
SECRET=<secret value>
```

Ange `TENANT` till klient-ID-värdet som du sparade i steg 7 i [skapar en ny klient](howto-create-tenant.md#create-a-new-azure-ad-tenant)  

```bash
TENANT=<tenant ID>
```

Skapa resursgrupp för klustret. Kör följande kommando från Bash-gränssnitt som du använde för att definiera variabler ovan:

```bash
az group create --name $CLUSTER_NAME --location $LOCATION
```

### <a name="optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network"></a>Valfritt: Ansluta klustrets virtuella nätverk till ett befintligt virtuellt nätverk

Om du inte behöver ansluta det virtuella nätverket (VNET) i klustret som du skapar i ett befintligt VNET kan du hoppa över det här steget.

Först hämta identifierare för det befintliga virtuella nätverket. Identifieraren ska vara i formatet: `/subscriptions/{subscription id}/resourceGroups/{resource group of VNET}/providers/Microsoft.Network/virtualNetworks/{VNET name}`.

Om du inte vet nätverksnamnet eller resursgruppen som det befintliga virtuella nätverket tillhör, går du till den [virtuella nätverksbladet](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Network%2FvirtualNetworks) och klicka på det virtuella nätverket. Sidan virtuella nätverk visas och visar en lista över namnet på nätverket och den resursgrupp som den tillhör.

Definiera en variabel för VNET_ID med hjälp av följande CLI-kommando i ett BASH-gränssnitt:

```bash
VNET_ID=$(az network vnet show -n {VNET name} -g {VNET resource group} --query id -o tsv)
```

Exempel: `VNET_ID=$(az network vnet show -n MyVirtualNetwork -g MyResourceGroup --query id -o tsv`

### <a name="create-the-cluster"></a>Skapa klustret

Du är nu redo att skapa ett kluster.

 Om du inte ansluter det virtuella nätverket i klustret till ett befintligt virtuellt nätverk, utelämnar avslutande `--vnet-peer-id $VNET_ID` parameter i följande exempel.

```bash
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --fqdn $FQDN --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --vnet-peer-id $VNET_ID
```

Efter ett par minuter `az openshift create` ska slutföras och returnerar ett JSON-svar som innehåller information om ditt kluster.

> [!NOTE]
> Om du får ett felmeddelande att värdnamnet inte är tillgänglig kan bero det klusternamnet inte är unikt. Försök ta bort din ursprungliga appregistrering och göra om stegen i [skapa en ny appregistrering] (howto-aad-app-configuration.md#create-a-new-app-registration) (utan det sista steget för att skapa en ny användare, eftersom du redan skapat en) med en annat klusternamn.

## <a name="step-3-sign-in-to-the-openshift-console"></a>Steg 3: Logga in på konsolen OpenShift

Du är nu redo att logga in på konsolen OpenShift för det nya klustret. Den [OpenShift webbkonsolen](https://docs.openshift.com/dedicated/architecture/infrastructure_components/web_console.html) kan du visualisera, bläddra och hantera innehållet i dina OpenShift-projekt.

Vi ska logga in som den [nya Azure AD-användare](howto-aad-app-configuration.md#create-a-new-active-directory-user) du skapade för testning. Om du vill göra detta måste en ny webbläsare-instans som inte har cachelagrade identiteten som du vanligtvis använder för att logga in på Azure-portalen.

1. Öppna en *incognito* fönstret (Chrome) eller *InPrivate* fönstret (Microsoft Edge).
2. Gå till inloggnings-URL som du skapade i steg 6 i [skapa en ny appregistrering](howto-aad-app-configuration.md#create-a-new-app-registration). Till exempel, https://constoso.eastus.cloudapp.azure.com

> [!NOTE]
> OpenShift-konsolen använder ett självsignerat certifikat.
> När du uppmanas till detta i din webbläsare kringgå varningen och godkänna ”ej betrodd” certifikatet.

Logga in med användare och lösenord som du skapade i [skapa en ny Active Directory-användare](howto-aad-app-configuration.md#create-a-new-active-directory-user) när den **behörigheter som begärdes** dialogrutan visas, väljer **ge samtycke åt din organisation**  och sedan **acceptera**.

Du är nu inloggad på kluster-konsolen.

[Skärmbild av konsolen OpenShift-kluster](./media/aro-console.png)

 Du kan läsa mer om [med hjälp av konsolen OpenShift](https://docs.openshift.com/dedicated/getting_started/developers_console.html) att skapa och inbyggda avbildningar i den [Red Hat OpenShift](https://docs.openshift.com/dedicated/welcome/index.html) dokumentation.

## <a name="step-4-install-the-openshift-cli"></a>Steg 4: Installera CLI OpenShift

Den [OpenShift CLI](https://docs.openshift.com/dedicated/cli_reference/get_started_cli.html) (eller *OC verktyg*) ger kommandon för att hantera dina program och verktyg på lägre nivå för att interagera med olika komponenter i klustret OpenShift.

Klicka på frågetecknet i det övre högra hörnet av ditt inloggningsnamn i OpenShift-konsolen och välj **kommandoradsverktyg**.  Följ den **senaste versionen** länk för att hämta och installera stöds oc CLI för Linux, MacOS och Windows.

> [!NOTE]
> Om du inte ser frågetecken-ikonen i det övre högra hörnet väljer du *Tjänstkatalogen* eller *Application konsolen* från den övre vänstra listrutan.
>
> Alternativt kan [hämtar oc CLI](https://www.okd.io/download.html) direkt.

Den **kommandoradsverktyg** sidan innehåller ett kommando i formatet `oc login https://<your cluster name>.<azure region>.cloudapp.azure.com --token=<token value>`.  Klicka på den *kopiera till Urklipp* för att kopiera det här kommandot.  I ett terminalfönster [ange sökvägen](https://docs.okd.io/latest/cli_reference/get_started_cli.html#installing-the-cli) att inkludera den lokala installationen av oc-verktyg. Logga sedan in till klustret med oc CLI-kommando som du kopierade.

Om du gick inte att hämta token värdet med hjälp av stegen ovan, token värdet hämtas från: `https://<your cluster name>.<azure region>.cloudapp.azure.com/oauth/token/request`.

## <a name="next-steps"></a>Nästa steg

I den här självstudiedelen lärde du dig att:

> [!div class="checklist"]
> * Skapa ett Azure Red Hat OpenShift-kluster

Gå vidare till nästa kurs:
> [!div class="nextstepaction"]
> [Skala ett Azure Red Hat OpenShift-kluster](tutorial-scale-cluster.md)