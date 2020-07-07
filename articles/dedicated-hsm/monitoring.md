---
title: Övervaknings alternativ – Azure Dedicated HSM | Microsoft Docs
description: Översikt över Azure Dedicated HSM-övervaknings alternativ och övervaknings ansvars områden
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
ms.openlocfilehash: 3fde577a6b0efb7584e1c9efd57c95583ebe4ec9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "70881415"
---
# <a name="azure-dedicated-hsm-monitoring"></a>Dedikerad HSM-övervakning i Azure

Azure Dedicated HSM-tjänsten tillhandahåller en fysisk enhet för den enda kund användningen med fullständig administrativ kontroll och hanterings ansvar. Den tillgängliga enheten är en [Gemalto SafeNet Luna 7 HSM-modell A790](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/).  Microsoft kommer inte att ha någon administrativ åtkomst efter att ha tillhandahållit en kund, utöver den fysiska seriella port bilagan som en övervaknings roll. Därför är kunderna ansvariga för vanliga operativa aktiviteter, inklusive omfattande övervakning och logg analys.
Kunder är fullt ansvariga för program som använder HSM: er och bör arbeta med Gemalto för support eller konsult hjälp. På grund av omfattningen av kundens ägande av drifts hygien är det inte möjligt för Microsoft att erbjuda någon typ av garanti för hög tillgänglighet för den här tjänsten. Det är kundens ansvar att se till att deras program är korrekt konfigurerade för att uppnå hög tillgänglighet. Microsoft övervakar och underhåller enhetens hälso tillstånd och nätverks anslutning.

## <a name="microsoft-monitoring"></a>Microsoft-övervakning

Gemalto SafeNet-enheten som används har som standard SNMP och seriell port som alternativ för övervakning av enheten. Microsoft har använt seriell port anslutning som ett fysiskt sätt att ansluta till enheten för att hämta grundläggande telemetri om enhetens hälso tillstånd. Detta inkluderar objekt som temperatur och komponent status som strömförsörjning och fläktar.
För att uppnå detta använder Microsoft en icke-administrativ övervaknings roll som kon figurer ATS på Gemalto-enheten. Den här rollen ger möjlighet att hämta Telemetrin men ger ingen åtkomst till enheten vad gäller administrativ uppgift eller på något sätt som visar kryptografisk information. Våra kunder kan vara säkra på att deras enheter är egna för att hantera, administrera och använda för känslig kryptografisk nyckel lagring. Om kunden inte är nöjd med den här minimala åtkomsten för grundläggande hälso övervakning har de möjlighet att inaktivera övervaknings kontot. Den uppenbara konsekvensen av detta är att Microsoft inte kommer att ha någon information och därför inte möjlighet att ge någon proaktiv avisering om enhetens hälso problem. I den här situationen ansvarar kunden för enhetens hälso tillstånd.
Själva övervaknings funktionen har kon figurer ATS för att avsöka enheten var tionde minut för att hämta hälso data. På grund av ett fel som är känsligt för seriell kommunikation kan det uppstå en avisering om flera negativa hälso indikatorer under en timmes period. Den här aviseringen skulle leda till en proaktiv kund kommunikation som meddelar problemet.
Beroende på problemets beskaffenhet skulle lämplig åtgärd vidtas för att minska påverkan och säkerställa en låg risk åtgärd. Ett strömförsörjnings haveri är till exempel en frekvent metod som inte har någon resulterande ändrings händelse, vilket kan utföras med låg påverkan och minimal risk för drift. Andra procedurer kan kräva att en enhet nollställs och avetableras för att minimera säkerhets risken för kunden. I det här fallet skulle en kund etablera en alternativ enhet och återansluta till ett par med hög tillgänglighet, vilket innebär att synkroniseringen av enheten aktive ras. Normal drift återupptas i minimal tid, med minimal störning och låg säkerhets risk.  

## <a name="customer-monitoring"></a>Kund övervakning

Ett värde för dedikerad HSM-tjänst är den kontroll som kunden får av enheten, särskilt när den är en moln levererad enhet. En konsekvens av den här kontrollen är ansvaret för att övervaka och hantera enhetens hälso tillstånd. Gemalto SafeNet-enheten levereras med vägledning för SNMP-och syslog-implementering. Kunder med dedikerad HSM-tjänst rekommenderas att använda detta även när Microsoft Monitor-kontot är aktivt och bör överväga det om de inaktiverar Microsoft Monitor-kontot.
Med den tillgängliga tekniken kan en kund identifiera problem och kontakta Microsoft Support för att initiera lämpligt reparations arbete.

## <a name="next-steps"></a>Nästa steg

Vi rekommenderar att alla viktiga begrepp för tjänsten, till exempel hög tillgänglighet och säkerhet till exempel, är bra förstå före eventuell enhets etablering och program design eller distribution. Avsnitt om ytterligare begrepps nivå:

* [Hög tillgänglighet](high-availability.md)
* [Fysisk säkerhet](physical-security.md)
* [Nätverk](networking.md)
* [Support](supportability.md)
