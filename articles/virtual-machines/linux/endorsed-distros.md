---
title: Godkända distributioner av Linux | Microsoft Docs
description: Läs mer om Linux på Azure-godkända distributioner, inklusive riktlinjer för Ubuntu, CentOS, Oracle och SUSE.
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
ms.openlocfilehash: c2233ab32314655c510c8c884641293ae3325ef5
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/06/2018
ms.locfileid: "34809768"
---
# <a name="linux-on-distributions-endorsed-by-azure"></a>Linux på distributioner godkända av Azure
Partners ger Linux bilder i Azure Marketplace. Vi arbetar med olika Linux communities att lägga till ytterligare varianter i listan över godkända Distribution. Under tiden för distributioner som inte är tillgängliga från Marketplace, du kan alltid ta egna Linux genom att följa riktlinjerna i [skapa och ladda upp en virtuell hårddisk som innehåller Linux-operativsystem](https://docs.microsoft.com/azure/virtual-machines/linux/create-upload-generic).

## <a name="supported-distributions-and-versions"></a>Distributioner som stöds och versioner
I följande tabell visas de Linux-distributioner och versioner som stöds på Azure. Referera till [stöd för Linux-avbildningar i Microsoft Azure](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) mer detaljerad information om stöd för Linux och öppenkällteknik i Azure.

Linux Integration Services (LIS)-drivrutiner för Hyper-V och Azure är kernel-moduler som Microsoft bidrar direkt till den överordnade Linux-kerneln.  Vissa drivrutiner LIS är inbyggda i kernel för den distribution som standard. Äldre distributioner som är baserade på Red Hat Enterprise (RHEL) och CentOS är tillgängliga som en separat fil på [Linux Integration Services Version 4.2 för Hyper-V och Azure](https://www.microsoft.com/en-us/download/details.aspx?id=55106). Se [Linux kernel krav](create-upload-generic.md#linux-kernel-requirements) för mer information om LIS drivrutiner.

Azure Linux-agenten är redan installerad på Azure Marketplace-bilder och är vanligtvis tillgänglig från den distributionsplatsen paketdatabasen. Källkoden kan hittas på [GitHub](https://github.com/azure/walinuxagent).

  
| Distribution | Version | Drivrutiner | Agent |
| --- | --- | --- | --- |
| CentOS |CentOS 6.3 + 7.0 + |CentOS 6.3: [LIS hämta](https://www.microsoft.com/en-us/download/details.aspx?id=55106)<p>CentOS 6.4 +: I kernel |Paket: I [lagringsplatsen](http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/) under ”WALinuxAgent” <br/>Källkod: [GitHub](https://github.com/Azure/WALinuxAgent) |
| [CoreOS](https://coreos.com/docs/running-coreos/cloud-providers/azure/) |494.4.0+ |I kernel |Källkod: [GitHub](https://github.com/coreos/coreos-overlay/tree/master/app-emulation/wa-linux-agent) |
| Debian |Debian 7,9 +, 8.2 + |I kernel |Paket: I lagringsplatsen under ”waagent” <br/>Källkod: [GitHub](https://github.com/Azure/WALinuxAgent) |
| Oracle Linux |6.4+, 7.0+ |I kernel |Paket: I lagringsplatsen under ”WALinuxAgent” <br/>Källkod: [GitHub](http://go.microsoft.com/fwlink/p/?LinkID=250998) |
| Red Hat Enterprise Linux |RHEL 6.7 +, 7.1 + |I kernel |Paket: I lagringsplatsen under ”WALinuxAgent” <br/>Källkod: [GitHub](https://github.com/Azure/WALinuxAgent) |
| SUSE Linux Enterprise |SLES/SLES för SAP<br>11 SP4<br>12 SP1 +|I kernel |Paket:<p> för 11 i [molnet: verktyg](https://build.opensuse.org/project/show/Cloud:Tools) lagringsplatsen<br>för 12 ingår i modulen ”offentliga moln” under ”python-azure-agent”<br/>Källkod: [GitHub](http://go.microsoft.com/fwlink/p/?LinkID=250998) |
| openSUSE |openSUSE Leap 42.2 + |I kernel |Paket: I [molnet: verktyg](https://build.opensuse.org/project/show/Cloud:Tools) lagringsplatsen under ”python-azure-agent” <br/>Källkod: [GitHub](https://github.com/Azure/WALinuxAgent) |
| Ubuntu |Ubuntu 12.04 +  **<sup>1</sup>** |I kernel |Paket: I lagringsplatsen under ”walinuxagent” <br/>Källkod: [GitHub](https://github.com/Azure/WALinuxAgent) |

  - **<sup>1</sup>**  stöd för Ubuntu 12.04 på Azure hittar du den [EOL meddelande](https://azure.microsoft.com/blog/ubuntu-12-04-precise-pangolin-nearing-end-of-life/).


## <a name="partners"></a>Partner

### <a name="coreos"></a>CoreOS
[https://coreos.com/docs/running-coreos/cloud-providers/azure/](https://coreos.com/docs/running-coreos/cloud-providers/azure/)

Från virtuell CoreOS-webbplats:

*Virtuell CoreOS är utformad för säkerhet, konsekvens och tillförlitlighet. I stället för att installera paket via yum eller lgh använder virtuell CoreOS Linux behållare för att hantera dina tjänster på en högre abstraktionsnivå. En enskild tjänst kod och alla beroenden paketeras i en behållare som kan köras på en eller flera virtuell CoreOS-datorer.*

### <a name="credativ"></a>credativ
[http://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure](http://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure)

Credativ är en oberoende rådgivning och tjänster företaget som är specialiserat på utveckling och implementering av professional lösningar med hjälp av kostnadsfri programvara. Som inledande öppen källkod specialister har Credativ internationella recognition med många IT-avdelningar som använder sina stöd. Tillsammans med Microsoft förbereds Credativ för närvarande för motsvarande Debian avbildningar för Debian 8 (Jessie) och Debian innan 7 (Wheezy). Båda bilderna är särskilt utformade för att köras på Azure och kan enkelt hanteras via plattformen. Credativ stöder också långsiktiga underhållet och uppdatering av Debian avbildningar för Azure via dess supportcenter för öppen källa.

### <a name="oracle"></a>Oracle
[http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html](http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

Oracles strategi är att erbjuda en bred portfölj av lösningar för offentliga och privata moln. Strategin ger kunder valmöjligheterna och flexibiliteten i hur de distribuerar en Oracle-programvara i Oracle-moln och andra moln. Oracles partnerskap med Microsoft gör att kunderna kan distribuera Oracle-program i Microsofts offentliga och privata moln med certifiering och support från Oracle.  Oracles åtaganden och investering i Oracle offentliga och privata molnlösningar är oförändrade.

### <a name="red-hat"></a>Red Hat
[http://www.redhat.com/en/partners/strategic-alliance/microsoft](http://www.redhat.com/en/partners/strategic-alliance/microsoft)

Världens ledande leverantör av lösningar med öppen källkod, Red Hat hjälper mer än 90% av Fortune 500-företagen lösa utmaningar för företag, justera sina IT och företag strategier och förbereda för framtida teknik. Red Hat gör detta genom att tillhandahålla säkra lösningar via en öppen affärsmodell och prenumerationer prisvärt, förutsägbar modellen.

### <a name="suse"></a>SUSE
[http://www.suse.com/suse-linux-enterprise-server-on-azure](http://www.suse.com/suse-linux-enterprise-server-on-azure)

SUSE Linux Enterprise Server på Azure är en beprövad plattform som ger högre tillförlitlighet och säkerhet för molntjänster. SUSES flexibel Linux-plattformen integreras sömlöst med Azure-molntjänster att leverera ett enkelt hanterbar molnmiljö. Mer än 9,200 certifierade program från mer än 1 800 oberoende programvaruleverantörer för SUSE Linux Enterprise Server säkerställer SUSE att arbetsbelastningar som körs stöds i datacentret säkert sätt kan distribueras på Azure.

### <a name="canonical"></a>Canonical
[http://www.ubuntu.com/cloud/azure](http://www.ubuntu.com/cloud/azure)

Kanoniskt tekniker och öppna community styrning enheten Ubuntus lyckade i klienten, servern och molntjänster som innehåller personliga molntjänster för konsumenterna. Kanoniskt visionen för en enhetlig ledigt plattform i Ubuntu, från phone till molnet, ger en familj av sammanhängande gränssnitt för telefon, surfplatta, TV och skrivbordet. Denna vision gör Ubuntu det första valet för olika institutioner från leverantörer av offentliga molntjänster beslutsfattare konsumenten Electronics och en favorit bland enskilda tekniker.

Canonical är unikt placerad för att samarbeta med maskinvara beslutsfattare innehållsleverantörer och utvecklare att göra Ubuntu lösningar marknaden för datorer, servrar och handdatorer med utvecklare och engineering Datacenter runtom i världen.
