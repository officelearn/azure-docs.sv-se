---
title: Självstudiekurs - Skapa ett Azure Red Hat OpenShift-kluster
description: Lär dig hur du skapar ett Microsoft Azure Red Hat OpenShift-kluster med Azure CLI
author: jimzim
ms.author: jzim
ms.topic: tutorial
ms.service: container-service
ms.date: 11/04/2019
ms.openlocfilehash: 58fc695707995aafe4d804ffab8beee7c52b4320
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79455306"
---
# <a name="tutorial-create-an-azure-red-hat-openshift-cluster"></a>Självstudiekurs: Skapa ett Azure Red Hat OpenShift-kluster

Den här självstudien ingår i en serie. Du får lära dig hur du skapar ett Microsoft Azure Red Hat OpenShift-kluster med Azure CLI, skala det och sedan ta bort det för att rensa resurser.

I del ett av serien får du lära dig att:

> [!div class="checklist"]
> * Skapa ett Azure Red Hat OpenShift-kluster

I den här självstudieserien får du lära du dig att:
> [!div class="checklist"]
> * Skapa ett Azure Red Hat OpenShift-kluster
> * [Skala ett Azure Red Hat OpenShift-kluster](tutorial-scale-cluster.md)
> * [Ta bort ett Azure Red Hat OpenShift-kluster](tutorial-delete-cluster.md)

## <a name="prerequisites"></a>Krav

> [!IMPORTANT]
> Den här självstudien kräver version 2.0.65 av Azure CLI.

Innan du börjar den här självstudien:

Kontrollera att du har [konfigurerat utvecklingsmiljön](howto-setup-environment.md), som omfattar:
- Installera den senaste CLI (version 2.0.65 eller högre)
- Skapa en klient om du inte redan har en
- Skapa ett Azure Application-objekt om du inte redan har ett
- Skapa en säkerhetsgrupp
- Skapa en Active Directory-användare för att logga in i klustret.

## <a name="step-1-sign-in-to-azure"></a>Steg 1: Logga in på Azure

Om du kör Azure CLI lokalt öppnar du ett `az login` Bash-kommandoskal och kör för att logga in på Azure.

```azurecli
az login
```

 Om du har åtkomst till `az account set -s {subscription ID}` flera `{subscription ID}` prenumerationer kör du ersättning med den prenumeration du vill använda.

## <a name="step-2-create-an-azure-red-hat-openshift-cluster"></a>Steg 2: Skapa ett Azure Red Hat OpenShift-kluster

I ett bash-kommandofönster ställer du in följande variabler:

> [!IMPORTANT]
> Välj ett namn för dig kluster som är unik och alla gemener eller kluster skapande misslyckas.

```bash
CLUSTER_NAME=<cluster name in lowercase>
```

