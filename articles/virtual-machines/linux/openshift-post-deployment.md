---
title: "OpenShift på uppgifter för Azure efter distribution | Microsoft Docs"
description: "Uppgifter för OpenShift efter distribution"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 
ms.author: haroldw
ms.openlocfilehash: 12e6785358f5f412326418b0c64eeaeabdaa3b5f
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2017
---
# <a name="post-deployment-tasks"></a>Uppgifter efter distributionen

Efter OpenShift klustret har distribuerats, finns det ytterligare objekt som kan konfigureras. Den här artikeln beskriver följande:

- Konfigurera enkel inloggning med Azure Active Directory (AAD)
- Konfigurera OMS för att övervaka OpenShift
- Konfigurera måtten och loggning

## <a name="single-sign-on-using-aad"></a>Enkel inloggning med AAD

En Azure AD App-registrering måste skapas först för att kunna använda AAD för autentisering. Den här processen kommer omfattar två steg - skapande av registreringen av appen och sedan konfigurera behörigheter.

### <a name="create-app-registration"></a>Skapa appregistrering

Vi använder Azure CLI för att skapa appen registreringen och det grafiska Användargränssnittet (Portal) för att ange behörigheter. Om du vill skapa App-registrering fem typer av information som behövs.

- Visningsnamn: Appregistrering namn (ex: OCPAzureAD)
- Startsida: OpenShift-konsolens URL (ex: https://masterdns343khhde.westus.cloudapp.azure.com:8443-konsolen)
- URI-ID: OpenShift-konsolens URL (ex: https://masterdns343khhde.westus.cloudapp.azure.com:8443-konsolen)
- Reply-URL: Master-offentlig URL och namnet på appen registrering (ex: OCPAzureAD-https://masterdns343khhde.westus.cloudapp.azure.com:8443/oauth2callback)
- Lösenord: Säkert lösenord (Använd ett starkt lösenord)

I följande exempel skapar en App-registrering med hjälp av informationen från ovan.

```azurecli
az ad app create --display-name OCPAzureAD --homepage https://masterdns343khhde.westus.cloudapp.azure.com:8443/console --reply-urls https://masterdns343khhde.westus.cloudapp.azure.com:8443/oauth2callback/hwocpadint --identifier-uris https://masterdns343khhde.westus.cloudapp.azure.com:8443/console --password {Strong Password}
```

Om kommandot lyckas får du en JSON-utdata som liknar:

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

I den **Azure-portalen**:

1.  Välj **Azure Active Directory** --> **Appregistrering**
2.  Sök efter registreringen App (ex: OCPAzureAD)
3.  I resultaten klickar du på App-registrering
4.  I inställningar-bladet välj **nödvändiga behörigheter**
5.  I bladet nödvändiga behörigheter klickar du på **Lägg till**

  ![App-registrering](media/openshift-post-deployment/app-registration.png)

6.  Klicka på steg 1: Välj API och klicka sedan på **Windows Azure Active Directory (Microsoft.Azure.ActiveDirectory)** och på **Välj** längst ned

  ![Välj API för App-registrering](media/openshift-post-deployment/app-registration-select-api.png)

7.  I steg 2: Välj behörigheter, Välj **logga in och Läs användarprofil** under **delegerade behörigheter** och klicka på **Välj**

  ![Registrering av App-åtkomst](media/openshift-post-deployment/app-registration-access.png)

8.  Klicka på **klar**

### <a name="configure-openshift-for-azure-ad-authentication"></a>Konfigurera OpenShift för Azure AD-autentisering

Så här konfigurerar du OpenShift för att använda Azure AD som en autentiseringsprovider, den **/etc/origin/master/master-config.yaml** filen måste redigeras på alla noder i Master.

Klient-Id kan hittas med hjälp av följande CLI-kommando:

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

Infoga följande rader omedelbart efter ovanstående rader:

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

Klient-Id kan hittas med hjälp av följande CLI-kommando:```az account show```

Starta om tjänsterna på alla noder i Master OpenShift Master

**OpenShift Origin**

```bash
sudo systemctl restart origin-master-api
sudo systemctl restart origin-master-controllers
```

**OpenShift behållaren plattform med flera huvudservrar**

```bash
sudo systemctl restart atomic-openshift-master-api
sudo systemctl restart atomic-openshift-master-controllers
```

**OpenShift behållaren plattform med single Master Operations**

```bash
sudo systemctl restart atomic-openshift-master
```

I konsolen OpenShift nu visas två alternativ för autentisering – htpasswd_auth och **[Appregistrering]**.

## <a name="monitor-openshift-with-oms"></a>Övervakaren OpenShift med OMS

Övervaka OpenShift med OMS kan uppnås med hjälp av ett av två alternativ - OMS-Agent-installation på Virtuella eller OMS-behållaren. Den här artikeln innehåller instruktioner om hur du distribuerar OMS-behållaren.

## <a name="create-an-openshift-project-for-oms-and-set-user-access"></a>Skapa ett OpenShift projekt för OMS och ange användaråtkomst

```bash
oadm new-project omslogging --node-selector='zone=default'
oc project omslogging
oc create serviceaccount omsagent
oadm policy add-cluster-role-to-user cluster-reader system:serviceaccount:omslogging:omsagent
oadm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent
```

## <a name="create-daemon-set-yaml-file"></a>Skapa daemon ange yaml filer

Skapa en fil med namnet ocp-omsagent.yml.

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

## <a name="create-secret-yaml-file"></a>Skapa hemliga yaml-fil

Två typer av information krävs för att skapa filen hemliga yaml - OMS arbetsyte-ID och OMS arbetsytan delad nyckel. 

Ocp-secret.yml exempelfilen 

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: omsagent-secret
data:
  WSID: wsid_data
  KEY: key_data
```

Ersätt wsid_data med Base64-kodade OMS arbetsyte-ID och Ersätt key_data med Base64-kodade OMS arbetsytan delad nyckel.

```bash
wsid_data='11111111-abcd-1111-abcd-111111111111'
key_data='My Strong Password'
echo $wsid_data | base64 | tr -d '\n'
echo $key_data | base64 | tr -d '\n'
```

## <a name="create-secret-and-daemon-set"></a>Skapa hemlighet och daemon

Distribuera filen hemliga

```bash
oc create -f ocp-secret.yml
```

Distribuera uppsättningen Daemon OMS-Agent

```bash
oc create -f ocp-omsagent.yml
```

## <a name="configure-metrics-and-logging"></a>Konfigurera mått och loggning

OpenShift behållaren plattform (OCP) Resource Manager-mall innehåller indataparametrar för att aktivera mätvärden och loggning. Mallen OpenShift behållare plattform Marketplace erbjuder och OpenShift Origin Resource Manager inte.

Om OCP Resource Manager-mallen användes och mått och loggning inte aktiverade vid tidpunkten för installationen eller OCP Marketplace-erbjudande användes, aktiveras dessa enkelt efteråt. Om du använder OpenShift Origin Resource Manager-mallen, krävs vissa före arbete.

### <a name="openshift-origin-template-pre-work"></a>OpenShift ursprung före arbete

SSH till den första huvudnod som använder port 2200

Exempel

```bash
ssh -p 2200 clusteradmin@masterdnsixpdkehd3h.eastus.cloudapp.azure.com 
```

Redigera den **/etc/ansible/hosts filen** och lägger till följande rader efter providern identitetsavsnittet (# aktivera HTPasswdPasswordIdentityProvider)

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

Ersätt $ROUTING med strängen som används för **openshift_master_default_subdomain** alternativet i samma **/etc/ansible/hosts** fil.

### <a name="azure-cloud-provider-in-use"></a>Azure Cloud-providern används

På den första huvudnod (ursprungliga) eller en Skyddsmiljö nod (OCP), SSH med de autentiseringsuppgifter som anges under distributionen. Kör följande kommando:

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True \
-e openshift_metrics_cassandra_storage_type=dynamic

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-logging.yml \
-e openshift_logging_install_logging=True \
-e openshift_hosted_logging_storage_kind=dynamic
```

### <a name="azure-cloud-provider-not-in-use"></a>Azure Cloud-providern används

På den första huvudnod (ursprungliga) eller en Skyddsmiljö nod (OCP), SSH med de autentiseringsuppgifter som anges under distributionen. Kör följande kommando:

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True 

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-logging.yml \
-e openshift_logging_install_logging=True 
```

## <a name="next-steps"></a>Nästa steg

- [Komma igång med OpenShift behållare plattform](https://docs.openshift.com/container-platform/3.6/getting_started/index.html)
- [Komma igång med OpenShift Origin](https://docs.openshift.org/latest/getting_started/index.html)