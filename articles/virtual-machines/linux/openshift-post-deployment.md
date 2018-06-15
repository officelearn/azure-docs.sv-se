---
title: OpenShift på Azure efter distributionsuppgifter | Microsoft Docs
description: Ytterligare uppgifter för efter ett OpenShift kluster har distribuerats.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: ''
ms.author: haroldw
ms.openlocfilehash: bdfd075b9438ee12e940f3ec4fddebf467c93ca8
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
ms.locfileid: "31796167"
---
# <a name="post-deployment-tasks"></a>Uppgifter efter distributionen

När du distribuerar en OpenShift kluster måste konfigurera du ytterligare objekt. Den här artikeln beskriver följande:

- Hur du konfigurerar enkel inloggning med hjälp av Azure Active Directory (AD Azure)
- Så här konfigurerar du Log Analytics för att övervaka OpenShift
- Hur du konfigurerar mått och loggning

## <a name="configure-single-sign-on-by-using-azure-active-directory"></a>Konfigurera enkel inloggning med hjälp av Azure Active Directory

Om du vill använda Azure Active Directory för autentisering måste du först skapa en Azure AD app-registrering. Den här processen består av två steg: skapa appregistrering och hur du konfigurerar behörigheter.

### <a name="create-an-app-registration"></a>Skapa en appregistrering

De här stegen använda Azure CLI för att skapa appregistrering och det grafiska Användargränssnittet (portal) att ange behörigheter. Du behöver följande fem typer av information för att skapa appregistrering:

