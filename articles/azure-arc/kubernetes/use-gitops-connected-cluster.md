---
title: Distribuera konfigurationer med GitOps på Arc-aktiverade Kubernetes-kluster (för hands version)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Använda GitOps för en Azure Arc-aktiverad kluster konfiguration (förhands granskning)
keywords: GitOps, Kubernetes, K8s, Azure, Arc, Azure Kubernetes service, containers
ms.openlocfilehash: ce6c754c308d2979db9b1b8eb36e7858e8a91c3c
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659802"
---
# <a name="deploy-configurations-using-gitops-on-arc-enabled-kubernetes-cluster-preview"></a>Distribuera konfigurationer med GitOps på Arc-aktiverade Kubernetes-kluster (för hands version)

GitOps är syftet med att deklarera det önskade läget för Kubernetes-konfiguration (distributioner, namnrymder och så vidare) i en git-lagringsplats följt av en avsöknings-och pull-baserad distribution av dessa konfigurationer till klustret med hjälp av en operatör. Det här dokumentet beskriver installationen av sådana arbets flöden i Azure Arc-aktiverade Kubernetes-kluster.

Anslutningen mellan klustret och en eller flera git-databaser spåras i Azure Resource Manager som `sourceControlConfiguration` tilläggs resurs. `sourceControlConfiguration`Resurs egenskaperna representerar var och hur Kubernetes resurser ska flöda från git till klustret. `sourceControlConfiguration`Data lagras krypterade i vila i en Azure Cosmos DB databas för att säkerställa data sekretessen.

Den som `config-agent` körs i klustret ansvarar för att titta efter nya eller uppdaterade `sourceControlConfiguration` tilläggs resurser på den Azure Arc-aktiverade Kubernetes-resursen, distribuera en flödes operatör för att se git-lagringsplatsen och sprida alla uppdateringar som görs till `sourceControlConfiguration` . Det går även att skapa flera `sourceControlConfiguration` resurser med `namespace` omfång på samma Azure Arc-Kubernetes-kluster för att uppnå flera innehavare. I sådana fall kan varje operatör bara distribuera konfigurationer till dess respektive namnrymd.

Git-lagringsplatsen kan innehålla alla giltiga Kubernetes-resurser, inklusive namnrymder, ConfigMaps, distributioner, DaemonSets osv.  Det kan också innehålla Helm-diagram för att distribuera program. En vanlig uppsättning scenarier innefattar att definiera en bas linje konfiguration för din organisation, som kan innehålla vanliga Azure-roller och bindningar, övervaknings-eller loggnings agenter eller klustrade tjänster.

Samma mönster kan användas för att hantera en större samling kluster, som kan distribueras i heterogena miljöer. Du kan till exempel ha en lagrings plats som definierar bas linje konfigurationen för din organisation och tillämpa den på flera Kubernetes-kluster samtidigt. Med [Azure policy kan du automatisera](use-azure-policy.md) skapandet av en `sourceControlConfiguration` med en speciell uppsättning parametrar på alla Azure Arc-aktiverade Kubernetes-resurser under ett omfång (prenumeration eller resurs grupp).

Den här kom igång-guiden hjälper dig att använda en uppsättning konfigurationer med kluster administratörs omfång.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt Azure-båg aktiverat Kubernetes-kopplat kluster. Om du behöver ett anslutet kluster kan du läsa snabb starten för att [ansluta ett kluster](./connect-cluster.md).

## <a name="create-a-configuration"></a>Skapa en konfiguration

