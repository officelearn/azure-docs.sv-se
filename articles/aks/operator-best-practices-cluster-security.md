---
title: Metodtips för klustersäkerhet
titleSuffix: Azure Kubernetes Service
description: Lär dig metodtips för klusteroperatören för hur du hanterar klustersäkerhet och uppgraderingar i Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: 3d4e8577116ba1d78aaa881887f64e71c04af4f2
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668330"
---
# <a name="best-practices-for-cluster-security-and-upgrades-in-azure-kubernetes-service-aks"></a>Metodtips för klustersäkerhet och uppgraderingar i Azure Kubernetes Service (AKS)

När du hanterar kluster i Azure Kubernetes Service (AKS) är säkerheten för dina arbetsbelastningar och data en viktig faktor. Särskilt när du kör kluster med flera innehavare med logisk isolering måste du skydda åtkomsten till resurser och arbetsbelastningar. För att minimera risken för angrepp måste du också se till att du tillämpar de senaste säkerhetsuppdateringarna för Kubernetes och nodoperativsystemet.

Den här artikeln fokuserar på hur du skyddar AKS-klustret. Lär dig att:

> [!div class="checklist"]
> * Använda Azure Active Directory och rollbaserade åtkomstkontroller för att skydda API-serveråtkomst
> * Säker behållaråtkomst till nodresurser
> * Uppgradera ett AKS-kluster till den senaste Kubernetes-versionen
> * Håll noderna uppdaterade och tillämpa automatiskt säkerhetskorrigeringar

Du kan också läsa metodtipsen för hantering av [behållaravbildningar][best-practices-container-image-management] och för [pod-säkerhet][best-practices-pod-security].

Du kan också använda [Azure Kubernetes Services-integrering med Security Center][security-center-aks] för att identifiera hot och visa rekommendationer för att skydda dina AKS-kluster.

## <a name="secure-access-to-the-api-server-and-cluster-nodes"></a>Säker åtkomst till API-servern och klusternoderna

**Vägledning för bästa praxis** – Att skydda åtkomsten till Kubernetes API-Server är en av de viktigaste sakerna du kan göra för att skydda klustret. Integrera Kubernetes rollbaserade åtkomstkontroll (RBAC) med Azure Active Directory för att styra åtkomsten till API-servern. Med de här kontrollerna kan du skydda AKS på samma sätt som du skyddar åtkomsten till dina Azure-prenumerationer.

Kubernetes API-server ger en enda anslutningspunkt för begäranden att utföra åtgärder i ett kluster. Om du vill skydda och granska åtkomsten till API-servern begränsar du åtkomsten och ger de minst privilegierade åtkomstbehörigheter som krävs. Den här metoden är inte unik för Kubernetes, men är särskilt viktig när AKS-klustret är logiskt isolerat för användning av flera innehavare.

Azure Active Directory (AD) tillhandahåller en företagsklar identitetshanteringslösning som integreras med AKS-kluster. Eftersom Kubernetes inte tillhandahåller en identitetshanteringslösning kan det annars vara svårt att tillhandahålla ett detaljerat sätt att begränsa åtkomsten till API-servern. Med Azure AD-integrerade kluster i AKS använder du dina befintliga användar- och gruppkonton för att autentisera användare till API-servern.

![Azure Active Directory-integrering för AKS-kluster](media/operator-best-practices-cluster-security/aad-integration.png)

Använd Kubernetes RBAC och Azure AD-integrering för att skydda API-servern och ge minst antal behörigheter som krävs för en begränsad uppsättning resurser, till exempel ett enda namnområde. Olika användare eller grupper i Azure AD kan beviljas olika RBAC-roller. Med de här detaljerade behörigheterna kan du begränsa åtkomsten till API-servern och tillhandahålla en tydlig granskningsspårning av åtgärder som utförs.

Den rekommenderade bästa metoden är att använda grupper för att ge åtkomst till filer och mappar jämfört med enskilda identiteter, använda Azure *AD-gruppmedlemskap* för att binda användare till RBAC-roller i stället för enskilda *användare*. När en användares gruppmedlemskap ändras ändras deras åtkomstbehörigheter i AKS-klustret i enlighet med detta. Om du binder användaren direkt till en roll kan deras jobbfunktion ändras. Azure AD-gruppmedlemskap skulle uppdateras, men behörigheter för AKS-klustret återspeglar inte det. I det här fallet får användaren fler behörigheter än vad en användare kräver.

