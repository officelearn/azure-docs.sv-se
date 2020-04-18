---
title: Linuxdistributioner som är godkända på Azure
description: Lär dig mer om Linux på Azure-stödda distributioner, inklusive riktlinjer för Ubuntu, CentOS, Oracle och SUSE.
services: virtual-machines-linux
documentationcenter: ''
author: gbowerman
manager: gwallace
tags: azure-service-management,azure-resource-manager
ms.assetid: 2777a526-c260-4cb9-a31a-bdfe1a55fffc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 11/04/2019
ms.author: guybo
ms.openlocfilehash: ed7755251feb04a5f811d6ed96b00a347fba8994
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605925"
---
# <a name="endorsed-linux-distributions-on-azure"></a>Godkände Linux-distributioner på Azure
Partner tillhandahåller Linux-avbildningar på Azure Marketplace. Vi arbetar med olika Linux-samhällen för att lägga till ännu fler smaker till listan Övertecknade distributionsplatser. Under tiden, för distributioner som inte är tillgängliga från Marketplace, kan du alltid ta med din egen Linux genom att följa riktlinjerna på [Skapa och ladda upp en virtuell hårddisk som innehåller Operativsystemet Linux](https://docs.microsoft.com/azure/virtual-machines/linux/create-upload-generic).

## <a name="supported-distributions-and-versions"></a>Distributioner och versioner som stöds
I följande tabell visas de Linux-distributioner och versioner som stöds på Azure. Mer detaljerad information om stöd för Linux och teknik med öppen källkod i Azure finns [i Support för Linux-avbildningar i Microsoft Azure.](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure)

LIS-drivrutinerna (Linux Integration Services) för Hyper-V och Azure är kärnmoduler som Microsoft bidrar direkt till linuxkärnan i uppströms.  Vissa LIS-drivrutiner är inbyggda i distributionens kärna som standard. Äldre distributioner som baseras på Red Hat Enterprise (RHEL)/CentOS är tillgängliga som en separat nedladdning på [Linux Integration Services Version 4.2 för Hyper-V och Azure](https://www.microsoft.com/download/details.aspx?id=55106). Se [Linux-kärnkrav](create-upload-generic.md#linux-kernel-requirements) för mer information om LIS-drivrutinerna.

Azure Linux Agent är redan förinstallerad på Azure Marketplace-avbildningar och är vanligtvis tillgänglig från distributionens paketlagringsplats. Källkoden finns på [GitHub](https://github.com/azure/walinuxagent).


| Distribution | Version | Drivrutiner | Agent |
| --- | --- | --- | --- |
| CentOS |CentOS 6,3+, 7,0+, 8,0+ |CentOS 6.3: [LIS nedladdning](https://www.microsoft.com/download/details.aspx?id=55106)<p>CentOS 6.4+: I kärnan |Paket: I [repo](http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/) under "WALinuxAgent" <br/>Källkod: [GitHub](https://github.com/Azure/WALinuxAgent) |
| [CoreOS](https://coreos.com/docs/running-coreos/cloud-providers/azure/) |494.4.0+ |I kärnan |Källkod: [GitHub](https://github.com/coreos/coreos-overlay/tree/master/app-emulation/wa-linux-agent) |
| Debian |Debian 7.9+, 8.2+, 9, 10 |I kärnan |Paket: I repo under "waagent" <br/>Källkod: [GitHub](https://github.com/Azure/WALinuxAgent) |
| Oracle Linux |6,4+, 7,0+ |I kärnan |Paket: I repo under "WALinuxAgent" <br/>Källkod: [GitHub](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| Red Hat Enterprise Linux |RHEL 7,1+, 8,0+ |I kärnan |Paket: I repo under "WALinuxAgent" <br/>Källkod: [GitHub](https://github.com/Azure/WALinuxAgent) |
| SUSE Linux Företag |SLES/SLES för SAP<br>11 SP4<br>12 SP1+<br>15|I kärnan |Paket:<p> för 11 i [Cloud:Tools](https://build.opensuse.org/project/show/Cloud:Tools) repo<br>för 12 ingår i "Public Cloud" Module under "python-azure-agent"<br/>Källkod: [GitHub](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| openSUSE |openSUSE Leap 42.2+ |I kärnan |Paket: I [Cloud:Tools](https://build.opensuse.org/project/show/Cloud:Tools) repo under "python-azure-agent" <br/>Källkod: [GitHub](https://github.com/Azure/WALinuxAgent) |
| Ubuntu |Ubuntu 12.04+ ** <sup>1</sup>** |I kärnan |Paket: I repo under "walinuxagent" <br/>Källkod: [GitHub](https://github.com/Azure/WALinuxAgent) |

  - **<sup>1</sup>** Information om utökat stöd för Ubuntu 12.04 och 14.04 hittar du här: [Ubuntu Extended Security Maintenance](https://www.ubuntu.com/esm).


## <a name="image-update-cadence"></a>Kadens för bilduppdatering
Azure kräver att utgivare av de godkända Linux-distributionerna regelbundet uppdaterar sina avbildningar på Azure Marketplace med de senaste korrigeringarna och säkerhetskorrigeringarna, med en kvartalsvis eller snabbare kadens. Uppdaterade avbildningar på Azure Marketplace är automatiskt tillgängliga för kunder som nya versioner av en avbildning SKU. Mer information om hur du hittar Linux-avbildningar: [Hitta VM-avbildningar på Linux på Azure Marketplace](https://docs.microsoft.com/azure/virtual-machines/linux/cli-ps-findimage).

### <a name="additional-links"></a>Ytterligare länkar
 - [Livscykel för SUSE Public Cloud Image](https://www.suse.com/c/suse-public-cloud-image-life-cycle/)

## <a name="azure-tuned-kernels"></a>Azure-trimmade kärnor

Azure har ett nära samarbete med olika godkända Linuxdistributioner för att optimera avbildningarna som de publicerade på Azure Marketplace. En aspekt av detta samarbete är utvecklingen av "trimmade" Linux-kärnor som är optimerade för Azure-plattformen och levereras som fullt stöd komponenter i Linux-distributionen. Azure-tuned-kärnorna innehåller nya funktioner och prestandaförbättringar och med en snabbare (vanligtvis kvartalsvis) kadens jämfört med standardkärnor eller allmänna kärnor som är tillgängliga från distributionen.

I de flesta fall hittar du dessa kärnor förinstallerade på standardavbildningarna på Azure Marketplace, så Azure-kunder kommer omedelbart att få nytta av dessa optimerade kärnor. Mer information om dessa Azure-tuned-kärnor finns i följande länkar:

 - CentOS Azure-trimmad kärna - tillgänglig via CentOS Virtualization SIG - [Mer info](https://wiki.centos.org/SpecialInterestGroup/Virtualization)
 - Debians molnkärna - tillgänglig med Debian 10 och Debian 9 "backports" på Azure - [Mer info](https://wiki.debian.org/Cloud/MicrosoftAzure)
 - SLES Azure-trimmad kärna – [mer information](https://www.suse.com/c/a-different-builtin-kernel-for-azure-on-demand-images/)
 - Ubuntu Azure-trimmad kärna – [mer information](https://blog.ubuntu.com/2017/09/21/microsoft-and-canonical-increase-velocity-with-azure-tailored-kernel)


## <a name="partners"></a>Partner

### <a name="coreos"></a>CoreOS
[https://coreos.com/docs/running-coreos/cloud-providers/azure/](https://coreos.com/docs/running-coreos/cloud-providers/azure/)

Från CoreOS webbplats:

*CoreOS är utformat för säkerhet, konsekvens och tillförlitlighet. Istället för att installera paket via yum eller apt använder CoreOS Linux-behållare för att hantera dina tjänster på en högre abstraktionsnivå. En enda tjänsts kod och alla beroenden är förpackade i en behållare som kan köras på en eller flera CoreOS-datorer.*

### <a name="credativ"></a>Credativ (credativ)
[https://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure](https://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure)

Credativ är ett oberoende konsult- och tjänsteföretag som specialiserat sig på utveckling och implementering av professionella lösningar med hjälp av fri programvara. Som ledande specialister med öppen källkod har Credativ internationellt erkännande med många IT-avdelningar som använder deras support. Tillsammans med Microsoft förbereder Credativ för närvarande motsvarande Debianavbildningar för Debian 8 (Jessie) och Debian före 7 (Wheezy). Båda avbildningarna är speciellt utformade för att köras på Azure och kan enkelt hanteras via plattformen. Credativ kommer också att stödja långsiktigt underhåll och uppdatering av Debianavbildningar för Azure via dess supportcenter för öppen källkod.

### <a name="oracle"></a>Oracle
[https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

Oracles strategi är att erbjuda en bred portfölj av lösningar för offentliga och privata moln. Strategin ger kunderna valfrihet och flexibilitet i hur de distribuerar Oracle-programvara i Oracle-moln och andra moln. Oracles partnerskap med Microsoft gör det möjligt för kunder att distribuera Oracle-programvara i Microsofts offentliga och privata moln med förtroende för certifiering och support från Oracle.  Oracles engagemang och investeringar i Oracles offentliga och privata molnlösningar är oförändrade.

### <a name="red-hat"></a>Red Hat
[https://www.redhat.com/en/partners/strategic-alliance/microsoft](https://www.redhat.com/en/partners/strategic-alliance/microsoft)

Red Hat är världens ledande leverantör av lösningar med öppen källkod och hjälper mer än 90 % av Fortune 500-företagen att lösa affärsutmaningar, anpassa sina IT- och affärsstrategier och förbereda sig för teknikens framtid. Red Hat gör detta genom att tillhandahålla säkra lösningar genom en öppen affärsmodell och en prisvärd, förutsägbar prenumerationsmodell.

### <a name="suse"></a>SUSE
[https://www.suse.com/suse-linux-enterprise-server-on-azure](https://www.suse.com/suse-linux-enterprise-server-on-azure)

SUSE Linux Enterprise Server på Azure är en beprövad plattform som ger överlägsen tillförlitlighet och säkerhet för molnbaserad databehandling. SUSE:s mångsidiga Linux-plattform integreras sömlöst med Azures molntjänster för att leverera en lätthanterlig molnmiljö. Med mer än 9 200 certifierade program från mer än 1 800 oberoende programvaruleverantörer för SUSE Linux Enterprise Server säkerställer SUSE att arbetsbelastningar som körs stöds i datacentret kan distribueras på ett säkert sätt på Azure.

### <a name="canonical"></a>Canonical
[https://www.ubuntu.com/cloud/azure](https://www.ubuntu.com/cloud/azure)

Kanonisk teknik och öppen community styrning driva Ubuntu framgång inom klient, server och cloud computing, som inkluderar personliga molntjänster för konsumenter. Canonical vision av en enhetlig, fri plattform i Ubuntu, från telefon till moln, ger en familj av sammanhängande gränssnitt för telefon, surfplatta, TV och skrivbordet. Denna vision gör Ubuntu till det första valet för olika institutioner från offentliga molnleverantörer till tillverkare av hemelektronik och en favorit bland enskilda tekniker.

Med utvecklare och ingenjörscentra runt om i världen är Canonical unikt positionerat för att samarbeta med maskinvarutillverkare, innehållsleverantörer och programvaruutvecklare för att få Ubuntu-lösningar till marknaden för datorer, servrar och handhållna enheter.
