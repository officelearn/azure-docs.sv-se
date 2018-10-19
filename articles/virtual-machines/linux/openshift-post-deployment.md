---
title: OpenShift på Azure efter distribution aktiviteter | Microsoft Docs
description: Ytterligare uppgifter för efter ett OpenShift-kluster har distribuerats.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: najoshi
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/09/2018
ms.author: haroldw
ms.openlocfilehash: 39febceff58127fb9777ace6e3063fbe41605b79
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2018
ms.locfileid: "49426455"
---
# <a name="post-deployment-tasks"></a>Uppgifter efter distribution

När du distribuerar en OpenShift-kluster måste konfigurera du ytterligare objekt. Den här artikeln beskriver följande:

- Så här konfigurerar du enkel inloggning med hjälp av Azure Active Directory (AD Azure)
- Hur du konfigurerar Log Analytics för att övervaka OpenShift
- Så här konfigurerar du mått och loggning

## <a name="configure-single-sign-on-by-using-azure-active-directory"></a>Konfigurera enkel inloggning med hjälp av Azure Active Directory

För att använda Azure Active Directory för autentisering, måste du först skapa en Azure AD app-registrering. Den här processen omfattar två steg: skapa appregistreringen och konfigurera behörigheter.

### <a name="create-an-app-registration"></a>Skapa en appregistrering

De här stegen används Azure CLI för att skapa registreringen och det grafiska Användargränssnittet (portal) att ange behörigheter. Om du vill skapa registreringen, behöver du följande fem typer av information:

- Visningsnamn: registrering namn (till exempel OCPAzureAD)
- Startsidan: OpenShift konsolen URL (t.ex. https://masterdns343khhde.westus.cloudapp.azure.com:8443/console)
- Identifierar-URI: OpenShift-konsolens URL (t.ex. https://masterdns343khhde.westus.cloudapp.azure.com:8443/console)
- Svars-URL: Master offentlig URL och namnet på registrering (t.ex. https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/OCPAzureAD)
- Lösenord: Säkra lösenord (Använd ett starkt lösenord)

I följande exempel skapas en appregistrering med hjälp av informationen ovan:

```azurecli
az ad app create --display-name OCPAzureAD --homepage https://masterdns343khhde.westus.cloudapp.azure.com:8443/console --reply-urls https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/OCPAzureAD --identifier-uris https://masterdns343khhde.westus.cloudapp.azure.com:8443/console --password {Strong Password}
```

Om kommandot lyckas får JSON-utdata liknar:

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
    "https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/OCPAzureAD"
  ]
}
```

Anteckna appId-egenskapen som returneras från kommandot för ett senare steg.

På Azure Portal:

1.  Välj **Azure Active Directory** > **Appregistrering**.
2.  Sök efter din appregistrering (till exempel OCPAzureAD).
3.  Klicka på appregistreringen i resultatet.
4.  Under **inställningar**väljer **behörigheter som krävs för**.
5.  Under **nödvändiga behörigheter**väljer **Lägg till**.

  ![Appregistrering](media/openshift-post-deployment/app-registration.png)

6.  Klicka på steg 1: Välj API och klicka sedan på **Azure Active Directory (Microsoft.Azure.ActiveDirectory)**. Klicka på **Välj** längst ned på sidan.

  ![Appregistrering väljer API](media/openshift-post-deployment/app-registration-select-api.png)

7.  I steg 2: Välj behörigheter, Välj **logga in och läsa användarprofil** under **delegerade behörigheter**, och klicka sedan på **Välj**.

  ![Appregistrering åtkomst](media/openshift-post-deployment/app-registration-access.png)

8.  Välj **Done** (Klar).

### <a name="configure-openshift-for-azure-ad-authentication"></a>Konfigurera OpenShift för Azure AD-autentisering

Om du vill konfigurera OpenShift för att använda Azure AD som en autentiseringsprovider redigeras filen /etc/origin/master/master-config.yaml på alla överordnade noder.

Hitta klient-ID genom att använda följande CLI-kommando:

```azurecli
az account show
```

Yaml-fil innehåller följande rader:

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

Hitta klient-ID genom att använda följande CLI-kommando: ```az account show```

Starta om OpenShift master tjänsterna på alla överordnade noder:

**OpenShift Origin**

```bash
sudo systemctl restart origin-master-api
sudo systemctl restart origin-master-controllers
```

**OpenShift Container Platform (OCP) med flera huvudservrar**

```bash
sudo systemctl restart atomic-openshift-master-api
sudo systemctl restart atomic-openshift-master-controllers
```

**OpenShift Container Platform med en enda hanterare**

```bash
sudo systemctl restart atomic-openshift-master
```

I OpenShift-konsolen visas nu två alternativ för autentisering: htpasswd_auth och [Appregistrering].

## <a name="monitor-openshift-with-log-analytics"></a>Övervaka OpenShift med Log Analytics

Om du vill övervaka OpenShift med Log Analytics kan du använda ett av två alternativ: Log Analytics-agentinstallationen på VM-värd eller Log Analytics-behållare. Den här artikeln innehåller instruktioner för distribution av Log Analytics-behållaren.

## <a name="create-an-openshift-project-for-log-analytics-and-set-user-access"></a>Skapa ett projekt med OpenShift för Log Analytics och ange användaråtkomst

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

Om du vill skapa hemliga yaml-fil som du behöver två typer av information: Log Analytics arbetsyte-ID och delad nyckel för Log Analytics-arbetsyta. 

En exempelfil ocp-secret.yml följande: 

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: omsagent-secret
data:
  WSID: wsid_data
  KEY: key_data
```

