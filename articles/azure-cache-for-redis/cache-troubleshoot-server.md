---
title: Felsöka problem på Azure Cache for Redis-serversidan
description: Lär dig hur du löser vanliga problem på serversidan med Azure Cache för Redis, till exempel minnestryck, hög CPU, kommandon för tidskrävande drift eller bandbreddsbegränsningar.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/18/2019
ms.openlocfilehash: a68c27de304a0da6470745ee4abf69590d9bf78c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277939"
---
# <a name="troubleshoot-azure-cache-for-redis-server-side-issues"></a>Felsöka problem på Azure Cache for Redis-serversidan

I det här avsnittet beskrivs felsökningsproblem som uppstår på grund av ett villkor på en Azure-cache för Redis eller den virtuella datorn som är värd för den.

- [Minnestryck på Redis-server](#memory-pressure-on-redis-server)
- [Hög CPU-användning eller serverbelastning](#high-cpu-usage-or-server-load)
- [Tidskrävande kommandon](#long-running-commands)
- [Bandbreddsbegränsning på serversidan](#server-side-bandwidth-limitation)

> [!NOTE]
> Flera av felsökningsstegen i den här guiden innehåller instruktioner för att köra Redis-kommandon och övervaka olika prestandamått. Mer information och instruktioner finns i artiklarna i avsnittet [Ytterligare information.](#additional-information)
>

## <a name="memory-pressure-on-redis-server"></a>Minnestryck på Redis-server

Minnestryck på serversidan leder till alla typer av prestandaproblem som kan fördröja bearbetningen av begäranden. När minnestrycket träffar kan systemet söka data till disk. Den här _sidan fel_ orsakar systemet att sakta ner avsevärt. Det finns flera möjliga orsaker till detta minnestryck:

- Cachen fylls med data nära sin maximala kapacitet.
- Redis ser hög minnesfragmentering. Denna fragmentering orsakas oftast av lagring av stora objekt eftersom Redis är optimerad för små objekt.

Redis exponerar två statistik via [info-kommandot](https://redis.io/commands/info) som kan hjälpa dig att identifiera problemet: "used_memory" och "used_memory_rss". Du kan [visa dessa mått](cache-how-to-monitor.md#view-metrics-with-azure-monitor) med hjälp av portalen.

Det finns flera möjliga ändringar du kan göra för att hålla minnesanvändningen felfri:

- [Konfigurera en minnesprincip](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) och ange förfallotider på dina nycklar. Denna politik kanske inte är tillräcklig om du har fragmentering.
- [Konfigurera ett maxmemory-reserverat värde](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) som är tillräckligt stort för att kompensera för minnesfragmentering.
- Dela upp dina stora cachelagrade objekt i mindre relaterade objekt.
- [Skapa aviseringar](cache-how-to-monitor.md#alerts) om mått som använt minne som ska meddelas tidigt om potentiella effekter.
- [Skala](cache-how-to-scale.md) till en större cachestorlek med mer minneskapacitet.

## <a name="high-cpu-usage-or-server-load"></a>Hög CPU-användning eller serverbelastning

En hög serverbelastning eller CPU-användning innebär att servern inte kan bearbeta begäranden i tid. Servern kan vara långsam att svara och inte hålla jämna steg med begäran priser.

[Övervaka mått](cache-how-to-monitor.md#view-metrics-with-azure-monitor) som CPU eller serverbelastning. Titta efter toppar i CPU-användning som motsvarar timeout.

Det finns flera ändringar du kan göra för att minska hög serverbelastning:

- Undersök vad som orsakar [CPU-toppar](#long-running-commands) som långvariga kommandon som anges nedan eller sida fel på grund av högt minnestryck.
- [Skapa aviseringar](cache-how-to-monitor.md#alerts) om mått som CPU eller serverbelastning som ska meddelas tidigt om potentiella effekter.
- [Skala](cache-how-to-scale.md) till en större cachestorlek med mer CPU-kapacitet.

## <a name="long-running-commands"></a>Tidskrävande kommandon

Vissa Redis-kommandon är dyrare att utföra än andra. Dokumentationen [för Redis-kommandon](https://redis.io/commands) visar tidskomplexiteten för varje kommando. Eftersom Redis kommandobearbetning är entrådig, blockerar ett kommando som tar tid att köra alla andra som kommer efter den. Du bör granska de kommandon som du utfärdar till Redis-servern för att förstå deras prestandaeffekter. Kommandot [NYCKLAR](https://redis.io/commands/keys) används till exempel ofta utan att veta att det är en O(N)-åtgärd. Du kan undvika NYCKLAR genom att använda [SCAN](https://redis.io/commands/scan) för att minska CPU-toppar.

Med kommandot [SLOWLOG](https://redis.io/commands/slowlog) kan du mäta dyra kommandon som körs mot servern.

## <a name="server-side-bandwidth-limitation"></a>Bandbreddsbegränsning på serversidan

Olika cachestorlekar har olika kapacitet för nätverksbandbredd. Om servern överskrider den tillgängliga bandbredden skickas data inte till klienten lika snabbt. Klientbegäranden kan time out eftersom servern inte kan skicka data till klienten tillräckligt snabbt.

Mätvärdena "Cache Read" och "Cache Write" kan användas för att se hur mycket bandbredd på serversidan som används. Du kan [visa dessa mått](cache-how-to-monitor.md#view-metrics-with-azure-monitor) i portalen.

Så här minskar du situationer där användningen av nätverksbandbredd ligger nära maximal kapacitet:

- Ändra klientanropsbeteende för att minska nätverkets efterfrågan.
- [Skapa aviseringar](cache-how-to-monitor.md#alerts) om mått som cacheläsning eller cacheskrivning som ska meddelas tidigt om potentiella effekter.
- [Skala](cache-how-to-scale.md) till en större cachestorlek med mer kapacitet för nätverksbandbredd.

## <a name="additional-information"></a>Ytterligare information

- [Felsöka problem på Azure Cache for Redis-klientsidan](cache-troubleshoot-client.md)
- [Vad Azure Cache för Redis erbjuder och storlek ska jag använda?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)
- [Hur kan jag jämföra och testa prestanda för min cache?](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
- [Övervaka Azure Cache för Redis](cache-how-to-monitor.md)
- [Hur kör jag Redis-kommandon?](cache-faq.md#how-can-i-run-redis-commands)
