---
title: Azure VMware-lösning av CloudSimple – översikt
description: Lär dig mer om funktioner, scenarier och fördelar med VMware-lösningar på Azure av CloudSimple-tjänsten.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 01fd132b3e43d15c5f2eee0114ef09dbac6df2a6
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812455"
---
# <a name="what-is-vmware-solution-on-azure-by-cloudsimple"></a>Vad är VMware-lösning på Azure av CloudSimple

**VMware-lösningen på Azure av CloudSimple** är en helt hanterad tjänst som gör att du kan köra VMware-plattformen i Azure. Den här lösningen omfattar vSphere, vCenter, virtuellt San, NSX-T och motsvarande verktyg.
Din VMware-miljö körs internt i Azure-infrastrukturen för Bare Metal på Azures moln platser. Tjänsten innehåller alla funktioner som krävs för att använda VMware-plattformarna på ett effektivt och säkert sätt.

![VMware-lösning på Azure av CloudSimple-översikt](media/azure-vmware-solution-by-cloudsimple.png)

## <a name="features"></a>Funktioner

* Självbetjänings etablering på begäran av VMware Cloud-miljöer. Möjlighet att lägga till och ta bort kapacitet på begäran
* Distribution av VMware Platform, uppgradering, hanterings plan, säkerhets kopiering, hälso tillstånd/kapacitets övervakning, varningar, fel sökning och reparation.
* Underlay nätverks tjänster som krävs för att aktivera VMware, inklusive L2/L3-tjänster och hantering av brand Väggs regler.
* Nätverks tjänster för Edge-typ, inklusive VPN, offentlig IP och Internet-gatewayer. Dessa Edge-tjänster körs på Azure och har motsvarande säkerhets-och DDoS-skydd i Azure.
* Kapacitets reservation till lägre kostnader.
* Höghastighets anslutning med låg latens med hög hastighet till Azure och lokalt.
* Lösnings arkitekturer för kunder att använda Azure-tjänster på ett integrerat sätt kan du dra nytta av detta unika "VMware-moln i en offentlig moln arkitektur". Dessa Azure-tjänster inkluderar Azure AD, Storage, Application gateways och andra.
* Infrastrukturen är fullständigt dedikerad och är fysiskt isolerad från andra kunders infrastruktur.
* Hanterings funktioner som hantering av aktiviteter, användning, fakturering/mätning och användar hantering.
* kund support dygnet runt.

## <a name="benefits"></a>Fördelar

* **Drift kontinuitet:** CloudSimple erbjuder inbyggd åtkomst till VMware-plattformar. CloudSimple-arkitekturen är kompatibel med din befintliga:
  * Program
  * Åtgärder
  * Nätverk
  * Säkerhet
  * Säkerhetskopiera
  * Haveriberedskap
  * Granska
  * Verktyg för efterlevnad
  * Processer
* **Ingen omskolning:** Med VMware Platform-kompatibilitet kan du använda befintliga kunskaper och kunskaper.
* **Flexibilitet i infrastrukturen:** Du behöver inte längre förutsäga alla dina kapacitets behov och sedan få slut på utrymmes kapacitet eller infrastrukturs brist. CloudSimple levereras som en moln tjänst och du kan lägga till eller minska kapaciteten när som helst
* **Bullet** Åtkomst till CloudSimple-miljön via Azure innehåller inbyggt DDoS-skydd och säkerhets övervakning.
* **Lägre kostnad:** CloudSimple-plattformen är hög utformad och ger höga nivåer av automatisering, operationell effektivitet och stor drifts skala. Vidare publicerar CloudSimple lösnings arkitekturer som utnyttjar VMware i ett offentligt moln för att sänka kostnaderna. Exempel är Azure AD, backup till Azure Storage, Application Gateway, belastningsutjämnare och andra.
* **En ny hybrid plattform:** Tjänsten ger hög hastighet med låg latens åtkomst till resten av Azure. Dessutom möjliggör CloudSimple-hantering enhetlig hantering av VMware Virtual Machines och resten av Azure med samma användar gränssnitt och API. Dina utvecklings grupper kan dra nytta av både offentliga och privata plattformar på ett integrerat, konsekvent sätt.
* **Infrastruktur övervakning, fel sökning och support:** CloudSimple fungerar som en tjänst för den underliggande infrastrukturen. Felaktig maskin vara byts ut automatiskt. Du kan fokusera på förbrukning medan CloudSimple säkerställer att miljön fungerar smidigt.
* **Kompatibilitet för princip:** Behåll dina VMware-baserade verktyg, säkerhets procedurer, gransknings metoder och certifierings certifieringar.

## <a name="scenarios"></a>Scenarier

* **Data Center pension eller migrering:** Få ytterligare kapacitet när du når gränser för ditt befintliga data Center eller uppdatera maskin vara. Det är enkelt att lägga till nödvändig kapacitet i molnet och slippa att hantera maskin varu uppdateringar. Minska risken och kostnaden för migrering av moln jämfört med tids krävande konverteringar eller omarkitektur. Använd välkända VMware-verktyg och-kunskaper för att påskynda migreringen av molnet. Använd Azure-tjänster i molnet för att modernisera dina program i din takt.
* **Expandera på begäran:** Utöka molnet för att möta oväntade behov, till exempel nya utvecklings miljöer eller säsongs kapacitets burst. Du kan enkelt skapa en ny kapacitet på begäran och bara hålla den så länge du behöver den. Minska din första investering, påskynda etableringen och minska komplexiteten med samma arkitektur och principer i både lokalt och i molnet.
* **Haveri beredskap och virtuella skriv bord i Azure-molnet:** Upprätta fjärråtkomst till data, appar och skriv bord i Azure-molnet. Med anslutningar med hög bandbredd laddar du upp/ladda ned data snabbt för att återställa från incidenter. Nätverk med låg latens ger snabba svars tider som användarna förväntar sig från en Skriv bords app. Med CloudSimple är det enkelt att replikera alla principer och nätverk i molnet med hjälp av CloudSimple-portalen och välkända VMware-verktyg. Enkel återställning och replikering minskar kraftigt ansträngningen och risken för att skapa och hantera DR-och VDI-implementeringar.
* **Program och databaser med hög prestanda:** CloudSimple tillhandahåller en konvergerad arkitektur som är utformad för att köra dina mest krävande VMware-arbetsbelastningar. Kör Oracle, Microsoft SQL Server, mellanprogram och databaser med höga prestanda SQL. Upplev molnet som ditt eget Data Center med höghastighets nätverk på 25 Gbit/s som gör att du kan köra hybrid program som omfattar lokala, VMware på Azure och privata arbets belastningar i Azure utan att kompromissa med prestanda.
* **True hybrid:** Förena DevOps över VMware och Azure. Optimera VMware-administration för Azure-tjänster och-lösningar som kan tillämpas på alla dina arbets belastningar. Få åtkomst till offentliga moln tjänster utan att behöva expandera ditt data Center eller skapa en ny arkitektur för dina program. Centralisera identiteter, åtkomst kontroll principer, loggning och övervakning av VMware-program på Azure.

![Scenarier](media/cloudsimple-scenarios.png)

## <a name="next-steps"></a>Nästa steg

* [Skapa CloudSimple-tjänst](quickstart-create-cloudsimple-service.md)
* [Skapa privat moln](quickstart-create-private-cloud.md)