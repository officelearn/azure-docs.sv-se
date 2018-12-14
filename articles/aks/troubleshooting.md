---
title: Felsöka vanliga problem med Azure Kubernetes Service
description: Lär dig att felsöka och lösa vanliga problem när du använder Azure Kubernetes Service (AKS)
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: troubleshooting
ms.date: 08/13/2018
ms.author: saudas
ms.openlocfilehash: c20f2cc03565ce861dfc6317be8459fdafeef0bf
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2018
ms.locfileid: "53384113"
---
# <a name="aks-troubleshooting"></a>AKS felsökning
När du skapar eller manager AKS-kluster, kan ibland uppstå problem. Den här artikeln beskriver några vanliga problem och felsökning.

### <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-issues"></a>I allmänhet var hittar jag information om hur du felsöker problem med Kubernetes?

[Här](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/) är ett officiellt länk till felsökning kubernetes-kluster.
[Här](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md) är en länk till en felsökningsguide som publicerats av en Microsoft-tekniker kring felsökning poddar, noder, kluster, osv.

### <a name="i-am-getting-a-quota-exceeded-error-during-create-or-upgrade-what-should-i-do"></a>Jag får ett kvoten har överskridits-fel under Skapa eller uppgradering. Vad ska jag göra? 

Du kommer att behöva begära kärnor [här](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

### <a name="what-is-the-max-pods-per-node-setting-for-aks"></a>Vad är maximala poddarna per nod inställning för AKS?

Max poddarna per nod är inställda på 30 som standard om du distribuerar ett AKS-kluster i Azure-portalen.
Max poddarna per nod är inställda på 110 som standard om du distribuerar ett AKS-kluster i Azure CLI. (Se till att du använder den senaste versionen av Azure CLI). Den här standardinställningen kan ändras med hjälp av – max-noder-per-pod-flaggan i az-aks skapar kommandot.

### <a name="i-am-getting-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>Jag får ”insufficientSubnetSize”-fel när du distribuerar ett AKS-kluster med avancerade nätverk. Vad ska jag göra?

Anpassad VNET-alternativet som valts för nätverk under AKS skapar används Azure-CNI för IPAM. Antalet noder i ett AKS-kluster kan finnas var som helst mellan 1 och 100. Baserat på 2) ovan undernätet storlek måste vara större än produkten av antalet noder och max pod per undernät nodstorlek > Antal noder i klustret * max poddar per nod.

### <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>Min pod har fastnat i ”CrashLoopBackOff”-läge. Vad ska jag göra?

Det kan finnas olika anledningar för pod som fastnat i detta läge. Du kanske vill titta på den 
* Pod själva med hjälp av `kubectl describe pod <pod-name>`
* Loggar med  `kubectl log <pod-name>`

### <a name="i-am-trying-to-enable-rbac-on-an-existing-cluster-can-you-tell-me-how-i-can-do-that"></a>Jag försöker aktivera RBAC på ett befintligt kluster. Kan du berätta hur kan jag göra?

Tyvärr att aktivera RBAC på befintliga kluster stöds inte just nu. Du måste uttryckligen skapa nya kluster. Om du använder CLI RBAC är aktiverat som standard en knapp så att den är tillgänglig i portalen AKS skapa arbetsflöde.

### <a name="i-created-a-cluster-using-the-azure-cli-with-defaults-or-the-azure-portal-with-rbac-enabled-and-numerous-warnings-in-the-kubernetes-dashboard-the-dashboard-used-to-work-before-without-any-warnings-what-should-i-do"></a>Jag har skapat ett kluster med Azure CLI med standardinställningarna eller Azure-portalen med RBAC aktiverat och ett stort antal varningar i kubernetes-instrumentpanelen. Instrumentpanelen som används för att fungera innan alla varningar. Vad ska jag göra?

Orsak för att få varningar på instrumentpanelen är att den är nu aktiverad med RBAC'ed och åtkomst till den har inaktiverats som standard. Den här metoden betraktas som i allmänhet bra eftersom standard exponering av instrumentpanelen för alla användare av klustret kan leda till säkerhetshot. Om du vill aktivera instrumentpanelen kan du följa den här [blogg](https://pascalnaber.wordpress.com/2018/06/17/access-dashboard-on-aks-with-rbac-enabled/) att aktivera den.

### <a name="i-cant-seem-to-connect-to-the-dashboard-what-should-i-do"></a>Jag kan inte ansluta till instrumentpanelen. Vad ska jag göra?

Det enklaste sättet att komma åt tjänsten utanför klustret är att köra kubectl-proxy som proxy-förfrågningarna ska skickas till din localhost port 8001 för Kubernetes API-servern. Därifrån kan apiserver proxy till din tjänst: http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#! / nod? namnområde = standard

Om du inte ser kubernetes-instrumentpanelen kan du kontrollera om kube-proxy-pod körs i namnområdet kube system. Om den inte körs, ta bort en pod och kommer att startas om.

### <a name="i-could-not-get-logs-using-kubectl-logs-or-cannot-connect-to-the-api-server-getting-the-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>Jag gick inte att hämta loggar med Kubectl loggar eller kan inte ansluta till api-servern hämtar den ”fel från servern: fel uppringning backend: Ring tcp...”. Vad ska jag göra?

Se till att standardwebbplatsen NSG inte har ändrats och den port 22 är öppen för anslutning till API-servern. Kontrollera om tunnelfront pod körs i namnområdet kube system. Om det inte är Framtvinga borttagning av den och kommer att startas om.

### <a name="i-am-trying-to-upgrade-or-scale-and-am-getting-message-changing-property-imagereference-is-not-allowed-error--how-do-i-fix-this-issue"></a>Jag försöker uppgradera eller skala och får ”meddelande”: ”'ImageReference' är inte tillåtet att ändra egenskapen”. Fel.  Hur kan jag åtgärda det här problemet?

Det är möjligt att du får det här felet eftersom du har ändrat taggar i agentnoder i AKS-klustret. Ändra och ta bort taggar och andra egenskaper för resurser i resursgruppen MC_ * kan leda till oväntade resultat. Ändra resurser under MC_ * i AKS-kluster delar på SLO.

### <a name="how-do-i-renew-the-service-principal-secret-on-my-aks-cluster"></a>Hur förnyar jag tjänstens huvudhemlighet på mitt AKS-kluster?

Som standard skapas AKS-kluster med en tjänst huvudnamn som har en förfallotid för ett år. Som du nära förfallodatum för ett år, kan du återställa autentiseringsuppgifter för att utöka tjänstens huvudnamn för en ytterligare period.

I följande exempel utförs följande steg:

1. Hämtar ID för tjänstens huvudnamn för ditt kluster med hjälp av den [az aks show](/cli/azure/aks#az-aks-show) kommando.
1. Visar en lista över den service principal client secret med hjälp av den [az ad sp credential list](/cli/azure/ad/sp/credential#az-ad-sp-credential-list)
1. Utökar tjänstens huvudnamn för en annan ett år med den [az ad sp credential-återställning](/cli/azure/ad/sp/credential#az-ad-sp-credential-reset) kommando. Klienthemlighet för tjänstens huvudnamn måste vara samma för AKS-klustret ska fungera korrekt.

```azurecli
# Get the service principal ID of your AKS cluster
sp_id=$(az aks show -g myResourceGroup -n myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)

# Get the existing service principal client secret
key_secret=$(az ad sp credential list --id $sp_id --query [].keyId -o tsv)

# Reset the credentials for your AKS service principal and extend for 1 year
az ad sp credential reset \
    --name $sp_id \
    --password $key_secret \
    --years 1
```