- Visningsnamn: appens registrering namn (till exempel OCPAzureAD)
- Startsidan: OpenShift konsolen URL (t.ex. https://masterdns343khhde.westus.cloudapp.azure.com:8443/console)
- URI-ID: OpenShift konsolens URL (t.ex. https://masterdns343khhde.westus.cloudapp.azure.com:8443/console)
- Svars-URL: Master offentlig URL och appens registrering namn (t.ex. https://masterdns343khhde.westus.cloudapp.azure.com:8443/oauth2callback/OCPAzureAD)
- Lösenord: Säkert lösenord (Använd ett starkt lösenord)

I följande exempel skapas en appregistrering med hjälp av informationen ovan:

```azurecli
az ad app create --display-name OCPAzureAD --homepage https://masterdns343khhde.westus.cloudapp.azure.com:8443/console --reply-urls https://masterdns343khhde.westus.cloudapp.azure.com:8443/oauth2callback/hwocpadint --identifier-uris https://masterdns343khhde.westus.cloudapp.azure.com:8443/console --password {Strong Password}
```

Om kommandot lyckas får en JSON-utdata liknar:

```json
{
  "appId": "12345678-ca3c-427b-9a04-ab12345cd678",
  "appPermissions": null,
  "availableToOtherTenants": false,
  "displayName": "OCPAzureAD",
  "homepage": "https://masterdns343khhde.westus.cloudapp.azure.com:8443/console",
  "identifierUris": [
    "https://masterdns343khhde.westus.cloudapp.azure.com:8443/console"
  ],
  "objectId": "62cd74c9-42bb-4b9f-b2b5-b6ee88991c80",
  "objectType": "Application",
  "replyUrls": [
    "https://masterdns343khhde.westus.cloudapp.azure.com:8443/oauth2callback/OCPAzureAD"
  ]
}
```

Anteckna egenskapen appId returnerades från kommandot för ett senare steg.

På Azure Portal:

1.  Välj **Azure Active Directory** > **Appregistrering**.
2.  Sök efter registreringen app (till exempel OCPAzureAD).
3.  Klicka på appregistrering i resultaten.
4.  Under **inställningar**väljer **nödvändiga behörigheter**.
5.  Under **nödvändiga behörigheter**väljer **Lägg till**.

  ![App-registrering](media/openshift-post-deployment/app-registration.png)

6.  Klicka på steg 1: Välj API och klicka sedan på **Windows Azure Active Directory (Microsoft.Azure.ActiveDirectory)**. Klicka på **Välj** längst ned.

  ![Välj API för App-registrering](media/openshift-post-deployment/app-registration-select-api.png)

7.  I steg 2: Välj behörigheter, Välj **logga in och Läs användarprofil** under **delegerade behörigheter**, och klicka sedan på **Välj**.

  ![Registrering av App-åtkomst](media/openshift-post-deployment/app-registration-access.png)

8.  Välj **klar**.

### <a name="configure-openshift-for-azure-ad-authentication"></a>Konfigurera OpenShift för Azure AD-autentisering

Om du vill konfigurera OpenShift för att använda Azure AD som en autentiseringsprovider redigeras filen /etc/origin/master/master-config.yaml på alla överordnade noder.

Hitta klient-ID med hjälp av följande CLI-kommando:

```azurecli
az account show
```

Sök efter följande rader i filen yaml:

```yaml
oauthConfig:
  assetPublicURL: https://masterdns343khhde.westus.cloudapp.azure.com:8443/console/
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

Infoga följande rader omedelbart efter de föregående raderna:

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

Hitta klient-ID med hjälp av följande CLI-kommando: ```az account show```

Starta om tjänsterna OpenShift master på alla överordnade noder:

**OpenShift Origin**

```bash
sudo systemctl restart origin-master-api
sudo systemctl restart origin-master-controllers
```

**OpenShift behållaren plattform (OCP) med flera huvudservrar**

```bash
sudo systemctl restart atomic-openshift-master-api
sudo systemctl restart atomic-openshift-master-controllers
```

**OpenShift behållaren plattform med en enda hanterare**

```bash
sudo systemctl restart atomic-openshift-master
```

I konsolen OpenShift visas nu två alternativ för verifiering: htpasswd_auth och [Appregistrering].

## <a name="monitor-openshift-with-log-analytics"></a>Övervakaren OpenShift med logganalys

Om du vill övervaka OpenShift med logganalys, du kan använda ett av två alternativ: OMS-Agent-installation på Virtuella, eller OMS-behållaren. Den här artikeln innehåller instruktioner för distribution av OMS-behållaren.

## <a name="create-an-openshift-project-for-log-analytics-and-set-user-access"></a>Skapa ett OpenShift projekt för Log Analytics och ange användaråtkomst

```bash
oadm new-project omslogging --node-selector='zone=default'
oc project omslogging
oc create serviceaccount omsagent
oadm policy add-cluster-role-to-user cluster-reader system:serviceaccount:omslogging:omsagent
oadm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent
```

## <a name="create-a-daemon-set-yaml-file"></a>Skapa en daemon set yaml-fil

Skapa en fil med namnet ocp-omsagent.yml:

```yaml
apiVersion: extensions/v1beta1
kind: DaemonSet
metadata:
  name: oms
spec:
  selector:
    matchLabels:
      name: omsagent
  template:
    metadata:
      labels:
        name: omsagent
        agentVersion: 1.4.0-45
        dockerProviderVersion: 10.0.0-25
    spec:
      nodeSelector:
        zone: default
      serviceAccount: omsagent
      containers:
      - image: "microsoft/oms"
        imagePullPolicy: Always
        name: omsagent
        securityContext:
          privileged: true
        ports:
        - containerPort: 25225
          protocol: TCP
        - containerPort: 25224
          protocol: UDP
        volumeMounts:
        - mountPath: /var/run/docker.sock
          name: docker-sock
        - mountPath: /etc/omsagent-secret
          name: omsagent-secret
          readOnly: true
        livenessProbe:
          exec:
            command:
              - /bin/bash
              - -c
              - ps -ef | grep omsagent | grep -v "grep"
          initialDelaySeconds: 60
          periodSeconds: 60
      volumes:
      - name: docker-sock
        hostPath:
          path: /var/run/docker.sock
      - name: omsagent-secret
        secret:
         secretName: omsagent-secret
````

## <a name="create-a-secret-yaml-file"></a>Skapa en hemlig yaml-fil

Den hemliga yaml-fil du behöver för att skapa två typer av information: Log Analytics arbetsyte-ID och Log Analytics arbetsytan delad nyckel. 

En exempelfil för ocp-secret.yml följande: 

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: omsagent-secret
data:
  WSID: wsid_data
  KEY: key_data
```

Ersätt wsid_data med Base64-kodade Log Analytics arbetsyte-ID. Ersätt key_data med Base64-kodad Log Analytics arbetsytan delad nyckel.

```bash
wsid_data='11111111-abcd-1111-abcd-111111111111'
key_data='My Strong Password'
echo $wsid_data | base64 | tr -d '\n'
echo $key_data | base64 | tr -d '\n'
```

## <a name="create-the-secret-and-daemon-set"></a>Skapa hemlighet och daemon?

Distribuera filen hemliga:

```bash
oc create -f ocp-secret.yml
```

Distribuera uppsättningen demon OMS-Agent:

```bash
oc create -f ocp-omsagent.yml
```

## <a name="configure-metrics-and-logging"></a>Konfigurera mått och loggning

Azure Resource Manager-mall för OpenShift behållare plattform innehåller indataparametrar för att aktivera mätvärden och loggning. OpenShift behållaren plattform Marketplace-erbjudande och OpenShift Origin Resource Manager-mallen inte.

Om du använde OCP Resource Manager-mall och mått och loggning inte var aktiverad vid tidpunkten för installationen eller om du använder OCP Marketplace-erbjudande, du kan enkelt aktivera dessa efteråt. Om du använder OpenShift Origin Resource Manager-mallen krävs vissa före arbete.

### <a name="openshift-origin-template-pre-work"></a>OpenShift ursprung före arbete

1. SSH till den första master noden med hjälp av port 2200.

   Exempel:

   ```bash
   ssh -p 2200 clusteradmin@masterdnsixpdkehd3h.eastus.cloudapp.azure.com 
   ```

2. Redigera filen /etc/ansible/hosts och Lägg till följande rader efter providern identitetsavsnittet (# aktivera HTPasswdPasswordIdentityProvider):

   ```yaml
   # Setup metrics
   openshift_hosted_metrics_deploy=false
   openshift_metrics_cassandra_storage_type=dynamic
   openshift_metrics_start_cluster=true
   openshift_metrics_hawkular_nodeselector={"type":"infra"}
   openshift_metrics_cassandra_nodeselector={"type":"infra"}
   openshift_metrics_heapster_nodeselector={"type":"infra"}
   openshift_hosted_metrics_public_url=https://metrics.$ROUTING/hawkular/metrics

   # Setup logging
   openshift_hosted_logging_deploy=false
   openshift_hosted_logging_storage_kind=dynamic
   openshift_logging_fluentd_nodeselector={"logging":"true"}
   openshift_logging_es_nodeselector={"type":"infra"}
   openshift_logging_kibana_nodeselector={"type":"infra"}
   openshift_logging_curator_nodeselector={"type":"infra"}
   openshift_master_logging_public_url=https://kibana.$ROUTING
   ```

3. Ersätt $ROUTING med den sträng som används för alternativet openshift_master_default_subdomain i samma /etc/ansible/hosts-fil.

### <a name="azure-cloud-provider-in-use"></a>Azure Cloud-providern används

På den första master noden (ursprungliga) eller skyddsmiljö (OCP) SSH med hjälp av autentiseringsuppgifterna som anges under distributionen. Kör följande kommando:

```bash
ansible-playbook $HOME/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True \
-e openshift_metrics_cassandra_storage_type=dynamic

ansible-playbook $HOME/openshift-ansible/playbooks/byo/openshift-cluster/openshift-logging.yml \
-e openshift_logging_install_logging=True \
-e openshift_hosted_logging_storage_kind=dynamic
```

### <a name="azure-cloud-provider-not-in-use"></a>Azure Cloud-providern används

På den första master noden (ursprungliga) eller skyddsmiljö (OCP) SSH med hjälp av autentiseringsuppgifterna som anges under distributionen. Kör följande kommando:

```bash
ansible-playbook $HOME/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True 

ansible-playbook $HOME/openshift-ansible/playbooks/byo/openshift-cluster/openshift-logging.yml \
-e openshift_logging_install_logging=True 
```

## <a name="next-steps"></a>Nästa steg

- [Komma igång med OpenShift behållare plattform](https://docs.openshift.com/container-platform/3.6/getting_started/index.html)
- [Komma igång med OpenShift Origin](https://docs.openshift.org/latest/getting_started/index.html)
