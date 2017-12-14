---
title: Replikering i Azure Storage | Microsoft Docs
description: "Data i ditt Microsoft Azure Storage-konto replikeras för hållbarhet och hög tillgänglighet. Replikeringsalternativ är lokalt redundant lagring (LRS), zonredundant lagring (ZRS), geo-redundant lagring (GRS) och geo-redundant lagring med läsbehörighet (RA-GRS)."
services: storage
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 86bdb6d4-da59-4337-8375-2527b6bdf73f
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: tamram
ms.openlocfilehash: 45883d59e5fe9ab2b7a09bfdc6c11a681bd43d0b
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="azure-storage-replication"></a>Azure Storage-replikering
Data i ditt Microsoft Azure-lagringskonto replikeras alltid för att säkerställa hållbarhet och hög tillgänglighet. Replikeringen kopierar dina data inom samma datacenter eller till en andra datacenter, beroende på vilket replikeringsalternativ du väljer. Replikering skyddar dina data och bevarar dina programs drifttid vid tillfälliga maskinvarufel. Om dina data replikeras till andra datacenter, skyddas den från ett oåterkalleligt fel på den primära platsen.

Replikeringen garanterar att ditt lagringskonto uppfyller [Servicenivåavtal (SLA) för lagring](https://azure.microsoft.com/support/legal/sla/storage/) även vid fel. Visa SLA för information om Azure Storage-garantier för hållbarhet och tillgänglighet.

När du skapar ett lagringskonto kan du välja något av följande replikeringsalternativ:

* [Lokalt redundant lagring (LRS)](#locally-redundant-storage)
* [Zonredundant lagring (ZRS)](#zone-redundant-storage)
* [Geo-redundant lagring (GRS)](#geo-redundant-storage)
* [Geo-redundant lagring med läsbehörighet (RA-GRS)](#read-access-geo-redundant-storage)

Geo-redundant lagring med läsbehörighet (RA-GRS) är standardalternativet när du skapar ett lagringskonto.

Följande tabell ger en snabb överblick över skillnaderna mellan LRS-, ZRS-, GRS- och RA-GRS medan efterföljande avsnitt åtgärda varje typ av replikering i detalj.

| Replikeringsstrategi | LRS | ZRS | GRS | RA-GRS |
|:--- |:--- |:--- |:--- |:--- |
| Data replikeras mellan flera datacenter. |Nej |Ja |Ja |Ja |
| Data kan läsas från en sekundär plats som den primära platsen. |Nej |Nej |Nej |Ja |
| Utformad för att ge ___ hållbarhet objekt under ett visst år. |minst 99.999999999% (11 9's)|minst 99.9999999999% (12 9's)|minst 99.99999999999999% (16 9's)|minst 99.99999999999999% (16 9's)|

Se [priser för Azure Storage](https://azure.microsoft.com/pricing/details/storage/) för prisuppgifter för olika redundansalternativ.

> [!NOTE]
> Premium-lagring stöder endast lokalt redundant lagring (LRS). Information om Premium-lagring finns [Premium-lagring: högpresterande lagring för arbetsbelastningar på virtuella Azure](../../virtual-machines/windows/premium-storage.md).
>

## <a name="locally-redundant-storage"></a>Lokalt redundant lagring
[!INCLUDE [storage-common-redundancy-LRS](../../../includes/storage-common-redundancy-LRS.md)]

## <a name="zone-redundant-storage"></a>Zonredundant lagring
Zonredundant lagring (ZRS) är utformad att ge minst 99.9999999999% (12 9's) hållbarhet objekt under ett visst år genom att replikera data asynkront i datacenter i en eller två regioner, vilket ger en högre hållbarhet än LRS. Data som lagras i ZRS är beständig även om det primära datacentret är inte tillgänglig eller oåterkalleligt.
Kunder som planerar att använda ZRS bör vara medveten om som:

* ZRS är endast tillgängligt för blockblobbar i allmänna lagringskonton och fungerar bara i storage-tjänstversioner 2014-02-14 och senare.
* Eftersom asynkron replikering innefattar en fördröjning, händelse av en lokal katastrofåterställning är det möjligt att ändringar som ännu inte har replikerats till sekundärt kommer att förloras om data inte kan återställas från den primära servern.
* Repliken kanske inte tillgänglig förrän Microsoft initierar redundans för sekundärt.
* ZRS konton kan inte konverteras till LRS eller GRS senare. Ett befintligt LRS eller GRS-konto kan inte konverteras till ett ZRS-konto.
* ZRS konton har inte mått eller loggningsfunktioner.

## <a name="geo-redundant-storage"></a>Geografiskt redundant lagring.
[!INCLUDE [storage-common-redundancy-GRS](../../../includes/storage-common-redundancy-GRS.md)]

## <a name="read-access-geo-redundant-storage"></a>Geo-redundant lagring med läsbehörighet
Geo-redundant lagring med läsbehörighet (RA-GRS) maximerar tillgänglighet för ditt lagringskonto genom att ange skrivskyddad åtkomst till data i den sekundära platsen, förutom replikering mellan två regioner som tillhandahålls av GRS.

När du aktiverar skrivskyddad åtkomst till dina data i den sekundära regionen är dina data tillgängliga på en sekundär slutpunkt, utöver primära slutpunkten för ditt lagringskonto. Sekundär slutpunkt liknar primära slutpunkten men lägger till suffixet `–secondary` i kontonamnet. Om din primära slutpunkt för Blob-tjänsten är till exempel `myaccount.blob.core.windows.net`, sekundära slutpunkten är `myaccount-secondary.blob.core.windows.net`. Snabbtangenter för ditt lagringskonto är samma för både de primära och sekundära slutpunkterna.

Att tänka på:

* Programmet måste hantera vilken slutpunkt den interagerar med när du använder RA-GRS.
* Eftersom asynkron replikering innefattar en fördröjning, händelse regionala katastrofåterställning är det möjligt att ändringar som ännu inte har replikerats till den sekundära regionen kommer att förloras om data inte kan återställas från den primära regionen.
* Om Microsoft initierar redundans till den sekundära regionen kan du kommer har läs- och skrivbehörighet till dessa data efter växlingen har slutförts. Mer information finns [Disaster Recovery vägledning](../storage-disaster-recovery-guidance.md).
* RA-GRS är avsedd för hög tillgänglighet. Riktlinjer för skalbarhet, granska den [prestanda checklista](../storage-performance-checklist.md).

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

<a id="howtochange"></a>
#### <a name="1-how-can-i-change-the-geo-replication-type-of-my-storage-account"></a>1. Hur kan jag ändra typ för geo-replikering för mitt lagringskonto?

   Du kan ändra typ för geo-replikering för ditt lagringskonto mellan LRS-, GRS- och RA-GRS med hjälp av den [Azure-portalen](https://portal.azure.com/), [Azure Powershell](storage-powershell-guide-full.md) eller programmässigt med hjälp av en av våra många Storage-klientbibliotek .
Observera att ZRS-konton inte får vara konverterade LRS eller GRS. Ett befintligt LRS eller GRS-konto kan inte konverteras till ett ZRS-konto.

<a id="changedowntime"></a>
#### <a name="2-will-there-be-any-down-time-if-i-change-the-replication-type-of-my-storage-account"></a>2. Kommer det att finnas någon stillestånd om jag ändrar replikeringstyp för mitt lagringskonto?

   Nej, det finns inte någon stillestånd.

<a id="changecost"></a>
#### <a name="3-will-there-be-any-additional-cost-if-i-change-the-replication-type-of-my-storage-account"></a>3. Kommer det att finnas någon extra kostnad ändrar replikeringstyp för mitt lagringskonto?

   Ja. Om du ändrar från LRS till GRS eller RA-GRS för ditt lagringskonto, skulle det innebära en extra kostnad för utgående trafik som ingår i kopiera befintliga data från primär plats till den sekundära platsen. När den första informationen kopieras är kostnadsfri ytterligare ytterligare utgång för geo-replikering av data från primär till sekundär plats. Information om bandbredd avgifter kan hittas på den [priser för Azure Storage sidan](https://azure.microsoft.com/pricing/details/storage/blobs/).
Om du ändrar från GRS till LRS finns utan extra kostnad, men data tas bort från den sekundära platsen.

<a id="ragrsbenefits"></a>
#### <a name="4-how-can-ra-grs-help-me"></a>4. Hur kan RA-GRS hjälpa mig?

   GRS storage tillhandahåller replikering av data från en primär till en sekundär region hundratals mil bort från den primära regionen är. Dina data skyddas i sådant fall även vid en fullständig regionalt strömavbrott eller en katastrof där den primära regionen inte kan återställas. RA-GRS lagring inkluderar den här plus den lägger till möjligheten att läsa data från den sekundära platsen. Några tips om hur man utnyttjar denna möjlighet finns [skapar hög tillgängliga program använder RA-GRS lagring](../storage-designing-ha-apps-with-ragrs.md).

<a id="lastsynctime"></a>
#### <a name="5-is-there-a-way-for-me-to-figure-out-how-long-it-takes-to-replicate-my-data-from-the-primary-to-the-secondary-region"></a>5. Finns det ett sätt för att ta reda på hur lång tid det tar för att replikera data från primärt till den sekundära regionen?

   Om du använder RA-GRS-lagring kan kontrollera du tid för senaste synkronisering för ditt lagringskonto. Tid för senaste synkronisering är ett GMT datum/tid-värde. alla primära skrivningar innan tid för senaste synkronisering har skrivits till den sekundära platsen, vilket innebär att de är tillgängliga för att läsa från den sekundära platsen. Primär skriver när tid för senaste synkronisering kanske eller kanske inte tillgänglig för läsningar ännu. Du kan fråga efter detta värde med hjälp av den [Azure-portalen](https://portal.azure.com/), [Azure PowerShell](storage-powershell-guide-full.md), eller programmässigt med hjälp av REST-API: et eller någon av Lagringsklientbiblioteken.

<a id="outage"></a>
#### <a name="6-how-can-i-switch-to-the-secondary-region-if-there-is-an-outage-in-the-primary-region"></a>6. Hur kan jag växla till den sekundära regionen om det finns ett avbrott i den primära regionen?

   Mer information finns i artikeln [vad du ska göra om ett Azure Storage-avbrott inträffar](../storage-disaster-recovery-guidance.md) för mer information.

<a id="rpo-rto"></a>
#### <a name="7-what-is-the-rpo-and-rto-with-grs"></a>7. Vad är Återställningspunktmål och Återställningstidsmål med GRS?

   Återställningspunktmål för återställningspunkt (RPO): I GRS och RA-GRS lagring tjänsten asynkront geo replikerar data från primär till sekundär plats. Om det finns en större katastrof för regional och en växling vid fel måste utföras kan sedan senaste deltaändringar som inte har georeplikerad gå förlorade. Antal minuter potentiella data förlorade kallas Återställningspunktmålet (vilket innebär punkten i tiden som data kan återställas). Vi har vanligtvis ett Återställningpunktsmål mindre än 15 minuter, även om det finns för närvarande inga SLA på hur länge geo-replikering tar.

   Återställning tid mål för Återställningstid: Detta är ett mått på hur lång tid det tar vi göra växling vid fel och hämta storage-konto online igen om vi behöver göra en växling vid fel. Dags för växling vid fel omfattar följande:
    * Den tid det tar att undersöka och avgöra om vi kan återställa data på den primära platsen eller om vi behöver göra en växling vid fel.
    * Redundans kontot genom att ändra de primära DNS-posterna så att den pekar till den sekundära platsen.

   Vi tar ansvar bevara data allvar, så om det finns några risken för återställning av data, kommer vi håller på gör växling vid fel och fokusera på att återställa data på den primära platsen. I framtiden kommer vi planerar att tillhandahåller ett API så att du kan utlösa en växling vid fel på en kontonivå som skulle sedan kan du styra Återställningstidsmålet själv, men detta är inte tillgängligt ännu.

## <a name="next-steps"></a>Nästa steg
* [Utforma högtillgänglig program som använder RA-GRS-lagring](../storage-designing-ha-apps-with-ragrs.md)
* [Prissättning för Azure Storage](https://azure.microsoft.com/pricing/details/storage/)
* [Om Azure storage-konton](../storage-create-storage-account.md)
* [Skalbarhets- och prestandamål i Azure Storage](storage-scalability-targets.md)
* [Microsoft Azure Storage redundans alternativ och läsbehörighet geo-redundant lagring](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)
* [SOSP Paper - Azure Storage: En högtillgänglig lagring molntjänst med stark konsekvens](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)
