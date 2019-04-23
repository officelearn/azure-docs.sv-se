---
title: OpenShift på Azure efter distribution aktiviteter | Microsoft Docs
description: Ytterligare uppgifter för efter ett OpenShift-kluster har distribuerats.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/19/2019
ms.author: haroldw
ms.openlocfilehash: fba29cd55f2d765faa107de3a8961032ef44deec
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "59997415"
---
# <a name="post-deployment-tasks"></a>Uppgifter efter distribution

När du distribuerar en OpenShift-kluster måste konfigurera du ytterligare objekt. Den här artikeln beskriver:

- Så här konfigurerar du enkel inloggning med hjälp av Azure Active Directory (AD Azure)
- Så här konfigurerar du Azure Monitor-loggar för att övervaka OpenShift
- Så här konfigurerar du mått och loggning
- Installera Open Service Broker for Azure (OSBA)

## <a name="configure-single-sign-on-by-using-azure-active-directory"></a>Konfigurera enkel inloggning med hjälp av Azure Active Directory

För att använda Azure Active Directory för autentisering, måste du först skapa en Azure AD app-registrering. Den här processen omfattar två steg: skapa appregistreringen och konfigurera behörigheter.

### <a name="create-an-app-registration"></a>Skapa en appregistrering

De här stegen används Azure CLI för att skapa registreringen och det grafiska Användargränssnittet (portal) att ange behörigheter. Om du vill skapa registreringen, behöver du följande fem typer av information:

- Visningsnamn: Registrering av namn (till exempel OCPAzureAD)
- Startsida: OpenShift-konsolens URL (t.ex. https://masterdns343khhde.westus.cloudapp.azure.com/console)
- Identifierar-URI: OpenShift-konsolens URL (t.ex. https://masterdns343khhde.westus.cloudapp.azure.com/console)
- Svars-URL: Master-offentlig URL och namnet på registrering (t.ex. https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/OCPAzureAD)
- Lösenord: Säkra lösenord (Använd ett starkt lösenord)

I följande exempel skapas en appregistrering med hjälp av informationen ovan:

```azurecli
az ad app create --display-name OCPAzureAD --homepage https://masterdns343khhde.westus.cloudapp.azure.com/console --reply-urls https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/hwocpadint --identifier-uris https://masterdns343khhde.westus.cloudapp.azure.com/console --password {Strong Password}
```

Om kommandot lyckas får JSON-utdata liknar:

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

Anteckna appId-egenskapen som returneras från kommandot för ett senare steg.

På Azure Portal:

1. Välj **Azure Active Directory** > **Appregistrering**.
2. Sök efter din appregistrering (till exempel OCPAzureAD).
3. Klicka på appregistreringen i resultatet.
4. Under **inställningar**väljer **behörigheter som krävs för**.
5. Under **nödvändiga behörigheter**väljer **Lägg till**.

   ![Appregistrering](media/openshift-post-deployment/app-registration.png)

6. Klicka på steg 1: Välj API och klicka sedan på **Windows Azure Active Directory (Microsoft.Azure.ActiveDirectory)**. Klicka på **Välj** längst ned på sidan.

   ![Appregistrering väljer API](media/openshift-post-deployment/app-registration-select-api.png)

7. I steg 2: Välj behörigheter, Välj **logga in och läsa användarprofil** under **delegerade behörigheter**, och klicka sedan på **Välj**.

   ![Appregistrering åtkomst](media/openshift-post-deployment/app-registration-access.png)

8. Välj **Done** (Klar).

### <a name="configure-openshift-for-azure-ad-authentication"></a>Konfigurera OpenShift för Azure AD-autentisering

Om du vill konfigurera OpenShift för att använda Azure AD som en autentiseringsprovider redigeras filen /etc/origin/master/master-config.yaml på alla överordnade noder.

Hitta klient-ID genom att använda följande CLI-kommando:

```azurecli
az account show
```

Yaml-fil innehåller följande rader:

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

Lägg till följande rader direkt efter de föregående raderna:

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

Kontrollera att korrekt justeras texten under identityProviders. Hitta klient-ID genom att använda följande CLI-kommando: ```az account show```

Starta om OpenShift master tjänsterna på alla överordnade noder:

```bash
sudo /usr/local/bin/master-restart api
sudo /usr/local/bin/master-restart controllers
```

I OpenShift-konsolen visas nu två alternativ för autentisering: htpasswd_auth och [Appregistrering].

## <a name="monitor-openshift-with-azure-monitor-logs"></a>Övervaka OpenShift med Azure Monitor-loggar

Det finns tre sätt att lägga till Log Analytics-agenten i OpenShift.
- Installera Log Analytics-agenten för Linux direkt på varje nod för OpenShift
- Aktivera Azure Monitor VM-tillägget på varje nod för OpenShift
- Installera Log Analytics-agenten som en daemon-set OpenShift

Läs fullständiga [instruktioner](https://docs.microsoft.com/azure/log-analytics/log-analytics-containers#configure-a-log-analytics-agent-for-red-hat-openshift) för mer information.

## <a name="configure-metrics-and-logging"></a>Konfigurera mått och loggning

Baserat på grenen, ange Azure Resource Manager-mallar för OpenShift Container Platform och OKD indataparametrar för att aktivera mätvärden och loggning som en del av installationen.

OpenShift Container Platform Marketplace-erbjudandet innehåller också ett alternativ för att aktivera mätvärden och loggning under installationen av klustret.

Om mått / loggning har inte aktiverats under installationen av klustret, att de enkelt kan aktiveras i efterhand.

### <a name="azure-cloud-provider-in-use"></a>Azure Cloud-providern används

SSH till skyddsmiljö noden eller första huvudnoden (baserat på mallen och grenen som används) med hjälp av autentiseringsuppgifterna som du angav under distributionen. Kör följande kommando:

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-metrics/config.yml \
-e openshift_metrics_install_metrics=True \
-e openshift_metrics_cassandra_storage_type=dynamic

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-logging/config.yml \
-e openshift_logging_install_logging=True \
-e openshift_logging_es_pvc_dynamic=true
```

### <a name="azure-cloud-provider-not-in-use"></a>Azure Cloud-providern inte är i användning

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-metrics/config.yml \
-e openshift_metrics_install_metrics=True

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-logging/config.yml \
-e openshift_logging_install_logging=True
```

## <a name="install-open-service-broker-for-azure-osba"></a>Installera Open Service Broker for Azure (OSBA)

Öppna Service Broker för Azure eller OSBA, kan du etablera Azure Cloud Services direkt från OpenShift. OSBA i en Open Service Broker-API-implementering för Azure. Öppna Service Broker API är en spec som definierar ett gemensamt språk för leverantörer som molntjänster interna program kan använda för att hantera molntjänster utan Lås i molnet.

Installera OSBA på OpenShift genom att följa anvisningarna som finns här: https://github.com/Azure/open-service-broker-azure#openshift-project-template. 
> [!NOTE]
> Följ bara metoden stegen i den projektmall för OpenShift och inte i hela Installing avsnitt.

## <a name="next-steps"></a>Nästa steg

- [Komma igång med OpenShift Container Platform](https://docs.openshift.com/container-platform)
