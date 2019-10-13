---
title: Självstudie – Skapa ett kluster med en Red Hat OpenShift | Microsoft Docs
description: Lär dig hur du skapar ett Microsoft Azure Red Hat OpenShift-kluster med Azure CLI
services: container-service
author: jimzim
ms.author: jzim
manager: jeconnoc
ms.topic: tutorial
ms.service: container-service
ms.date: 05/14/2019
ms.openlocfilehash: 01319de8fd72875ca35bb7a869a6eaedee62f2a7
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2019
ms.locfileid: "72285531"
---
# <a name="tutorial-create-an-azure-red-hat-openshift-cluster"></a>Självstudie: skapa ett Azure Red Hat OpenShift-kluster

Den här självstudien är del ett i en serie. Du lär dig hur du skapar ett Microsoft Azure Red Hat OpenShift-kluster med hjälp av Azure CLI, skalar det och sedan tar bort det för att rensa resurser.

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
> I den här självstudien krävs version 2.0.65 av Azure CLI.
>    
> Innan du kan använda en Red Hat OpenShift-växel måste du köpa minst 4 Azure Red Hat OpenShift-reserverade programnoder enligt beskrivningen i [Konfigurera din Azure Red Hat-utvecklings miljö](howto-setup-environment.md#purchase-azure-red-hat-openshift-application-nodes-reserved-instances).

Innan du börjar den här självstudien:

Kontrol lera att du har [konfigurerat utvecklings miljön](howto-setup-environment.md), som innehåller:
- Installera den senaste CLI-versionen (version 2.0.65 eller senare)
- Skapa en klient om du inte redan har en
- Skapa ett Azure Application-objekt om du inte redan har ett
- Skapa en säkerhets grupp
- Skapa en Active Directory användare för att logga in i klustret.

## <a name="step-1-sign-in-to-azure"></a>Steg 1: Logga in på Azure

Om du kör Azure CLI lokalt öppnar du en bash kommando tolk och kör `az login` för att logga in på Azure.

```bash
az login
```

 Om du har åtkomst till flera prenumerationer kör du `az account set -s {subscription ID}` och ersätter `{subscription ID}` med den prenumeration som du vill använda.

## <a name="step-2-create-an-azure-red-hat-openshift-cluster"></a>Steg 2: skapa ett Azure Red Hat OpenShift-kluster

I ett bash kommando fönster anger du följande variabler:

> [!IMPORTANT]
> Välj ett namn för ett kluster som är unikt och att det inte går att skapa ett gement eller kluster.

```bash
CLUSTER_NAME=<cluster name in lowercase>
```

Välj en plats för att skapa klustret. En lista över Azure-regioner som stöder OpenShift på Azure finns i [regioner som stöds](supported-resources.md#azure-regions). Till exempel: `LOCATION=eastus`.

```bash
LOCATION=<location>
```

Ange `APPID` till värdet som du sparade i steg 5 i [skapa en Azure AD App-registrering](howto-aad-app-configuration.md#create-an-azure-ad-app-registration).  

```bash
APPID=<app ID value>
```

Ange "kund" till värdet som du sparade i steg 10 i [skapa en Azure AD-säkerhetsgrupp](howto-aad-app-configuration.md#create-an-azure-ad-security-group).

```bash
GROUPID=<group ID value>
```

Ange `SECRET` till värdet som du sparade i steg 8 i [skapa en klient hemlighet](howto-aad-app-configuration.md#create-a-client-secret).  

```bash
SECRET=<secret value>
```

Ange `TENANT` till klient-ID-värdet som du sparade i steg 7 i [skapa en ny klient](howto-create-tenant.md#create-a-new-azure-ad-tenant)  

```bash
TENANT=<tenant ID>
```

Skapa resurs gruppen för klustret. Kör följande kommando från samma bash-gränssnitt som du använde för att definiera variablerna ovan:

```bash
az group create --name $CLUSTER_NAME --location $LOCATION
```

### <a name="optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network"></a>Valfritt: Anslut klustrets virtuella nätverk till ett befintligt virtuellt nätverk

Om du inte behöver ansluta det virtuella nätverket (VNET) för klustret som du skapar till ett befintligt VNET via peering hoppar du över det här steget.

Om peer-koppla till ett nätverk utanför standard prenumerationen måste du även registrera providern Microsoft. container service i den prenumerationen. Det gör du genom att köra kommandot nedan i den prenumerationen. Annars kan du hoppa över registrerings steget om det virtuella nätverk som du peering finns i samma prenumeration. 

`az provider register -n Microsoft.ContainerService --wait`

Hämta först identifieraren för det befintliga virtuella nätverket. Identifieraren har formatet: `/subscriptions/{subscription id}/resourceGroups/{resource group of VNET}/providers/Microsoft.Network/virtualNetworks/{VNET name}`.

Om du inte känner till nätverks namnet eller resurs gruppen som det befintliga VNET tillhör, går du till [bladet virtuella nätverk](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Network%2FvirtualNetworks) och klickar på det virtuella nätverket. Sidan virtuellt nätverk visas och visar namnet på nätverket och den resurs grupp som det tillhör.

Definiera en VNET_ID-variabel med följande CLI-kommando i ett BASH-gränssnitt:

```bash
VNET_ID=$(az network vnet show -n {VNET name} -g {VNET resource group} --query id -o tsv)
```

Exempel: `VNET_ID=$(az network vnet show -n MyVirtualNetwork -g MyResourceGroup --query id -o tsv`

### <a name="create-the-cluster"></a>Skapa klustret

Nu är du redo att skapa ett kluster. Följande skapar klustret i den angivna Azure AD-klienten, anger det Azure AD-App-objekt och den hemlighet som ska användas som säkerhets objekt och säkerhets gruppen som innehåller de medlemmar som har administratörs åtkomst till klustret.

> [!IMPORTANT]
> Kontrol lera att du har lagt till lämpliga behörigheter för Azure AD-appen korrekt genom att följa anvisningarna [här](howto-aad-app-configuration.md#add-api-permissions) innan du skapar klustret

Om du **inte** peer-koppla klustret till ett virtuellt nätverk använder du följande kommando:

```bash
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --customer-admin-group-id $GROUPID
```

Om du peer- **koppla** klustret till ett virtuellt nätverk använder du följande kommando som lägger till flaggan `--vnet-peer`:
 
```bash
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --customer-admin-group-id $GROUPID --vnet-peer $VNET_ID
```

> [!NOTE]
> Om du får ett fel meddelande om att värd namnet inte är tillgängligt kan det bero på att kluster namnet inte är unikt. Försök att ta bort den ursprungliga appens registrering och gör om stegen med ett annat kluster namn i [skapa en ny app-registrering](howto-aad-app-configuration.md#create-an-azure-ad-app-registration), utan steget för att skapa en ny användare och säkerhets grupp.

Efter några minuter kommer `az openshift create` att slutföras.

### <a name="get-the-sign-in-url-for-your-cluster"></a>Hämta inloggnings-URL: en för ditt kluster

Hämta webb adressen för att logga in i klustret genom att köra följande kommando:

```bash
az openshift show -n $CLUSTER_NAME -g $CLUSTER_NAME
```

Leta efter `publicHostName` i utdata, till exempel: `"publicHostname": "openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io"`

Inloggnings-URL: en för klustret kommer att `https://` följt av värdet @no__t 1.  Till exempel: `https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io`.  Du kommer att använda denna URI i nästa steg som en del av omdirigerings-URI för app-registrering.

## <a name="step-3-update-your-app-registration-redirect-uri"></a>Steg 3: uppdatera omdirigerings-URI för program registrering

Nu när du har inloggnings-URL: en för klustret, anger du omdirigera användar gränssnitt för app-registrering:

1. Öppna [bladet Appregistreringar](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview).
2. Klicka på ditt app Registration-objekt.
3. Klicka på **Lägg till en omdirigerings-URI**.
4. Se till att **typen** är **webb** och ange **omdirigerings-URI** med följande mönster: `https://<public host name>/oauth2callback/Azure%20AD`. Exempel: `https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io/oauth2callback/Azure%20AD`
5. Klicka på **Spara**

## <a name="step-4-sign-in-to-the-openshift-console"></a>Steg 4: Logga in i OpenShift-konsolen

Du är nu redo att logga in i OpenShift-konsolen för det nya klustret. Med [webb konsolen OpenShift](https://docs.openshift.com/aro/architecture/infrastructure_components/web_console.html) kan du visualisera, bläddra i och hantera innehållet i dina OpenShift-projekt.

Du behöver en ny webb läsar instans som inte cachelagrar den identitet som du vanligt vis använder för att logga in på Azure Portal.

1. Öppna ett *Incognito* -fönster (Chrome) eller *InPrivate* -fönster (Microsoft Edge).
2. Navigera till inloggnings-URL: en som du fick ovan, till exempel: `https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io`

Logga in med det användar namn som du skapade i steg 3 i [skapa en ny Azure Active Directory användare](howto-aad-app-configuration.md#create-a-new-azure-active-directory-user).

En **begärd behörighets** dialog ruta visas. Klicka på **medgivande för din organisations räkning** och klicka sedan på **Godkänn**.

Du är nu inloggad på kluster konsolen.

![Skärm bild av kluster konsolen för OpenShift](./media/aro-console.png)

 Lär dig mer om att [använda OpenShift-konsolen](https://docs.openshift.com/aro/getting_started/developers_console.html) för att skapa och skapa avbildningar i [Red Hat OpenShift](https://docs.openshift.com/aro/welcome/index.html) -dokumentationen.

## <a name="step-5-install-the-openshift-cli"></a>Steg 5: installera OpenShift CLI

Kommandona [OpenShift CLI](https://docs.openshift.com/aro/cli_reference/get_started_cli.html) (eller *oc*) innehåller kommandon för att hantera dina program och lågnivå verktyg för att interagera med de olika komponenterna i ditt OpenShift-kluster.

I OpenShift-konsolen klickar du på frågetecknet i det övre högra hörnet efter ditt inloggnings namn och väljer **kommando rads verktyg**.  Följ den **senaste Release** -länken för att ladda ned och installera den oc CLI som stöds för Linux, MacOS eller Windows.

> [!NOTE]
> Om du inte ser frågetecknet-ikonen i det övre högra hörnet väljer du *tjänst katalog* eller *program konsol* i den övre vänstra List rutan.
>
> Alternativt kan du [Hämta oc CLI](https://www.okd.io/download.html) direkt.

Sidan **kommando rads verktyg** innehåller ett kommando med formatet `oc login https://<your cluster name>.<azure region>.cloudapp.azure.com --token=<token value>`.  Kopiera det här kommandot genom att klicka på knappen *Kopiera till Urklipp* .  I ett terminalfönster ställer du [in sökvägen](https://docs.okd.io/latest/cli_reference/get_started_cli.html#installing-the-cli) så att den lokala installationen av oc-verktygen inkluderas. Logga sedan in på klustret med kommandot oc CLI som du kopierade.

Om du inte kan hämta token-värdet med hjälp av stegen ovan hämtar du värdet för token från: `https://<your cluster name>.<azure region>.cloudapp.azure.com/oauth/token/request`.

## <a name="next-steps"></a>Nästa steg

I den här självstudiedelen lärde du dig att:

> [!div class="checklist"]
> * Skapa ett Azure Red Hat OpenShift-kluster

Gå vidare till nästa kurs:
> [!div class="nextstepaction"]
> [Skala ett Azure Red Hat OpenShift-kluster](tutorial-scale-cluster.md)