Välj en plats för att skapa klustret. En lista över azure-regioner som stöder OpenShift på Azure finns i [Regioner som stöds](supported-resources.md#azure-regions). Till exempel: `LOCATION=eastus`.

```bash
LOCATION=<location>
```

Ange `APPID` till det värde som du sparade i steg 5 i [Skapa en Azure AD-appregistrering](howto-aad-app-configuration.md#create-an-azure-ad-app-registration).

```bash
APPID=<app ID value>
```

Ange "GROUPID" till det värde som du sparade i steg 10 i [Skapa en Azure AD-säkerhetsgrupp](howto-aad-app-configuration.md#create-an-azure-ad-security-group).

```bash
GROUPID=<group ID value>
```

Ange `SECRET` värdet som du sparade i steg 8 i [Skapa en klienthemlighet](howto-aad-app-configuration.md#create-a-client-secret).

```bash
SECRET=<secret value>
```

Ange `TENANT` till klient-ID-värdet som du sparade i steg 7 [i Skapa en ny klientorganisation](howto-create-tenant.md#create-a-new-azure-ad-tenant)

```bash
TENANT=<tenant ID>
```

Skapa resursgruppen för klustret. Kör följande kommando från samma Bash-skal som du använde för att definiera variablerna ovan:

```azurecli
az group create --name $CLUSTER_NAME --location $LOCATION
```

### <a name="optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network"></a>Valfritt: Anslut klustrets virtuella nätverk till ett befintligt virtuellt nätverk

Om du inte behöver ansluta det virtuella nätverket (VNET) för klustret som du skapar till ett befintligt virtuellt nätverk via peering hoppar du över det här steget.

Om peering till ett nätverk utanför standardprenumerationen då i den prenumerationen, måste du också registrera leverantören Microsoft.ContainerService. Det gör du genom att köra kommandot nedan i den prenumerationen. Annars, om det virtuella nätverk du peering finns i samma prenumeration, kan du hoppa över registreringssteget.

`az provider register -n Microsoft.ContainerService --wait`

Hämta först identifieraren för det befintliga virtuella nätverket. Identifieraren kommer att vara `/subscriptions/{subscription id}/resourceGroups/{resource group of VNET}/providers/Microsoft.Network/virtualNetworks/{VNET name}`av formuläret: .

Om du inte känner till nätverksnamnet eller resursgruppen som det befintliga virtuella nätverket tillhör går du till [bladet virtuella nätverk](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Network%2FvirtualNetworks) och klickar på det virtuella nätverket. Sidan Virtuellt nätverk visas och visar namnet på nätverket och den resursgrupp det tillhör.

Definiera en VNET_ID variabel med följande CLI-kommando i ett BASH-skal:

```azurecli
VNET_ID=$(az network vnet show -n {VNET name} -g {VNET resource group} --query id -o tsv)
```

Exempel: `VNET_ID=$(az network vnet show -n MyVirtualNetwork -g MyResourceGroup --query id -o tsv`

### <a name="optional-connect-the-cluster-to-azure-monitoring"></a>Valfritt: Anslut klustret till Azure-övervakning

Hämta först identifieraren för den **befintliga** logganalysarbetsytan. Identifieraren kommer att vara av formuläret:

`/subscriptions/{subscription}/resourceGroups/{resourcegroup}/providers/Microsoft.OperationalInsights/workspaces/{workspace-id}`.

Om du inte känner till namnet på logganalysarbetsytan eller om resursgruppen som den befintliga logganalysarbetsytan tillhör går du till [log-analytics-arbetsytan](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.OperationalInsights%2Fworkspaces) och klickar på dina logganalysarbetsytor. Arbetsytan för logganalys visas och listar namnet på arbetsytan och den resursgrupp den tillhör.

_Så här skapar du en logganalysarbetsyta [se Skapa logganalysarbetsyta](../azure-monitor/learn/quick-create-workspace-cli.md)_

Definiera en WORKSPACE_ID variabel med följande CLI-kommando i ett BASH-skal:

```azurecli
WORKSPACE_ID=$(az monitor log-analytics workspace show -g {RESOURCE_GROUP} -n {NAME} --query id -o tsv)
```

### <a name="create-the-cluster"></a>Skapa klustret

Du är nu redo att skapa ett kluster. Följande kommer att skapa klustret i den angivna Azure AD-klienten, ange Azure AD-appobjektet och hemligheten att använda som säkerhetsobjekt och säkerhetsgruppen som innehåller de medlemmar som har administratörsåtkomst till klustret.

> [!IMPORTANT]
> Kontrollera att du har lagt till rätt behörigheter för Azure AD-appen som [beskrivs här](howto-aad-app-configuration.md#add-api-permissions) innan du skapar klustret

Om du **inte** peering ditt kluster till ett virtuellt nätverk eller **inte** vill Azure Övervakning, använd följande kommando:

```azurecli
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --customer-admin-group-id $GROUPID
```

Om **du** peering klustret till ett virtuellt nätverk, använd följande kommando som lägger till `--vnet-peer` flaggan:

```azurecli
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --customer-admin-group-id $GROUPID --vnet-peer $VNET_ID
```

Om du **vill** ha Azure Monitoring med klustret använder du följande kommando som lägger till `--workspace-id` flaggan:

```azurecli
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --customer-admin-group-id $GROUPID --workspace-id $WORKSPACE_ID
```

> [!NOTE]
> Om du får ett felmeddelande om att värdnamnet inte är tillgängligt kan det bero på att klusternamnet inte är unikt. Prova att ta bort den ursprungliga appregistreringen och gör om stegen med ett annat klusternamn i [Skapa en ny appregistrering,](howto-aad-app-configuration.md#create-an-azure-ad-app-registration)vilket utelämnar steget att skapa en ny användare och säkerhetsgrupp.




Efter några minuter, `az openshift create` kommer att slutföra.

### <a name="get-the-sign-in-url-for-your-cluster"></a>Hämta inloggningsadressen för klustret

Hämta URL:en för att logga in på klustret genom att köra följande kommando:

```azurecli
az openshift show -n $CLUSTER_NAME -g $CLUSTER_NAME
```

Leta efter `publicHostName` i utdata, till exempel:`"publicHostname": "openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io"`

Inloggnings-URL:en för `https://` klustret `publicHostName` följs av värdet.  Till exempel: `https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io`.  Du kommer att använda den här URI:n i nästa steg som en del av URI-appens omdirigering av appregistrering.

## <a name="step-3-update-your-app-registration-redirect-uri"></a>Steg 3: Uppdatera URI-appens omdirigering av appregistrering

Nu när du har inloggnings-URL:en för klustret anger du användargränssnittet för omdirigering av appregistrering:

1. Öppna [bladet Appregistreringar](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview).
2. Klicka på ditt appregistreringsobjekt.
3. Klicka på **Lägg till en omdirigerings-URI**.
4. Kontrollera att **TYPE** är **webb** och ange REDIRECT `https://<public host name>/oauth2callback/Azure%20AD` **URI** med hjälp av följande mönster: . Exempel: `https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io/oauth2callback/Azure%20AD`
5. Klicka på **Spara**

## <a name="step-4-sign-in-to-the-openshift-console"></a>Steg 4: Logga in på OpenShift-konsolen

Nu är du redo att logga in på OpenShift-konsolen för det nya klustret. [Med OpenShift Web Console](https://docs.openshift.com/aro/architecture/infrastructure_components/web_console.html) kan du visualisera, bläddra bland och hantera innehållet i dina OpenShift-projekt.

Du behöver en ny webbläsarinstans som inte har cachelagrat den identitet som du normalt använder för att logga in på Azure-portalen.

1. Öppna ett *inkognitofönster* (Chrome) eller *InPrivate-fönstret* (Microsoft Edge).
2. Navigera till inloggningsadressen som du fick ovan, till exempel:`https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io`

Logga in med användarnamnet som du skapade i steg 3 i [Skapa en ny Azure Active Directory-användare](howto-aad-app-configuration.md#create-a-new-azure-active-directory-user).

En dialogrutan **Begärda behörigheter** visas. Klicka **på Samtycke för din organisations räkning** och klicka sedan på **Acceptera**.

Du är nu inloggad på klusterkonsolen.

![Skärmbild av OpenShift-klusterkonsolen](./media/aro-console.png)

 Läs mer om [hur du använder OpenShift-konsolen](https://docs.openshift.com/aro/getting_started/developers_console.html) för att skapa och skapa bilder i [Red Hat OpenShift-dokumentationen.](https://docs.openshift.com/aro/welcome/index.html)

## <a name="step-5-install-the-openshift-cli"></a>Steg 5: Installera OpenShift CLI

[OpenShift CLI](https://docs.openshift.com/aro/cli_reference/get_started_cli.html) (eller *OC Tools)* innehåller kommandon för att hantera dina program och verktyg på lägre nivå för att interagera med de olika komponenterna i OpenShift-klustret.

I OpenShift-konsolen klickar du på frågetecknet i det övre högra hörnet med ditt inloggningsnamn och väljer **Kommandoradsverktyg**.  Följ länken **Senaste version** för att hämta och installera oc CLI som stöds för Linux, MacOS eller Windows.

> [!NOTE]
> Om frågetecknet inte visas i det övre högra hörnet väljer du *Servicekatalog* eller *Programkonsol* i den övre vänstra nedrullningen.
>
> Alternativt kan du [ladda ner oc CLI](https://www.okd.io/download.html) direkt.

Sidan **Kommandoradsverktyg** innehåller ett kommando `oc login https://<your cluster name>.<azure region>.cloudapp.azure.com --token=<token value>`i formuläret .  Klicka på knappen *Kopiera till Urklipp* om du vill kopiera det här kommandot.  I ett terminalfönster [anger du sökvägen](https://docs.okd.io/latest/cli_reference/openshift_cli/getting-started-cli.html#installing-the-cli) så att den lokala installationen av oc-verktygen inkluderas. Logga sedan in i klustret med kommandot oc CLI som du kopierade.

Om du inte kunde hämta tokenvärdet med hjälp av stegen `https://<your cluster name>.<azure region>.cloudapp.azure.com/oauth/token/request`ovan hämtar du tokenvärdet från: .

## <a name="next-steps"></a>Nästa steg

I den här självstudiedelen lärde du dig att:

> [!div class="checklist"]
> * Skapa ett Azure Red Hat OpenShift-kluster

Gå vidare till nästa kurs:
> [!div class="nextstepaction"]
> [Skala ett Azure Red Hat OpenShift-kluster](tutorial-scale-cluster.md)
