---
title: Övervakningsalternativ - Azure Dedikerad HSM | Microsoft-dokument
description: Översikt över Azure Dedikerad HSM-övervakningsalternativ och övervakningsansvar
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "70881415"
---
# <a name="azure-dedicated-hsm-monitoring"></a>Azure Dedikerad HSM-övervakning

Azure Dedicated HSM-tjänsten tillhandahåller en fysisk enhet för ensam kundanvändning med fullständigt administrativt kontroll- och hanteringsansvar. Enheten som görs tillgänglig är en [Gemalto SafeNet Luna 7 HSM modell A790](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/).  Microsoft har ingen administrativ åtkomst när den har etablerats av en kund, utöver den fysiska bifogade seriella portar som en övervakningsroll. Som ett resultat av detta ansvarar kunderna för typiska operativa aktiviteter, inklusive omfattande övervakning och logganalys.
Kunderna är fullt ansvariga för applikationer som använder HSM och bör arbeta med Gemalto för support eller konsulthjälp. På grund av omfattningen av kundägandet av drifthygien är det inte möjligt för Microsoft att erbjuda någon form av hög tillgänglighetsgaranti för den här tjänsten. Det är kundens ansvar att se till att deras program är korrekt konfigurerade för att uppnå hög tillgänglighet. Microsoft övervakar och underhåller enhetens hälsa och nätverksanslutning.

## <a name="microsoft-monitoring"></a>Microsoft-övervakning

Gemalto SafeNet-enheten som används har som standard SNMP och seriell port som alternativ för övervakning av enheten. Microsoft har använt den seriella portanslutningen som ett fysiskt sätt att ansluta till enheten för att hämta grundläggande telemetri på enhetens hälsa. Detta inkluderar objekt som temperatur och komponentstatus som nätaggregat och fläktar.
För att uppnå detta använder Microsoft en icke-administrativ "övervakare"-roll som konfigurerats på Gemalto-enheten. Den här rollen ger möjlighet att hämta telemetrin men ger inte någon åtkomst till enheten när det gäller administrativ uppgift eller på något sätt visa kryptografisk information. Våra kunder kan vara säkra på att deras enhet verkligen är deras egen att hantera, administrera och använda för känslig kryptografisk nyckellagring. Om någon kund inte är nöjd med denna minimala åtkomst för grundläggande hälsoövervakning, har de möjlighet att inaktivera övervakningskontot. Den uppenbara konsekvensen av detta är att Microsoft inte kommer att ha någon information och därmed ingen möjlighet att ge någon proaktiv anmälan av enhetens hälsoproblem. I det här fallet är kunden ansvarig för enhetens hälsa.
Själva monitorfunktionen är inställd på att avsöka enheten var 10:e minut för att hämta hälsodata. På grund av felbenägna karaktär seriell kommunikation, först efter flera negativa hälsoindikatorer under en timme skulle en varning höjas. Den här aviseringen skulle i slutändan leda till en proaktiv kundkommunikation som meddelar problemet.
Beroende på frågans art skulle lämpliga åtgärder vidtas för att minska effekterna och säkerställa en låg risksanering. Ett strömförsörjningsfel är till exempel en hot-swap-procedur utan resulterande manipuleringshändelse så kan utföras med låg påverkan och minimal risk för drift. Andra procedurer kan kräva att en enhet nollställs och avetableras för att minimera eventuella säkerhetsrisker för kunden. I det här fallet en kund skulle etablera en alternativ enhet, återansluta en hög tillgänglighet ihopkoppling vilket utlöser enhetssynkronisering. Normal drift skulle återupptas på minimal tid, med minimala störningar och lägsta säkerhetsrisk.  

## <a name="customer-monitoring"></a>Kundövervakning

Ett värdeerbjudande för den dedikerade HSM-tjänsten är den kontroll som kunden får av enheten, särskilt med tanke på att det är en molnlevererad enhet. En konsekvens av denna kontroll är ansvaret att övervaka och hantera enhetens hälsa. Gemalto SafeNet-enheten levereras med vägledning för SNMP- och Syslog-implementering. Kunder i den dedikerade HSM-tjänsten rekommenderas att använda detta även när Microsofts övervakarkonto förblir aktivt och bör anse det obligatoriskt om de inaktiverar Microsofts övervakarkonto.
Antingen tillgänglig teknik gör det möjligt för en kund att identifiera problem och ringa Microsoft-support för att initiera lämpligt reparationsarbete.

## <a name="next-steps"></a>Nästa steg

Vi rekommenderar att alla viktiga begrepp för tjänsten, till exempel hög tillgänglighet och säkerhet, är väl förstådda innan någon enhetsetablering och programdesign eller distribution. Ytterligare begreppsnivå ämnen:

* [Hög tillgänglighet](high-availability.md)
* [Fysisk säkerhet](physical-security.md)
* [Nätverk](networking.md)
* [Support](supportability.md)
