---
title: Operatorn metodtips – Klustersäkerhet i Azure Kubernetes Services (AKS)
description: Läs kluster operatorn metodtipsen för hur du hanterar Klustersäkerhet och uppgraderingar i Azure Kubernetes Service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: mlearned
ms.openlocfilehash: 46e44804ddbabd8bf5620ad9516f1ca2d5017bfa
ms.sourcegitcommit: b12a25fc93559820cd9c925f9d0766d6a8963703
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/14/2019
ms.locfileid: "69019313"
---
# <a name="best-practices-for-cluster-security-and-upgrades-in-azure-kubernetes-service-aks"></a>Metodtips för Klustersäkerhet och uppgraderingar i Azure Kubernetes Service (AKS)

När du hanterar kluster i Azure Kubernetes Service (AKS) är en nyckelfaktor säkerheten för dina arbetsbelastningar och data. Särskilt när du kör kluster för flera innehavare med hjälp av logisk isolering kan behöva du säker åtkomst till resurser och arbetsbelastningar. För att minimera risken för angrepp, måste du också se till att du använder den senaste Kubernetes och noden OS-säkerhetsuppdateringar.

Den här artikeln handlar om hur du skyddar ditt AKS-kluster. Lär dig att:

> [!div class="checklist"]
> * Använd Azure Active Directory och rollbaserade åtkomstkontroller för att säker åtkomst till API-servern
> * Säkra behållare åtkomst till nod-resurser
> * Uppgradera ett AKS-kluster till den senaste versionen av Kubernetes
> * Behåll noder update hittills och automatiskt tillämpa säkerhetsuppdateringar

Du kan också läsa metod tips för [hantering][best-practices-container-image-management] av behållar avbildningar och [Pod säkerhet][best-practices-pod-security].

## <a name="secure-access-to-the-api-server-and-cluster-nodes"></a>Säker åtkomst till API-noder för server och kluster

**Bästa praxis riktlinjer** -skydda åtkomst till Kubernetes API-Server är en av de viktigaste sakerna som du kan göra för att säkra ditt kluster. Integrera Kubernetes rollbaserad åtkomstkontroll (RBAC) med Azure Active Directory för att styra åtkomsten till API-servern. Dessa kontroller kan du skydda AKS på samma sätt som du säker åtkomst till dina Azure-prenumerationer.

Kubernetes API-servern tillhandahåller en enda anslutningspunkt för begäranden för att utföra åtgärder i ett kluster. Om du vill skydda och granska åtkomst till API-servern, begränsar åtkomst och ange de minsta Privilegierade åtkomstbehörigheter som krävs. Den här metoden är inte unikt för Kubernetes, men det är särskilt viktigt när AKS-klustret är logiskt isolerad för flera innehavare.

Azure Active Directory (AD) innehåller en lösning för Identitetshantering för företag som kan integreras med AKS-kluster. Eftersom Kubernetes inte tillhandahåller en lösning för Identitetshantering, kan det annars vara svårt att tillhandahålla ett detaljerat sätt att begränsa åtkomsten till API-servern. Med Azure AD-integrerade kluster i AKS kan använda du din befintliga användarkonton och gruppkonton för att autentisera användare till API-servern.

![Azure Active Directory-integrering för AKS-kluster](media/operator-best-practices-cluster-security/aad-integration.png)

Använda Kubernetes RBAC och Azure AD-integrering för att skydda API-servern och ge minst antal behörigheter som krävs till en begränsad uppsättning resurser, till exempel en enda namnrymd. Olika användare eller grupper i Azure AD kan tilldelas olika RBAC-roller. De här detaljerade behörigheter kan du begränsa åtkomsten till API-servern och ge en tydlig granskningslogg för åtgärder som utförs.

