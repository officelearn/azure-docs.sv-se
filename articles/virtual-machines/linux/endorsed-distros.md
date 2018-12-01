---
title: Linux-distributioner på Azure-godkända | Microsoft Docs
description: Läs mer om Linux på Azure-godkända distributioner, däribland riktlinjer för Ubuntu, CentOS, Oracle och SUSE.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: jeconnoc
editor: tysonn
tags: azure-service-management,azure-resource-manager
ms.assetid: 2777a526-c260-4cb9-a31a-bdfe1a55fffc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/21/2017
ms.author: szark
ms.openlocfilehash: 3c904dadcb01dc889b159dff9ce1b9114db8103e
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2018
ms.locfileid: "52681814"
---
# <a name="linux-distributions-endorsed-on-azure"></a>På Azure-godkända Linux-distributioner
Linux-avbildningar i Azure Marketplace erbjuder våra partner. Vi arbetar med flera Linux-communities för att lägga till ännu fler alternativ i listan över godkända Distribution. Under tiden för distributioner som inte är tillgängliga från Marketplace kan du kan alltid ta med din egen Linux genom att följa riktlinjerna i [skapa och ladda upp en virtuell hårddisk som innehåller operativsystemet Linux](https://docs.microsoft.com/azure/virtual-machines/linux/create-upload-generic).

## <a name="supported-distributions-and-versions"></a>Distributioner som stöds och versioner
I följande tabell visas de Linux-distributioner och versioner som stöds på Azure. Referera till [stöd för Linux-avbildningar i Microsoft Azure](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) mer detaljerad information om stöd för Linux och teknik med öppen källkod i Azure.

Linux Integration Services (LIS)-drivrutiner för Hyper-V och Azure är kernel-moduler som Microsoft bidrar direkt till den överordnade Linux-kerneln.  Vissa LIS drivrutiner är inbyggda i den distribution kernel som standard. Äldre distributioner som är baserade på Red Hat Enterprise (RHEL) / CentOS är tillgängliga som en separat fil på [Linux Integration Services Version 4.2 för Hyper-V och Azure](https://www.microsoft.com/en-us/download/details.aspx?id=55106). Se [krav för Linux-kernel](create-upload-generic.md#linux-kernel-requirements) för mer information om LIS drivrutiner.

Azure Linux Agent har redan installerats på Azure Marketplace-avbildningar och är vanligtvis tillgängligt från paketdatabasen för den distributionsplatsen. Källkoden finns på [GitHub](https://github.com/azure/walinuxagent).

  
| Distribution | Version | Drivrutiner | Agent |
| --- | --- | --- | --- |
| CentOS |CentOS 6.3 +, 7.0 + |CentOS 6.3: [LIS ladda ned](https://www.microsoft.com/en-us/download/details.aspx?id=55106)<p>CentOS 6.4 +: I kernel |Paket: I [lagringsplatsen](http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/) under ”WALinuxAgent” <br/>Källkod: [GitHub](https://github.com/Azure/WALinuxAgent) |
| [CoreOS](https://coreos.com/docs/running-coreos/cloud-providers/azure/) |494.4.0+ |I kernel |Källkod: [GitHub](https://github.com/coreos/coreos-overlay/tree/master/app-emulation/wa-linux-agent) |
| Debian |Debian 7,9 +, 8.2 + |I kernel |Paket: I lager under ”waagent” <br/>Källkod: [GitHub](https://github.com/Azure/WALinuxAgent) |
| Oracle Linux |6.4+, 7.0+ |I kernel |Paket: I lager under ”WALinuxAgent” <br/>Källkod: [GitHub](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| Red Hat Enterprise Linux |RHEL 6.7 +, 7.1 + |I kernel |Paket: I lager under ”WALinuxAgent” <br/>Källkod: [GitHub](https://github.com/Azure/WALinuxAgent) |
| SUSE Linux Enterprise |SLES/SLES för SAP<br>11 SP4<br>12 SP1 +<br>15|I kernel |Paket:<p> för 11 i [molnet: verktyg](https://build.opensuse.org/project/show/Cloud:Tools) lagringsplats<br>för 12 som ingår i modulen ”offentliga moln” under ”python – azure-agent”<br/>Källkod: [GitHub](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| openSUSE |openSUSE steg 42.2 + |I kernel |Paket: I [molnet: verktyg](https://build.opensuse.org/project/show/Cloud:Tools) lagringsplatsen under ”python – azure-agent” <br/>Källkod: [GitHub](https://github.com/Azure/WALinuxAgent) |
| Ubuntu |Ubuntu 12.04 +  **<sup>1</sup>** |I kernel |Paket: I lager under ”walinuxagent” <br/>Källkod: [GitHub](https://github.com/Azure/WALinuxAgent) |

  - **<sup>1</sup>**  för Ubuntu 12.04 support på Azure finns i den [EOL meddelande](https://azure.microsoft.com/blog/ubuntu-12-04-precise-pangolin-nearing-end-of-life/).


## <a name="partners"></a>Partner

### <a name="coreos"></a>CoreOS
[https://coreos.com/docs/running-coreos/cloud-providers/azure/](https://coreos.com/docs/running-coreos/cloud-providers/azure/)

Från CoreOS-webbplats:

*CoreOS är utformat för säkerhet, konsekvens och tillförlitlighet. Istället för att installera paket via yum eller apt använder CoreOS Linux-behållare för att hantera dina tjänster på en högre abstraktionsnivå. En enskild tjänst kod och alla beroenden paketeras i en behållare som kan köras på en eller flera CoreOS-datorer.*

### <a name="credativ"></a>credativ
[http://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure](http://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure)

Credativ är en oberoende consulting services-företag som specialiserar sig på utveckling och implementering av professionella lösningar med hjälp av kostnadsfri programvara. Som ledande experter för öppen källkod har Credativ internationella erkännande med många IT-avdelningar som använder sina stöd. Tillsammans med Microsoft Credativ för närvarande förbereder motsvarande Debian avbildningar för Debian 8 (Jessie) och Debian innan 7 (Wheezy). Båda bilderna är särskilt utformade för att köra på Azure och kan enkelt hanteras via plattformen. Credativ stöder också det långsiktiga underhållet och uppdaterar Debian avbildning för Azure via dess supportcenter för öppen källkod.

### <a name="oracle"></a>Oracle
[http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html](http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

Oracles strategin är att erbjuda en bred portfölj med lösningar för offentliga och privata moln. Den här strategin kan kunderna valmöjligheter och flexibilitet i hur de distribuerar en Oracle-programvara i Oracle-moln och i andra moln. Oracles partnerskap med Microsoft gör att kunderna kan distribuera Oracle-program i Microsofts offentliga och privata moln med certifiering och support från Oracle.  Oracles åtagande och investeringar i Oracle offentliga och privata molnlösningar är oförändrat.

### <a name="red-hat"></a>Red Hat
[http://www.redhat.com/en/partners/strategic-alliance/microsoft](http://www.redhat.com/en/partners/strategic-alliance/microsoft)

Världens ledande leverantör av lösningar med öppen källkod, Red Hat hjälper att mer än 90% av Fortune 500-företagen stärka verksamheten, justera sina IT och verksamheten strategier och förbereda för teknikens framtid. Red Hat gör detta genom att tillhandahålla säkra lösningar via en öppen affärsmodell och en prisvärd, förutsägbar prenumerationsmodell.

### <a name="suse"></a>SUSE
[http://www.suse.com/suse-linux-enterprise-server-on-azure](http://www.suse.com/suse-linux-enterprise-server-on-azure)

SUSE Linux Enterprise Server på Azure är en beprövad plattform som erbjuder överlägsen pålitlighet och säkerhet för molnbaserad databehandling. SUSES mångsidig Linux-plattformen integreras sömlöst med Azure cloud services för att leverera en lätthanterlig molnmiljö. Med mer än 9,200 certifierade program från mer än 1 800 oberoende programvaruleverantörer för SUSE Linux Enterprise Server säkerställer SUSE att arbetsbelastningar som körs stöds i datacentret tryggt kan distribueras på Azure.

### <a name="canonical"></a>Canonical
[http://www.ubuntu.com/cloud/azure](http://www.ubuntu.com/cloud/azure)

Canonical tekniker och öppna community-styrning enhet Ubuntus framgång i klienten, server och molnbaserad databehandling, som innehåller personliga molntjänster för konsumenter. Canonicals vision om en enhetlig, kostnadsfri plattform i Ubuntu, via telefon till molnet, ger en serie sammanhängande gränssnitt för telefon, surfplatta, TV och desktop. Den här visuellt innehåll gör Ubuntu första valet för skilda från leverantörer av offentliga molntjänster skaparna av elektroniska enheter och en favorit bland enskilda tekniker.

Med utvecklare och engineering Datacenter över hela världen kan placeras unikt Canonical om du vill samarbeta med maskinvara beslutsfattare, innehållsleverantörer och programutvecklare för att publicera Ubuntu lösningar på marknaden för datorer, servrar och handdator enheter.
