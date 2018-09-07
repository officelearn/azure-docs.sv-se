---
title: Integrationskrav för SAP HANA på Azure (stora instanser) | Microsoft Docs
description: Onboarding-krav för SAP HANA på Azure (stora instanser).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d287f79f4161c509f96b679d4b794c2906f2e020
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/06/2018
ms.locfileid: "44028487"
---
# <a name="onboarding-requirements"></a>Värvningskrav

Den här listan monterar krav för att köra SAP HANA på Azure (stora instanser).

**Microsoft Azure**

- En Azure-prenumeration som kan länkas till SAP HANA på Azure (stora instanser).
- Microsoft Premier support-kontrakt. Specifik information som rör köra SAP i Azure finns i [SAP Support Obs! #2015553 – SAP på Microsoft Azure: supportkrav](https://launchpad.support.sap.com/#/notes/2015553). Om du använder stora HANA-instansen enheter 384 och mer CPU: er kan du också behöva utöka Premier support-kontrakt med Azure Rapid Response.
- Medvetenhet om HANA stora instans SKU: er du behöver när du har utfört en storlek Övning med SAP.

**Nätverksanslutning**

- ExpressRoute mellan en lokal plats till Azure: se till att beställa på minst 1 Gbit/s-anslutning från Leverantören för att ansluta ditt lokala Datacenter till Azure. 

**Operativsystem**

- Licenser för SUSE Linux Enterprise Server 12 för SAP-program.

   > [!NOTE] 
   > Operativsystemet som levereras av Microsoft är inte registrerad med SUSE. Det är inte ansluten till en prenumeration hanteringsverktyg-instans.

- SUSE Linux prenumeration hanteringsverktyg distribueras i Azure på en virtuell dator. Det här verktyget innehåller funktioner för SAP HANA på Azure (stora instanser) ska registreras och respektive uppdaterad av SUSE. (Det finns ingen internet-åtkomst i datacenter stora HANA-instansen.) 
- Licenser för Red Hat Enterprise Linux 6.7 eller 7.x för SAP HANA.

   > [!NOTE]
   > Operativsystemet som levereras av Microsoft är inte registrerad med Red Hat. Det är inte ansluten till en Red Hat prenumeration Manager-instans.

- Red Hat prenumeration Manager distribueras i Azure på en virtuell dator. Red Hat prenumeration Manager innehåller funktioner för SAP HANA på Azure (stora instanser) ska registreras och uppdateras respektive från Red Hat. (Det finns ingen direkt Internetåtkomst från den klient som distribuerats på stora Azure-instanser stämpeln.)
- SAP kräver att du har stöd för ett avtal med din Linux-leverantör. Det här kravet tas inte bort med hjälp av lösningen på stora HANA-instansen eller det faktum att du kör Linux i Azure. Till skillnad från med några av galleriavbildningar Linux Azure serviceavgiften är *inte* ingår i erbjudandet lösning för stora HANA-instansen. Det är ditt ansvar att uppfylla kraven i SAP om supportkontrakt med Linux-distributören. 
   - För SUSE Linux, leta upp kraven för supportkontrakt i [SAP Obs! #1984787 - SUSE Linux Enterprise Server 12: installationsinformation](https://launchpad.support.sap.com/#/notes/1984787) och [SAP Obs! #1056161 - SUSE prioriterad support för SAP-program](https://launchpad.support.sap.com/#/notes/1056161).
   - Du måste ha rätt prenumerationsnivåer som omfattar support och uppdateringar för operativsystem för stora HANA-instansen av tjänsten för Red Hat Linux. Red Hat rekommenderar prenumerationen Red Hat Enterprise Linux för SAP-lösning. Se https://access.redhat.com/solutions/3082481. 

Stödmatris för de olika versionerna för SAP HANA med olika Linux-versioner, se [SAP Obs! #2235581](https://launchpad.support.sap.com/#/notes/2235581).

Kompatibilitetsöversikten av operativsystemet och HLI firmware/drivrutinsversion finns [uppgradering av operativsystem för HLI](os-upgrade-hana-large-instance.md).


> [!IMPORTANT] 
> Versionen stöds nu för Type II enheter endast SLES 12 SP2 OS. 


**Databas**

- Licenser och software installationskomponenter för SAP HANA (plattform eller enterprise edition).

**Program**

- Licenser och programvarukomponenter i installationen för alla SAP-program som ansluter till SAP HANA och relaterade SAP supportavtal.
- Licenser och programvarukomponenter i installationen för alla icke-SAP-program används i förhållande till SAP HANA på Azure (stora instanser)-miljöer och relaterade supportavtal.

**Kunskaper**

- Erfarenhet och kunskap om Azure IaaS och dess komponenter.
- Erfarenhet och kunskap om hur du distribuerar en SAP-arbetsbelastning i Azure.
- SAP HANA-installation-certifierad personal.
- SAP architect kunskaper för att utforma hög tillgänglighet och katastrofåterställning recovery runt SAP HANA.

**SAP**

- Förutsätts att du är en SAP-kund och har stöd för ett avtal med SAP.
- Kontakta SAP på versioner av SAP HANA och eventuell konfigurationer i stor skala upp maskinvaran särskilt för implementeringar av typ II-klassen för HANA stora instans SKU: er.

**Nästa steg**
- Se [SAP HANA (stora instanser)-arkitektur på Azure](hana-architecture.md)