Mer information om Azure AD-integrering och RBAC finns [i Metodtips för autentisering och auktorisering i AKS][aks-best-practices-identity].

## <a name="secure-container-access-to-resources"></a>Säker behållaråtkomst till resurser

**Vägledning för bästa praxis** - Begränsa åtkomsten till åtgärder som behållare kan utföra. Ange minst antal behörigheter och undvik användning av rot/privilegierad eskalering.

På samma sätt som du bör ge användare eller grupper minst antal privilegier som krävs, bör behållare också begränsas till endast de åtgärder och processer som de behöver. För att minimera risken för angrepp ska du inte konfigurera program och behållare som kräver eskalerade privilegier eller root-åtkomst. Ange till `allowPrivilegeEscalation: false` exempel i pod-manifestet. Dessa *pod säkerhetskontexter* är inbyggda i Kubernetes och låter dig definiera ytterligare behörigheter som användaren eller gruppen att köra som, eller vilka Linux-funktioner att exponera. Mer metodtips finns i [Säker pod-åtkomst till resurser][pod-security-contexts].

För mer detaljerad kontroll av behållaråtgärder kan du också använda inbyggda Linux-säkerhetsfunktioner som *AppArmor* och *seccomp*. Dessa funktioner definieras på nodnivå och implementeras sedan via ett pod-manifest. Inbyggda Linux-säkerhetsfunktioner är endast tillgängliga på Linux-noder och poddar.

> [!NOTE]
> Kubernetes miljöer, i AKS eller någon annanstans, är inte helt säkra för fientlig användning av flera innehavare. Ytterligare säkerhetsfunktioner som *AppArmor*, *seccomp,* *Pod Security Policies*eller mer detaljerade rollbaserade åtkomstkontroller (RBAC) för noder gör det svårare att utnyttja. Men för verklig säkerhet när du kör fientliga arbetsbelastningar med flera innehavare är en hypervisor den enda säkerhetsnivå som du bör lita på. Säkerhetsdomänen för Kubernetes blir hela klustret, inte en enskild nod. För dessa typer av fientliga arbetsbelastningar med flera innehavare bör du använda fysiskt isolerade kluster.

### <a name="app-armor"></a>App Rustning

Om du vill begränsa de åtgärder som behållare kan utföra kan du använda säkerhetsmodulen [AppArmor][k8s-apparmor] Linux kernel. AppArmor är tillgängligt som en del av det underliggande AKS-nodoperativsystemet och är aktiverat som standard. Du skapar AppArmor-profiler som begränsar åtgärder som att läsa, skriva eller köra, eller systemfunktioner som montering av filsystem. StandardappArmor-profiler begränsar `/proc` åtkomsten till olika platser och `/sys` ger ett sätt att logiskt isolera behållare från den underliggande noden. AppArmor fungerar för alla program som körs på Linux, inte bara Kubernetes poddar.

![AppArmor-profiler som används i ett AKS-kluster för att begränsa behållaråtgärder](media/operator-best-practices-container-security/apparmor.png)

Om du vill visa AppArmor i praktiken skapar följande exempel en profil som förhindrar att filer skrivs. [SSH][aks-ssh] till en AKS-nod och skapa sedan en fil med namnet *deny-write.profile* och klistra in följande innehåll:

```
#include <tunables/global>
profile k8s-apparmor-example-deny-write flags=(attach_disconnected) {
  #include <abstractions/base>
  
  file,
  # Deny all file writes.
  deny /** w,
}
```

AppArmor-profiler läggs `apparmor_parser` till med kommandot. Lägg till profilen i AppArmor och ange namnet på den profil som skapades i föregående steg:

```console
sudo apparmor_parser deny-write.profile
```

Ingen utdata returneras om profilen är korrekt tolkad och tillämpas på AppArmor. Du är tillbaka i kommandotolken.

Från din lokala dator skapar du nu ett pod-manifest med namnet *aks-apparmor.yaml* och klistrar in följande innehåll. Det här manifestet definierar `container.apparmor.security.beta.kubernetes` en anteckning för att lägga till referenser den *neka-skriva-profil* som skapats i föregående steg:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: hello-apparmor
  annotations:
    container.apparmor.security.beta.kubernetes.io/hello: localhost/k8s-apparmor-example-deny-write
