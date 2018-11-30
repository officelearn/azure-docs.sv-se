---
title: Azure dedikerad HSM övervakning | Microsoft Docs
description: Azure dedikerade HSM tillhandahåller funktioner för lagring av nycklar i Azure som uppfyller FIPS 140-2 Level 3-certifiering
services: dedicated-hsm
author: barclayn
manager: mbaldwin
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/20/2018
ms.author: barclayn
ms.openlocfilehash: 4e2089c62da5fec1e1cfe96ce27b363cfb35eadf
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2018
ms.locfileid: "52319577"
---
# <a name="azure-dedicated-hsm-monitoring"></a>Azure dedikerad HSM-övervakning

Tjänsten Azure dedikerad HSM ger en fysisk enhet för användning efter kund fullständig administrativ kontroll och hantering av ansvar. Enheten som gjorts tillgängliga är en [Gemalto SafeNet Luna 7 HSM modellen A790](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/).  Microsoft kommer inte ha administrativ åtkomst när etablerats av en kund, utöver fysiska serieport bifogad fil som en övervakning roll. Därför kan kunder ansvarar för vanliga driftaktiviteter inklusive omfattande övervakning och logga analys.
Kunderna ansvarar helt för program som använder HSM: erna och ska fungera med Gemalto för support eller konsult hjälp. På grund av omfattningen av kunden ägarskapet för operativa hygien är det inte möjligt för Microsoft att erbjuda alla slags garanti för hög tillgänglighet för den här tjänsten. Det är kundens ansvar att se till att programmen är korrekt konfigurerade för att uppnå hög tillgänglighet. Microsoft kommer att övervaka och underhålla enhetens hälsotillstånd och nätverksanslutningen.

## <a name="microsoft-monitoring"></a>Övervakning av Microsoft

Gemalto SafeNet enheten används har som standard SNMP och seriell port som alternativ för övervakning av enheten. Microsoft har använt serieport anslutningen som en fysisk innebär att ansluta till enheten för att hämta grundläggande telemetri på enhetens hälsotillstånd. Detta inkluderar objekt, till exempel temperatur och komponenten status, till exempel strömförsörjning och fläktar.
Microsoft använder en icke-administrativa ”övervaka” roll på Gemalto enheten för att uppnå detta. Den här rollen ger möjlighet att hämta telemetri, men ger inte någon åtkomst till enheten när det gäller administrativ åtgärd eller på något sätt visa kryptografiska information. Kunderna kan lita på enheten är helt egna för att hantera, administrera och använda för känsliga kryptografiska nyckellagring. Om vilken kund som inte är nöjd med den minimala åtkomsten för grundläggande hälsoövervakning har de alternativet att inaktivera kontot för övervakning. En självklar följd av detta är att Microsoft har ingen information och kan därför ingen möjlighet att tillhandahålla några Proaktiva meddelanden om enhetens hälsotillstånd utfärdar. I så fall kan ansvarar kunden för hälsotillståndet för enheten.
Funktionen övervakaren har ställts in att avsöka enheten var tionde minut för att hämta hälsotillståndsdata. På grund av fel felbenägna beskaffenhet seriell kommunikation, bara när du har flera negativt hälsoindikatorer över en timme skulle en varning att utfärdas. Den här aviseringen skulle slutligen leda till en proaktiv kundkommunikationen meddela problemet.
Beroende på hur allvarligt problemet, skulle vilka lämpliga åtgärder vidtas för att minska inverkan och se till att lågrisk reparation. En STRÖMFÖRSÖRJNINGSFEL är till exempel en byte-procedur med inga gällande Förvanska händelse så kan utföras med låg inverkan och minimal risk för åtgärden. Andra procedurer kan kräva en enhet ska vara zeroized och tagit bort etableringen för att minimera eventuella säkerhetsrisk för kunden. I så fall skulle en kund etablera en annan enhet, återansluta till en hög tillgänglighet parkoppling därför utlösa synkronisering av enheter. Normal drift skulle återupptas på minimal tid med minimalt avbrott och lägsta säkerhetsrisk.  

## <a name="customer-monitoring"></a>Kunden övervakning

En förslagsvärde dedikerad HSM-tjänst är kontrollen kunden hämtar av enheten, särskilt funderar på att det är ett moln som levereras av enheten. En följd av den här kontrollen är ansvar för att övervaka och hantera hälsotillståndet för enheten. Gemalto SafeNet enheten levereras med vägledning för implementering av SNMP- och Syslog. Dedikerad HSM-tjänst-kunder rekommenderas att använda detta även om Microsoft-övervakningskontot förblir aktiv och bör det obligatoriska om de inaktiverar övervaka Microsoft-kontot.
Antingen tekniken skulle kan en kund att identifiera problem och kontakta Microsoft-supporten om du vill initiera lämpliga avhjälpande arbete.

## <a name="next-steps"></a>Nästa steg

Vi rekommenderar att alla nyckelbegreppen för tjänst, till exempel hög tillgänglighet och säkerhet till exempel är väl förstådda innan några enhetsetablering och programmets design eller distribution. Ytterligare konceptet på ämnen:

* [Hög tillgänglighet](high-availability.md)
* [Fysisk säkerhet](physical-security.md)
* [Nätverk](networking.md)
* [Support](supportability.md)
