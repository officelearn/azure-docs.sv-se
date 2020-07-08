---
title: Använda GitOps för en Azure Arc-aktiverad kluster konfiguration (förhands granskning)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Använda GitOps för en Azure Arc-aktiverad kluster konfiguration (förhands granskning)
keywords: GitOps, Kubernetes, K8s, Azure, Arc, Azure Kubernetes service, containers
ms.openlocfilehash: 890b35aac33a6fa207a71d76143997a1b93116bf
ms.sourcegitcommit: 9b5c20fb5e904684dc6dd9059d62429b52cb39bc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85856978"
---
# <a name="use-gitops-for-an-azure-arc-enabled--configuration-preview"></a>Använda GitOps för en Azure Arc-aktiverad konfiguration (för hands version)

Den här arkitekturen använder ett GitOps-arbetsflöde för att konfigurera klustret och distribuera program. Konfigurationen beskrivs i deklarativt i. yaml-filer och lagras i git. En agent bevakar git-lagrings platsen för ändringar och tillämpar dem.  Samma agent säkerställer också regelbundet att kluster tillstånden matchar det tillstånd som har deklarerats i git-lagrings platsen och returnerar klustret till önskat tillstånd om några ohanterade ändringar har skett.

Anslutningen mellan klustret och en eller flera git-databaser spåras i Azure Resource Manager som `sourceControlConfiguration` tilläggs resurs. `sourceControlConfiguration`Resurs egenskaperna representerar var och hur Kubernetes resurser ska flöda från git till klustret. `sourceControlConfiguration`Data lagras krypterade i vila i en CosmosDb-databas för att säkerställa datakonfidentialitet.

Azure-bågen aktiverade Kubernetes som `config-agent` körs i klustret ansvarar för att titta efter nya eller uppdaterade `sourceControlConfiguration` resurser och dirigerar att lägga till, uppdatera eller ta bort git lagrings platsen-länkar automatiskt.

Samma mönster kan användas för att hantera en större samling kluster, som kan distribueras i heterogena miljöer. Du kan till exempel ha en lagrings plats som definierar bas linje konfigurationen för din organisation och tillämpa den på flera Kubernetes-kluster samtidigt.

Git-lagringsplatsen kan innehålla alla giltiga Kubernetes-resurser, inklusive namnrymder, ConfigMaps, distributioner, DaemonSets osv.  Det kan också innehålla Helm-diagram för att distribuera program. En vanlig uppsättning scenarier innefattar att definiera en bas linje konfiguration för din organisation, som kan innehålla vanliga RBAC-roller och bindningar, övervaknings-eller loggnings agenter eller kluster för många tjänster.

Den här kom igång-guiden hjälper dig att använda en uppsättning konfigurationer med kluster administratörs omfång.

## <a name="create-a-configuration"></a>Skapa en konfiguration

- Exempel lagrings plats:<https://github.com/Azure/arc-k8s-demo>

Exempel lagrings platsen är strukturerad runt personen som är medlem av en kluster operatör som vill etablera några få namn rymder, distribuera en gemensam arbets belastning och ange en team-speciell konfiguration. När du använder den här databasen skapas följande resurser i klustret:

**Namnrymder:** `cluster-config` , `team-a` , `team-b` 
 **distribution:** `cluster-config/azure-vote` 
 **ConfigMap:**`team-a/endpoints`

