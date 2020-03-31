---
title: Introduktionskrav för SAP HANA på Azure (stora instanser) | Microsoft-dokument
description: Introduktionskrav för SAP HANA på Azure (stora instanser).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/31/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 141a75a75a214ff4a6f136df7570d6e81f7f4e82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617005"
---
# <a name="onboarding-requirements"></a>Registreringskrav

Den här listan samlar krav för att köra SAP HANA på Azure (större instanser).

**Microsoft Azure**

- En Azure-prenumeration som kan länkas till SAP HANA på Azure (Stora instanser).
- Microsoft Premier-supportkontrakt. Mer information om hur du kör SAP i Azure finns i [SAP Support Note #2015553 – SAP på Microsoft Azure: Supportkrav](https://launchpad.support.sap.com/#/notes/2015553). Om du använder HANA Large Instance-enheter med 384 och fler processorer måste du också utöka Premier-supportkontraktet till att omfatta Azure Rapid Response.
- Medvetenhet om DE SKU:er för stora instanser som du behöver när du har utför en storleksövning med SAP.

**Nätverksanslutningar**

- ExpressRoute mellan lokalt till Azure: Om du vill ansluta ditt lokala datacenter till Azure, se till att beställa minst en 1-Gbps-anslutning från internetleverantören. Anslutningen mellan HANA Large Instance-enheter och Azure använder expressroute-teknik också. Den här ExpressRoute-anslutningen mellan HANA-enheter för stora instanser och Azure ingår i priset för HANA-stora instansenheter, inklusive alla dataingående och utgående avgifter för den här specifika ExpressRoute-kretsen. Därför stöter du som kund inte på extra kostnader utöver din ExpressRoute-länk mellan lokala och Azure.

**Operativsystem**

- Licenser för SUSE Linux Enterprise Server 12 för SAP-program.

   > [!NOTE] 
   > Operativsystemet som levereras av Microsoft är inte registrerat hos SUSE. Den är inte ansluten till en Prenumerationshanteringsverktygsinstans.

- SUSE Linux Subscription Management Tool distribueras i Azure på en virtuell dator. Det här verktyget ger möjlighet för SAP HANA på Azure (stora instanser) som ska registreras och uppdateras av SUSE. (Det finns ingen internetåtkomst i datacentret för STORA INSTANSEN I HANA.) 
- Licenser för Red Hat Enterprise Linux 6.7 eller 7.x för SAP HANA.

   > [!NOTE]
   > Operativsystemet som levereras av Microsoft är inte registrerat hos Red Hat. Den är inte ansluten till en Red Hat Subscription Manager-instans.

- Red Hat-prenumerationshanteraren distribueras i Azure på en virtuell dator. Red Hat-prenumerationshanteraren tillhandahåller möjligheten för SAP HANA på Azure (Stora instanser) som ska registreras och uppdateras av Red Hat. (Det finns ingen direkt internetåtkomst inifrån klienten som distribueras på Azure Large Instance-stämpeln.)
- SAP kräver att du har ett supportavtal med din Linux-leverantör också. Det här kravet tas inte bort av lösningen av HANA Large Instance eller det faktum att du kör Linux i Azure. Till skillnad från vissa av Linux Azure-galleriavbildningarna ingår *inte* serviceavgiften i lösningserbjudandet för HANA Large Instance. Det är ditt ansvar att uppfylla kraven i SAP när det gäller supportavtal med Linux-distributören. 
   - För SUSE Linux letar du upp kraven för supportkontrakt i [SAP Note #1984787 - SUSE Linux Enterprise Server 12: Installationsanteckningar](https://launchpad.support.sap.com/#/notes/1984787) och SAP Note #1056161 - [SUSE-prioritetsstöd för SAP-program](https://launchpad.support.sap.com/#/notes/1056161).
   - För Red Hat Linux måste du ha rätt prenumerationsnivåer som inkluderar support- och tjänstuppdateringar till operativsystemen för HANA Large Instance. Red Hat rekommenderar Red Hat Enterprise Linux-prenumerationen för SAP-lösning. Se https://access.redhat.com/solutions/3082481. 

För supportmatrisen för de olika SAP HANA-versionerna med de olika Linux-versionerna finns i [SAP Note #2235581](https://launchpad.support.sap.com/#/notes/2235581).

Information om kompatibilitetsmatrisen för operativsystem- och HLI-versioner av inbyggd programvara/drivrutin finns [i OS Upgrade for HLI](os-upgrade-hana-large-instance.md).


> [!IMPORTANT] 
> För typ II-enheter stöds endast SLES 12 SP2 OS-versionen just nu. 


**Databas**

- Licenser och programvaruinstallationskomponenter för SAP HANA (plattform eller företagsutgåva).

**Program**

- Licenser och programvaruinstallationskomponenter för alla SAP-program som ansluter till SAP HANA och relaterade SAP-supportavtal.
- Licenser och programvaruinstallationskomponenter för icke-SAP-program som används med SAP HANA på Azure-miljöer (stora instanser) och relaterade supportkontrakt.

**Färdigheter**

- Erfarenhet av och kunskap om Azure IaaS och dess komponenter.
- Erfarenhet av och kunskap om hur du distribuerar en SAP-arbetsbelastning i Azure.
- SAP HANA installation certifierad personlig.
- SAP-arkitektens färdigheter för att utforma hög tillgänglighet och katastrofåterställning runt SAP HANA.

**SAP**

- Förhoppningen är att du är en SAP-kund och har ett supportavtal med SAP.
- Speciellt för implementeringar av klass för typ II av HANA Large Instance SKU: er, rådgöra med SAP om versioner av SAP HANA och eventuella konfigurationer på storskalig skala upp hårdvara.

**Nästa steg**
- Referera [till SAP HANA-arkitektur (stora instanser) på Azure](hana-architecture.md)