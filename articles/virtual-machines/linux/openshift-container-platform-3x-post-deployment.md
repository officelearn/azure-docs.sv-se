---
title: OpenShift Container Platform 3.11 i Azure-uppgifter efter distribution
description: Ytterligare uppgifter för efter att ett OpenShift Container Platform 3.11-kluster har distribuerats.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: 1635589b282dc33f6a1e9c2552dc8a73c67b9004
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294746"
---
# <a name="post-deployment-tasks"></a>Uppgifter efter distribution

När du har distribuerat ett OpenShift-kluster kan du konfigurera ytterligare objekt. Denna artikel innehåller:

- Konfigurera enkel inloggning med hjälp av Azure Active Directory (Azure AD)
- Konfigurera Azure Monitor-loggar för att övervaka OpenShift
- Konfigurera mått och loggning
- Så här installerar du Open Service Broker för Azure (OSBA)

## <a name="configure-single-sign-on-by-using-azure-active-directory"></a>Konfigurera enkel inloggning med hjälp av Azure Active Directory

Om du vill använda Azure Active Directory för autentisering måste du först skapa en Azure AD-appregistrering. Den här processen omfattar två steg: att skapa appregistreringen och konfigurera behörigheter.

### <a name="create-an-app-registration"></a>Skapa en appregistrering

I de här stegen används Azure CLI för att skapa appregistreringen och GUI (portalen) för att ange behörigheter. För att skapa appregistreringen behöver du följande fem informationsdelar:

- Visningsnamn: Namn på appregistrering (till exempel OCPAzureAD)
- Startsida: Url till OpenShift-konsolen (till exempel `https://masterdns343khhde.westus.cloudapp.azure.com/console`)
- Identifierare URI: Url till OpenShift-konsolen (till `https://masterdns343khhde.westus.cloudapp.azure.com/console`exempel)
- Svara på URL: Huvudadress för offentlig WEBBADRESS `https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/OCPAzureAD`och appens registreringsnamn (till exempel)
- Lösenord: Säkert lösenord (använd ett starkt lösenord)

I följande exempel skapas en appregistrering med hjälp av ovanstående information:

```azurecli
az ad app create --display-name OCPAzureAD --homepage https://masterdns343khhde.westus.cloudapp.azure.com/console --reply-urls https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/hwocpadint --identifier-uris https://masterdns343khhde.westus.cloudapp.azure.com/console --password {Strong Password}
```

Om kommandot lyckas får du en JSON-utdata som liknar:

```json
{
  "appId": "12345678-ca3c-427b-9a04-ab12345cd678",
  "appPermissions": null,
  "availableToOtherTenants": false,
  "displayName": "OCPAzureAD",
  "homepage": "https://masterdns343khhde.westus.cloudapp.azure.com/console",
  "identifierUris": [
    "https://masterdns343khhde.westus.cloudapp.azure.com/console"
  ],
  "objectId": "62cd74c9-42bb-4b9f-b2b5-b6ee88991c80",
  "objectType": "Application",
  "replyUrls": [
    "https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/OCPAzureAD"
  ]
}
```

Anteckna egenskapen appId som returnerats från kommandot för ett senare steg.

På Azure Portal:

1. Välj Registrering av Azure Active > **Directory-appar**. **Azure Active Directory**
2. Sök efter din appregistrering (till exempel OCPAzureAD).
3. Klicka på appregistreringen i resultatet.
4. Under **Inställningar**väljer du **Krävs behörigheter**.
5. Under **Obligatoriska behörigheter**väljer du **Lägg till**.

   ![Registrering av appar](media/openshift-post-deployment/app-registration.png)

6. Klicka på Steg 1: Välj API och klicka sedan på **Windows Azure Active Directory (Microsoft.Azure.ActiveDirectory)**. Klicka på **Markera** längst ned.

   ![Api för välj appregistrering](media/openshift-post-deployment/app-registration-select-api.png)

7. På steg 2: Välj Behörigheter väljer du **Logga in och läser användarprofil** under **Delegerade behörigheter**och klickar sedan på **Välj**.

   ![Åtkomst till appregistrering](media/openshift-post-deployment/app-registration-access.png)

8. Välj **Done** (Klar).

### <a name="configure-openshift-for-azure-ad-authentication"></a>Konfigurera OpenShift för Azure AD-autentisering

Om du vill konfigurera OpenShift för att använda Azure AD som autentiseringsprovider måste filen /etc/origin/master/master-config.yaml redigeras på alla huvudnoder.