Rekommenderad praxis är att använda grupper för att ge åtkomst till filer och mappar jämfört med individuella identiteter, använder du Azure AD *grupp* medlemskap att binda användare till RBAC-roller i stället för enskilda *användare*. Som en användares ändringar i gruppmedlemskap, skulle deras åtkomstbehörigheter på AKS-klustret ändras. Om du binder användaren direkt till en roll, ändra arbetsuppgifter. Azure AD-gruppmedlemskap uppdateras, men behörigheter på AKS-klustret skulle inte visa som. I det här scenariot identisk användaren beviljas fler behörigheter än vad en användare kräver.

Mer information om Azure AD-integrering och RBAC finns i [metod tips för autentisering och auktorisering i AKS][aks-best-practices-identity].

## <a name="secure-container-access-to-resources"></a>Säkra behållare åtkomst till resurser

**Bästa praxis riktlinjer** – begränsa åtkomsten till åtgärder som behållare kan utföra. Ange minst antal behörigheter, och Undvik att använda root / privilegierad eskalering.

På samma sätt som du bör ge användare eller grupper minsta möjliga antal behörigheter som krävs, behållare bör också begränsas till endast de åtgärder och processer som de behöver. För att minimera risken för angrepp, inte konfigurera program och behållare som kräver eskalerade privilegier eller rot åtkomst. Till exempel `allowPrivilegeEscalation: false` i pod-manifestet. Dessa *pod security kontexter* är inbyggda i Kubernetes och kan du definiera ytterligare behörigheter som användaren eller gruppen som ska köras som, eller vilka Linux-funktioner för att exponera. Mer metod tips finns i [skydda Pod-åtkomst till resurser][pod-security-contexts].

För mer detaljerad kontroll över behållaråtgärder du kan också använda inbyggda säkerhetsfunktioner för Linux som *AppArmor* och *seccomp*. De här funktionerna definieras nivån, och sedan implementerats via en pod-manifestet. Inbyggda Linux-säkerhetsfunktioner är bara tillgängliga på Linux-noder och poddar.

> [!NOTE]
> Kubernetes-miljöer, i AKS eller någon annan stans, är inte helt säkra för att ta skydd på flera klienter. Ytterligare säkerhetsfunktioner som *apparmor*, *Seccomp*, *Pod Security Policies*eller mer detaljerade rollbaserade åtkomst kontroller (RBAC) för noder gör det svårare att utnyttja dem. Men för verklig säkerhet när du kör en skydds arbets belastning med flera innehavare, är en hypervisor den enda säkerhets nivå som du bör lita på. Säkerhets domänen för Kubernetes blir hela klustret, inte en enskild nod. För dessa typer av farliga arbets belastningar med flera klienter bör du använda fysiskt isolerade kluster.

### <a name="app-armor"></a>App Armor

Om du vill begränsa vilka åtgärder som behållare kan utföra kan du använda [apparmor][k8s-apparmor] Linux kernel-säkerhetsmodulen. AppArmor är tillgänglig som en del av den underliggande AKS-noden OS, och är aktiverad som standard. Du skapar AppArmor profiler som till exempel begränsar åtgärder, läsa, skriva eller köra eller systemfunktioner, till exempel montera filsystem. AppArmor standardprofiler begränsa åtkomsten till olika `/proc` och `/sys` platser, och tillhandahålla ett sätt att isolera logiskt behållare från den underliggande noden. AppArmor fungerar för alla program som körs på Linux, inte bara Kubernetes-poddar.

![AppArmor profiler som används i ett AKS-kluster för att begränsa behållaråtgärder](media/operator-best-practices-container-security/apparmor.png)

Om du vill se hur AppArmor fungerar, skapas i följande exempel en profil som förhindrar att skriva till filer. [SSH][aks-ssh] till en AKS-nod och skapa sedan en fil med namnet *Deny-Write. Profile* och klistra in följande innehåll:

```
#include <tunables/global>
profile k8s-apparmor-example-deny-write flags=(attach_disconnected) {
  #include <abstractions/base>
  
  file,
  # Deny all file writes.
  deny /** w,
}
```