`config-agent`Söker Azure efter nya eller uppdaterade `sourceControlConfiguration` var 30: e sekund.  Detta är den längsta tid det tar för `config-agent` att hämta en ny eller uppdaterad konfiguration.
Om du associerar ett privat lagrings lager bör du även slutföra stegen i [tillämpa konfiguration från en privat git-lagringsplats](#apply-configuration-from-a-private-git-repository)

### <a name="using-azure-cli"></a>Använda Azure CLI

Vi använder Azure CLI-tillägget för `k8sconfiguration` , vi länkar vårt anslutna kluster till ett [exempel git-lagringsplats](https://github.com/Azure/arc-k8s-demo). Vi kommer att ge den här konfigurationen ett namn `cluster-config` , instruera agenten att distribuera operatorn i `cluster-config` namn området och ge operatörs `cluster-admin` behörighet.

```console
az k8sconfiguration create \
    --name cluster-config \
    --cluster-name AzureArcTest1 --resource-group AzureArcTest \
    --operator-instance-name cluster-config --operator-namespace cluster-config \
    --repository-url https://github.com/Azure/arc-k8s-demo \
    --scope cluster --cluster-type connectedClusters
```

**Utdataparametrar**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
{
  "complianceStatus": {
    "ComplianceStatus": 1,
    "clientAppliedTime": null,
    "level": 3,
    "message": "{\"OperatorMessage\":null,\"ClusterState\":null}"
  },
  "configKind": "Git",
  "configName": "cluster-config",
  "configOperator": {
    "operatorInstanceName": "cluster-config",
    "operatorNamespace": "cluster-config",
    "operatorParams": "--git-readonly",
    "operatorScope": "cluster",
    "operatorType": "flux"
  },
  "configType": "",
  "id": null,
  "name": null,
  "operatorInstanceName": null,
  "operatorNamespace": null,
  "operatorParams": null,
  "operatorScope": null,
  "operatorType": null,
  "providerName": "ConnectedClusters",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": null,
  "repositoryUrl": null,
  "sourceControlConfiguration": {
    "repositoryPublicKey": "",
    "repositoryUrl": "git://github.com/Azure/arc-k8s-demo.git"
  },
  "type": null
}
```

#### <a name="repository-url-parameter"></a>Parameter för lagrings plats-URL

Här följer de scenarier som stöds för värdet av--URL-parametern-URL.

| Scenario | Format | Beskrivning |
| ------------- | ------------- | ------------- |
| Privat GitHub-lagrings platsen – SSH | git@github.com:username/repo | SSH-nyckelpar genererat av flöde.  Användaren måste lägga till den offentliga nyckeln i GitHub-kontot som distributions nyckel. |
| Offentlig GitHub-lagrings platsen | `http://github.com/username/repo`eller git://github.com/username/repo   | Offentlig git-lagrings platsen  |

Dessa scenarier stöds av flöde, men inte av sourceControlConfiguration än. 

| Scenario | Format | Beskrivning |
| ------------- | ------------- | ------------- |
| Privat GitHub lagrings platsen-HTTPS | `https://github.com/username/repo` | Flödet genererar inte SSH-nyckelpar.  [Instruktioner](https://docs.fluxcd.io/en/1.17.0/guides/use-git-https.html) |
| Privat git-värd | user@githost:path/to/repo | [Instruktioner](https://docs.fluxcd.io/en/1.18.0/guides/use-private-git-host.html) |
| Privat GitHub lagrings platsen – SSH (ta med din egen nyckel) | git@github.com:username/repo | [Använd ditt eget SSH-nyckelpar](https://docs.fluxcd.io/en/1.17.0/guides/provide-own-ssh-key.html) |


#### <a name="additional-parameters"></a>Ytterligare parametrar

Här följer några ytterligare parametrar för att anpassa skapandet av konfigurationen:

`--enable-helm-operator`: *Valfri* växel för att aktivera stöd för Helm-diagram distributioner. Som standard är detta inaktiverat.

`--helm-operator-chart-values`: *Valfria* diagram värden för Helm-operatorn (om aktive rad).  Till exempel "--Set Helm. versions = v3".

`--helm-operator-chart-version`: *Valfri* diagram version för Helm-operatorn (om aktive rad). Standard: ' 0.6.0 '.

`--operator-namespace`: Det *valfria* namnet för operatorns namn område. Standard: standard

`--operator-params`: *Valfria* parametrar för operatorn. Måste anges inom enkla citat tecken. Till exempel, ```--operator-params='--git-readonly --git-path=releases/prod' ```

Alternativ som stöds i--Operator-params

| Alternativ | Beskrivning |
| ------------- | ------------- |
| --git-gren  | Gren av Git-lagrings platsen som ska användas för Kubernetes-manifest. Standardvärdet är Master. |
| --git-sökväg  | Relativ sökväg i git-lagrings platsen för flöde för att hitta Kubernetes-manifest. |
| --git-ReadOnly | Git-lagrings platsen anses vara skrivskyddad; Flödet försöker inte skriva till den. |
| --manifest-generation  | Om aktive rad kommer flödet att leta efter. flöde. yaml och köra Kustomize eller andra manifest generatorer. |
| --git-Poll-Interval  | Den period då git-lagrings platsen ska avsökas för nya incheckningar. Standardvärdet är ' 5 m ' (5 minuter). |
| --Sync-skräp insamling  | Om det här alternativet är aktiverat tas resurser som den skapade bort, men de finns inte längre i git. |
| --git-Label  | Etikett för att hålla koll på synkroniseringens förlopp, som används för att tagga git-grenen.  Standardvärdet är flödes-Sync. |
| --git-användare  | Användar namn för git-incheckning. |
| --git-e-post  | E-postmeddelande som ska användas för git-incheckning. |

* Om "--git-User" eller "-git-e-post" inte har angetts (vilket innebär att du inte vill att flödet ska skriva till lagrings platsen), så anges--git-ReadOnly automatiskt (om du inte redan har gjort det).

* Om enableHelmOperator är true kan operatorInstanceName + operatorNamespace-strängar inte överstiga 47 tecken.  Om du inte följer denna gräns får du följande fel meddelande:

   ```console
   {"OperatorMessage":"Error: {failed to install chart from path [helm-operator] for release [<operatorInstanceName>-helm-<operatorNamespace>]: err [release name \"<operatorInstanceName>-helm-<operatorNamespace>\" exceeds max length of 53]} occurred while doing the operation : {Installing the operator} on the config","ClusterState":"Installing the operator"}
   ```

Mer information finns i [flödes dokumentation](https://aka.ms/FluxcdReadme).

## <a name="validate-the-sourcecontrolconfiguration"></a>Verifiera sourceControlConfiguration

Verifiera att du har skapat genom att använda Azure CLI `sourceControlConfiguration` .

```console
az k8sconfiguration show --resource-group AzureArcTest --name cluster-config --cluster-name AzureArcTest1 --cluster-type connectedClusters
```

Observera att `sourceControlConfiguration` resursen har uppdaterats med kompatibilitetsstatus, meddelanden och fel söknings information.

**Utdataparametrar**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
{
  "complianceStatus": {
    "complianceState": "Installed",
    "lastConfigApplied": "2019-12-05T05:34:41.481000",
    "message": "...",
    "messageLevel": "3"
  },
  "id": "/subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/cluster-config",
  "name": "cluster-config",
  "operatorInstanceName": "cluster-config",
  "operatorNamespace": "cluster-config",
  "operatorParams": "--git-readonly",
  "operatorScope": "cluster",
  "operatorType": "Flux",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": "...",
  "repositoryUrl": "git://github.com/Azure/arc-k8s-demo.git",
  "resourceGroup": "AzureArcTest",
  "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
}
```

När `sourceControlConfiguration` har skapats händer några saker under huven:

1. Azure-bågen `config-agent` övervakar Azure Resource Manager för nya eller uppdaterade konfigurationer ( `Microsoft.KubernetesConfiguration/sourceControlConfiguration` )
1. `config-agent`Observera den nya `Pending` konfigurationen
1. `config-agent`läser konfigurations egenskaperna och förbereder distributionen av en hanterad instans av`flux`
    * `config-agent`skapar mål namn området
    * `config-agent`förbereder ett Kubernetes tjänst konto med rätt behörighet ( `cluster` eller `namespace` omfång)
    * `config-agent`distribuerar en instans av`flux`
    * `flux`genererar en SSH-nyckel och loggar den offentliga nyckeln
1. `config-agent`rapporterar status tillbaka till`sourceControlConfiguration`

När etablerings processen utförs går det `sourceControlConfiguration` igenom några tillstånds ändringar. Övervaka förloppet med `az k8sconfiguration show ...` kommandot ovan:

1. `complianceStatus` -> `Pending`: representerar inledande och pågående tillstånd
1. `complianceStatus` -> `Installed`: `config-agent` kunde konfigurera klustret och distribuera `flux` utan fel
1. `complianceStatus` -> `Failed`: `config-agent` ett fel uppstod vid distribution `flux` . informationen bör vara tillgänglig i `complianceStatus.message` svars texten

## <a name="apply-configuration-from-a-private-git-repository"></a>Tillämpa konfigurationen från en privat git-lagringsplats

Om du använder en privat git-lagrings platsen måste du utföra ytterligare en uppgift för att stänga slingan: du måste lägga till den offentliga nyckeln som genererades av `flux` som en **distributions nyckel** i lagrings platsen.

**Hämta den offentliga nyckeln med Azure CLI**

```console
$ az k8sconfiguration show --resource-group <resource group name> --cluster-name <connected cluster name> --name <configuration name> --query 'repositoryPublicKey'
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAREDACTED"
```

**Hämta den offentliga nyckeln från Azure Portal**

1. I Azure Portal navigerar du till den anslutna kluster resursen.
2. På sidan resurs väljer du "konfigurationer" och visar listan över konfigurationer för klustret.
3. Välj den konfiguration som använder den privata git-lagringsplatsen.
4. I kontext fönstret som öppnas längst ned i fönstret kopierar du **lagrings platsens offentliga nyckel**.

**Lägg till den offentliga nyckeln som en distributions nyckel till git-lagrings platsen**

1. Öppna GitHub, navigera till din förgrening, till **Inställningar**, och **distribuera nycklar**
2. Klicka på **Lägg till distributions nyckel**
3. Ange en rubrik
4. Kontrol lera **Tillåt skriv åtkomst**
5. Klistra in den offentliga nyckeln (minus omgivande citat tecken)
6. Klicka på **Lägg till nyckel**

Mer information om hur du hanterar distributions nycklar finns i GitHub-dokumenten.

**Om du använder en Azure DevOps-lagringsplats lägger du till nyckeln till dina SSH-nycklar**

1. Under **användar inställningar** längst upp till höger (bredvid profil avbildningen) klickar du på **offentliga SSH-nycklar**
1. Välj **+ ny nyckel**
1. Ange ett namn
1. Klistra in den offentliga nyckeln utan omgivande citat tecken
1. Klicka på **Lägg till**

## <a name="validate-the-kubernetes-configuration"></a>Verifiera Kubernetes-konfigurationen

När `config-agent` har installerat `flux` instansen ska resurser som lagras i git-lagringsplatsen börja flöda till klustret. Kontrol lera att namn områdena, distributionerna och resurserna har skapats:

```console
kubectl get ns --show-labels
```

**Utdataparametrar**

```console
NAME              STATUS   AGE    LABELS
azure-arc         Active   24h    <none>
cluster-config    Active   177m   <none>
default           Active   29h    <none>
itops             Active   177m   fluxcd.io/sync-gc-mark=sha256.9oYk8yEsRwWkR09n8eJCRNafckASgghAsUWgXWEQ9es,name=itops
kube-node-lease   Active   29h    <none>
kube-public       Active   29h    <none>
kube-system       Active   29h    <none>
team-a            Active   177m   fluxcd.io/sync-gc-mark=sha256.CS5boSi8kg_vyxfAeu7Das5harSy1i0gc2fodD7YDqA,name=team-a
team-b            Active   177m   fluxcd.io/sync-gc-mark=sha256.vF36thDIFnDDI2VEttBp5jgdxvEuaLmm7yT_cuA2UEw,name=team-b
```

Vi kan se att `team-a` `team-b` `itops` namn områdena,, och `cluster-config` har skapats.

`flux`Operatören har distribuerats till `cluster-config` namn området enligt följande `sourceControlConfig` :

```console
kubectl -n cluster-config get deploy  -o wide
```

**Utdataparametrar**

```console
NAME             READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS   IMAGES                         SELECTOR
cluster-config   1/1     1            1           3h    flux         docker.io/fluxcd/flux:1.16.0   instanceName=cluster-config,name=flux
memcached        1/1     1            1           3h    memcached    memcached:1.5.15               name=memcached
```

## <a name="further-exploration"></a>Ytterligare utforskning

Du kan utforska de andra resurserna som distribueras som en del av konfigurations lagrings platsen:

```console
kubectl -n team-a get cm -o yaml
kubectl -n itops get all
```

## <a name="delete-a-configuration"></a>Ta bort en konfiguration

Du kan ta bort en `sourceControlConfiguration` med hjälp av Azure CLI eller Azure Portal.  När du har initierat kommandot Ta bort `sourceControlConfiguration` kommer resursen att tas bort omedelbart i Azure, men det kan ta upp till 1 timme för fullständig borttagning av de associerade objekten från klustret (vi har ett artikel efter släpning för att förkorta detta). Om `sourceControlConfiguration` har skapats med namn områdes omfånget tas inte namn området bort från klustret (för att undvika att andra resurser som kan ha skapats i namn området) bryts.

Observera att ändringar i klustret som resulterade i distributioner från det spårade git-lagrings platsen inte tas bort när tas `sourceControlConfiguration` bort.

```console
az k8sconfiguration delete --name '<config name>' -g '<resource group name>' --cluster-name '<cluster name>' --cluster-type connectedClusters
```

**Utdataparametrar**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
```

## <a name="next-steps"></a>Nästa steg

- [Använd GitOps med Helm för kluster konfiguration](./use-gitops-with-helm.md)
- [Använd Azure Policy för att styra kluster konfigurationen](./use-azure-policy.md)
