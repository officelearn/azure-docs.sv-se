---
title: Linux-distributioner som har godkänts på Azure
description: Lär dig mer om Linux på Azure-godkända distributioner, inklusive rikt linjer för Ubuntu, CentOS, Oracle och SUSE.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81605925"
---
# <a name="endorsed-linux-distributions-on-azure"></a>Signerade Linux-distributioner på Azure
Partner tillhandahåller linux-avbildningar på Azure Marketplace. Vi arbetar med olika Linux-communities för att lägga till ännu fler varianter i den godkända distributions listan. Under tiden kan du för distributioner som inte är tillgängliga från Marketplace alltid ta med din egen Linux genom att följa rikt linjerna i [skapa och ladda upp en virtuell hård disk som innehåller Linux-operativsystemet](https://docs.microsoft.com/azure/virtual-machines/linux/create-upload-generic).

## <a name="supported-distributions-and-versions"></a>Distributioner och versioner som stöds
I följande tabell visas de Linux-distributioner och-versioner som stöds i Azure. Mer detaljerad information om support för Linux och teknik med öppen källkod i Azure hittar du i [stöd för Linux-avbildningar i Microsoft Azure](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) .

LIS-drivrutinerna (Linux Integration Services) för Hyper-V och Azure är kernel-moduler som Microsoft bidrar direkt till i den överordnade Linux-kärnan.  Vissa LIS-drivrutiner är inbyggda i distributionens kernel som standard. Äldre distributioner som baseras på RHEL-/CentOS (Red Hat Enterprise) är tillgängliga som separat hämtning på [Linux Integration Services Version 4,2 för Hyper-V och Azure](https://www.microsoft.com/download/details.aspx?id=55106). Se [krav för linux-kernel](create-upload-generic.md#linux-kernel-requirements) för mer information om Lis-drivrutinerna.

Azure Linux-agenten är redan förinstallerad på Azure Marketplace-avbildningar och är vanligt vis tillgänglig från distributionens paket lagrings plats. Du hittar käll koden på [GitHub](https://github.com/azure/walinuxagent).


| Distribution | Version | Drivrutiner | Agent |
| --- | --- | --- | --- |
| CentOS |CentOS 6.3 +, 7.0 +, 8.0 + |CentOS 6,3: [Lis-hämtning](https://www.microsoft.com/download/details.aspx?id=55106)<p>CentOS 6.4 +: i kernel |Paket: i [lagrings platsen](http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/) under "WALinuxAgent" <br/>Källkod: [GitHub](https://github.com/Azure/WALinuxAgent) |
| [CoreOS](https://coreos.com/docs/running-coreos/cloud-providers/azure/) |494.4.0 + |I kernel |Källkod: [GitHub](https://github.com/coreos/coreos-overlay/tree/master/app-emulation/wa-linux-agent) |
| Debian |Debian 7.9 +, 8.2 +, 9, 10 |I kernel |Paket: i lagrings platsen under "waagent" <br/>Källkod: [GitHub](https://github.com/Azure/WALinuxAgent) |
| Oracle Linux |6.4 +, 7.0 + |I kernel |Paket: i lagrings platsen under "WALinuxAgent" <br/>Källkod: [GitHub](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| Red Hat Enterprise Linux |RHEL 7.1 +, 8.0 + |I kernel |Paket: i lagrings platsen under "WALinuxAgent" <br/>Källkod: [GitHub](https://github.com/Azure/WALinuxAgent) |
| SUSE Linux Enterprise |SLES/SLES för SAP<br>11 SP4<br>12 SP1 +<br>15|I kernel |Paketfilerna<p> för 11 i [molnet: verktyg](https://build.opensuse.org/project/show/Cloud:Tools) lagrings platsen<br>för 12 som ingår i modulen "offentligt moln" under "python-Azure-agent"<br/>Källkod: [GitHub](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| openSUSE |openSUSE skottår 42.2 + |I kernel |Paket: i [molnet: tools](https://build.opensuse.org/project/show/Cloud:Tools) lagrings platsen under "python-Azure-agent" <br/>Källkod: [GitHub](https://github.com/Azure/WALinuxAgent) |
| Ubuntu |Ubuntu 12.04 + ** <sup>1</sup>** |I kernel |Paket: i lagrings platsen under "walinuxagent" <br/>Källkod: [GitHub](https://github.com/Azure/WALinuxAgent) |

  - **<sup>1</sup>** information om utökat stöd för Ubuntu 12,04 och 14,04 hittar du här: [Ubuntu Extended Security Maintenance](https://www.ubuntu.com/esm).


## <a name="image-update-cadence"></a>Takt för avbildnings uppdatering
Azure kräver att utgivare av de signerade Linux-distributionerna regelbundet uppdaterar sina avbildningar på Azure Marketplace med de senaste korrigeringarna och säkerhets korrigeringarna, på ett kvartal eller snabbare takt. Uppdaterade avbildningar på Azure Marketplace är automatiskt tillgängliga för kunder som nya versioner av en avbildnings-SKU. Mer information om hur du hittar Linux-avbildningar: [hitta virtuella Linux-avbildningar på Azure Marketplace](https://docs.microsoft.com/azure/virtual-machines/linux/cli-ps-findimage).

### <a name="additional-links"></a>Ytterligare länkar
 - [Bild livs cykel för SUSE offentlig moln bild](https://www.suse.com/c/suse-public-cloud-image-life-cycle/)

## <a name="azure-tuned-kernels"></a>Azure-justerade kärnor

Azure fungerar nära olika godkända Linux-distributioner för att optimera de bilder som de har publicerat på Azure Marketplace. En aspekt av det här samarbetet är utvecklingen av "justerade" Linux-kerneler som är optimerade för Azure-plattformen och levereras som komponenter som stöds fullt ut av Linux-distributionen. Azure-justerade kernels innehåller nya funktioner och prestanda förbättringar och går snabbare (kvartals vis) takt jämfört med de standard-eller generiska kärnor som är tillgängliga från distributionen.

I de flesta fall kommer du att se dessa kernels förinstallerade på standard avbildningarna på Azure Marketplace och så att Azure-kunder omedelbart får fördelarna med dessa optimerade kärnor. Mer information om dessa Azure-justerade kärnor finns i följande länkar:

 - CentOS Azure-justerad kernel – tillgängligt via CentOS Virtualization SIG- [Mer information](https://wiki.centos.org/SpecialInterestGroup/Virtualization)
 - Debian Cloud kernel – tillgängligt med Debian 10-och Debian 9 "backports"-avbildningen på Azure – [Mer information](https://wiki.debian.org/Cloud/MicrosoftAzure)
 - SLES Azure-justerad kernel – [Mer information](https://www.suse.com/c/a-different-builtin-kernel-for-azure-on-demand-images/)
 - Ubuntu Azure-justerad kernel – [Mer information](https://blog.ubuntu.com/2017/09/21/microsoft-and-canonical-increase-velocity-with-azure-tailored-kernel)


## <a name="partners"></a>Partner

### <a name="coreos"></a>CoreOS
[https://coreos.com/docs/running-coreos/cloud-providers/azure/](https://coreos.com/docs/running-coreos/cloud-providers/azure/)

Från webbplatsen Core:

*Core är utformat för säkerhet, konsekvens och pålitlighet. I stället för att installera paket via yum eller apt använder kärnan Linux-behållare för att hantera dina tjänster på en högre nivå av abstraktion. En enskild tjänst kod och alla beroenden paketeras i en behållare som kan köras på en eller flera kärnor datorer.*

### <a name="credativ"></a>Credativ
[https://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure](https://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure)

Credativ är ett oberoende konsult-och tjänste företag som specialiserar sig på utveckling och implementering av yrkes lösningar med hjälp av kostnads fri program vara. Som ledande specialister med öppen källkod har credativ internationell igenkänning med många IT-avdelningar som använder sin support. Tillsammans med Microsoft förbereder credativ för närvarande motsvarande Debian-avbildningar för Debian 8 (Jessie) och Debian innan 7 (Wheezy). Båda bilderna är särskilt utformade för att köras på Azure och kan enkelt hanteras via plattformen. Credativ kommer också att ha stöd för långsiktigt underhåll och uppdatering av Debian-avbildningarna för Azure via Support Center för öppen källkod.

### <a name="oracle"></a>Oracle
[https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

Oracle-strategin är att erbjuda en bred portfölj med lösningar för offentliga och privata moln. Strategin ger kunderna valmöjligheter och flexibilitet i hur de distribuerar Oracle-programvara i Oracle-moln och andra moln. Oracle: s partnerskap med Microsoft gör det möjligt för kunder att distribuera Oracle-program i Microsofts offentliga och privata moln med förtroende för certifiering och support från Oracle.  Oracles åtagande och investering i offentliga och privata moln lösningar i Oracle är oförändrade.

### <a name="red-hat"></a>Red Hat
[https://www.redhat.com/en/partners/strategic-alliance/microsoft](https://www.redhat.com/en/partners/strategic-alliance/microsoft)

Världens ledande leverantör av lösningar med öppen källkod, Red Hat hjälper mer än 90% av Fortune 500-företag att lösa affärs utmaningar, anpassa sina IT-och affärs strategier och förbereda för framtida teknik. Red Hat gör detta genom att tillhandahålla säkra lösningar via en öppen affärs modell och en prisvärd, förutsägbar prenumerations modell.

### <a name="suse"></a>SUSE
[https://www.suse.com/suse-linux-enterprise-server-on-azure](https://www.suse.com/suse-linux-enterprise-server-on-azure)

SUSE Linux Enterprise Server på Azure är en beprövad plattform som ger överlägsen tillförlitlighet och säkerhet för molnbaserad data behandling. SUSEs mångsidiga Linux-plattform integreras sömlöst med Azure Cloud Services för att leverera en lätt hanterbar moln miljö. Med fler än 9 200 certifierade program från fler än 1 800 oberoende program varu leverantörer för SUSE Linux Enterprise Server, säkerställer SUSE att arbets belastningar som körs i data centret kan distribueras på ett säkert sätt i Azure.

### <a name="canonical"></a>Canonical
[https://www.ubuntu.com/cloud/azure](https://www.ubuntu.com/cloud/azure)

Kanonisk teknik och öppen community styrnings enhet Ubuntu lyckades i klient-, server-och molnbaserad data behandling, som innehåller privata moln tjänster för konsumenter. Kanoniskt innehåll för en enhetlig, kostnads fri plattform i Ubuntu, från telefon till moln, tillhandahåller en serie sammanhängande gränssnitt för telefon, surfplatta, TV och skriv bord. Den här synen gör Ubuntu det första valet för olika institutioner från offentliga moln leverantörer till de som tillverkar konsument elektronik och en favorit i olika Technologists.

Med utvecklare och teknik Center runt om i världen är kanoniskt unikt placerad till partner med maskin varu tillverkare, innehålls leverantörer och programutvecklare för att få Ubuntu-lösningar till marknaden för datorer, servrar och handhållna enheter.
