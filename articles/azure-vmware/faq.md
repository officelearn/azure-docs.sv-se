---
title: Vanliga frågor och svar
description: Innehåller svar på några vanliga frågor om Azure VMware-lösningen.
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: dikamath
ms.openlocfilehash: 7b4abc2d711a3da6a6df125854759e083d7e04a7
ms.sourcegitcommit: 0fd1f3fe7817ad44d878d580ec167e1508051795
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2020
ms.locfileid: "90817844"
---
# <a name="frequently-asked-questions-about-azure-vmware-solution-preview"></a>Vanliga frågor om för hands versionen av Azure VMware-lösningen

Svar på vanliga frågor om Azure VMware-lösningen.

## <a name="general"></a>Allmänt

### <a name="what-is-azure-vmware-solution"></a>Vad är Azure VMware Solution?

Eftersom företag eftersträvar IT-modernisering strategier för att förbättra flexibiliteten i verksamheten, minska kostnaderna och påskynda innovationen, har hybrid moln plattformarna uppfyllts som viktigare för kunders digitala omvandling. Azure VMware-lösningen kombinerar VMwares SDDC-programvara (Software Defined Data Center) med Microsoft Azure globalt moln tjänst eko system. Azure VMware-lösningen hanteras för att uppfylla kraven på prestanda, tillgänglighet, säkerhet och efterlevnad.

## <a name="azure-vmware-solution-service"></a>Azure VMware Solution service

### <a name="where-is-azure-vmware-solution-available-today"></a>Var finns Azure VMware-lösningen idag?

Tjänsten läggs kontinuerligt till i nya regioner, så se den [senaste tjänst tillgänglighets informationen](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware) för mer information. 

### <a name="can-workloads-running-in-an-azure-vmware-solution-instance-consume-or-integrate-with-azure-services"></a>Kan arbets belastningar som körs i en Azure VMware Solution-instans förbruka eller integrera med Azure-tjänster?

Alla Azure-tjänster kommer att vara tillgängliga för kunder med Azure VMware-lösningar. Begränsningar för prestanda och tillgänglighet för vissa tjänster måste åtgärdas från fall till fall.

### <a name="do-i-use-the-same-tools-that-i-use-now-to-manage-private-cloud-resources"></a>Använder jag samma verktyg som jag använder nu för att hantera privata moln resurser?

Ja. Azure Portal används för distribution och ett antal hanterings åtgärder. vCenter och NSX Manager används för att hantera vSphere-och NSX-T-resurser.

### <a name="can-i-manage-a-private-cloud-with-my-on-premises-vcenter"></a>Kan jag hantera ett privat moln med min lokala vCenter?

Vid lanseringen har Azure VMware-lösningen inte stöd för en enda hanterings upplevelse i lokala och privata moln miljöer. Privata moln kluster kommer att hanteras med vCenter och NSX Manager lokalt i ett privat moln.

### <a name="can-i-use-vrealize-suite-running-on-premises"></a>Kan jag använda vRealize Suite som körs lokalt? 

Vissa integreringar och användnings fall kan utvärderas från fall till fall.

### <a name="can-i-migrate-vsphere-vms-from-on-premises-environments-to-azure-vmware-solution-private-clouds"></a>Kan jag migrera virtuella vSphere-datorer från lokala miljöer till Azure VMware-lösningar privata moln?