Hitta klient-ID:et med hjälp av följande CLI-kommando:

```azurecli
az account show
```

Leta reda på följande rader i yaml-filen:

```yaml
oauthConfig:
  assetPublicURL: https://masterdns343khhde.westus.cloudapp.azure.com/console/
  grantConfig:
    method: auto
  identityProviders:
  - challenge: true
    login: true
    mappingMethod: claim
    name: htpasswd_auth
    provider:
      apiVersion: v1
      file: /etc/origin/master/htpasswd
      kind: HTPasswdPasswordIdentityProvider
```

Infoga följande rader omedelbart efter föregående rader:

```yaml
  - name: <App Registration Name>
    challenge: false
    login: true
    mappingMethod: claim
    provider:
      apiVersion: v1
      kind: OpenIDIdentityProvider
      clientID: <appId>
      clientSecret: <Strong Password>
      claims:
        id:
        - sub
        preferredUsername:
        - unique_name
        name:
        - name
        email:
        - email
      urls:
        authorize: https://login.microsoftonline.com/<tenant Id>/oauth2/authorize
        token: https://login.microsoftonline.com/<tenant Id>/oauth2/token
```

Kontrollera att texten justeras korrekt under identityProviders. Hitta klient-ID:et med hjälp av följande CLI-kommando:```az account show```

Starta om OpenShift-huvudtjänsterna på alla huvudnoder:

```bash
sudo /usr/local/bin/master-restart api
sudo /usr/local/bin/master-restart controllers
```

I OpenShift-konsolen ser du nu två alternativ för autentisering: htpasswd_auth och [Appregistrering].

## <a name="monitor-openshift-with-azure-monitor-logs"></a>Övervaka OpenShift med Azure Monitor-loggar

Det finns tre sätt att lägga till Log Analytics-agenten i OpenShift.
- Installera Log Analytics-agenten för Linux direkt på varje OpenShift-nod
- Aktivera Azure Monitor VM-tillägg på varje OpenShift-nod
- Installera Log Analytics-agenten som en OpenShift-demonuppsättning

Läs de fullständiga [instruktionerna](https://docs.microsoft.com/azure/log-analytics/log-analytics-containers#configure-a-log-analytics-agent-for-red-hat-openshift) för mer information.

## <a name="configure-metrics-and-logging"></a>Konfigurera mått och loggning

Baserat på grenen kan Azure Resource Manager-mallarna för OpenShift Container Platform och OKD tillhandahålla indataparametrar för att aktivera mått och loggning som en del av installationen.

OpenShift Container Platform Marketplace erbjuder också ett alternativ för att aktivera mått och loggning under klusterinstallation.

Om mått / loggning inte var aktiverad under installationen av klustret, kan de enkelt aktiveras i efterhand.

### <a name="azure-cloud-provider-in-use"></a>Azure Cloud Provider används

SSH till skyddsnoden eller den första huvudnoden (baserat på mall och gren som används) med hjälp av autentiseringsuppgifterna som anges under distributionen. Utfärda följande kommando:

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-metrics/config.yml \
-e openshift_metrics_install_metrics=True \
-e openshift_metrics_cassandra_storage_type=dynamic

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-logging/config.yml \
-e openshift_logging_install_logging=True \
-e openshift_logging_es_pvc_dynamic=true
```

### <a name="azure-cloud-provider-not-in-use"></a>Azure Cloud Provider används inte

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-metrics/config.yml \
-e openshift_metrics_install_metrics=True

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-logging/config.yml \
-e openshift_logging_install_logging=True
```

## <a name="install-open-service-broker-for-azure-osba"></a>Installera öppen tjänstmäklare för Azure (OSBA)

Open Service Broker för Azure, eller OSBA, kan du etablera Azure Cloud Services direkt från OpenShift. OSBA i en API-implementering för Öppen tjänstutjämning för Azure. Api:et för öppen tjänstutjämning är en specifikation som definierar ett gemensamt språk för molnleverantörer som molninbyggda program kan använda för att hantera molntjänster utan inlåsning.

Så här installerar du OSBA på OpenShift: https://github.com/Azure/open-service-broker-azure#openshift-project-template 
> [!NOTE]
> Slutför bara stegen i avsnittet OpenShift-projektmall och inte hela installationsavsnittet.

## <a name="next-steps"></a>Nästa steg

- [Komma igång med OpenShift Container Platform](https://docs.openshift.com)
