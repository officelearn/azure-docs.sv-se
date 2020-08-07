---
title: Vanliga frågor och svar
description: Innehåller svar på några vanliga frågor om Azure VMware-lösningen (AVS).
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: dikamath
ms.openlocfilehash: bd27d4669788b10fc12c47e4514020f6b01300bc
ms.sourcegitcommit: dea88d5e28bd4bbd55f5303d7d58785fad5a341d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2020
ms.locfileid: "87872335"
---
# <a name="frequently-asked-questions-about-azure-vmware-solution-avs-preview"></a>Vanliga frågor om för hands versionen av Azure VMware-lösningen (AVS)

Svar på vanliga frågor om Azure VMware-lösning (AVS).

## <a name="general"></a>Allmänt

**Vad är Azure VMware-lösning (AVS)?**

Eftersom företag eftersträvar IT-modernisering strategier för att förbättra flexibiliteten i verksamheten, minska kostnaderna och påskynda innovationen, har hybrid moln plattformarna uppfyllts som viktigare för kunders digitala omvandling. AVS kombinerar VMwares SDDC-programvara (Software Defined Data Center) med Microsoft Azure globalt moln tjänst eko system. AVS-lösningen hanteras för att uppfylla kraven på prestanda, tillgänglighet, säkerhet och efterlevnad.

## <a name="avs-service"></a>AVS-tjänst

**Var finns AVS tillgängligt idag?**

Tjänsten läggs kontinuerligt till i nya regioner, så se den [senaste tjänst tillgänglighets informationen](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware) för mer information. 

**Kan arbets belastningar som körs i en Azure VMware-lösning (AVS)-instans förbruka eller integrera med Azure-tjänster?**

Alla Azure-tjänster kommer att vara tillgängliga för kunder med AVS-lösningar. Begränsningar för prestanda och tillgänglighet för vissa tjänster måste åtgärdas från fall till fall.

**Använder jag samma verktyg som jag använder nu för att hantera privata moln resurser?**

Ja. Azure Portal används för distribution och ett antal hanterings åtgärder. vCenter och NSX Manager används för att hantera vSphere-och NSX-T-resurser.

**Kan jag hantera ett privat moln med min lokala vCenter?**

Vid lanseringen har AVS inte stöd för en enda hanterings upplevelse i lokala och privata moln miljöer. Privata moln kluster kommer att hanteras med vCenter och NSX Manager lokalt i ett privat moln.

**Kan jag använda vRealize Suite som körs lokalt?** 

Vissa integreringar och användnings fall kan utvärderas från fall till fall.

**Kan jag migrera virtuella vSphere-datorer från lokala miljöer till AVS-privata moln?**

