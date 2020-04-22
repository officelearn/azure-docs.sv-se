---
title: 'SAP på Azure: Vilken SAP-programvara som stöds på Azure'
description: Förklarar vad SAP-programvara stöds för att distribueras på Azure
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/21/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0cfc66789360f78c927dd157305074ed5ea6f32a
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81768441"
---
# <a name="what-sap-software-is-supported-for-azure-deployments"></a>Vilken SAP-programvara som stöds för Azure-distributioner
I den här artikeln beskrivs hur du kan ta reda på vad SAP-programvara som stöds för Azure-distributioner och vilka nödvändiga operativsystemversioner eller DBMS-versioner som behövs.

Utvärdera, om din nuvarande SAP-programvara stöds och vilka OS- och DBMS-versioner som stöds med din SAP-programvara i Azure, behöver du åtkomst till:

- SAP-supportanteckningar
- SAP-produkttillgänglighetsmatris



## <a name="general-restrictions-for-sap-workload"></a>Allmänna begränsningar för SAP-arbetsbelastning
Azure IaaS-tjänster som kan användas för SAP-arbetsbelastning är begränsade till x86-64- eller x64-maskinvara. Det finns inga Sparc- eller Power CPU-baserade erbjudanden som gäller för SAP-arbetsbelastning. Kunder som kör på sina program på operativsystem som ägs av maskinvaruarkitekturer som IBM-stordator eller AS400, eller där operativsystemen HP-UX, Solaris eller AIX används, måste ändra sina SAP-program, inklusive DBMS till något av följande operativsystem:

- Windows-server 64bit för x86-64-plattformen
- SUSE linux 64bit för x86-64-plattformen
- Röd hatt Linux 64Bit för x86-64-plattformen
- Oracle Linux 64bit för x86-64-plattformen

I kombination med SAP-programvara stöds inga andra OS-utgåvor eller Linux-distributioner. Exakta uppgifter om specifika versioner och ärenden dokumenteras senare i dokumentet.