Ja. Migrering av virtuella datorer och vMotion kan användas för att flytta virtuella datorer till ett privat moln om standard kraven för Cross vCenter [vMotion](https://kb.vmware.com/s/article/210695) är uppfyllda.

### <a name="is-a-specific-version-of-vsphere-required-in-on-premises-environments"></a>Krävs en speciell version av vSphere i lokala miljöer?

Eftersom alla moln miljöer levereras med HCX, vSphere 5,5 eller senare i lokala miljöer för vMotion.

### <a name="what-does-the-change-control-process-look-like"></a>Vad ser processen för ändrings kontroll ut?

Uppdateringar som görs i själva tjänsten följer Microsoft Azure standard processen för ändrings hantering. Kunderna är ansvariga för alla arbets belastnings administrations uppgifter och de tillhör ande processerna för ändrings hantering.

### <a name="how-is-this-different-from-azure-vmware-solution-by-cloudsimple"></a>Hur skiljer sig detta från Azure VMware-lösningen av CloudSimple?

Med den nya Azure VMware-lösningen har Microsoft och VMware en direkt moln leverantörs koppling. Den nya lösningen är helt utformad, byggd och stöds av Microsoft och har godkänts av VMware. Lösningarna är dessutom konsekventa med VMware Technology-stacken som körs på en dedikerad Azure-infrastruktur.

### <a name="if-im-an-existing-azure-vmware-solution-customer-what-does-this-preview-mean-for-me"></a>Vad betyder den här för hands versionen för mig om jag är en befintlig Azure VMware Solution-kund?

Det finns ingen ändring av den befintliga Azure VMware-lösningen från CloudSimple. Vi fortsätter att stödja lösningen på Azure. Azure VMware-lösningen backas upp av vårt service nivå avtal [(SLA)](https://aka.ms/CSVMwareSLA). Kunderna bör fortsätta att använda tjänsten för produktions arbets belastningar. Det här är en tillgänglig lösning som styrs av [Microsofts tjänst villkor](https://azure.microsoft.com/support/legal/).

### <a name="can-i-migrate-from-azure-vmware-solution-by-cloudsimple-to-this-new-solution"></a>Kan jag migrera från Azure VMware-lösningen från CloudSimple till den här nya lösningen?

Ja, Azure VMware-lösningen stöder migrering med välbekanta VMware-verktyg som HCX. För kunder som vill migrera till den nya lösningen arbetar du med ditt Microsoft-konto-team för att utforska alternativ och tillgänglig support.



## <a name="compute-network-and-storage"></a>Beräkning, nätverk och lagring

### <a name="is-there-more-than-one-type-of-host-available"></a>Finns det fler än en typ av värd tillgänglig?

Det finns bara en typ av värd som är tillgänglig.

### <a name="what-are-the-cpu-specifications-in-each-type-of-host"></a>Vilka är CPU-specifikationerna i varje typ av värd?

Servrarna har dubbla 18 kärnor på 2,3 GHz Intel-processorer.

### <a name="how-much-memory-is-in-each-host"></a>Hur mycket minne finns på varje värd?

Servrarna har 576 GB RAM-minne.

### <a name="what-is-the-storage-capacity-of-each-host"></a>Vilken lagrings kapacitet för varje värd?

Varje ESXi-värd har två virtuellt San-diskgroups med kapacitets nivån 15,2 TB och en 3,2 TB NVMe-cachenivå (1,6 TB i varje diskgroup).

### <a name="how-much-network-bandwidth-is-available-in-each-esxi-host"></a>Hur mycket nätverks bandbredd finns på varje ESXi-värd?

Varje ESXi-värd är en Azure VMware-lösning som är konfigurerad med 4 25 Gbit/s nätverkskort, med två nätverkskort som har allokerats för ESXi system trafik och två nätverkskort som har allokerats för arbets belastnings trafik. 

### <a name="is-data-stored-on-the-vsan-datastores-encrypted-at-rest"></a>Är data lagrade på virtuellt San-datalager krypterade i vila?

Ja, alla virtuellt San-data krypteras som standard med hjälp av nycklar som lagras i Azure Key Vault.

## <a name="hosts-clusters-and-private-clouds"></a>Värdar, kluster och privata moln

### <a name="is-the-underlying-infrastructure-shared"></a>Delas den underliggande infrastrukturen?

Nej, värdar och kluster för privata moln är dedikerade och raderas på ett säkert sätt före och efter användning.

### <a name="what-are-the-minimum-and-maximum-number-of-hosts-per-cluster"></a>Vilka är det lägsta och högsta antalet värdar per kluster?

Kluster kan skalas mellan 3-och 16 ESXi-värdar. Utvärderings kluster är begränsade till tre värdar.

### <a name="can-i-scale-my-private-cloud-clusters"></a>Kan jag skala mina privata moln kluster?

Ja, klustren skalas mellan det lägsta och högsta antalet ESXi-värdar. Utvärderings kluster är begränsade till tre värdar.

### <a name="what-are-trial-clusters"></a>Vad är utvärderings kluster?

Utvärderings kluster är tre värd kluster som används för en månads utvärdering av privata moln i Azure VMware-lösningen.

### <a name="can-i-use-high-end-hosts-for-trial-clusters"></a>Kan jag använda avancerade värdar för utvärderings kluster?

Nej. Avancerade ESXi-värdar är reserverade för användning i produktions kluster.

## <a name="azure-vmware-solution-and-vmware-software"></a>Azure VMware-lösning och VMware-programvara

### <a name="what-versions-of-vmware-software-is-used-in-private-clouds"></a>Vilka versioner av VMware-programvaran används i privata moln?

Privata moln använder vSphere 6,7, virtuellt San 6,7, HCX och version 2,5 av NSX-T.  

### <a name="do-private-clouds-use-vmware-nsx"></a>Använder privata moln VMware-NSX?

Ja, NSX-T 2,5 används för program vara som definierats i Azure VMware-lösningar privata moln.

### <a name="can-i-use-vmware-nsx-v-in-a-private-cloud"></a>Kan jag använda VMware NSX-V i ett privat moln?

Nej. NSX-T är den enda version av NSX som stöds.

### <a name="is-nsx-required-in-on-premises-environments-or-networks-that-connect-to-a-private-cloud"></a>Krävs NSX i lokala miljöer eller nätverk som ansluter till ett privat moln?

Nej, du behöver inte använda NSX lokalt.

### <a name="what-is-the-upgrade-and-update-schedule-for-vmware-software-in-a-private-cloud"></a>Vad är uppgraderings-och uppdaterings schema för VMware-programvara i ett privat moln?

Uppgraderingarna av det privata molnet för program varu paketet görs för att hålla program varan i en version av den senaste versionen av program varu paketet från VMware. Program varu versionerna för det privata molnet kan skilja sig från de senaste versionerna av de enskilda program varu komponenterna (ESXi, NSX-T, vCenter, virtuellt SAN).

### <a name="how-often-will-the-private-cloud-software-stack-be-updated"></a>Hur ofta uppdateras program stacken för privata moln?

Program varan för det privata molnet uppgraderas enligt ett schema som spårar program varu paketets version från VMware. Det privata molnet kräver ingen stillestånds tid för uppgraderingar.

## <a name="connectivity"></a>Anslutning

### <a name="what-network-ip-address-planning-is-required-to-incorporate-private-clouds-with-on-premises-environments"></a>Vilken nätverks-IP-adress planering krävs för att inkludera privata moln med lokala miljöer?

Det krävs ett privat nätverk/22-adressutrymme för att distribuera ett privat moln i Azure VMware-lösningen. Det privata adress utrymmet får inte överlappa andra virtuella nätverk i en prenumeration eller med lokala nätverk.
 
### <a name="how-do-i-connect-from-on-premises-environments-to-an-azure-vmware-solution-private-cloud"></a>Hur gör jag för att ansluta från lokala miljöer till ett privat moln i Azure VMware-lösningen?

Du kan ansluta till tjänsten på något av två sätt: 

- Med en virtuell dator eller Programgateway distribuerad i ett virtuellt Azure-nätverk som peer-kopplas via ExpressRoute till det privata molnet.
- Via ExpressRoute Global Reach från ditt lokala data Center till en Azure ExpressRoute-krets.

### <a name="how-do-i-connect-a-workload-vm-to-the-internet-or-an-azure-service-endpoint"></a>Hur gör jag för att ansluter du en virtuell dator för arbets belastning till Internet eller en Azure-tjänst slut punkt?

I Azure Portal aktiverar du Internet anslutning för ett privat moln. Med NSX-T Manager skapar du en NSX-T T1-router och en logisk växel. Sedan använder du vCenter för att distribuera en virtuell dator i det nätverks segment som definieras av den logiska växeln. Den virtuella datorn kommer att ha nätverks åtkomst till Internet och till Azure-tjänster.

### <a name="do-i-need-to-restrict-access-from-the-internet-to-vms-on-logical-networks-in-a-private-cloud"></a>Behöver jag begränsa åtkomst från Internet till virtuella datorer i logiska nätverk i ett privat moln?

Nej. Inkommande nätverks trafik från Internet direkt till privata moln är inte tillåten.

### <a name="do-i-need-to-restrict-internet-access-from-vms-on-logical-networks-to-the-internet"></a>Måste jag begränsa Internet åtkomst från virtuella datorer i logiska nätverk till Internet?

Ja. Du måste använda NSX-T-hanteraren för att skapa en brand vägg som begränsar VM-åtkomsten till Internet.

## <a name="accounts-and-privileges"></a>Konton och behörigheter

### <a name="what-accounts-and-privileges-will-i-get-with-my-new-azure-vmware-solution-private-cloud"></a>Vilka konton och behörigheter får jag med mitt nya Azure VMware-lösningar privat moln?

Du har angett autentiseringsuppgifter för en cloudadmin-användare i vCenter-och admin-åtkomst i NSX-T Manager. Det finns också en CloudAdmin-grupp som kan användas för att införliva Azure Active Directory. Mer information finns i [åtkomst-och identitets koncept](concepts-identity.md).

### <a name="can-have-administrator-access-to-esxi-hosts"></a>Kan administratörs åtkomst till ESXi-värdar?

Nej, administratörs åtkomst till ESXi är begränsad för att uppfylla säkerhets kraven för lösningen.

### <a name="what-privileges-and-permissions-will-i-have-in-vcenter"></a>Vilka behörigheter och behörigheter kommer jag att ha i vCenter?

Du har CloudAdmin grupp privilegier. Mer information finns i [åtkomst-och identitets koncept](concepts-identity.md).

### <a name="what-privileges-and-permissions-will-i-have-on-the-nsx-t-manager"></a>Vilka behörigheter och behörigheter kommer jag att ha på NSX-T-hanteraren?

Du kommer att ha fullständiga administratörs behörigheter på NSX-T och kan hantera rollbaserad åtkomst kontroll som du skulle göra med NSX-T Data Center lokalt. Mer information finns i [åtkomst-och identitets koncept](concepts-identity.md).

> [!NOTE]
> En t0-router skapas och konfigureras som en del av distributionen av ett privat moln. Eventuella ändringar av den logiska routern eller den virtuella NSX-T Edge-noden kan påverka anslutningen till ditt privata moln.

## <a name="billing-and-support"></a>Fakturering och support

### <a name="how-will-i-be-billed-during-the-preview-of-azure-vmware-solution"></a>Hur kommer jag att faktureras under för hands versionen av Azure VMware-lösningen?

Faktureringen för Azure VMware-lösningen under för hands versionen är månatlig enligt principen betala per användning. Ytterligare alternativ är allmänt tillgängliga.

### <a name="how-will-pricing-be-structured-during-the-preview-of-azure-vmware-solution"></a>Hur struktureras priserna under för hands versionen av Azure VMware-lösningen?

Allmänna frågor om prissättning finns på [prissättnings](https://azure.microsoft.com/pricing/details/azure-vmware) sidan för Azure VMware-lösningen. Priset för för hands versionen är tillgängligt på begäran, kontakta ditt konto team eller följ länken på prissättnings sidan för att kontakta Sales.

### <a name="who-supports-azure-vmware-solution"></a>Vem stöder Azure VMware-lösningen?

Support för Azure VMware-lösningen levereras av Microsoft. Obs! enligt våra rikt linjer för förhands granskning kommer vi att ge support under 9 – 5 PM PST-arbetstimme måndag-fredag. Du kan utlösa ett support ärende från [den här länken](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

### <a name="what-accounts-do-i-need-to-create-an-azure-vmware-solution-private-cloud"></a>Vilka konton behöver jag för att skapa ett privat moln I Azure VMware-lösningen?

Du behöver ett Azure-konto i en Azure-prenumeration.

### <a name="how-do-i-request-a-host-quota-increase-for-azure-vmware-solution"></a>Hur gör jag för att begära en värd kvot ökning för Azure VMware-lösningen?

* Du behöver ett [Azure-Enterprise-avtal (EA)](https://docs.microsoft.com/azure/cost-management-billing/manage/ea-portal-agreements) med Microsoft.
* Du behöver ett Azure-konto i en Azure-prenumeration.

Innan du skapar din Azure VMware-lösnings resurs måste du skicka in ett support ärende om du vill att dina noder ska tilldelas. När support teamet har tagit emot din begäran tar det upp till fem arbets dagar för att bekräfta din begäran och allokera noderna. Om du har ett befintligt privat moln i Azure VMware-lösningen och vill att fler noder ska tilldelas, går du igenom samma process.


1. I Azure Portal, under **Hjälp + Support**, skapa en **[ny supportbegäran](https://rc.portal.azure.com/#create/Microsoft.Support)** och ange följande information för biljetten:
   - **Typ av problem:** Produkt
   - **Prenumeration:** Välj din prenumeration
   - **Tjänst:** Alla tjänster > Azure VMware-lösning
   - **Resurs:** Allmän fråga 
   - **Sammanfattning:** Behöver kapacitet
   - **Problem typ:** Problem med kapacitets hantering
   - **Problem under typ:** Kund förfrågan om ytterligare värd kvot/-kapacitet

1. I **beskrivningen** av support ärendet på fliken **information** anger du:

   - POC eller produktion 
   - Regionsnamn
   - Antal noder
   - Annan information

   >[!NOTE]
   >Azure VMware-lösningen rekommenderar minst tre noder för att skapa ett privat moln och för redundans av N + 1-noder. 

1. Välj **Granska + skapa** för att skicka begäran.

   Det tar upp till fem arbets dagar för en support representant att bekräfta din begäran.

   >[!IMPORTANT] 
   >Om du redan har en befintlig Azure VMware-lösning, och du begär ytterligare noder, bör vi tänka på att vi behöver fem arbets dagar för att allokera noderna. 

1. Innan du kan etablera dina noder bör du kontrol lera att du registrerar resurs leverantören för **Microsoft. AVS** i Azure Portal.  

   ```azurecli-interactive
   az provider register -n Microsoft.AVS --subscription <your subscription ID>
   ```

   Ytterligare sätt att registrera resurs leverantören finns i [Azure Resource providers och-typer](../azure-resource-manager/management/resource-providers-and-types.md).

<!-- LINKS - external -->
[kb2106952]: https://kb.vmware.com/s/article/2106952

<!-- LINKS - internal -->
[Access and Identity Concepts]: concepts-identity.md