[Exempel lagrings platsen](https://github.com/Azure/arc-k8s-demo) som används i det här dokumentet är strukturerad runt personen i en kluster operatör som vill etablera några få namn rymder, distribuera en gemensam arbets belastning och ange en team-speciell konfiguration. När du använder den här databasen skapas följande resurser i klustret:

**Namnrymder:** `cluster-config` , `team-a` , `team-b` 
 **distribution:** `cluster-config/azure-vote` 
 **ConfigMap:**`team-a/endpoints`

`config-agent`Söker Azure efter nya eller uppdaterade `sourceControlConfiguration` var 30: e sekund, vilket är den längsta tid det tar för `config-agent` att hämta en ny eller uppdaterad konfiguration.
Om du associerar ett privat lager med `sourceControlConfiguration` måste du också slutföra stegen i [tillämpa konfiguration från en privat git-lagringsplats](#apply-configuration-from-a-private-git-repository).

### <a name="using-azure-cli"></a>Använda Azure CLI

Vi använder Azure CLI-tillägget för `k8sconfiguration` , vi länkar vårt anslutna kluster till ett [exempel git-lagringsplats](https://github.com/Azure/arc-k8s-demo). Vi kommer att ge den här konfigurationen ett namn `cluster-config` , instruera agenten att distribuera operatorn i `cluster-config` namn området och ge operatörs `cluster-admin` behörighet.

```console
az k8sconfiguration create --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --operator-instance-name cluster-config --operator-namespace cluster-config --repository-url https://github.com/Azure/arc-k8s-demo --scope cluster --cluster-type connectedClusters
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

| Scenario | Format | Description |
| ------------- | ------------- | ------------- |
| Offentlig git-lagrings platsen | http [s]://Server/repo.git eller git://server/repo.git   | Offentlig git-lagrings platsen  |
| Privata git-lagrings platsen – SSH – flöden-skapade nycklar | SSH://[user@] Server/lagrings platsen. git eller [user@] Server: lagrings platsen. git | Den offentliga nyckeln som genereras av flöde måste läggas till i användar kontot i git-tjänstprovidern. Om distributions nyckeln läggs till i lagrings platsen i stället för användar kontot använder du `git@` i stället för `user@` . [Här](#apply-configuration-from-a-private-git-repository) hittar du mer information |

Dessa scenarier stöds av flöde, men ännu inte av sourceControlConfiguration.

| Scenario | Format | Description |
| ------------- | ------------- | ------------- |
| Privat git lagrings platsen-HTTPS | https://server/repo.git | Kommer snart (stöd för användar namn/lösen ord, användar namn/token, certifikat) |
| Privata git-lagrings platsen – SSH – användarspecifika nycklar | SSH://[user@] Server/lagrings platsen. git eller [user@] Server: lagrings platsen. git | Kommer snart |
| Privat git-värd – SSH – anpassad known_hosts | SSH://[user@] Server/lagrings platsen. git eller [user@] Server: lagrings platsen. git | Kommer snart |

#### <a name="additional-parameters"></a>Ytterligare parametrar

Här följer några ytterligare parametrar för att anpassa skapandet av konfigurationen:

`--enable-helm-operator` : *Valfri* växel för att aktivera stöd för Helm-diagram distributioner.

`--helm-operator-chart-values` : *Valfria* diagram värden för Helm-operatorn (om aktive rad).  Till exempel "--Set Helm. versions = v3".

`--helm-operator-chart-version` : *Valfri* diagram version för Helm-operatorn (om aktive rad). Standard: ' 0.6.0 '.

`--operator-namespace` : Det *valfria* namnet för operatorns namn område. Standard: standard

`--operator-params` : *Valfria* parametrar för operatorn. Måste anges inom enkla citat tecken. Till exempel ```--operator-params='--git-readonly --git-path=releases' ```

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

* Om enableHelmOperator är true kan operatorInstanceName + operatorNamespace-strängar inte överstiga 47 tecken.  Om du inte följer den här gränsen får du följande fel meddelande:

   ```console
   {"OperatorMessage":"Error: {failed to install chart from path [helm-operator] for release [<operatorInstanceName>-helm-<operatorNamespace>]: err [release name \"<operatorInstanceName>-helm-<operatorNamespace>\" exceeds max length of 53]} occurred while doing the operation : {Installing the operator} on the config","ClusterState":"Installing the operator"}
   ```

Mer information finns i [flödes dokumentation](https://aka.ms/FluxcdReadme).

> [!TIP]
> Det är möjligt att skapa en sourceControlConfiguration på Azure Portal och på fliken **konfigurationer** på resurs bladet Azure Arc-aktiverade Kubernetes.

## <a name="validate-the-sourcecontrolconfiguration"></a>Verifiera sourceControlConfiguration

Verifiera att du har skapat genom att använda Azure CLI `sourceControlConfiguration` .

```console
az k8sconfiguration show --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
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
1. `config-agent` Observera den nya `Pending` konfigurationen
1. `config-agent` läser konfigurations egenskaperna och förbereder distributionen av en hanterad instans av `flux`
    * `config-agent` skapar mål namn området
    * `config-agent` förbereder ett Kubernetes tjänst konto med rätt behörighet ( `cluster` eller `namespace` omfång)
    * `config-agent` distribuerar en instans av `flux`
    * `flux` genererar en SSH-nyckel och loggar den offentliga nyckeln
1. `config-agent` rapporterar status tillbaka till `sourceControlConfiguration`

När etablerings processen utförs går det `sourceControlConfiguration` igenom några tillstånds ändringar. Övervaka förloppet med `az k8sconfiguration show ...` kommandot ovan:

1. `complianceStatus` -> `Pending`: representerar inledande och pågående tillstånd
1. `complianceStatus` -> `Installed`: `config-agent` kunde konfigurera klustret och distribuera `flux` utan fel
1. `complianceStatus` -> `Failed`: `config-agent` ett fel uppstod vid distribution `flux` . informationen bör vara tillgänglig i `complianceStatus.message` svars texten

## <a name="apply-configuration-from-a-private-git-repository"></a>Tillämpa konfigurationen från en privat git-lagringsplats

Om du använder en privat git-lagrings platsen måste du utföra ytterligare en uppgift för att stänga slingan: Lägg till den offentliga nyckeln som genereras av `flux` som en **distributions nyckel** i lagrings platsen.

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

Om du använder GitHub kan du använda något av följande två alternativ:

**Alternativ 1: Lägg till den offentliga nyckeln till ditt användar konto**

1. Öppna GitHub, klicka på din profil ikon i det övre högra hörnet på sidan.
2. Klicka på **Inställningar**
3. Klicka på **SSH-och GPG-nycklar**
4. Klicka på **ny SSH-nyckel**
5. Ange en rubrik
6. Klistra in den offentliga nyckeln (minus omgivande citat tecken)
7. Klicka på **Lägg till SSH-nyckel**

**Alternativ 2: Lägg till den offentliga nyckeln som en distributions nyckel till git-lagrings platsen**

1. Öppna GitHub, navigera till lagrings platsen, klicka på **Inställningar** och **distribuera nycklar**
2. Klicka på **Lägg till distributions nyckel**
3. Ange en rubrik
4. Kontrol lera **Tillåt skriv åtkomst**
5. Klistra in den offentliga nyckeln (minus omgivande citat tecken)
6. Klicka på **Lägg till nyckel**

**Om du använder en Azure DevOps-lagringsplats lägger du till nyckeln till dina SSH-nycklar**

1. Under **användar inställningar** längst upp till höger (bredvid profil avbildningen) klickar du på **offentliga SSH-nycklar**
1. Välj  **+ ny nyckel**
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

Ta bort en `sourceControlConfiguration` med hjälp av Azure CLI eller Azure Portal.  När du har initierat kommandot Ta bort `sourceControlConfiguration` kommer resursen att tas bort omedelbart i Azure, men det kan ta upp till 1 timme för fullständig borttagning av de associerade objekten från klustret (vi har ett artikel efter släpning för att minska den här tids fördröjningen).

> [!NOTE]
> När en sourceControlConfiguration med namn områdes omfånget har skapats är det möjligt för användare med `edit` roll bindning i namn område att distribuera arbets belastningar i namn området. När detta `sourceControlConfiguration` med namn områdes omfånget tas bort lämnas namn området kvar och tas inte bort för att undvika att de andra arbets belastningarna bryts.
> Eventuella ändringar i klustret som resulterade i distributioner från det spårade git-lagrings platsen tas inte bort när tas `sourceControlConfiguration` bort.

```console
az k8sconfiguration delete --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

**Utdataparametrar**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
```

## <a name="next-steps"></a>Nästa steg

- [Använd Helm med käll kontroll konfiguration](./use-gitops-with-helm.md)
- [Använd Azure Policy för att styra kluster konfigurationen](./use-azure-policy.md)