Ja. Migrering av virtuella datorer och vMotion kan användas för att flytta virtuella datorer till ett privat moln om standard kraven för Cross vCenter [vMotion](https://kb.vmware.com/s/article/210695) är uppfyllda.

**Krävs en speciell version av vSphere i lokala miljöer?**

Eftersom alla moln miljöer levereras med HCX, vSphere 5,5 eller senare i lokala miljöer för vMotion.

**Vad ser processen för ändrings kontroll ut?**

Uppdateringar som görs i själva tjänsten följer Microsoft Azure standard processen för ändrings hantering. Kunderna är ansvariga för alla arbets belastnings administrations uppgifter och de tillhör ande processerna för ändrings hantering.

**Hur skiljer sig detta från Azure VMware-lösningen av CloudSimple?**

Med den nya Azure VMware-lösningen har Microsoft och VMware en direkt moln leverantörs koppling. Den nya lösningen är helt utformad, byggd och stöds av Microsoft och har godkänts av VMware. Lösningarna är dessutom konsekventa med VMware Technology-stacken som körs på en dedikerad Azure-infrastruktur.

**Vad betyder den här för hands versionen för mig om jag är en befintlig Azure VMware Solution-kund?**

Det finns ingen ändring av den befintliga Azure VMware-lösningen från CloudSimple. Vi fortsätter att stödja lösningen på Azure. Azure VMware-lösningen backas upp av vårt service nivå avtal [(SLA)](https://aka.ms/CSVMwareSLA). Kunderna bör fortsätta att använda tjänsten för produktions arbets belastningar. Det här är en tillgänglig lösning som styrs av [Microsofts tjänst villkor](https://azure.microsoft.com/support/legal/).

**Kan jag migrera från Azure VMware-lösningen från CloudSimple till den här nya lösningen?**

Ja, Azure VMware-lösningen stöder migrering med välbekanta VMware-verktyg som HCX. För kunder som vill migrera till den nya lösningen arbetar du med ditt Microsoft-konto-team för att utforska alternativ och tillgänglig support.



## <a name="compute-network-and-storage"></a>Beräkning, nätverk och lagring

**Finns det fler än en typ av värd tillgänglig?**

Det finns bara en typ av värd som är tillgänglig.

**Vilka är CPU-specifikationerna i varje typ av värd?**

Servrarna har dubbla 18 kärnor på 2,3 GHz Intel-processorer.

**Hur mycket minne finns på varje värd?**

Servrarna har 576 GB RAM-minne.

**Vilken lagrings kapacitet för varje värd?**

Varje ESXi-värd har två virtuellt San-diskgroups med kapacitets nivån 15,2 TB och en 3,2 TB NVMe-cachenivå (1,6 TB i varje diskgroup).

**Hur mycket nätverks bandbredd finns på varje ESXi-värd?**

Varje ESXi-värd är AVS-konfigurerad med 4 25 Gbit/s nätverkskort, med två nätverkskort som har allokerats för ESXi system trafik och två nätverkskort som har allokerats för arbets belastnings trafik. 

**Är data lagrade på virtuellt San-datalager krypterade i vila?**

Ja, alla virtuellt San-data krypteras som standard med hjälp av nycklar som lagras i Azure Key Vault.

## <a name="hosts-clusters-and-private-clouds"></a>Värdar, kluster och privata moln

**Delas den underliggande infrastrukturen?**

Nej, värdar och kluster för privata moln är dedikerade och raderas på ett säkert sätt före och efter användning.

**Vilka är det lägsta och högsta antalet värdar per kluster?**

Kluster kan skalas mellan 3-och 16 ESXi-värdar. Utvärderings kluster är begränsade till tre värdar.

**Kan jag skala mina privata moln kluster?**

Ja, klustren skalas mellan det lägsta och högsta antalet ESXi-värdar. Utvärderings kluster är begränsade till tre värdar.

**Vad är utvärderings kluster?**

Utvärderings kluster är tre värd kluster som används för en månads utvärdering av moln privata moln.

**Kan jag använda avancerade värdar för utvärderings kluster?**

Nej. Avancerade ESXi-värdar är reserverade för användning i produktions kluster.

## <a name="avs-and-vmware-software"></a>AVS-och VMware-programvara

**Vilka versioner av VMware-programvaran används i privata moln?**

Privata moln använder vSphere 6,7, virtuellt San 6,7, HCX och version 2,5 av NSX-T.  

**Använder privata moln VMware-NSX?**

Ja, NSX-T 2,5 används för program vara som definierats i molnets privata moln.

**Kan jag använda VMware NSX-V i ett privat moln?**

Nej. NSX-T är den enda version av NSX som stöds.

**Krävs NSX i lokala miljöer eller nätverk som ansluter till ett privat moln.**

Nej, du behöver inte använda NSX lokalt.

**Vad är uppgraderings-och uppdaterings schema för VMware-programvara i ett privat moln?**

Uppgraderingarna av det privata molnet för program varu paketet görs för att hålla program varan i en version av den senaste versionen av program varu paketet från VMware. Program varu versionerna för det privata molnet kan skilja sig från de senaste versionerna av de enskilda program varu komponenterna (ESXi, NSX-T, vCenter, virtuellt SAN).

**Hur ofta uppdateras program stacken för privata moln?**

Program varan för det privata molnet uppgraderas enligt ett schema som spårar program varu paketets version från VMware. Det privata molnet kräver ingen stillestånds tid för uppgraderingar.

## <a name="connectivity"></a>Anslutningsmöjlighet

**Vilken nätverks-IP-adress planering krävs för att inkludera privata moln med lokala miljöer?**

Det krävs ett privat nätverk/22-adressutrymme för att distribuera ett moln i molnet. Det privata adress utrymmet får inte överlappa andra virtuella nätverk i en prenumeration eller med lokala nätverk.
 
**Hur gör jag för att ansluta från lokala miljöer till ett privat moln i molnet?**

Du kan ansluta till tjänsten på något av två sätt: 

- Med en virtuell dator eller Programgateway distribuerad i ett virtuellt Azure-nätverk som peer-kopplas via ExpressRoute till det privata molnet.
- Via ExpressRoute Global Reach från ditt lokala data Center till en Azure ExpressRoute-krets.

**Hur gör jag för att ansluter du en virtuell dator för arbets belastning till Internet eller en Azure-tjänst slut punkt?**

I Azure Portal aktiverar du Internet anslutning för ett privat moln. Med NSX-T Manager skapar du en NSX-T T1-router och en logisk växel. Sedan använder du vCenter för att distribuera en virtuell dator i det nätverks segment som definieras av den logiska växeln. Den virtuella datorn kommer att ha nätverks åtkomst till Internet och till Azure-tjänster.

**Behöver jag begränsa åtkomst från Internet till virtuella datorer i logiska nätverk i ett privat moln?**

Nej. Inkommande nätverks trafik från Internet direkt till privata moln är inte tillåten.

**Måste jag begränsa Internet åtkomst från virtuella datorer i logiska nätverk till Internet?**

Ja. Du måste använda NSX-T-hanteraren för att skapa en brand vägg som begränsar VM-åtkomsten till Internet.

## <a name="accounts-and-privileges"></a>Konton och behörigheter

**Vilka konton och behörigheter får jag med mitt nya AVS-privata moln?**

Du har angett autentiseringsuppgifter för en cloudadmin-användare i vCenter-och admin-åtkomst i NSX-T Manager. Det finns också en CloudAdmin-grupp som kan användas för att införliva Azure Active Directory. Mer information finns i [åtkomst-och identitets koncept](concepts-identity.md).

**Kan administratörs åtkomst till ESXi-värdar?**

Nej, administratörs åtkomst till ESXi är begränsad för att uppfylla säkerhets kraven för lösningen.

**Vilka behörigheter och behörigheter kommer jag att ha i vCenter?**

Du har CloudAdmin grupp privilegier. Mer information finns i [åtkomst-och identitets koncept](concepts-identity.md).

**Vilka behörigheter och behörigheter kommer jag att ha på NSX-T-hanteraren?**

Du kommer att ha fullständiga administratörs behörigheter på NSX-T och kan hantera rollbaserad åtkomst kontroll som du skulle göra med NSX-T Data Center lokalt. Mer information finns i [åtkomst-och identitets koncept](concepts-identity.md).

> [!NOTE]
> En t0-router skapas och konfigureras som en del av distributionen av ett privat moln. Eventuella ändringar av den logiska routern eller den virtuella NSX-T Edge-noden kan påverka anslutningen till ditt privata moln.

## <a name="billing-and-support"></a>Fakturering och support

**Hur kommer jag att faktureras under för hands versionen av AVS**

Fakturering för AVS-förhands granskning är månatligen enligt principen betala per användning. Ytterligare alternativ är allmänt tillgängliga.

**Hur struktureras priserna under för hands versionen av AVS?**

Allmänna frågor om prissättning finns på [prissättnings](https://azure.microsoft.com/pricing/details/azure-vmware) sidan för Azure VMware-lösningen. Priset för för hands versionen är tillgängligt på begäran, kontakta ditt konto team eller följ länken på prissättnings sidan för att kontakta Sales.

**Vem stöder AVS?**

Stöd för AVS levereras av Microsoft. Obs! enligt våra rikt linjer för förhands granskning kommer vi att ge support under 9 – 5 PM PST-arbetstimme måndag-fredag. Du kan utlösa ett support ärende från [den här länken](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

**Vilka konton behöver jag för att skapa ett privat AVS-moln?**

Du behöver ett Azure-konto i en Azure-prenumeration.

<a name="how-to-request-a-quota-increase-for-avs"></a>**Hur gör jag för att begära en värd kvot ökning för Azure VMware-lösningen?**

Du kan begära en kvot ökning genom att [skicka en support förfrågan](..\azure-portal\supportability\how-to-create-azure-support-request.md). Kvot hanterings teamet utvärderar begäran och godkänner den inom tre arbets dagar.  

> [!IMPORTANT]
> Innan du kan begära en kvot ökning måste du kontrol lera att du registrerar resurs leverantören för **Microsoft. AVS** i Azure Portal.  
> ```azurecli-interactive
> az provider register -n Microsoft.AVS --subscription <your subscription ID>
> ```
> Ytterligare sätt att registrera resurs leverantören finns i [Azure Resource providers och-typer](../azure-resource-manager/management/resource-providers-and-types.md).

1. I Azure Portal, under **Hjälp + Support**, skapa en **ny supportbegäran** och ange följande information för biljetten:
   - **Typ av problem:** Produkt
   - **Prenumeration:** Ditt prenumerations-ID
   - **Tjänst:**  Azure VMware-lösning 
   - **Sammanfattning:** Kvot ökning
   - **Problem typ:** Problem med kapacitets hantering
   - **Problem under typ:** Kund förfrågan om ytterligare värd kvot/-kapacitet

1. I beskrivningen av support ärendet på fliken information anger du:
   - Antal ytterligare noder   
   - Node-SKU
   - Region

   > [!NOTE] 
   > Som standard kommer minst fyra noder att beviljas.

1. Klicka på **Granska + skapa** för att skicka begäran.

<!-- LINKS - external -->
[kb2106952]: https://kb.vmware.com/s/article/2106952

<!-- LINKS - internal -->
[Access and Identity Concepts]: concepts-identity.md
