---
title: Felsöka vanliga problem med Azure Kubernetes Service
description: Lär dig att felsöka och lösa vanliga problem när du använder Azure Kubernetes Service (AKS)
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: troubleshooting
ms.date: 08/13/2018
ms.author: saudas
ms.openlocfilehash: 8164e2db064523fe648ec9ef0c72754be846dff6
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/16/2019
ms.locfileid: "56327569"
---
# <a name="aks-troubleshooting"></a>AKS felsökning

När du skapar eller hantera kluster i Azure Kubernetes Service (AKS), kan det ibland uppstå problem. Den här artikeln beskriver några vanliga problem och felsökning.

## <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-problems"></a>I allmänhet var hittar jag information om hur du felsöker problem med Kubernetes?

Prova den [officiella guiden för att felsöka Kubernetes-kluster](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/).
Det finns också en [felsökningsguide](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md)publicerade av en Microsoft-tekniker för att felsöka poddar, noder, kluster och andra funktioner.

## <a name="im-getting-a-quota-exceeded-error-during-creation-or-upgrade-what-should-i-do"></a>Jag får ett ”kvoten har överskridits”-fel under generering och uppgradering. Vad ska jag göra? 

Du behöver [begär kärnor](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

## <a name="what-is-the-maximum-pods-per-node-setting-for-aks"></a>Vad är den maximala poddar per nod-inställningen för AKS?

Den maximala poddar per nod-inställningen är 30 som standard om du distribuerar ett AKS-kluster i Azure-portalen.
Den maximala poddar per nod-inställningen är 110 som standard om du distribuerar ett AKS-kluster i Azure CLI. (Kontrollera att du använder den senaste versionen av Azure CLI). Den här standardinställningen kan ändras med hjälp av den `–-max-pods` flagga i den `az aks create` kommando.

## <a name="im-getting-an-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>Jag får ett insufficientSubnetSize-fel när du distribuerar ett AKS-kluster med avancerade nätverk. Vad ska jag göra?

Om du använder Azure CNI (Avancerat nätverk), AKS preallocates IP-adresserna baserat på ”max-poddarna” per nod som har konfigurerats. Antalet noder i ett AKS-kluster kan finnas var som helst mellan 1 och 110. Baserat på de konfigurerade maximala poddarna per nod, ska storleken på undernätet vara större än ”produkten av antalet noder och max pod per nod”. Följande grundläggande beräkning beskrivs detta:

Undernätets storlek > Antal noder i klustret (ta hänsyn till kraven för framtida skalning) * max poddar per nod.

Mer information finns i [Planera IP-adresser för ditt kluster](configure-azure-cni.md#plan-ip-addressing-for-your-cluster).

## <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>Min pod har fastnat i CrashLoopBackOff läge. Vad ska jag göra?

Det kan finnas olika anledningar för pod som fastnat i detta läge. Du kan se ut till:

* Pod, med hjälp av `kubectl describe pod <pod-name>`.
* Loggar med hjälp av `kubectl log <pod-name>`.

Läs mer om hur du felsöker problem med pod [felsöka program](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/#debugging-pods).

## <a name="im-trying-to-enable-rbac-on-an-existing-cluster-how-can-i-do-that"></a>Jag försöker aktivera RBAC på ett befintligt kluster. Hur gör jag för att?

Tyvärr stöds aktiverar rollbaserad åtkomstkontroll (RBAC) på befintliga kluster inte just nu. Du måste uttryckligen skapa nya kluster. Om du använder CLI är RBAC aktiverad som standard. Om du använder AKS-portalen, är en växlingsknapp för att aktivera RBAC tillgänglig i skapa arbetsflödet.

## <a name="i-created-a-cluster-with-rbac-enabled-by-using-either-the-azure-cli-with-defaults-or-the-azure-portal-and-now-i-see-many-warnings-on-the-kubernetes-dashboard-the-dashboard-used-to-work-without-any-warnings-what-should-i-do"></a>Jag har skapat ett kluster med RBAC aktiverat med hjälp av Azure-CLI med standardinställningarna eller Azure portal och nu många varningar visas på Kubernetes-instrumentpanelen. Instrumentpanelen som används för att fungera utan alla varningar. Vad ska jag göra?

Orsaken till varningar på instrumentpanelen är att klustret har nu aktiverats med RBAC och åtkomst till den har inaktiverats som standard. I allmänhet är den här metoden bra eftersom standard exponering av instrumentpanelen för alla användare av klustret kan leda till säkerhetshot. Om du vill aktivera instrumentpanelen följer du stegen i [det här blogginlägget](https://pascalnaber.wordpress.com/2018/06/17/access-dashboard-on-aks-with-rbac-enabled/).

## <a name="i-cant-connect-to-the-dashboard-what-should-i-do"></a>Jag kan inte ansluta till instrumentpanelen. Vad ska jag göra?

Det enklaste sättet att komma åt tjänsten utanför klustret är att köra `kubectl proxy`, vilka proxyservrar förfrågningar som skickas till din localhost port 8001 till Kubernetes API-servern. Därifrån kan API-servern kan proxy till din tjänst: `http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/node?namespace=default`.

Om du inte ser Kubernetes-instrumentpanelen, kontrollera om den `kube-proxy` pod körs i den `kube-system` namnområde. Om den inte körs, ta bort en pod och kommer att startas om.

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>Jag kan inte hämta loggarna genom att använda kubectl loggar eller jag kan inte ansluta till API-servern. Jag får ”fel från servern: fel uppringning backend: Ring tcp...” Vad ska jag göra?

Se till att nätverkssäkerhetsgruppen (NSG) som standard inte ändrats och att port 22 är öppen för anslutning till API-servern. Kontrollera om den `tunnelfront` pod körs i den `kube-system` namnområde. Framtvinga borttagning av en pod och den startas om det inte finns.

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-message-changing-property-imagereference-is-not-allowed-error--how-do-i-fix-this-problem"></a>Jag försöker uppgradera eller skala och får en ”meddelande: Är inte tillåtet att ändra egenskapen 'imageReference' ”fel.  Hur kan jag åtgärda det här problemet?

Du kanske får felet eftersom du har ändrat taggar i agentnoder i AKS-klustret. Ändra och ta bort taggar och andra egenskaper för resurser i resursgruppen MC_ * kan leda till oväntade resultat. Ändra resurser under den MC_ *-gruppen i AKS delar kluster mål för servicenivå (SLO).