spec:
  containers:
  - name: hello
    image: busybox
    command: [ "sh", "-c", "echo 'Hello AppArmor!' && sleep 1h" ]
```

Distribuera exempelenheten med kommandot [kubectl apply:][kubectl-apply]

```console
kubectl apply -f aks-apparmor.yaml
```

När podden har distribuerats använder du kommandot [kubectl exec][kubectl-exec] för att skriva till en fil. Kommandot kan inte köras, vilket visas i följande exempelutdata:

```
$ kubectl exec hello-apparmor touch /tmp/test

touch: /tmp/test: Permission denied
command terminated with exit code 1
```

Mer information om AppArmor finns [i AppArmor-profiler i Kubernetes][k8s-apparmor].

### <a name="secure-computing"></a>Säker databehandling

Medan AppArmor fungerar för alla Linux-program, [sekcomp *(sec*ure *comp*uting)][seccomp] fungerar på processnivå. Seccomp är också en Linux-kärnsäkerhetsmodul och stöds internt av Docker-körningen som används av AKS-noder. Med seccomp är processanropen som behållare kan utföra begränsade. Du skapar filter som definierar vilka åtgärder som ska tillåtas eller nekas och använder sedan anteckningar i ett YAML-pod-manifest för att associera med sekcomp-filtret. Detta anpassar sig till bästa praxis att endast bevilja behållaren de minimala behörigheter som behövs för att köras och inte mer.

Om du vill se seccomp-åtgärden skapar du ett filter som förhindrar att behörigheter ändras för en fil. [SSH][aks-ssh] till en AKS-nod och skapa sedan ett seccomp-filter med namnet */var/lib/kubelet/seccomp/prevent-chmod* och klistra in följande innehåll:

```
{
  "defaultAction": "SCMP_ACT_ALLOW",
  "syscalls": [
    {
      "name": "chmod",
      "action": "SCMP_ACT_ERRNO"
    }
  ]
}
```

Från den lokala datorn skapar du nu ett pod-manifest med namnet *aks-seccomp.yaml* och klistrar in följande innehåll. Det här manifestet definierar `seccomp.security.alpha.kubernetes.io` en anteckning för och refererar till filtret *prevent-chmod* som skapades i föregående steg:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: chmod-prevented
  annotations:
    seccomp.security.alpha.kubernetes.io/pod: localhost/prevent-chmod
spec:
  containers:
  - name: chmod
    image: busybox
    command:
      - "chmod"
    args:
     - "777"
     - /etc/hostname
  restartPolicy: Never
```

Distribuera exempelenheten med kommandot [kubectl apply:][kubectl-apply]

```console
kubectl apply -f ./aks-seccomp.yaml
```

Visa status för poddar med kommandot [kubectl get pods.][kubectl-get] Podden rapporterar ett fel. Kommandot `chmod` hindras från att köras av seccomp-filtret, vilket visas i följande exempelutdata:

```
$ kubectl get pods

NAME                      READY     STATUS    RESTARTS   AGE
chmod-prevented           0/1       Error     0          7s
```

Mer information om tillgängliga filter finns i [Seccomp-säkerhetsprofiler för Docker][seccomp].

## <a name="regularly-update-to-the-latest-version-of-kubernetes"></a>Uppdatera regelbundet till den senaste versionen av Kubernetes

**Vägledning för bästa praxis** – Om du vill behålla aktuella funktioner och buggfixar uppgraderar du regelbundet till Kubernetes-versionen i AKS-klustret.

Kubernetes släpper nya funktioner i snabbare takt än mer traditionella infrastrukturplattformar. Kubernetes-uppdateringar innehåller nya funktioner och bugg- eller säkerhetskorrigeringar. Nya funktioner rör sig vanligtvis genom en *alfa-* och *betastatus* innan de blir *stabila* och är allmänt tillgängliga och rekommenderas för produktionsanvändning. Med den här versionscykeln kan du uppdatera Kubernetes utan att regelbundet stöta på bryta ändringar eller justera dina distributioner och mallar.

AKS stöder fyra delversioner av Kubernetes. Det innebär att när en ny mindre patchversion introduceras dras den äldsta delversionen och korrigeringsversionerna som stöds tillbaka. Mindre uppdateringar av Kubernetes sker regelbundet. Se till att du har en styrningsprocess för att kontrollera och uppgradera efter behov så att du inte faller ur supporten. Mer information finns i [Kubernetes-versioner SOM stöds AKS][aks-supported-versions]

