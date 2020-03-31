---
title: HSM fysisk säkerhet - Azure Dedikerad HSM | Microsoft-dokument
description: Information om Azure Dedicated HSM-enheters fysiska säkerhet i datacenter
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: mbaldwin
ms.openlocfilehash: ede1af4625d06af4e280eda86d09ae1db3dfdfd1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "70881027"
---
# <a name="azure-dedicated-hsm-physical-security"></a>Azure Dedikerad HSM fysisk säkerhet

Azure Dedicated HSM hjälper dig att uppfylla avancerade säkerhetskrav för nyckellagring. Den hanteras enligt stränga säkerhetsrutiner under hela livscykeln för att möta kundernas behov.

## <a name="security-through-procurement"></a>Säkerhet genom upphandling

Microsoft följer en säker upphandlingsprocess. Vi hanterar kedjan av vårdnad och se till att den specifika enheten beställt och levereras är den enhet som anländer till våra datacenter. Enheterna är i manipulering-händelse plast ryggar. De lagras i ett säkert lagringsutrymme tills de tas i drift i datagalleriet i datacentret.  Racken som innehåller HSM-enheterna anses vara hög påverkan på verksamheten(HBI). Enheterna är låsta och under videoövervakning hela tiden fram och bak.

## <a name="security-through-deployment"></a>Säkerhet genom distribution

HSM installeras i rack tillsammans med tillhörande nätverkskomponenter. En gång installerat måste de konfigureras innan de görs tillgängliga som en del av Azure Dedicated HSM-tjänsten. Den här konfigurationsaktiviteten utförs av Microsoft-anställda som har genomgått en bakgrundskontroll. "Just In Time" (JIT) administration används för att begränsa tillgången till endast rätt anställda och för endast den tid som tillgång behövs. De procedurer och system som används säkerställer också att all aktivitet som är relaterad till HSM-enheterna loggas.

## <a name="security-in-operations"></a>Säkerhet i verksamheten

HSM är maskinvaruapparater (den faktiska HSM är ett PCI-kort i apparaten) så det är möjligt att problem på komponentnivå kan uppstå. Potentiella problem inkluderar men är inte begränsade till fläkt- och strömförsörjningsfel. Den här typen av händelse kräver underhåll eller break/fix-aktiviteter för att ersätta alla utbytbara komponenter.

### <a name="component-replacement"></a>Byte av komponent

När en enhet har etablerats och under kundhantering är strömförsörjningen för drift under drift är den enda komponenter som skulle ersättas. Den här komponenten ligger utanför säkerhetsgränsen och orsakar inte en sabotagehändelse. Ett biljettsystem används för att ge en Microsoft-tekniker behörighet att komma åt baksidan av HBI-racket. När biljetten behandlas utfärdas en tillfällig fysisk nyckel. Den här nyckeln ger teknikern åtkomst till enheten och gör det möjligt för dem att byta den berörda komponenten. All annan åtkomst (det vill manipuleringshändelse som orsakar) skulle göras när en enhet inte allokeras till en kund, vilket minimerar säkerhets- och tillgänglighetsrisken.  

### <a name="device-replacement"></a>Byte av enhet

I händelse av totalt enhetsfel följs en process som liknar den som användes vid komponentfel. Om en kund inte kan nollställa enheten, eller om enheten är i ett okänt tillstånd, kommer datalagerenheterna att tas bort och placeras i en papperskorgen i rack. Enheter som placeras i papperskorgen kommer att förstöras på ett kontrollerat och säkert sätt. Inga datalagerenheter från ett HBI-rack kommer att lämna ett Microsoft-datacenter.

### <a name="other-rack-access-activities"></a>Andra rackåtkomstaktiviteter

Om en Microsoft-tekniker måste komma åt det rack som används av HSM-enheter (t.ex. underhåll av nätverksenheter) används standardsäkerhetsprocedurer för att få åtkomst till det HBI-säkra rack. All åtkomst kommer att ske under videoövervakning. HSM-enheterna valideras till [FIPS 140-2 Nivå 3](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.140-2.pdf) så all obehörig åtkomst till HSM-enheterna kommer att signaleras till kunden och data nollställs.

## <a name="logical-level-security-considerations"></a>Säkerhetsöverväganden på logisk nivå

HSM:er etableras i ett virtuellt nätverk som skapats av kunden. Det här är en kunds privata IUP-adressutrymme.  Den här konfigurationen ger en värdefull logisk isolering på nätverksnivå och säkerställer åtkomst endast av kunden. Detta innebär att alla logiska säkerhetskontroller är kundens ansvar.

## <a name="next-steps"></a>Nästa steg

Vi rekommenderar att alla viktiga begrepp för tjänsten, till exempel hög tillgänglighet och säkerhet och support, förstås väl före etablering av enheter, programdesign eller distribution.

* [Hög tillgänglighet](high-availability.md)
* [Nätverk](networking.md)
* [Support](supportability.md)
* [Övervakning](monitoring.md)
* [Distributionsarkitektur](deployment-architecture.md)