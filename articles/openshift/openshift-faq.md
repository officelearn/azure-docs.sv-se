---
title: Vanliga frågor och svar för Azure Red Hat OpenShift
description: Här är svar på vanliga frågor om Microsoft Azure Red Hat OpenShift
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f468cb294d79c44f92ef95437c0d88639a78b9a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77619479"
---
# <a name="azure-red-hat-openshift-faq"></a>Vanliga frågor och svar om Azure Red Hat OpenShift

Den här artikeln tar upp vanliga frågor och svar om Microsoft Azure Red Hat OpenShift.

## <a name="which-azure-regions-are-supported"></a>Vilka Azure-regioner stöds?

Se [Resurser som stöds](supported-resources.md#azure-regions) för en lista över globala regioner där Azure Red Hat OpenShift stöds.

## <a name="can-i-deploy-a-cluster-into-an-existing-virtual-network"></a>Kan jag distribuera ett kluster till ett befintligt virtuellt nätverk?

Nej. Men du kan ansluta ett Azure Red Hat OpenShift-kluster till ett befintligt VNET via peering. Mer information finns i [Ansluta ett klusters virtuella nätverk till ett befintligt virtuellt nätverk.](tutorial-create-cluster.md#optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network)

## <a name="what-cluster-operations-are-available"></a>Vilka klusteråtgärder är tillgängliga?

Du kan bara skala upp eller ned antalet beräkningsnoder. Inga andra ändringar tillåts `Microsoft.ContainerService/openShiftManagedClusters` till resursen när den har skapats. Det maximala antalet beräkningsnoder är begränsat till 20.

## <a name="what-virtual-machine-sizes-can-i-use"></a>Vilka storlekar för virtuella datorer kan jag använda?

Se [Azure Red Hat OpenShift-storlekar](supported-resources.md#virtual-machine-sizes) för virtuella datorer för en lista över storlekar på virtuella datorer som du kan använda med ett Azure Red Hat OpenShift-kluster.

## <a name="is-data-on-my-cluster-encrypted"></a>Är data i mitt kluster krypterade?

Som standard finns det kryptering i vila. Azure Storage-plattformen krypterar automatiskt dina data innan den sparas och dekrypterar data före hämtning. Mer information finns i Azure Storage Service Encryption för data i [vila.](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)

## <a name="can-i-use-prometheusgrafana-to-monitor-my-applications"></a>Kan jag använda Prometheus/Grafana för att övervaka mina ansökningar?

Ja, du kan distribuera Prometheus i ditt namnområde och övervaka program i ditt namnområde.

## <a name="can-i-use-prometheusgrafana-to-monitor-metrics-related-to-cluster-health-and-capacity"></a>Kan jag använda Prometheus/Grafana för att övervaka mätvärden relaterade till klusterhälsa och kapacitet?

Nej, inte just nu.

## <a name="is-the-docker-registry-available-externally-so-i-can-use-tools-such-as-jenkins"></a>Är Docker-registret tillgängligt externt så att jag kan använda verktyg som Jenkins?

Docker-registret är tillgängligt `https://docker-registry.apps.<clustername>.<region>.azmosa.io/` från Men en stark lagringshållbarhetsgaranti finns inte. Du kan också använda [Azure Container Registry](https://azure.microsoft.com/services/container-registry/).

## <a name="is-cross-namespace-networking-supported"></a>Stöds nätverk över namnområdet?

Kund- och enskilda projektadministratörer kan anpassa nätverk mellan namnområden (inklusive neka `NetworkPolicy` det) per projekt med hjälp av objekt.

## <a name="can-an-admin-manage-users-and-quotas"></a>Kan en administratör hantera användare och kvoter?

Ja. En Azure Red Hat OpenShift-administratör kan hantera användare och kvoter förutom att komma åt alla användarskapade projekt.

## <a name="can-i-restrict-a-cluster-to-only-certain-azure-ad-users"></a>Kan jag begränsa ett kluster till endast vissa Azure AD-användare?

Ja. Du kan begränsa vilka Azure AD-användare som kan logga in på ett kluster genom att konfigurera Azure AD-programmet. Mer information finns i [Så här begränsar du appen till en uppsättning användare](https://docs.microsoft.com/azure/active-directory/develop/howto-restrict-your-app-to-a-set-of-users)

## <a name="can-i-restrict-users-from-creating-projects"></a>Kan jag hindra användare från att skapa projekt?

Ja. Logga in i klustret som Azure Red Hat OpenShift-administratör och kör det här kommandot:

```
oc adm policy \
    remove-cluster-role-from-group self-provisioner \
    system:authenticated:oauth
```

Mer information finns i OpenShift-dokumentationen om [hur du inaktiverar självetablering](https://docs.openshift.com/container-platform/3.11/admin_guide/managing_projects.html#disabling-self-provisioning).

## <a name="can-a-cluster-have-compute-nodes-across-multiple-azure-regions"></a>Kan ett kluster ha beräkningsnoder i flera Azure-regioner?

Nej. Alla noder i ett Azure Red Hat OpenShift-kluster måste komma från samma Azure-region.

## <a name="are-master-and-infrastructure-nodes-abstracted-away-as-they-are-with-azure-kubernetes-service-aks"></a>Är huvud- och infrastrukturnoder abstraherade bort som de är med Azure Kubernetes Service (AKS)?

Nej. Alla resurser, inklusive klusterhanteraren, körs i kundprenumerationen. Dessa typer av resurser läggs i en skrivskyddad resursgrupp.

## <a name="is-open-service-broker-for-azure-osba-supported"></a>Stöds Open Service Broker for Azure (OSBA)?

Ja. Du kan använda OSBA med Azure Red Hat OpenShift. Mer information finns i [Öppna tjänstmäklare för Azure.](https://github.com/Azure/open-service-broker-azure#openshift-project-template)

## <a name="i-am-trying-to-peer-into-a-virtual-network-in-a-different-subscription-but-getting-failed-to-get-vnet-cidr-error"></a>Jag försöker kika in i ett virtuellt `Failed to get vnet CIDR` nätverk i en annan prenumeration men få fel.

I prenumerationen som har det virtuella `Microsoft.ContainerService` nätverket, se till att registrera leverantör med`az provider register -n Microsoft.ContainerService --wait` 

## <a name="what-is-the-azure-red-hat-openshift-aro-maintenance-process"></a>Vad är underhållsprocessen för Azure Red Hat OpenShift (ARO)?

Det finns tre typer av underhåll för ARO: uppgraderingar, säkerhetskopiering och återställning av etcd-data och molnleverantörsinitierat underhåll.

+ Uppgraderingar inkluderar uppgraderingar och CVEs. CVE-reparation sker vid `yum update` start genom att köra och ger omedelbar begränsning.  Parallellt skapas en ny avbildningsversion för framtida klusterskapningar.

+ Säkerhetskopiering och hantering av etcd-data är en automatiserad process som kan kräva driftstopp i klustret beroende på åtgärden. Om ETCD-databasen återställs från en säkerhetskopia kommer det att finnas driftstopp. Vi säkerhetskopierar etcd varje timme och behåller de senaste 6 timmarna av säkerhetskopior.

+ Molnleverantörsinitierat underhåll omfattar nätverks-, lagrings- och regionala avbrott. Underhållet är beroende av molnleverantören och förlitar sig på uppdateringar som levereras av leverantören.

## <a name="what-is-the-general-upgrade-process"></a>Vad är den allmänna uppgraderingsprocessen?

Köra en uppgradering bör vara en säker process för att köra och bör inte störa klustertjänster. SRE kan utlösa uppgraderingsprocessen när nya versioner är tillgängliga eller CVEs är utestående.

Tillgängliga uppdateringar testas i en scenmiljö och tillämpas sedan på produktionskluster. När den används läggs en ny nod tillfälligt till och noder uppdateras på ett roterande sätt så att poddar upprätthåller replikantal. Följande metodtips hjälper till att säkerställa minimala till inga driftstopp.

Beroende på hur allvarlig den väntande uppgraderingen eller uppdateringen är kan processen skilja sig åt genom att uppdateringarna kan tillämpas snabbt för att minska tjänstens exponering för en CVE. En ny avbildning kommer att byggas asynkront, testas och distribueras som en klusteruppgradering. I övrigt är det ingen skillnad mellan akut och planerat underhåll. Planerat underhåll är inte förplanerat med kunden.

Meddelanden kan skickas via ICM och e-post om kommunikation till kunden krävs.

## <a name="what-about-emergency-vs-planned-maintenance-windows"></a>Vad sägs om nödläge kontra planerade underhållsfönster?

Vi skiljer inte mellan de två typerna av underhåll. Våra team är tillgängliga 24/7/365 och använder inte traditionella schemalagda "out-of-hours" underhållsfönster.

## <a name="how-will-host-operating-system-and-openshift-software-be-updated"></a>Hur kommer värd operativsystem och OpenShift programvara uppdateras?

Värdoperativsystemet och OpenShift-programvaran uppdateras genom vår allmänna uppgraderings- och avbildningsprocess.

## <a name="whats-the-process-to-reboot-the-updated-node"></a>Vad är processen för att starta om den uppdaterade noden?

Detta bör hanteras som en del av en uppgradering.

## <a name="is-data-stored-in-etcd-encrypted-on-aro"></a>Lagras data i etcd krypterade på ARO?

Det är inte krypterat på ETCD-nivå. Alternativet att aktivera den stöds för närvarande inte. OpenShift stöder den här funktionen, men tekniska insatser krävs för att göra det på färdplanen. Data krypteras på disknivå. Mer information finns i [Kryptera data på datalager.](https://docs.openshift.com/container-platform/3.11/admin_guide/encrypting_data.html)

## <a name="can-logs-of-underlying-vms-be-streamed-out-to-a-customer-log-analysis-system"></a>Kan loggar över underliggande virtuella datorer strömmas ut till ett kundlogganalyssystem?

Syslog, dockerloggar, journal och dmesg hanteras av den hanterade tjänsten och exponeras inte för kunder.

## <a name="how-can-a-customer-get-access-to-metrics-like-cpumemory-at-the-node-level-to-take-action-to-scale-debug-issues-etc-i-cannot-seem-to-run-kubectl-top-on-an-aro-cluster"></a>Hur kan en kund få tillgång till mått som CPU / minne på nod nivå för att vidta åtgärder för att skala, felsöka frågor, etc. Jag verkar inte `kubectl top` kunna köras på ett ARO-kluster.

Kunder kan komma åt processor-/minnesmåtten på nodnivå med kommandot `oc adm top nodes` eller `kubectl top nodes` med klusterrollen för kundadministratör.  Kunder kan också komma åt CPU/Minnesmåtten `pods` för med kommandot `oc adm top pods` eller`kubectl top pods`

## <a name="what-is-the-default-pod-scheduler-configuration-for-aro"></a>Vad är standardkonfigurationen för pod-schemaläggaren för ARO?

ARO använder standardschemaläggaren som levereras i OpenShift. Det finns ett par ytterligare mekanismer som inte stöds i ARO. Mer information finns i [standarddokumentationen](https://docs.openshift.com/container-platform/3.11/admin_guide/scheduling/scheduler.html#generic-scheduler) för schemaläggaren och dokumentationen för [huvudschemaläggare.](https://github.com/openshift/openshift-azure/blob/master/pkg/startup/v6/data/master/etc/origin/master/scheduler.json)

Avancerad/anpassad schemaläggning stöds för närvarande inte. Mer information finns i dokumentationen för [schemaläggning.](https://docs.openshift.com/container-platform/3.11/admin_guide/scheduling/index.html)

## <a name="if-we-scale-up-the-deployment-how-do-azure-fault-domains-map-into-pod-placement-to-ensure-all-pods-for-a-service-do-not-get-knocked-out-by-a-failure-in-a-single-fault-domain"></a>Om vi skalar upp distributionen, hur mappar Azure-feldomäner till pod-placering för att säkerställa att alla poddar för en tjänst inte slås ut av ett fel i en enda feldomän?

Det finns som standard fem feldomäner när du använder skaluppsättningar för virtuella datorer i Azure. Varje virtuell datorinstans i en skalningsuppsättning placeras i en av dessa feldomäner. Detta säkerställer att program som distribueras till beräkningsnoderna i ett kluster placeras i separata feldomäner.

Se [Välja rätt antal feldomäner för skalningsuppsättning](https://docs.microsoft.com//azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-fault-domains) för virtuella datorer för mer information.

## <a name="is-there-a-way-to-manage-pod-placement"></a>Finns det något sätt att hantera pod placering?

Kunderna har möjlighet att hämta noder och visa etiketter som kundadministratör.  Detta kommer att ge ett sätt att rikta alla virtuella datorer i skalan inställd.

Försiktighet måste iakttas vid användning av specifika etiketter:

- Värdnamn får inte användas. Hostname roteras ofta med uppgraderingar och uppdateringar och kommer garanterat att ändras.

- Om kunden har en begäran om specifika etiketter eller en distributionsstrategi kan detta utföras men skulle kräva tekniska insatser och stöds inte idag.

## <a name="what-is-the-maximum-number-of-pods-in-an-aro-cluster-what-is-the-maximum-number-of-pods-per-node-in-aro"></a>Vad är det maximala antalet poddar i ett ARO-kluster?Vad är det maximala antalet poddar per nod i ARO?

 Azure Red Hat OpenShift 3.11 har en gräns på 50 pod per nod med [ARO med en gräns på 20 beräkningsnod,](https://docs.microsoft.com/azure/openshift/openshift-faq#what-cluster-operations-are-available)så att det maximala antalet poddar som stöds i ett ARO-kluster till 50*20 = 1000.

## <a name="can-we-specify-ip-ranges-for-deployment-on-the-private-vnet-avoiding-clashes-with-other-corporate-vnets-once-peered"></a>Kan vi ange IP-intervall för distribution på det privata virtuella nätverket, undvika sammandrabbningar med andra företag VNETs gång peered?

Azure Red Hat OpenShift stöder VNET-peering och gör det möjligt för kunden att tillhandahålla ett VNET till peer med och en VNET CIDR där OpenShift-nätverket kommer att fungera.

Det virtuella nätverk som skapas av ARO skyddas och accepterar inte konfigurationsändringar. Det virtuella nätverk som är peered styrs av kunden och finns i deras prenumeration.

## <a name="does-the-cluster-reside-in-a-customer-subscription"></a>Finns klustret i en kundprenumeration? 

Azure Managed Application finns i en låst resursgrupp med kundprenumerationen. Kunden kan visa objekt i den RG men inte ändra.

## <a name="is-the-sdn-module-configurable"></a>Kan SDN-modulen konfigureras?

SDN är openshift-ovs-networkpolicy och kan inte konfigureras.

## <a name="which-unix-rights-in-iaas-are-available-for-mastersinfraapp-nodes"></a>Vilka UNIX-rättigheter (i IaaS) är tillgängliga för Masters/Infra/App Noder?

Gäller inte för detta erbjudande. Nodåtkomst är förbjuden.

## <a name="which-ocp-rights-do-we-have-cluster-admin-project-admin"></a>Vilka OCP-rättigheter har vi? Kluster-admin? Projekt-admin?

Mer information finns i översikten över Azure Red Hat [OpenShift-klusteradministration](https://docs.openshift.com/aro/admin_guide/index.html).

## <a name="which-kind-of-federation-with-ldap"></a>Vilken typ av federation med LDAP?

Detta skulle uppnås via Azure AD-integrering. 

## <a name="is-there-any-element-in-aro-shared-with-other-customers-or-is-everything-independent"></a>Finns det något element i ARO som delas med andra kunder? Eller är allt självständigt?

Varje Azure Red Hat OpenShift-kluster är dedikerat till en viss kund och bor inom kundens prenumeration. 

## <a name="can-we-choose-any-persistent-storage-solution-like-ocs"></a>Kan vi välja någon ihållande lagringslösning, som OCS? 

Det finns två lagringsklasser att välja mellan: Azure Disk och Azure File.

## <a name="how-is-a-cluster-updated-including-majors-and-minors-due-to-vulnerabilities"></a>Hur uppdateras ett kluster (inklusive större och minderåriga på grund av sårbarheter)?

Se [Vad är den allmänna uppgraderingsprocessen?](https://docs.microsoft.com/azure/openshift/openshift-faq#what-is-the-general-upgrade-process)

## <a name="what-azure-load-balancer-is-used-by-aro-is-it-standard-or-basic-and-is-it-configurable"></a>Vilken Azure Load Balancer används av ARO?Är det Standard eller Basic och är det konfigurerbart?

ARO använder Standard Azure Load Balancer och det kan inte konfigureras.

## <a name="can-aro-use-netapp-based-storage"></a>Kan ARO använda NetApp-baserad lagring?

För närvarande är de enda lagringsalternativ som stöds Azure Disk- och Azure File storage-klasser. 


