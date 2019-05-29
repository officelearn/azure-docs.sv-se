---
title: Självstudie – skapa ett kluster i Azure Red Hat OpenShift | Microsoft Docs
description: Lär dig hur du skapar ett Microsoft Azure Red Hat OpenShift-kluster med Azure CLI
services: container-service
author: jimzim
ms.author: jzim
manager: jeconnoc
ms.topic: tutorial
ms.service: openshift
ms.date: 05/14/2019
ms.openlocfilehash: 651236c25ed912ebd7399d351677a67e3826278c
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66306188"
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
> * [Ta bort ett Azure Red Hat OpenShift-kluster](tutorial-delete-cluster.md)

## <a name="prerequisites"></a>Nödvändiga komponenter

> [!IMPORTANT]
> Den här självstudien kräver version 2.0.65 av Azure CLI.
>    
> Innan du kan använda Azure Red Hat OpenShift, måste du köpa minst 4 noder för reserverade Azure Red Hat OpenShift-programmet enligt beskrivningen i [ställa in din utvecklingsmiljö för Azure Red Hat OpenShift](howto-setup-environment.md#purchase-azure-red-hat-openshift-application-nodes-reserved-instances).

Innan du börjar den här självstudien:

Se till att du har [ställa in din utvecklingsmiljö](howto-setup-environment.md), vilket inkluderar:
- Installera den senaste versionen av CLI (version 2.0.65 eller senare)
- Skapa en klient om du inte redan har ett
- Skapa ett Azure-program-objekt om du inte redan har ett
- Skapa en säkerhetsgrupp
- Skapa en Active Directory-användare att logga in på klustret.

## <a name="step-1-sign-in-to-azure"></a>Steg 1: Logga in till Azure

Om du använder Azure CLI lokalt kan du öppna ett Bash-kommando skal och kör `az login` att logga in på Azure.

```bash
az login
```

 Om du har åtkomst till flera prenumerationer kan köra `az account set -s {subscription ID}` ersätter `{subscription ID}` med den prenumeration som du vill använda.

## <a name="step-2-create-an-azure-red-hat-openshift-cluster"></a>Steg 2: Skapa ett Azure Red Hat OpenShift-kluster

I ett Bash-kommando ställer du in följande variabler:

> [!IMPORTANT]
> Välj ett namn för du klustret som är unik och alla gemener eller ett kluster skapas kommer att misslyckas.

```bash
CLUSTER_NAME=<cluster name in lowercase>
```

Välj en plats för att skapa klustret. En lista över azure-regioner som har stöd för OpenShift på Azure finns i [stöds regioner](supported-resources.md#azure-regions). Till exempel: `LOCATION=eastus`.

```bash
LOCATION=<location>
```

Ange `APPID` till det värde som du sparade i steg 5 i [skapa en Azure AD-appregistrering](howto-aad-app-configuration.md#create-an-azure-ad-app-registration).  

```bash
APPID=<app ID value>
```

Inställd på värdet som du sparade i steg 10 i ”GROUPID' [skapa en Azure AD-säkerhetsgrupp](howto-aad-app-configuration.md#create-an-azure-ad-security-group).

```bash
GROUPID=<group ID value>
```

Ange `SECRET` till det värde som du sparade i steg 8 i [skapa en klienthemlighet](howto-aad-app-configuration.md#create-a-client-secret).  

```bash
SECRET=<secret value>
```

Ange `TENANT` till klient-ID-värdet som du sparade i steg 7 i [skapar en ny klient](howto-create-tenant.md#create-a-new-azure-ad-tenant)  

```bash
TENANT=<tenant ID>
```

Skapa resursgrupp för klustret. Kör följande kommando från samma Bash-gränssnittet som du använde för att definiera variabler ovan:

```bash
az group create --name $CLUSTER_NAME --location $LOCATION
```

### <a name="optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network"></a>Valfritt: Ansluta klustrets virtuella nätverk till ett befintligt virtuellt nätverk

Om du inte behöver ansluta det virtuella nätverket (VNET) i klustret du skapar i ett befintligt VNET via peering kan du hoppa över det här steget.

Först hämta identifierare för det befintliga virtuella nätverket. Identifieraren ska vara i formatet: `/subscriptions/{subscription id}/resourceGroups/{resource group of VNET}/providers/Microsoft.Network/virtualNetworks/{VNET name}`.

Om du inte vet nätverksnamnet eller resursgruppen som det befintliga virtuella nätverket tillhör, går du till den [virtuella nätverksbladet](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Network%2FvirtualNetworks) och klicka på det virtuella nätverket. Sidan virtuella nätverk visas och visar en lista över namnet på nätverket och den resursgrupp som den tillhör.

Definiera en variabel för VNET_ID med hjälp av följande CLI-kommando i ett BASH-gränssnitt:

```bash
VNET_ID=$(az network vnet show -n {VNET name} -g {VNET resource group} --query id -o tsv)
```

Exempel: `VNET_ID=$(az network vnet show -n MyVirtualNetwork -g MyResourceGroup --query id -o tsv`

### <a name="create-the-cluster"></a>Skapa klustret

Du är nu redo att skapa ett kluster. Följande kommer skapa klustret i den angivna Azure AD-klientorganisation, ange Azure AD app-objekt och hemligheten ska användas som ett säkerhetsobjekt och den säkerhetsgrupp som innehåller de medlemmar som har administratörsåtkomst till klustret.

Om du är **inte** peering ditt kluster till ett virtuellt nätverk, använder du följande kommando:

```bash
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --customer-admin-group-id $GROUPID
```

Om du **är** peering ditt kluster till ett virtuellt nätverk, använder du följande kommando som lägger till den `--vnet-peer` flaggan:
 
```bash
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --customer-admin-group-id $GROUPID --vnet-peer $VNET_ID
```

> [!NOTE]
> Om du får ett felmeddelande att värdnamnet inte är tillgänglig kan bero det klusternamnet inte är unikt. Försök ta bort din ursprungliga appregistrering och göra om steg med ett annat klusternamn i [skapa en ny appregistrering] (howto-aad-app-configuration.md#create-a-new-app-registration), om du utesluter steg för att skapa en ny grupp för användare och säkerhetsgrupper.

Efter ett par minuter `az openshift create` slutförs.

### <a name="get-the-sign-in-url-for-your-cluster"></a>Hämta tecken i URL: en för ditt kluster

Hämta URL: en för att logga in på ditt kluster genom att köra följande kommando:

```bash
az openshift show -n $CLUSTER_NAME -g $CLUSTER_NAME
```

Leta efter den `publicHostName` i utdata, till exempel: `"publicHostname": "openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io"`

Logga in URL: en för klustret kommer att `https://` följt av den `publicHostName` värde.  Till exempel: `https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io`.  Du använder den här URI: N i nästa steg som en del av appen registrering omdirigerings-URI.

## <a name="step-3-update-your-app-registration-redirect-uri"></a>Steg 3: Uppdatera din app registrering omdirigerings-URI

Nu när du har tecken i URL: en för klustret, ange app registrering omdirigering Användargränssnittet:

1. Öppna den [registreringar appbladet](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview).
2. Klicka på ditt app-objekt för registrering.
3. Klicka på **lägga till en omdirigerings-URI**.
4. Se till att **typ** är **Web** och ange den **OMDIRIGERINGS-URI** med hjälp av följande mönster: `https://<public host name>/oauth2callback/Azure%20AD`. Exempel: `https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io/oauth2callback/Azure%20AD`
5. Klicka på **Spara**

## <a name="step-4-sign-in-to-the-openshift-console"></a>Steg 4: Logga in på konsolen OpenShift

Nu är du redo att logga in på konsolen OpenShift för det nya klustret. Den [OpenShift webbkonsolen](https://docs.openshift.com/aro/architecture/infrastructure_components/web_console.html) kan du visualisera, bläddra och hantera innehållet i dina OpenShift-projekt.

Du behöver en ny webbläsare-instans som inte har cachelagrade identiteten som du vanligtvis använder för att logga in på Azure-portalen.

1. Öppna en *incognito* fönstret (Chrome) eller *InPrivate* fönstret (Microsoft Edge).
2. Gå till inloggnings-URL: en som du fick ovan, till exempel: `https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io`

Logga in med användarnamnet som du skapade i steg 3 i [skapa en ny Azure Active Directory-användare](howto-aad-app-configuration.md#create-a-new-azure-active-directory-user).

En **behörigheter som begärdes** dialogrutan visas. Klicka på **ge samtycke åt din organisation** och klicka sedan på **acceptera**.

Du är nu inloggad på kluster-konsolen.

![Skärmbild av konsolen OpenShift-kluster](./media/aro-console.png)

 Läs mer om [med hjälp av konsolen OpenShift](https://docs.openshift.com/aro/getting_started/developers_console.html) att skapa och inbyggda avbildningar i den [Red Hat OpenShift](https://docs.openshift.com/aro/welcome/index.html) dokumentation.

## <a name="step-5-install-the-openshift-cli"></a>Steg 5: Installera CLI OpenShift

Den [OpenShift CLI](https://docs.openshift.com/aro/cli_reference/get_started_cli.html) (eller *OC verktyg*) ger kommandon för att hantera dina program och verktyg på lägre nivå för att interagera med olika komponenter i klustret OpenShift.

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