Om du vill kontrollera vilka versioner som är tillgängliga för klustret använder du kommandot [az aks get-upgrades][az-aks-get-upgrades] som visas i följande exempel:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

Du kan sedan uppgradera AKS-klustret med kommandot [az aks upgrade.][az-aks-upgrade] Uppgraderingsprocessen spärrar säkert av och tömmer en nod i taget, schemalägger poddar på återstående noder och distribuerar sedan en ny nod som kör de senaste OS- och Kubernetes-versionerna.

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version KUBERNETES_VERSION
```

Mer information om uppgraderingar i AKS finns i [Kubernetes-versioner som stöds i AKS][aks-supported-versions] och [Uppgradera ett AKS-kluster][aks-upgrade].

## <a name="process-linux-node-updates-and-reboots-using-kured"></a>Bearbeta uppdateringar och omstarter av Linux-nod med kured

**Vägledning för bästa praxis** - AKS hämtar och installerar automatiskt säkerhetskorrigeringar på varje Linux-noder, men startas inte om automatiskt om det behövs. Används `kured` för att titta på väntande omstarter, sedan säkert avspärrning och tömma noden så att noden att starta om, tillämpa uppdateringarna och vara så säker som möjligt med avseende på operativsystemet. För Windows Server-noder (för närvarande i förhandsversion i AKS) utför du regelbundet en AKS-uppgradering för att på ett säkert sätt avspärra och tömma poddar och distribuera uppdaterade noder.

Varje kväll får Linux-noder i AKS säkerhetskorrigeringar tillgängliga via deras distributionsuppdateringskanal. Det här beteendet konfigureras automatiskt när noderna distribueras i ett AKS-kluster. För att minimera störningar och potentiell påverkan på arbetsbelastningar som körs startas noder inte automatiskt om en säkerhetskorrigering eller kärnuppdatering kräver det.

Projektet med öppen källkod [(KUbernetes REboot Daemon)][kured] av Weaveworks klockor för väntande nod omstarter. När en Linux-nod tillämpar uppdateringar som kräver en omstart, är noden säkert avspärrad och dränerad för att flytta och schemalägga poddar på andra noder i klustret. När noden startas om läggs den tillbaka till klustret och Kubernetes återupptar schemaläggningsenheter på den. För att minimera störningar tillåts endast en nod `kured`i taget att startas om av .

![AKS-noden omstartsprocessen med hjälp av kured](media/operator-best-practices-cluster-security/node-reboot-process.png)

Om du vill ha finare kornkontroll `kured` över när omstarter sker, kan integrera med Prometheus för att förhindra omstarter om det finns andra underhållshändelser eller klusterproblem pågår. Den här integreringen minimerar ytterligare komplikationer genom att starta om noder medan du aktivt felsöker andra problem.

Mer information om hur du hanterar omstarter av nod finns i [Tillämpa säkerhets- och kerneluppdateringar på noder i AKS][aks-kured].

## <a name="next-steps"></a>Nästa steg

Den här artikeln fokuserade på hur du skyddar AKS-klustret. Information om hur du implementerar några av dessa områden finns i följande artiklar:

* [Integrera Azure Active Directory med AKS][aks-aad]
* [Uppgradera ett AKS-kluster till den senaste versionen av Kubernetes][aks-upgrade]
* [Bearbeta säkerhetsuppdateringar och omstarter av noden med kured][aks-kured]

<!-- EXTERNAL LINKS -->
[kured]: https://github.com/weaveworks/kured
[k8s-apparmor]: https://kubernetes.io/docs/tutorials/clusters/apparmor/
[seccomp]: https://kubernetes.io/docs/concepts/policy/pod-security-policy/#seccomp
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-exec]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- INTERNAL LINKS -->
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[aks-supported-versions]: supported-kubernetes-versions.md
[aks-upgrade]: upgrade-cluster.md
[aks-best-practices-identity]: concepts-identity.md
[aks-kured]: node-updates-kured.md
[aks-aad]: azure-ad-integration.md
[best-practices-container-image-management]: operator-best-practices-container-image-management.md
[best-practices-pod-security]: developer-best-practices-pod-security.md
[pod-security-contexts]: developer-best-practices-pod-security.md#secure-pod-access-to-resources
[aks-ssh]: ssh.md
[security-center-aks]: /azure/security-center/azure-kubernetes-service-integration