AppArmor profiler har lagts till med den `apparmor_parser` kommando. Lägga till profilen AppArmor och ange namnet på profilen som du skapade i föregående steg:

```console
sudo apparmor_parser deny-write.profile
```

Det finns inga utdata som returneras om profilen är korrekt parsas och tillämpas på AppArmor. Du kommer tillbaka till Kommandotolken.

Från den lokala datorn nu skapa en pod-manifest med namnet *aks apparmor.yaml* och klistra in följande innehåll. Den här tjänstmanifestet definierar en kommentar för `container.apparmor.security.beta.kubernetes` Lägg till referenser i *Neka skrivning* profil som skapats i föregående steg:

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

Distribuera exempel-Pod med kommandot [kubectl Apply][kubectl-apply] :

```console
kubectl apply -f aks-apparmor.yaml
```

När Pod har distribuerats använder du kommandot [kubectl exec][kubectl-exec] för att skriva till en fil. Att går inte köra kommandot som visas i följande Exempelutdata:

```
$ kubectl exec hello-apparmor touch /tmp/test

touch: /tmp/test: Permission denied
command terminated with exit code 1
```

Mer information om AppArmor finns [i AppArmor-profiler i Kubernetes][k8s-apparmor].

### <a name="secure-computing"></a>Säker databehandling

Medan AppArmor fungerar för alla Linux-program fungerar [seccomp (*SEK*urera *comp*uting)][seccomp] på process nivå. Seccomp är också en Linux-kernel-modul för maskinvarusäkerhet och stöds internt av Docker-IR som används av AKS-noder. Med seccomp är process-anrop som behållare kan utföra begränsade. Du skapar filter som definierar vilka åtgärder som tillåter eller nekar och sedan använda anteckningar i en pod YAML-manifestet för att associera med filtret seccomp. Detta stämmer överens med bästa praxis och endast beviljar behållaren minimala behörigheter som behövs för att köra och inga fler.

Om du vill se seccomp i praktiken, skapa ett filter som förhindrar att ändra behörigheterna för en fil. [SSH][aks-ssh] till en AKS-nod och skapa sedan ett seccomp-filter med namnet */var/lib/kubelet/seccomp/Prevent-chmod* och klistra in följande innehåll:

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

Från den lokala datorn nu skapa en pod-manifest med namnet *aks seccomp.yaml* och klistra in följande innehåll. Den här tjänstmanifestet definierar en kommentar för `seccomp.security.alpha.kubernetes.io` och refererar till den *förhindra chmod* adressfilter som skapades i föregående steg:

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

Distribuera exempel-Pod med kommandot [kubectl Apply][kubectl-apply] :

```console
kubectl apply -f ./aks-seccomp.yaml
```

Visa status för poddar med kommandot [kubectl get poddar][kubectl-get] . Poden rapporterar ett fel. Den `chmod` kommandot förhindras från att köras som seccomp-filtret som visas i följande Exempelutdata:

```
$ kubectl get pods

NAME                      READY     STATUS    RESTARTS   AGE
chmod-prevented           0/1       Error     0          7s
```

Mer information om tillgängliga filter finns i [Seccomp Security profils for Docker][seccomp].

## <a name="regularly-update-to-the-latest-version-of-kubernetes"></a>Regelbundet uppdatera till den senaste versionen av Kubernetes

**Bästa praxis riktlinjer** – om du vill hålla dig uppdaterad om nya funktioner och felkorrigeringar, regelbundet uppgradering till Kubernetes-version i AKS-klustret.

Kubernetes-versioner nya funktioner i en snabbare takt än mer traditionellt infrastrukturplattformar. Kubernetes-uppdateringar med nya funktioner och korrigeringar för bugg eller säkerhet. Nya funktioner går vanligtvis igenom en *alpha* och sedan *beta* status innan de blir *stabil* och är allmänt tillgängliga och rekommenderas för användning i produktion. Den här versionen cykeln värde kan du uppdatera Kubernetes utan regelbundet får större ändringar eller justera dina distributioner och mallar.

