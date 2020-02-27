---
title: Vanliga frågor och svar om Azure Red Hat OpenShift
description: Här är svar på vanliga frågor om Microsoft Azure Red Hat OpenShift
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f468cb294d79c44f92ef95437c0d88639a78b9a1
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2020
ms.locfileid: "77619479"
---
# <a name="azure-red-hat-openshift-faq"></a>Vanliga frågor och svar om Azure Red Hat

Den här artikeln behandlar vanliga frågor och svar om Microsoft Azure Red Hat OpenShift.

## <a name="which-azure-regions-are-supported"></a>Vilka Azure-regioner stöds?

Se [resurser som stöds](supported-resources.md#azure-regions) för en lista över globala regioner där Azure Red Hat OpenShift stöds.

## <a name="can-i-deploy-a-cluster-into-an-existing-virtual-network"></a>Kan jag distribuera ett kluster till ett befintligt virtuellt nätverk?

Nej. Men du kan ansluta ett Azure Red Hat OpenShift-kluster till ett befintligt VNET via peering. Mer information finns i [ansluta ett klusters virtuella nätverk till ett befintligt virtuellt nätverk](tutorial-create-cluster.md#optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network) .

## <a name="what-cluster-operations-are-available"></a>Vilka kluster åtgärder är tillgängliga?

Du kan bara skala upp eller ned antalet datornoder. Inga andra ändringar tillåts för den `Microsoft.ContainerService/openShiftManagedClusters` resursen när den har skapats. Det maximala antalet datornoder är begränsat till 20.

## <a name="what-virtual-machine-sizes-can-i-use"></a>Vilka storlekar för virtuella datorer kan jag använda?

Se [storlekar på virtuella datorer i Azure Red Hat](supported-resources.md#virtual-machine-sizes) för en lista över virtuella dator storlekar som du kan använda med ett kluster med ett i-kluster med Red Hat OpenShift.

## <a name="is-data-on-my-cluster-encrypted"></a>Är data i mitt kluster krypterad?

Som standard är kryptering i vila. Azure Storages plattformen krypterar automatiskt dina data innan de behålls och dekrypterar data innan de kan hämtas. Mer information finns i [Azure Storage tjänst kryptering för data i vila](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) .

## <a name="can-i-use-prometheusgrafana-to-monitor-my-applications"></a>Kan jag använda Prometheus/Grafana för att övervaka mina program?

Ja, du kan distribuera Prometheus i ditt namn område och övervaka program i namn området.

## <a name="can-i-use-prometheusgrafana-to-monitor-metrics-related-to-cluster-health-and-capacity"></a>Kan jag använda Prometheus/Grafana för att övervaka mått relaterade till kluster hälsa och-kapacitet?

Nej, inte vid aktuell tid.

## <a name="is-the-docker-registry-available-externally-so-i-can-use-tools-such-as-jenkins"></a>Är Docker-registret tillgängligt externt så att jag kan använda verktyg som Jenkins?

Docker-registret är tillgängligt från `https://docker-registry.apps.<clustername>.<region>.azmosa.io/` men det finns ingen stark garanti för lagring av lagrings utrymme. Du kan också använda [Azure Container Registry](https://azure.microsoft.com/services/container-registry/).

## <a name="is-cross-namespace-networking-supported"></a>Stöds anslutningar mellan namnrymder?

Kunder och enskilda projekt administratörer kan anpassa kors namns nätverk (inklusive neka det) per projekt med hjälp av `NetworkPolicy` objekt.

## <a name="can-an-admin-manage-users-and-quotas"></a>Kan en administratör hantera användare och kvoter?

Ja. En administratör med en Red Hat OpenShift-administratör kan hantera användare och kvoter, förutom att få åtkomst till alla användare som skapats av projektet.

## <a name="can-i-restrict-a-cluster-to-only-certain-azure-ad-users"></a>Kan jag begränsa ett kluster till endast vissa Azure AD-användare?

Ja. Du kan begränsa vilka Azure AD-användare som kan logga in i ett kluster genom att konfigurera Azure AD-programmet. Mer information finns i så här gör du för att [: begränsa appen till en uppsättning användare](https://docs.microsoft.com/azure/active-directory/develop/howto-restrict-your-app-to-a-set-of-users)

## <a name="can-i-restrict-users-from-creating-projects"></a>Kan jag begränsa användare från att skapa projekt?

Ja. Logga in på klustret som en Azure Red Hat OpenShift-administratör och kör det här kommandot:

```
oc adm policy \
    remove-cluster-role-from-group self-provisioner \
    system:authenticated:oauth
```

Mer information finns i OpenShift-dokumentationen om [inaktive ring av själv etablering](https://docs.openshift.com/container-platform/3.11/admin_guide/managing_projects.html#disabling-self-provisioning).

## <a name="can-a-cluster-have-compute-nodes-across-multiple-azure-regions"></a>Kan ett kluster ha Compute-noder över flera Azure-regioner?

Nej. Alla noder i ett Azure Red Hat OpenShift-kluster måste härstamma från samma Azure-region.

## <a name="are-master-and-infrastructure-nodes-abstracted-away-as-they-are-with-azure-kubernetes-service-aks"></a>Är master-och Infrastructure-noderna sammankopplade som de är med Azure Kubernetes service (AKS)?

Nej. Alla resurser, inklusive kluster huvud, körs i din kund prenumeration. De här typerna av resurser placeras i en skrivskyddad resurs grupp.

## <a name="is-open-service-broker-for-azure-osba-supported"></a>Finns det stöd för att öppna Service Broker för Azure (OSBA)?

Ja. Du kan använda OSBA med Azure Red Hat OpenShift. Mer information finns i [öppna Service Broker för Azure](https://github.com/Azure/open-service-broker-azure#openshift-project-template) .

## <a name="i-am-trying-to-peer-into-a-virtual-network-in-a-different-subscription-but-getting-failed-to-get-vnet-cidr-error"></a>Jag försöker peer-koppla till ett virtuellt nätverk i en annan prenumeration men får `Failed to get vnet CIDR` fel.

I prenumerationen som har det virtuella nätverket måste du registrera `Microsoft.ContainerService`-providern med `az provider register -n Microsoft.ContainerService --wait` 

## <a name="what-is-the-azure-red-hat-openshift-aro-maintenance-process"></a>Vad är underhålls processen för Azure Red Hat OpenShift (ARO)?

Det finns tre typer av underhåll för ARO: uppgraderingar, säkerhets kopiering och återställning av etcd-data och underhåll av moln leverantörer.

+ Uppgraderingar är bland annat program varu uppgraderingar och CVEs. CVE reparation sker vid start genom att köra `yum update` och ger omedelbar minskning.  Parallellt skapas en ny avbildning som skapas för framtida kluster.

+ Säkerhets kopiering och hantering av etcd-data är en automatiserad process som kan kräva kluster avbrott beroende på åtgärd. Om etcd-databasen återställs från en säkerhets kopia kommer det att vara stillestånds tid. Vi säkerhetskopierar etcd varje timme och behåller de senaste 6 timmarna med säkerhets kopior.

+ Cloud Provider – initierat underhåll omfattar nätverk, lagring och regionala avbrott. Underhållet är beroende av moln leverantören och förlitar sig på uppdateringar som tillhandahålls av leverantören.

## <a name="what-is-the-general-upgrade-process"></a>Vad är den allmänna uppgraderings processen?

Att köra en uppgradering bör vara en säker process att köra och bör inte störa kluster tjänsterna. SRE kan utlösa uppgraderings processen när nya versioner är tillgängliga eller CVEs är utestående.

Tillgängliga uppdateringar testas i en fas miljö och tillämpas sedan på produktions kluster. När den används läggs en ny nod till tillfälligt och noderna uppdateras i en roterande miljö så att poddar upprätthåller antalet repliker. Följande metod tips hjälper till att säkerställa minimal stillestånds tid.

Beroende på allvarlighets grad för den väntande uppgraderingen eller uppdateringen kan processen variera i att uppdateringarna kan tillämpas snabbt för att minimera tjänstens exponering till en CVE. En ny avbildning skapas asynkront, testas och distribueras som en kluster uppgradering. Förutom att det inte finns någon skillnad mellan nödfall och planerat underhåll. Planerat underhåll är inte förplanerat för kunden.

Meddelanden kan skickas via ICM och e-post om kommunikationen med kunden krävs.

## <a name="what-about-emergency-vs-planned-maintenance-windows"></a>Vad händer om det gäller nödfall kontra planerat underhålls fönster?

Vi skiljer inte mellan de två typerna av underhåll. Våra team är tillgängliga 24/7/365 och använder inte traditionella schemalagda "out-of-Hour"-underhålls perioder.

## <a name="how-will-host-operating-system-and-openshift-software-be-updated"></a>Hur uppdateras operativ systemet och OpenShift-programvaran?

Värd operativ systemet och OpenShift-programvaran uppdateras genom vår process för allmän uppgradering och avbildnings utveckling.

## <a name="whats-the-process-to-reboot-the-updated-node"></a>Vad är processen för att starta om den uppdaterade noden?

Detta bör hanteras som en del av en uppgradering.

## <a name="is-data-stored-in-etcd-encrypted-on-aro"></a>Är data lagrade i etcd krypterade på ARO?

Den är inte krypterad på etcd-nivån. Det finns för närvarande inte stöd för att aktivera det här alternativet. OpenShift stöder den här funktionen, men tekniska ansträngningar krävs för att göra det på väg kartan. Data krypteras på disk nivå. Mer information finns i [kryptera data i data lager lager](https://docs.openshift.com/container-platform/3.11/admin_guide/encrypting_data.html) .

## <a name="can-logs-of-underlying-vms-be-streamed-out-to-a-customer-log-analysis-system"></a>Kan loggar av underliggande virtuella datorer strömmas till ett system för kund logg analys?

Syslog, Docker-loggar, journal och dmesg hanteras av den hanterade tjänsten och exponeras inte för kunder.

## <a name="how-can-a-customer-get-access-to-metrics-like-cpumemory-at-the-node-level-to-take-action-to-scale-debug-issues-etc-i-cannot-seem-to-run-kubectl-top-on-an-aro-cluster"></a>Hur kan en kund få till gång till mått som processor/minne på nodnivå för att vidta åtgärder för skalning, fel söknings problem osv. Jag kan inte köra `kubectl top` i ett ARO-kluster.

Kunder kan komma åt CPU/minnes mått på nodnivå genom att använda kommandot `oc adm top nodes` eller `kubectl top nodes` med clusterrole kund administratör.  Kunder kan också komma åt CPU/minnes mått för `pods` med kommandot `oc adm top pods` eller `kubectl top pods`

## <a name="what-is-the-default-pod-scheduler-configuration-for-aro"></a>Vad är standard Scheduler-konfigurationen för Pod för ARO?

ARO använder standard Scheduler som levereras i OpenShift. Det finns ett par ytterligare mekanismer som inte stöds i ARO. Mer information finns i dokumentationen för [standard Scheduler](https://docs.openshift.com/container-platform/3.11/admin_guide/scheduling/scheduler.html#generic-scheduler) och [Master Scheduler-dokumentationen](https://github.com/openshift/openshift-azure/blob/master/pkg/startup/v6/data/master/etc/origin/master/scheduler.json) .

Avancerad/anpassad schemaläggning stöds inte för tillfället. Mer information finns i [schemaläggnings dokumentationen](https://docs.openshift.com/container-platform/3.11/admin_guide/scheduling/index.html) .

## <a name="if-we-scale-up-the-deployment-how-do-azure-fault-domains-map-into-pod-placement-to-ensure-all-pods-for-a-service-do-not-get-knocked-out-by-a-failure-in-a-single-fault-domain"></a>Om vi skalar upp distributionen kan Azure-feldomäner mappas till Pod-placering för att säkerställa att alla poddar för en tjänst inte blir blockerade av ett fel i en enskild feldomän?

Det finns som standard fem fel domäner när du använder skalnings uppsättningar för virtuella datorer i Azure. Varje virtuell dator instans i en skalnings uppsättning placeras i någon av dessa fel domäner. Detta säkerställer att program som distribueras till Compute-noderna i ett kluster placeras i olika fel domäner.

Se [hur du väljer rätt antal fel domäner för skalnings uppsättning för virtuella datorer](https://docs.microsoft.com//azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-fault-domains) för mer information.

## <a name="is-there-a-way-to-manage-pod-placement"></a>Finns det något sätt att hantera Pod-placering?

Kunder kan hämta noder och Visa etiketter som kund administratör.  Detta ger ett sätt att rikta in alla virtuella datorer i skalnings uppsättningen.

Varnings skydd måste användas när du använder vissa etiketter:

- Värdnamn får inte användas. Värdnamn roteras ofta med uppgraderingar och uppdateringar och kan ändras.

- Om kunden har en begäran om specifika etiketter eller en distributions strategi kan detta uppnås men skulle kräva tekniska åtgärder och stöds inte idag.

## <a name="what-is-the-maximum-number-of-pods-in-an-aro-cluster-what-is-the-maximum-number-of-pods-per-node-in-aro"></a>Vilket är det maximala antalet poddar i ett ARO-kluster?  Vilket är det maximala antalet poddar per nod i ARO?

 Azure Red Hat OpenShift 3,11 har en gräns på 50-Pod per nod med [Aro som har en gräns för 20-beräknings noder](https://docs.microsoft.com/azure/openshift/openshift-faq#what-cluster-operations-are-available), så att Caps det maximala antalet poddar som stöds i ett Aro-kluster till 50 * 20 = 1000.

## <a name="can-we-specify-ip-ranges-for-deployment-on-the-private-vnet-avoiding-clashes-with-other-corporate-vnets-once-peered"></a>Kan vi ange IP-intervall för distribution på det privata virtuella nätverket, vilket undviker konflikt med andra företags virtuella nätverk när de har peer-kopplats?

Azure Red Hat OpenShift stöder VNET-peering och gör det möjligt för kunden att tillhandahålla ett VNET för peer-koppla med och en VNET CIDR där OpenShift-nätverket fungerar.

Det virtuella nätverket som skapades av ARO kommer att skyddas och kommer inte att acceptera konfigurations ändringar. Det virtuella nätverket som är peer-kopplat styrs av kunden och finns i prenumerationen.

## <a name="does-the-cluster-reside-in-a-customer-subscription"></a>Finns klustret i en kund prenumeration? 

Det Azure-hanterade programmet finns i en låst resurs grupp med kund prenumerationen. Kunden kan visa objekt i den RG men inte ändra.

## <a name="is-the-sdn-module-configurable"></a>Kan modulen SDN konfigureras?

SDN är OpenShift-OVS-networkpolicy och kan inte konfigureras.

## <a name="which-unix-rights-in-iaas-are-available-for-mastersinfraapp-nodes"></a>Vilka UNIX-rättigheter (i IaaS) är tillgängliga för Masters/infraröda/app-noder?

Inte tillämpligt för det här erbjudandet. Åtkomst till noden är förbjuden.

## <a name="which-ocp-rights-do-we-have-cluster-admin-project-admin"></a>Vilka OCP-rättigheter har vi? Kluster-admin? Projekt-admin?

Mer information finns i Översikt över Azure Red Hat OpenShift [cluster administration](https://docs.openshift.com/aro/admin_guide/index.html).

## <a name="which-kind-of-federation-with-ldap"></a>Vilken typ av Federation med LDAP?

Detta kan uppnås via Azure AD-integrering. 

## <a name="is-there-any-element-in-aro-shared-with-other-customers-or-is-everything-independent"></a>Finns det något element i ARO som delas med andra kunder? Eller är allt oberoende?

Varje Azure Red Hat OpenShift-kluster är dedikerad till en bestämd kund och är i kund prenumerationen. 

## <a name="can-we-choose-any-persistent-storage-solution-like-ocs"></a>Kan vi välja vilken beständig lagrings lösning som helst, t. ex. OCS? 

Det finns två lagrings klasser att välja mellan: Azure disk och Azure-fil.

## <a name="how-is-a-cluster-updated-including-majors-and-minors-due-to-vulnerabilities"></a>Hur uppdateras ett kluster (inklusive huvud och minderåriga på grund av sårbarheter)?

Se [Vad är den allmänna uppgraderings processen?](https://docs.microsoft.com/azure/openshift/openshift-faq#what-is-the-general-upgrade-process)

## <a name="what-azure-load-balancer-is-used-by-aro-is-it-standard-or-basic-and-is-it-configurable"></a>Vilken Azure Load Balancer används av ARO?  Är det standard eller Basic och det kan konfigureras?

ARO använder standard Azure Load Balancer och kan inte konfigureras.

## <a name="can-aro-use-netapp-based-storage"></a>Kan ARO använda NetApp-baserad lagring?

Just nu är de enda lagrings alternativ som stöds för Azure disk-och Azure File Storage-klasser. 


