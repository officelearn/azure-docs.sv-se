---
title: Säkerhets kopiering och haveri beredskap i Azure Australien
description: Säkerhets kopiering och haveri beredskap i Microsoft Azure för myndigheter i Australien som är relaterade till ASD Essentials 8
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 078918cbfeffb1ff5f3626b5add96bc6622a90a7
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68571528"
---
# <a name="backup-and-disaster-recovery-in-azure-australia"></a>Säkerhets kopiering och haveri beredskap i Azure Australien

Säkerhets kopierings-och katastrof återställnings planer med den stödjande infrastrukturen på plats är avgörande för alla organisationer. Vikten av att ha en säkerhets kopierings lösning är markerad genom att dess införande i den [australiska cyberhot-Security Centeren är viktig 8](https://acsc.gov.au/publications/protect/essential-eight-explained.htm).

Microsoft Azure tillhandahåller två tjänster som möjliggör återhämtning: Azure Backup och Azure Site Recovery. Dessa tjänster gör att du kan skydda dina data, både lokalt och i molnet, för en rad olika design scenarier. Azure Backup och Azure Site Recovery både använda en gemensam lagrings-och hanterings resurs: Azure Recovery Services-valvet. Det här valvet används för att hantera, övervaka och särskilja Azure Backup och Azure Site Recovery data.

I den här artikeln beskrivs viktiga design element för att implementera Azure Backup och Azure Site Recovery i enlighet med de [australiska-kontrollerna (ASD) information Security manual (ISM](https://acsc.gov.au/infosec/ism/index.htm)).

## <a name="azure-backup"></a>Azure Backup

![Azure Backup](media/backup-overview.png)

Azure Backup liknar en traditionell lokal lösning för säkerhets kopiering och ger möjlighet att säkerhetskopiera både lokala och Azure-värdbaserade data. Azure Backup kan användas för att säkerhetskopiera följande data typer till Azure:

* Filer och mappar
* Windows-och Linux-operativsystem som stöds:
  * Hyper-V och VMWare-hypervisorer
  * Fysisk maskin vara
* Microsoft-program som stöds

### <a name="azure-site-recovery"></a>Azure Site Recovery

![Azure Site Recovery](media/asr-overview.png)

Azure Site Recovery replikerar arbets belastningar som består av antingen en enskild virtuell dator eller flera nivåer. Replikering stöds från lokala platser till Azure, mellan Azure-regioner eller mellan lokala platser som dirigeras av Azure Site Recovery. Lokala virtuella datorer kan replikeras till Azure eller till en lokal hypervisor som stöds. När den har kon figurer ATS dirigerar Azure Site Recovery replikering, redundans och återställning.

## <a name="key-design-considerations"></a>Viktiga design överväganden

När du implementerar en lösning för säkerhets kopiering eller haveri beredskap måste den föreslagna lösningen överväga följande:

* Omfattning och data volym som ska samlas in
* Hur länge data ska bevaras
* Hur dessa data lagras och hanteras på ett säkert sätt
* De geografiska platser där data lagras
* Rutin system test procedurer

ISM ger vägledning om de säkerhets aspekter som bör utföras när du skapar en lösning. Microsoft Azure innebär att du kan åtgärda dessa säkerhets aspekter.

### <a name="data-sovereignty"></a>Data suveränitet

Organisationer måste se till att data suveräniteten upprätthålls när de använder molnbaserade lagrings platser. Azure Policy ger möjlighet att begränsa de tillåtna platser där en Azure-resurs kan skapas. De inbyggda Azure Policy "tillåtna platser" används för att säkerställa att alla Azure-resurser som skapas under omfånget för en tilldelad Azure Policy bara kan skapas på de geografiska platser som nominerats.

Azure Policy objekt för geografisk begränsning för Azure-resurser är:

* allowedLocations
* allowedSingleLocation

Med dessa principer kan Azure-administratörer begränsa skapandet till en lista över nominerade platser eller till och med en enda geografisk plats.

### <a name="redundant-and-geographically-dispersed-storage"></a>Redundant och geografiskt utspridd lagring

Data som lagras i Azure Recovery Service-valvet lagras alltid på redundant lagring. Som standard använder Recovery Service-valvet Azure geografiskt redundant lagring (GRS). Data som lagras med GRS replikeras till andra Azure-datacenter i återställnings tjänst valvets [sekundära kopplade region](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). Dessa replikerade data lagras som skrivskyddade och görs bara skrivbara om det finns en Azure-redundansväxling. I Azure Data Centre replikeras data mellan separata fel domäner och uppgraderings domäner för att minimera risken för maskin vara eller underhålls avbrott. GRS tillhandahåller minst 99.99999999999999% tillgänglighet årligen.

Azure Recovery Services-valvet kan konfigureras för användning av lokalt redundant lagring (LRS). LRS är ett alternativ för lagring med lägre kostnad med minskad tillgänglighet. Denna redundans modell använder samma replikering mellan separata fel domäner och uppgraderings domäner, men replikeras inte mellan geografiska regioner. Data som finns på LRS-lagring, men inte lika elastiska som GRS, ger fortfarande minst 99,999999999% tillgänglighet per år.

Till skillnad från traditionella lagrings tekniker på annan plats som band medier skapas ytterligare kopior av data automatiskt och kräver ingen ytterligare administrativ belastning.

### <a name="restricted-access-and-activity-monitoring"></a>Begränsad åtkomst och aktivitets övervakning

Säkerhets kopierings data måste skyddas från skador, ändringar och ej godkända borttagningar. Både Azure Backup och Azure Site Recovery använda den vanliga Azures hanterings infrastruktur. Den här infrastrukturen ger detaljerad granskning, loggning och rollbaserad Access Control (RBAC) för resurser som finns i Azure. Åtkomst till säkerhets kopierings data kan begränsas till att välja administrativ personal och alla åtgärder som involverar säkerhets kopierings data kan loggas och granskas.

Både Azure Backup och Azure Site Recovery har inbyggda loggnings-och rapporterings funktioner. Eventuella problem som inträffar under säkerhets kopiering eller replikering rapporteras till administratörer med hjälp av Azures hanterings infrastruktur.

Azure Recovery Services Vault har också följande ytterligare data säkerhets åtgärder på plats:

* Säkerhets kopierings data bevaras i 14 dagar efter att en borttagning har utförts
* Aviseringar och meddelanden om kritiska åtgärder som "stoppa säkerhets kopiering med ta bort data"
* Krav på säkerhets PIN-kod för kritiska åtgärder
* Kontroll av minsta kvarhållningsintervall är på plats

Dessa kontroller för minsta kvarhållningsintervall omfattar:

* För daglig kvarhållning, minst sju dagars kvarhållning
* För veckovis kvarhållning, minst fyra veckors kvarhållning
* För månatlig kvarhållning, minst tre månaders kvarhållning
* För årlig kvarhållning, minst ett år för kvarhållning

Alla säkerhets kopierings data som lagras i Azure krypteras i vila med hjälp av Azures Kryptering för lagringstjänst (SSE). Detta är aktiverat för alla nya och befintliga lagrings konton som standard och kan inte inaktive ras. Krypterade data dekrypteras automatiskt under hämtning. Som standard krypteras data som krypteras med SSE med hjälp av en nyckel som tillhandahålls av och hanteras av Microsoft. Organisationer kan välja att tillhandahålla och hantera sin egen krypterings nyckel för användning med SSE. Detta ger ytterligare ett extra säkerhets lager för krypterade data. Den här nyckeln kan lagras av kunden lokalt eller säkert i Azure Key Vault.

### <a name="secure-data-transport"></a>Säker data transport

Azure Backup data krypterade under överföring med AES 256. Dessa data skyddas genom användning av en lösen fras som skapats av administrativ personal när säkerhets kopieringen först konfigureras. Microsoft har inte åtkomst till den här lösen frasen eftersom kunden måste se till att den här lösen frasen lagras på ett säkert sätt. Data överföringen sker sedan mellan den lokala miljön och Azure Recovery Services-valvet via en säker HTTPS-anslutning.  Data i Recovery Services-valvet krypteras sedan i vila med hjälp av Azure SSE.

Azure Site Recovery data krypteras också alltid vid överföring. Alla replikerade data transporteras säkert till Azure med hjälp av HTTPS och TLS. När en Azure-kund ansluter till Azure med en ExpressRoute-anslutning skickas Azure Site Recovery data via den här privata anslutningen.  När en Azure-kund ansluter till Azure med hjälp av en VPN-anslutning replikeras data mellan lokala platser och Recovery Services valvet på ett säkert sätt via Internet.

Den här säkra nätverks data överföringen tar bort säkerhets risken och minsknings kraven för att hantera traditionella externa säkerhets kopierings lagrings lösningar, till exempel band medier.

### <a name="data-retention-periods"></a>Data lagrings perioder

En minsta kvarhållning av säkerhets kopior av tre månader rekommenderas, men längre bevarande perioder krävs ofta. Azure Backup kan ge upp till 9999 kopior av en säkerhets kopia. Om en enskild Azure Backup av en skyddad instans togs dagligen, skulle detta möjliggöra bevarande av 27 års dagliga säkerhets kopieringar. Enskilda månatliga säkerhets kopieringar av en skyddad instans kan ge 833 års kvarhållning. När säkerhets kopierings data föråldras och mindre detaljerad säkerhets kopiering behålls över tid, ökar det totala kvarhållningsintervallet för säkerhetskopierade data.  Azure begränsar inte hur lång tid data kan finnas kvar i ett Azure Recovery Services-valv, bara det totala antalet säkerhets kopior per instans. Det finns inte heller någon prestanda skillnad mellan att återställa från gamla eller nya säkerhets kopior, varje återställning tar samma tid som inträffar.

Azure Recovery Services-valvet har ett antal standard principer för säkerhets kopiering och lagring.  Administrativa personal kan också skapa anpassade säkerhets kopierings-och bevarande principer.

![Azure Backup princip](media/create-policy.png)

Du måste hitta en balans mellan säkerhets kopierings frekvens och långsiktiga krav för kvarhållning när du konfigurerar Azure Backup-och bevarande principer.

### <a name="backup-and-restore-testing"></a>Säkerhets kopiering och återställning

ISM rekommenderar att du testar säkerhets kopierings data för att säkerställa att skyddade data är giltiga när en återställning eller redundans krävs. Azure Backup och Azure Site Recovery ger också möjlighet att testa skyddade data när de har säkerhetskopierats eller repliker ATS. Data som hanteras av Azure Backup kan återställas till en nominerad plats och konsekvensen för data kan sedan verifieras.

Azure Site Recovery har inbyggd funktion för att utföra testning av redundans. Arbets belastningar som replikeras till Recovery Services-valvet kan återställas till en nominerad Azure-miljö. Mål återställnings miljön kan vara helt isolerad från alla produktions miljöer för att säkerställa att det inte påverkar produktions systemen samtidigt som ett test utförs. När testet är klart kan test miljön och alla resurser tas bort omedelbart för att minska drifts kostnaderna.

Testning och validering av redundans kan automatiseras med hjälp av den Azure Automation tjänst som är inbyggd i Azure-plattformen. Detta gör att redundanstestning kan schemaläggas att ske automatiskt för att säkerställa att data replikeras till Azure.

## <a name="next-steps"></a>Nästa steg

Läs artikeln om [att säkerställa säkerheten med Azure policy](azure-policy.md).