AKS stöder fyra mindre versioner av Kubernetes. Det innebär att de äldsta mindre version och patch versioner som stöds är föråldrade när en ny Uppdateringsversion mindre införs. Mindre uppdateringar till Kubernetes inträffar på regelbunden basis. Se till att du har en styrning process för att kontrollera och uppgradera efter behov så att du inte följer support. Mer information finns i [Kubernetes-versioner som stöds AKS][aks-supported-versions]

Om du vill kontrol lera vilka versioner som är tillgängliga för klustret använder du kommandot [AZ AKS get-uppgraderingar][az-aks-get-upgrades] som visas i följande exempel:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

Sedan kan du uppgradera ditt AKS-kluster med hjälp av kommandot [AZ AKS Upgrade][az-aks-upgrade] . Uppgraderingsprocessen på ett säkert sätt cordons tömmer en nod i taget, schemalägger poddar på övriga noder och distribuerar sedan en ny nod som kör de senaste versionerna av OS och Kubernetes.

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version KUBERNETES_VERSION
```

Mer information om uppgraderingar i AKS finns i [Kubernetes-versioner som stöds i AKS][aks-supported-versions] och [uppgradera ett AKS-kluster][aks-upgrade].

## <a name="process-linux-node-updates-and-reboots-using-kured"></a>Bearbeta uppdateringar och omstarter av Linux-noder med kured

**Vägledning för bästa praxis** – AKS hämtar och installerar automatiskt säkerhets korrigeringar på varje Linux-noder, men startar inte om automatiskt om det behövs. Använd `kured` för att söka efter väntande omstarter, och sedan på ett säkert sätt här och tömmer noden så att noden ska startas om, tillämpa uppdateringarna och vara precis lika säkert som möjligt med avseende på datorns operativsystem. För Windows Server-noder (för närvarande i för hands version i AKS) utför du regelbundet en AKS uppgraderings åtgärd för att säkert Cordon och tömma poddar och distribuera uppdaterade noder.

Varje kväll får Linux-noder i AKS säkerhets korrigeringar som är tillgängliga via sin distribution Update-kanal. Det här beteendet konfigureras automatiskt när noderna är distribuerade i ett AKS-kluster. För att minimera störningar och möjliga inverkan på arbetsbelastningar som körs, noder automatiskt startas om inte om en säkerhetskorrigering eller kernel-uppdateringen kräver den.

Kured-projektet med öppen källkod [(KUbernetes REboot)][kured] av Weaveworks söker efter väntande Node-omstarter. När en Linux-nod använder uppdateringar som kräver en omstart, är noden säkert avspärrade och töms för att flytta och schemalägga poddar på andra noder i klustret. När noden startas om, läggs den tillbaka till klustret och Kubernetes återupptar schemaläggning poddar på den. För att minimera störningar tillåts bara en nod i taget startas av `kured`.

![AKS noden omstart processen med kured](media/operator-best-practices-cluster-security/node-reboot-process.png)

Om du vill ha mer detaljerad grain kontroll över omstarter uppstår när `kured` kan integreras med Prometheus att förhindra omstarter om det finns andra underhållshändelser eller kluster problem pågår. Den här integreringen minimerar ytterligare problem genom att starta om noderna medan du aktivt felsöker andra problem.

Mer information om hur du hanterar omstarter av noder finns i [tillämpa säkerhets-och kernel-uppdateringar på noder i AKS][aks-kured].

## <a name="next-steps"></a>Nästa steg

Den här artikeln fokuserar på hur du skyddar ditt AKS-kluster. Om du vill implementera vissa av dessa områden, finns i följande artiklar:

* [Integrera Azure Active Directory med AKS][aks-aad]
* [Uppgradera ett AKS-kluster till den senaste versionen av Kubernetes][aks-upgrade]
* [Bearbeta säkerhets uppdateringar och omstarter av noder med kured][aks-kured]

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
