---
title: HSM fysisk säkerhet – Azure dedikerad HSM | Microsoft Docs
description: Information om Azure dedikerad HSM enheters fysisk säkerhet i Datacenter
services: dedicated-hsm
author: barclayn
manager: mbaldwin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: barclayn
ms.openlocfilehash: 662800d2b9db7d7a19861431354e55b3897e020a
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53080052"
---
# <a name="azure-dedicated-hsm-physical-security"></a>Azure dedikerad HSM fysisk säkerhet

Azure dedikerad HSM hjälper dig att uppfylla kraven för avancerad säkerhet för lagring av nycklar. Det är hanterade strikta dessa säkerhetsmetoder under hela livscykeln fullständig att uppfylla kundernas behov.

## <a name="security-through-procurement"></a>Säkerhet via inköp

Microsoft följer en säker inköpsprocessen. Vi hanterar kedja för förvaring och se till att enheten sorterade och levereras enheten anländer till våra datacenter. Enheter är i förvanskningstålig händelse form baksidor. De lagras i en säker lagringsplats tills uppdrag i galleriet data i datacentret.  Rack som innehåller HSM-enheter anses vara viktiga för verksamheten impact(HBI). Enheterna är låsta och under video övervakning alltid fram och tillbaka.

## <a name="security-through-deployment"></a>Säkerhet via distribution

HSM: er har installerats i rack tillsammans med tillhörande nätverkskomponenter. När du har installerat de måste konfigureras innan de görs tillgängliga som en del av tjänsten Azure dedikerad HSM. Den här konfigurationen aktiviteten utförs av Microsoft-anställda som har genomgått en bakgrundskontroll. Just-In-Time ”(JIT) administration används för att begränsa åtkomsten till endast de rätta anställda och för den tid som behövs för åtkomst. Metoderna och system som används också se till att alla aktiviteter som rör HSM-enheter är inloggad.

## <a name="security-in-operations"></a>Säkerhet i operations

HSM-modulerna är maskinvaruinstallationer (den faktiska HSM är PCI-kort i installationen) så det är möjligt att komponenten på problem kan uppstå. Potentiella problem inkludera, men är inte begränsad till fläkt och power tillgångs-fel. Den här typen av händelse kräver underhåll eller webbsupport aktiviteter att ersätta alla komponenter som kan.

### <a name="component-replacement"></a>Utbyte av komponenter

När en enhet har etablerats och hanteras av kunden, är växlas strömförsörjning endast komponenterna som skulle ersättas. Den här komponenten utanför säkerhetsgränsen och orsakar inte en förvanskningstålig-händelse. Ett biljettsystem används för att auktorisera en Microsoft-tekniker för att komma åt HBI-rack bakom. När biljetten har bearbetats utfärdas en tillfällig fysiska nyckel. Den här nyckeln ger engineer åtkomst till enheten och låter dem att byta komponenten som påverkas. Andra åtkomst (det vill säga säkerhetsförsluten och orsakar händelsen) utförs när en enhet inte har allokerats till en kund som därför minimera risken för säkerhet och tillgänglighet.  

### <a name="device-replacement"></a>Ersättning av enheten

I händelse av enhetens totala fel följs en process som liknar den som används under komponentfel. Om en kund kan inte zeroize enheten, eller om enheten är i ett okänt tillstånd, tas data med enheter bort och placeras i en lagerplats i rack destruktion. Enheter som placeras i bin kommer att raderas på ett kontrollerat och säkert sätt. Inga data med enheter från ett HBI rack lämnar ett Microsoft-datacenter.

### <a name="other-rack-access-activities"></a>Andra Rack åtkomst-aktiviteter

Om en Microsoft-tekniker måste komma åt rack som används av HSM-enheter (till exempel nätverk enhetsunderhåll), används Standardsäkerhet procedurer för att få åtkomst till HBI säker rack. All åtkomst kommer att under video övervakning. HSM-enheter verifieras att [FIPS 140-2 nivå 3](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.140-2.pdf) så signaleras otillåten åtkomst till HSM-enheter till kunden och data kommer att zeroized.

## <a name="logical-level-security-considerations"></a>Logiska nivå säkerhetsöverväganden

HSM: er som tilldelas ett virtuellt nätverk som skapats av kunden. Det här är en kunds privata IUP adressutrymme.  Den här konfigurationen ger en nivå isolering av värdefulla logiska nätverk och säkerställer att åtkomst endast av kunden. Detta innebär att alla logiska nivå säkerhetskontroller är ansvar för kunden.

## <a name="next-steps"></a>Nästa steg

Vi rekommenderar att alla nyckelbegreppen för tjänsten, till exempel hög tillgänglighet och säkerhet och support exempelvis är väl förstådda innan enhetsetablering, programmets design eller distribution.

* [Hög tillgänglighet](high-availability.md)
* [Nätverk](networking.md)
* [Support](supportability.md)
* [Övervakning](monitoring.md)
* [Distributionsarkitektur för](deployment-architecture.md)