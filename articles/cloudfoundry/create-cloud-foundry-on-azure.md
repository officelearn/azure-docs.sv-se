---
title: Skapa Cloud Foundry på Azure
description: Lär dig hur du konfigurerar de parametrar som behövs för att etablera ett Cloud Foundry PCF-kluster på Azure
services: Cloud Foundry
documentationcenter: CloudFoundry
author: ruyakubu
manager: brunoborges
editor: ruyakubu
ms.assetid: ''
ms.author: ruyakubu
ms.date: 09/13/2018
ms.devlang: ''
ms.service: Cloud Foundry
ms.tgt_pltfrm: multiple
ms.topic: tutorial
ms.workload: web
ms.openlocfilehash: a0a3379a8a2579080d9b686917395feec9cf8f3d
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/03/2018
ms.locfileid: "48250674"
---
# <a name="create-cloud-foundry-on-azure"></a>Skapa Cloud Foundry på Azure

Den här självstudiekursen tillhandahåller snabba steg om hur du skapar och genererar parametrar som behövs för att etablera ett Pivotal Cloud Foundry PCF-kluster på Azure.  Du kan söka efter Pivotal Cloud Foundry-lösningen på Azure [MarketPlace](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry).

![Alt. bildtext](media/deploy/pcf-marketplace.png "Söka efter Pivotal Cloud Foundry i Azure")


## <a name="generate-an-ssh-public-key"></a>Generera en offentlig SSH-nyckel

Det finns flera sätt att generera en offentlig SSH-nyckel med hjälp av Windows, Mac eller Linux.

```Bash
ssh-keygen -t rsa -b 2048
```
- Klicka här om du vill se [instruktioner]( https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) för din miljö.

## <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten

> [!NOTE]
>
> Du behöver ägarbehörighet för kontot för att skapa tjänstens huvudnamn.  Dessutom kan du skriva ett skript för att automatisera skapandet av tjänstens huvudnamn. Till exempel med hjälp av Azure CLI [az ad sp create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest).

1. Logga in på Azure-kontot.

    `az login`

    ![Alt. bildtext](media/deploy/az-login-output.png "Azure CLI-inloggning")
 
    Kopiera ”id”-värdet som **prenumerations-ID** och **tenantId**-värdet som ska användas senare.

2. Ange din standardprenumeration för den här konfigurationen.

    `az account set -s {id}`

3. Skapa ett AAD-program för din PCF och ange ett unikt alfanumeriskt lösenord.  Lagra lösenordet som **clientSecret** som ska användas senare.

    `az ad app create --display-name "Svc Prinicipal for OpsManager" --password {enter-your-password} --homepage "{enter-your-homepage}" --identifier-uris {enter-your-homepage}`

    Kopiera sedan ”appId”-värdet i utdata som **ClientID** som ska användas senare.

    > [!NOTE]
    >
    > Välj din egen programstartsida och identifierar-URI.  t.ex. http://www.contoso.com.

4. Skapa ett huvudnamn för tjänsten med ditt nya ”appId”.

    `az ad sp create --id {appId}`

5. Ange behörighetsrollen för ditt huvudnamn för tjänsten som **Deltagare**.

    `az role assignment create --assignee “{enter-your-homepage}” --role “Contributor” `

    Du kan även använda…

    `az role assignment create --assignee {service-princ-name} --role “Contributor” `

    ![Alt. bildtext](media/deploy/svc-princ.png "Rolltilldelning för tjänstens huvudnamn")

6. Kontrollerar att du kan logga in på tjänstens huvudnamn med appId, lösenord och tenantId.

    `az login --service-principal -u {appId} -p {your-passward}  --tenant {tenantId}`

7. Skapa en .json-fil i följande format med hjälp av alla **prenumerations-ID**-, **tenantId**-, **clientID**- och **clientSecret**-värden som du har kopierat ovan.  Spara filen.

    ```json
    {
        "subscriptionID": "{enter-your-subscription-Id-here}",
        "tenantID": "{enter-your-tenant-Id-here}",
        "clientID": "{enter-your-app-Id-here}",
        "clientSecret": "{enter-your-key-here}"
    }
    ```

## <a name="get-the-pivotal-network-token"></a>Hämta Pivotal Network Token

1. Registrera dig eller logga in på ditt [Pivotal Network](https://network.pivotal.io)-konto.
2. Klicka på ditt profilnamn högst upp till höger på sidan och välj sedan **Edit Profile” (Redigera profil).
3. Rulla längst ned på sidan och kopiera **LEGACY API TOKEN**-värdet.  Det här är ditt **Pivotal Network Token**-värde som ska användas senare.

## <a name="provision-your-cloud-foundry-on-azure"></a>Etablera Cloud Foundry på Azure

1. Nu har du alla parametrar som behövs för att etablera ditt [Pivotal Cloud Foundry på Azure](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry)-kluster.
2. Ange parametrarna och skapa ditt PCF-kluster.

## <a name="verify-the-deployment-and-log-into-the-pivotal-ops-manager"></a>Kontrollera distributionen och logga in på Pivotal Ops Manager

1. PCF-klustret bör visa en distributionsstatus.

    ![Alt. bildtext](media/deploy/deployment.png "Azure-distributionsstatus")

2. Klicka på länken **Deployments** (Distributioner) i det vänstra navigeringsfönstret för att hämta autentiseringsuppgifter för PCF Ops Manager, och klicka sedan på **Deployment Name** (Distributionsnamn).
3. I det vänstra navigeringsfönstret klickar du på länken **Outputs** (Utdata) för att visa URL, Username (Användarnamn) och Password (Lösenord) för PCF Ops Manager.  ”OPSMAN-FQDN”-värdet är URL:en.
 
    ![Alt. bildtext](media/deploy/deploy-outputs.png "Cloud Foundry-distributionsutdata")
 
4. Starta URL:en i en webbläsare och ange autentiseringsuppgifterna från det föregående steget för att logga in.

    ![Alt. bildtext](media/deploy/pivotal-login.png "Pivotal-inloggningssida")
         
    > [!NOTE]
    >
    > Om åtgärden misslyckas i Internet Explorer på grund av varningsmeddelandet om att webbplatsen inte är säker klickar du på ”Mer information” och ”Fortsätt till webbsidan”.  I Firefox klickar du på Fortsätt och lägger till certifieringen för att fortsätta.

5. PCF Ops Manager bör visa de distribuerade Azure-instanserna. Nu kan du börja distribuera och hantera dina program här!
               
    ![Alt. bildtext](media/deploy/ops-mgr.png "Distribuerad Azure-instans i Pivotal")
 