Ersätt wsid_data med Base64-kodade Log Analytics arbetsyte-ID. Ersätt sedan key_data med Base64-kodad Log Analytics-arbetsyta delad nyckel.

```bash
wsid_data='11111111-abcd-1111-abcd-111111111111'
key_data='My Strong Password'
echo $wsid_data | base64 | tr -d '\n'
echo $key_data | base64 | tr -d '\n'
```

## <a name="create-the-secret-and-daemon-set"></a>Skapa hemlighet och daemon uppsättningen

Distribuera hemlighetsfilen:

```bash
oc create -f ocp-secret.yml
```

Distribuera daemon-uppsättningen Log Analytics-agenten:

```bash
oc create -f ocp-omsagent.yml
```

## <a name="configure-metrics-and-logging"></a>Konfigurera mått och loggning

Azure Resource Manager-mall för OpenShift Container Platform innehåller indataparametrar för att aktivera mätvärden och loggning. OpenShift Container Platform Marketplace-erbjudande och OpenShift Origin Resource Manager-mallen inte.

Om du använde OCP Resource Manager-mall och mått och loggning inte aktiverat vid tidpunkten för installationen, eller om du har använt OCP Marketplace-erbjudande du kan enkelt att aktivera dessa i efterhand. Om du använder OpenShift Origin Resource Manager-mallen krävs vissa före arbete.

### <a name="openshift-origin-template-pre-work"></a>Före arbete med OpenShift Origin

1. SSH till den första överordnade noden med hjälp av port 2200.

   Exempel:

   ```bash
   ssh -p 2200 clusteradmin@masterdnsixpdkehd3h.eastus.cloudapp.azure.com 
   ```

2. Redigera filen /etc/ansible/hosts och Lägg till följande rader efter identitetsavsnittet Provider (# aktivera HTPasswdPasswordIdentityProvider):

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

På den första huvudnoden (ursprungliga) eller skyddsmiljö nod (OCP), SSH med hjälp av autentiseringsuppgifterna som du angav under distributionen. Kör följande kommando:

```bash
ansible-playbook $HOME/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True \
-e openshift_metrics_cassandra_storage_type=dynamic

ansible-playbook $HOME/openshift-ansible/playbooks/byo/openshift-cluster/openshift-logging.yml \
-e openshift_logging_install_logging=True \
-e openshift_hosted_logging_storage_kind=dynamic
```

### <a name="azure-cloud-provider-not-in-use"></a>Azure Cloud-providern inte är i användning

På den första huvudnoden (ursprungliga) eller skyddsmiljö nod (OCP), SSH med hjälp av autentiseringsuppgifterna som du angav under distributionen. Kör följande kommando:

```bash
ansible-playbook $HOME/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True 

ansible-playbook $HOME/openshift-ansible/playbooks/byo/openshift-cluster/openshift-logging.yml \
-e openshift_logging_install_logging=True 
```

## <a name="next-steps"></a>Nästa steg

- [Komma igång med OpenShift Container Platform](https://docs.openshift.com/container-platform/3.6/getting_started/index.html)
- [Komma igång med OpenShift Origin](https://docs.openshift.org/latest/getting_started/index.html)
