GEO-redundant lagring (GRS) är utformad att ge minst 99.99999999999999% (16 9's) hållbarhet objekt under ett visst år genom att replikera data till en sekundär region hundratals mil bort från den primära regionen är. Om ditt lagringskonto har GRS aktiverat, sedan dina data skyddas även vid en fullständig regionalt strömavbrott eller en katastrof där den primära regionen inte kan återställas.

För ett lagringskonto med GRS aktiverad genomförs en uppdatering först till den primära regionen. Sedan replikeras uppdateringen asynkront till den sekundära regionen där de också replikeras.

Med GRS båda primära och sekundära regioner hantera repliker i separata feldomäner och uppgradera domäner inom en skala lagringsenhet enligt med LRS.

Att tänka på:

* Eftersom asynkron replikering innefattar en fördröjning, händelse regionala katastrofåterställning är det möjligt att ändringar som ännu inte har replikerats till den sekundära regionen kommer att förloras om data inte kan återställas från den primära regionen.
* Repliken är inte tillgängligt om inte Microsoft initierar redundans till den sekundära regionen. Om Microsoft initierat en växling till den sekundära regionen kan du kommer har läs- och skrivbehörighet till dessa data efter växlingen har slutförts. Mer information finns [Disaster Recovery vägledning](../articles/storage/common/storage-disaster-recovery-guidance.md).
* Om ett program att läsa från den sekundära regionen, bör användaren aktivera RA-GRS.

När du skapar ett lagringskonto, väljer du den primära regionen för kontot. Den sekundära regionen bestäms baserat på den primära regionen och kan inte ändras. I följande tabell visas de primära och sekundära region pairings.

| Primär | Sekundär |
| --- | --- |
| Norra centrala USA | Södra centrala USA |
| Södra centrala USA | Norra centrala USA |
| Östra USA | Västra USA |
| Västra USA | Östra USA |
| Östra USA 2 | Centrala USA |
| Centrala USA | Östra USA 2 |
| Norra Europa | Västra Europa |
| Västra Europa | Norra Europa |
| Sydostasien | Östasien |
| Östasien | Sydostasien |
| Östra Kina | Norra Kina |
| Norra Kina | Östra Kina |
| Östra Japan | Västra Japan |
| Västra Japan | Östra Japan |
| Södra Brasilien | Södra centrala USA |
| Östra Australien | Sydöstra Australien |
| Sydöstra Australien | Östra Australien |
| Södra Indien | Centrala Indien |
| Centrala Indien | Södra Indien |
| Västra Indien | Södra Indien |
| Iowa (USA-förvaltad region) | Virginia (USA-förvaltad region) |
| Virginia (USA-förvaltad region) | Texas (USA-förvaltad region) |
| Texas (USA-förvaltad region) | Arizona (USA-förvaltad region) |
| Arizona (USA-förvaltad region) | Texas (USA-förvaltad region) |
| Centrala Kanada | Östra Kanada |
| Östra Kanada | Centrala Kanada |
| Storbritannien, västra | Storbritannien, södra |
| Storbritannien, södra | Storbritannien, västra |
| Centrala Tyskland | Nordöstra Tyskland |
| Nordöstra Tyskland | Centrala Tyskland |
| Västra USA 2 | Västra centrala USA |
| Västra centrala USA | Västra USA 2 |

Uppdaterad information om regioner som stöds av Azure finns [Azure-regioner](https://azure.microsoft.com/regions/).

>[!NOTE]  
> USA Gov Virginia sekundär region är oss Gov Texas. Tidigare värdsystemet oss Gov Virginia oss Gov Iowa som en sekundär region. Storage-konton fortfarande utnyttja oss Gov Iowa som en sekundär region ska migreras till oss Gov Texas som en sekundär region.
>
>
