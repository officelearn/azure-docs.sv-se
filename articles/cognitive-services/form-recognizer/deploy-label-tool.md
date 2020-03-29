---
title: Distribuera exempeletikettverktyget för formulär recognizer
titleSuffix: Azure Cognitive Services
description: Lär dig olika sätt att distribuera exempeletikettverktyget för formulärmedkännare för att hjälpa till med övervakad inlärning.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 03/20/2020
ms.author: pafarley
ms.openlocfilehash: 795d21e05ade652b52c06d597ca4c5fef85e7245
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80152875"
---
# <a name="deploy-the-sample-labeling-tool"></a>Distribuera exempeletikettverktyget

Exempeletikettverktyget för formulärmedkänning är ett program som tillhandahåller ett enkelt användargränssnitt som du kan använda för att manuellt märka formulär (dokument) för övervakad inlärning. I den här artikeln ska vi tillhandahålla länkar och instruktioner som lär dig hur du:

* [Kör exempeletikettverktyget lokalt](#run-the-sample-labeling-tool-locally)
* [Distribuera exempeletikettverktyget till en ACI (Azure Container Instance)](#deploy-with-azure-container-instances-aci)
* [Använda och bidra till ocr-formuläretikettverktyget med öppen källkod](#open-source-on-github)

## <a name="run-the-sample-labeling-tool-locally"></a>Kör exempeletikettverktyget lokalt

Det snabbaste sättet att börja märka data är att köra exempeletikettverktyget lokalt. I följande snabbstart används REST-API:et för formulärmedkännare och exempeletikettverktyget för att träna en anpassad modell med manuellt märkta data. 

* [Snabbstart: Märk formulär, träna en modell och analysera ett formulär med hjälp av exempeletikettverktyget](./quickstarts/label-tool.md).

## <a name="deploy-with-azure-container-instances-aci"></a>Distribuera med Azure Container Instances (ACI)

Innan vi börjar är det viktigt att notera att det finns två sätt att distribuera exempeletikettverktyget till en Azure Container Instance (ACI). Båda alternativen används för att köra exempeletikettverktyget med ACI: 

* [Använda Azure-portalen](#azure-portal)
* [Använda Azure CLI](#azure-cli)

### <a name="azure-portal"></a>Azure Portal

Så här skapar du en ny resurs med Azure-portalen: 

1. Logga in på [Azure-portalen](https://portal.azure.com/signin/index/).
2. Välj **Skapa en resurs**. 
3. Välj sedan **Web App**. 

   > [!div class="mx-imgBorder"]
   > ![Välja webbapp](./media/quickstarts/formre-create-web-app.png)
   
4. Kontrollera först att fliken **Grunderna** är markerad. Nu måste du ge lite information: 

   > [!div class="mx-imgBorder"]
   > ![Välj grunderna](./media/quickstarts/formre-select-basics.png)
   * Prenumeration - Välj en befintlig Azure-prenumeration
   * Resursgrupp - Du kan återanvända en befintlig resursgrupp eller skapa en ny för det här projektet. Du rekommenderas att skapa en ny resursgrupp.
   * Namn - Ge din webbapp ett namn. 
   * Publicera - Välj **Docker Container**
   * Operativsystem - Välj **Linux**
   * Region - Välj en region som är vettigt för dig.
   * Linux Plan - Välj en prisnivå/-abonnemang för din apptjänst. 

   > [!div class="mx-imgBorder"]
   > ![Konfigurera webbappen](./media/quickstarts/formre-select-docker-linux.png)

5. Välj sedan fliken **Docker.** 

   > [!div class="mx-imgBorder"]
   > ![Välj Docker](./media/quickstarts/formre-select-docker.png)

6. Nu ska vi konfigurera Docker-behållaren. Alla fält krävs om inget annat anges:

   * Alternativ - Välj **en behållare**
   * Bildkälla - Välj **privat register** 
   * Server-URL - Ställ in detta på`https://mcr.microsoft.com`
   * Användarnamn (valfritt) - Skapa ett användarnamn. 
   * Lösenord (valfritt) – Skapa ett säkert lösenord som du kommer ihåg.
   * Bild och tagg - Ställ in det här på`mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest`
   * Startkommando - Ställ in det här på`./run.sh eula=accept`

   > [!div class="mx-imgBorder"]
   > ![Konfigurera Docker](./media/quickstarts/formre-configure-docker.png)

7. Klart! Välj sedan **Granska + Skapa**och sedan **Skapa** för att distribuera webbappen. När du är klar kan du komma åt webbappen på webbadressen som anges i **översikten** för din resurs.

> [!NOTE]
> När du skapar webbappen kan du också konfigurera auktorisering/autentisering. Detta är inte nödvändigt för att komma igång. 

### <a name="azure-cli"></a>Azure CLI

Som ett alternativ till att använda Azure-portalen kan du skapa en resurs med Hjälp av Azure CLI. Innan du fortsätter måste du installera [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Du kan hoppa över det här steget om du redan arbetar med Azure CLI. 

Det finns några saker du behöver veta om det här kommandot:

* `DNS_NAME_LABEL=aci-demo-$RANDOM`genererar ett slumpmässigt DNS-namn. 
* Det här exemplet förutsätter att du har en resursgrupp som du kan använda för att skapa en resurs. Ersätt `<resource_group_name>` med en giltig resursgrupp som är associerad med din prenumeration. 
* Du måste ange var du vill skapa resursen. Ersätt `<region name>` med önskad region för webbappen. 
* Det här kommandot accepterar automatiskt licensavtalet.

Från Azure CLI kör du det här kommandot för att skapa en webbappresurs för exempeletikettverktyget: 

```azurecli
DNS_NAME_LABEL=aci-demo-$RANDOM

az container create \
  --resource-group <resorunce_group_name> \
  --name <name> \
  --image mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool \
  --ports 3000 \
  --dns-name-label $DNS_NAME_LABEL \
  --location <region name> \
  --cpu 2 \
  --memory 8
  --command-line "./run.sh eula=accept"
```

### <a name="connect-to-azure-ad-for-authorization"></a>Ansluta till Azure AD för auktorisering

Vi rekommenderar att du ansluter webbappen till Azure Active Directory. Detta säkerställer att endast användare med giltiga autentiseringsuppgifter kan logga in och använda din webbapp. Följ instruktionerna i [Konfigurera appen App Service](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad) för att ansluta till Azure Active Directory.

## <a name="open-source-on-github"></a>Öppen källkod på GitHub

OCR-formulärmärkningsverktyget är också tillgängligt som ett projekt med öppen källkod på GitHub. Verktyget är ett webbprogram byggt med React + Redux och skrivs i TypeScript. Mer information om eller bidra finns i [OCR-formuläretikettverktyg](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md).

## <a name="next-steps"></a>Nästa steg

Använd snabbstarten [Tåg med etiketter](./quickstarts/label-tool.md) om du vill lära dig hur du använder verktyget för att manuellt märka träningsdata och utföra övervakad inlärning.