## <a name="you-start-here"></a>Du börjar här
Utgångspunkten för dig är [SAP-stödanteckning #1928533](https://launchpad.support.sap.com/#/notes/1928533). När du går igenom denna SAP-anteckning uppifrån och ned visas flera områden med programvara och virtuella datorer som stöds

I det första avsnittet visas minimikraven för driftversioner som stöds med SAP-programvara i virtuella Azure-datorer i allmänhet. Om du inte når dessa minimikrav och kör äldre versioner av dessa operativsystem, måste du uppgradera din OS-release till en sådan minsta utgåva eller ännu nyare utgåvor. Det är korrekt att Azure i allmänhet skulle stödja äldre versioner av vissa av dessa operativsystem. Men de begränsningar eller minimiversioner som anges är baserade på utförda tester och kvalifikationer och kommer inte att förlängas längre tillbaka. 


> [!NOTE]
>Det finns några specifika VM-typer, HANA Stora instanser eller SAP-arbetsbelastningar som kommer att kräva nyare OS-versioner. Sådana fall kommer att tas upp i hela dokumentet. Fall som detta är tydligt dokumenterade antingen i SAP-anteckningar eller andra SAP-publikationer.

Avsnittet nedan visar allmänna SAP-plattformar som stöds med de versioner som stöds och viktigare SAP-kärnor som stöds. Den listar NetWeaver/ABAP eller Java stackar som stöds OCH, som behöver minsta kärna utgåvor. Nyare ABAP-stackar stöds på Azure, men behöver inte minsta kärnversioner eftersom ändringar för Azure har implementerats från början av utvecklingen av de senaste stackarna

Du måste kontrollera:

- Oavsett om SAP-program du kör, omfattas av de minsta utgåvor som anges. Om inte, måste du definiera en ny målversion, kontrollera i SAP Product Availability Matrix, vilka operativsystembyggen och DBMS-kombinationer som stöds med den nya målversionen. Så, att du kan välja rätt operativsystem release och DBMS release
- Oavsett om du behöver uppdatera dina SAP-kärnor i en flytt till Azure
- Oavsett om du behöver uppdatera SAP-supportpaket. Speciellt Bas Support paket som kan krävas för fall där du är skyldig att flytta till en nyare DBMS release


Nästa avsnitt går in mer information om andra SAP-produkter och DBMS-versioner som stöds av SAP på Azure för Windows och Linux. 

> [!NOTE]
> De minsta versionerna av de olika DBMS är noggrant utvalda och kanske inte alltid återspeglar hela spektrumet av DBMS-versioner de olika DBMS-leverantörernas support på Azure i allmänhet. Många SAP-arbetsbelastningsrelaterade överväganden beaktades för att definiera dessa minimiversioner. Det finns ingen ansträngning att testa och kvalificera äldre DBMS utgåvor. 

> [!NOTE]
> De minsta versionerna som anges representerar äldre version av operativsystem och databasversioner. Vi rekommenderar starkt att använda de senaste operativsystemutgåvorna och databasutgåvorna. I många fall tog nyare operativsystem- och databasversioner användningsfallet att köra i offentligt moln i beaktande och anpassad kod för att optimera för att köras i offentligt moln eller mer specifikt Azure

## <a name="oracle-dbms-support"></a>Support för Oracle DBMS
Operativsystem, Oracle DBMS-versioner och Oracle-funktioner som stöds på Azure listas specifikt i [SAP-supportanteckning #2039619](https://launchpad.support.sap.com/#/notes/2039619). Essence ur denna anteckning kan sammanfattas som:

- Minsta Oracle-utgåva som stöds på virtuella Azure-datorer som är certifierade för NetWeaver är Oracle 11g Release 2 Patchset 3 (11.2.0.4)
- Som gästoperativsystem kvalificerar endast Windows och Oracle Linux. Exakta versioner av OS och relaterade minsta DBMS-versioner listas i anteckningen
- Stödet från Oracle Linux sträcker sig även till Oracle DBMS-klienten. Detta innebär att alla SAP-komponenter, som dialoginstanser av ABAP eller Java Stack måste köras på Oracle Linux också. Endast SAP-komponenter i ett sådant SAP-system som inte skulle ansluta till Oracle DBMS skulle tillåtas att köra ett annat Linux-operativsystem
- Oracle RAC stöds inte 
- Oracle ASM stöds för vissa fall. Detaljer visas i anteckningen
- Icke-Unicode SAP-system stöds endast med programservrar som körs med Windows gästoperativsystem. Dbms gästoperativsystem kan vara Oracle Linux eller Windows. Orsaken till den här begränsningen är uppenbar när du kontrollerar PAM (SAP Product Availability Matrix). För Oracle Linux släppte SAP aldrig icke-Unicode SAP-kärnor

Känna till DBMS-versionerna som stöds med den riktade Azure-infrastrukturen måste du kontrollera SAP-produkttillgänglighetsmatrisen om huruvida OS-utgåvorna och DBMS som krävs stöds med dina SAP-produktversioner som du är avsedd att köra. 


## <a name="sap-hana-support"></a>SUPPORT FÖR SAP HANA
I Azure finns det två tjänster som kan användas för att köra HANA-databas:

- Azure Virtual Machines
- [STORA HANA-instanser](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)

För att köra SAP HANA har SAP mer och starkare villkor som infrastrukturen måste uppfylla än för att köra NetWeaver eller andra SAP-program och DBMS. Därför är ett mindre antal virtuella Azure-datorer kvalificerade för att köra SAP HANA DBMS. Listan över Azure-infrastruktur som stöds för SAP HANA finns i den så kallade [SAP HANA-maskinvarukatalogen](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). 

> [!NOTE]
> De enheter som börjar med bokstaven "S" är [HANA Stora instanser](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) enheter. 

> [!NOTE]
> SAP har ingen specifik certifiering beroende på SAP HANA-huvudutgåvorna. I motsats till den allmänna uppfattningen, kolumnen **Certifieringsscenario** i [HANA certifierade IaaS plattformar](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure), kolumnen gör **inget uttalande om HANA större eller mindre release certifierad**. Du måste anta att alla enheter som anges som kan användas för HANA 1.0 och HANA 2.0 så länge det certifierade operativsystemet släpper för de specifika enheterna stöds av HANA 1.0-utgåvor också. 

För användning av SAP HANA kan olika minimi-OS-versioner gälla än för de allmänna NetWeaver-fallen. Du måste kolla in operativsystem som stöds för varje enhet individuellt eftersom dessa kan variera. Du gör det genom att klicka på varje enhet. Mer information kommer att visas. En av detaljerna i listan är de olika operativsystem som stöds för den här specifika enheten.

> [!NOTE]
> Azure HANA Stora instansenheter är mer restriktiva med operativsystem som stöds jämfört med virtuella Azure-datorer. Å andra sidan kan virtuella Azure-datorer framtvinga nyare driftversioner som minimiversioner. Detta gäller särskilt för några av de större VM-enheter som krävde ändringar i Linux-kärnor

När du känner till det operativsystem som stöds för Azure-infrastrukturen måste du kontrollera [SAP-supportanteckningen #2235581](https://launchpad.support.sap.com/#/notes/2235581) för de exakta SAP HANA-utgåvorna och korrigeringsnivåerna som stöds med de Azure-enheter som du riktar in dig på. 

> [!IMPORTANT]
> Steget för att kontrollera de exakta SAP HANA-utgåvorna och korrigeringsnivåerna som stöds är mycket viktigt. I många fall är stöd för en viss OS-utgåva beroende av en specifik patchnivå för SAP HANA-körbara filer.

Eftersom du vet vilka HANA-versioner du kan köra på den riktade Azure-infrastrukturen måste du checka in SAP-produkttillgänglighetsmatrisen för att ta reda på om det finns begränsningar med SAP-produktversionerna som stöder DE HANA-utgåvor som du har filtrerat bort


## <a name="certified-azure-vms-and-hana-large-instance-units-and-business-transaction-throughput"></a>Certifierade virtuella Azure-datorer och HANA-enheter med stor instans och affärstransaktionsdataflöde
Förutom att utvärdera operativsystemversioner som stöds, DBMS-versioner och beroende support SAP-programvaruversioner för Azure-infrastrukturenheter, har du behovet av att kvalificera dessa enheter efter affärstransaktionsgenomflöde, vilket uttrycks i enheten 'SAP' av SAP. Alla SAP-storlek beror på SAPS-beräkningar. Utvärdera befintliga SAP-system, kan du vanligtvis, med hjälp av din infrastrukturleverantör, beräkna SAPS av enheterna. För DBMS-lagret samt för programlagret. I andra fall där nya funktioner skapas kan en storleksövning med SAP avslöja de nödvändiga SAPS-numren för programlagret och DBMS-lagret. Som infrastrukturleverantör är Microsoft skyldigt att tillhandahålla SAP-genomströmningskarakterisering av de olika enheter som antingen är NetWeaver- och/eller HANA-certifierade.

För virtuella Azure-datorer dokumenteras dessa SAPS-dataflödesnummer i [SAP-supportanteckning #1928533](https://launchpad.support.sap.com/#/notes/1928533). För Azure HANA Large Instance-enheter dokumenteras SAPS-dataflödesnumren i [SAP-supportanteckning #2316233](https://launchpad.support.sap.com/#/notes/2316233)

Om du tittar på [SAP-stödanteckning #1928533](https://launchpad.support.sap.com/#/notes/1928533)gäller följande anmärkningar:

- För virtuella virtuella datorer i **M-serien och virtuella virtuella datorer i Mv2-serien gäller olika minimiversioner av operativsystemet än för andra Azure VM-typer**. Kravet på nyare OS-versioner baseras på ändringar som de olika operativsystemleverantörerna var tvungna att tillhandahålla i sina operativsystemversioner för att antingen aktivera sina operativsystem som körs på de specifika Azure VM-typerna eller optimera prestanda och dataflöde för SAP-arbetsbelastning på dessa VM-typer
- Det finns två tabeller som anger olika vm-typer. Den andra tabellen anger SAPS-dataflöde för Azure VM-typer som endast stöder Azure-standardlagring. DBMS-distribution på de enheter som anges i den andra tabellen i anteckningen stöds inte


## <a name="other-sap-products-supported-on-azure"></a>Andra SAP-produkter som stöds på Azure
I allmänhet antagandet är att med tillståndet för hyperskala moln som Azure, de flesta av SAP-programvaran bör köras utan funktionella problem i Azure. Ändå och motsatsen till privata moln visualisering, SAP uttrycker fortfarande stöd för de olika SAP-produkter uttryckligen för de olika hyerpscale molnleverantörer. Därför finns det olika SAP-supportanteckningar som anger stöd för Azure för olika SAP-produkter. 

För Business Objects BI-plattform ger [SAP-supportanteckning #2145537](https://launchpad.support.sap.com/#/notes/2145537) en lista över SAP Business Objects-produkter som stöds på Azure. Om det finns frågor kring komponenter eller kombinationer av programvaruversioner och OS-versioner som inte verkar listas eller stöds och som är nyare än de minsta versioner som anges, måste du öppna en SAP-supportbegäran mot den komponent du frågar stöd för.

För Business Objects Data Services förklarar [SAP-supportmeddelandet #22288344](https://launchpad.support.sap.com/#/notes/2288344) minimalt stöd för SAP-datatjänster som körs på Azure. 

> [!NOTE]
> Som anges i SAP-supportanteckningen måste du checka in SAP PAM för att identifiera rätt supportpaketnivå som ska stödjas på Azure

SAP Datahub/Vora-stöd i Azure Kubernetes Services (AKS) beskrivs i [SAP-support #2464722](https://launchpad.support.sap.com/#/notes/2464722)

Stöd för SAP BPC 10.1 SP08 beskrivs i [SAP-supportmeddelande #2451795](https://launchpad.support.sap.com/#/notes/2451795)

Stöd för SAP Hybris Commerce Platform på Azure beskrivs i [Hybris Wiki](https://cxwiki.sap.com/display/cloudss/Using+the+hybris+Platform+with+the+Cloud). Som av stöds DBMS för SAP Hybris Commerce Platform, den listar som:

- SQL Server och Oracle på Windows operativsystem plattform. Samma minimiversioner gäller som för SAP NetWeaver. Se [SAP-supportanteckning #1928533](https://launchpad.support.sap.com/#/notes/1928533) för mer information
- SAP HANA på Red Hat och SUSE Linux. SAP HANA-certifierade VM-typer krävs som dokumenterade tidigare i [det här dokumentet](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure#sap-hana-support). SAP (Hybris) Commerce Platform anses OLTP arbetsbelastning
- SQL Azure DB från och med SAP (Hybris) Commerce Platform version 1811




## <a name="next-steps"></a>Efterföljande moment
Läs nästa steg i [azure virtual machines planering och implementering för SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)

