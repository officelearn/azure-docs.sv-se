---
title: Översikt – Azure Database for MySQL – flexibel Server
description: Lär dig mer om den Azure Database for MySQL flexibla servern, en Relations databas tjänst i Microsoft-molnet baserat på MySQL Community Edition.
author: savjani
ms.service: mysql
ms.author: pariks
ms.custom: mvc
ms.topic: overview
ms.date: 8/21/2020
ms.openlocfilehash: 6af3d9b99625dbecf6aec656d4aa7a65e4d1b4cd
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "91715204"
---
# <a name="azure-database-for-mysql---flexible-server-preview"></a>Azure Database for MySQL-flexibel Server (för hands version)

Azure Database for MySQL som drivs av MySQL Community Edition finns i två distributions lägen:
- Enskild server 
- Flexibel server (förhandsversion)

I den här artikeln får du en översikt över och introduktion till grundläggande begrepp för flexibel Server distributions modell. Information om hur du bestämmer vilka distributions alternativ som passar din arbets belastning finns i [välja rätt MySQL Server-alternativ i Azure](./../select-right-deployment-type.md).

## <a name="overview"></a>Översikt

Azure Database for MySQL flexibel Server är en fullständigt hanterad databas tjänst som är utformad för att ge mer detaljerad kontroll och flexibilitet för funktioner i databas hantering och konfigurations inställningar. I allmänhet tillhandahåller tjänsten mer flexibilitet och anpassningar av Server konfigurationen baserat på användar kraven. Den flexibla Server arkitekturen gör det möjligt för användare att välja hög tillgänglighet i en zon för enskild tillgänglighet och över flera tillgänglighets zoner. Flexibla servrar ger också bättre kostnads optimerings kontroller med möjlighet att stoppa/starta din server och de enheter som går att använda och som är idealiska för arbets belastningar som inte behöver fullständig beräknings kapacitet kontinuerligt. Tjänsten stöder för närvarande community-versionen av MySQL 5,7. Tjänsten är för närvarande en för hands version som är tillgänglig idag i många olika [Azure-regioner](https://azure.microsoft.com/global-infrastructure/services/).

Flexibla servrar lämpar sig bäst för 
- Program utveckling som kräver bättre kontroll och anpassningar.
- Zon redundant hög tillgänglighet
- Hanterade underhålls fönster

![Flexibelt Server konceptuellt diagram](media/overview/1-flexible-server-conceptual-diagram.png) 

## <a name="high-availability-within-and-across-availability-zones"></a>Hög tillgänglighet inom och mellan tillgänglighets zoner

Den flexibla Server distributions modellen är utformad för att stödja hög tillgänglighet i en zon för enskild tillgänglighet och över flera tillgänglighets zoner. Arkitekturen separerar beräkning och lagring. Databas motorn körs på en virtuell dator, medan datafiler finns i Azure Storage. Lagringen har tre lokalt redundanta kopior av databasfilerna som säkerställer data hållbarhet hela tiden. 

I en enda tillgänglighets zon, om servern slutar på grund av planerade eller oplanerade händelser, behåller tjänsten hög tillgänglighet för servrarna med hjälp av följande automatiserade procedur:

1. En ny virtuell beräknings dator har tillhandahållits.
2. Lagringen med datafiler mappas till den nya virtuella datorn
3. MySQL Database Engine är online på den nya virtuella datorn.
4. Klient program kan återansluta när servern är redo att godkänna anslutningar.
   
:::image type="content" source="media/overview/2-flexible-server-architecture.png" alt-text="Konceptuellt diagram med hög tillgänglighet för en zon"::: 

Om zonens redundanta hög tillgänglighet har kon figurer ATS, etableras tjänsten och upprätthåller en snabb växlings Server mellan tillgänglighets zoner i samma Azure-region. Data ändringarna på käll servern replikeras synkront till vänte läges servern för att säkerställa noll data förlust. Med zon redundant hög tillgänglighet när den planerade eller oplanerade redundansväxlingen utlöses, kommer standby-servern att bli online omedelbart och är tillgänglig för bearbetning av inkommande transaktioner. Den normala tids växlings tiden sträcker sig från 60-120 sekunder. Detta gör att tjänsten stöder hög tillgänglighet och ger förbättrad återhämtning med tolerans för enskilda tillgänglighets zon problem i en viss Azure-region. 

Se [begrepp med hög tillgänglighet](concepts-high-availability.md) för mer information.

:::image type="content" source="media/overview/3-flexible-server-overview-zone-redundant-ha.png" alt-text="Konceptuellt diagram med hög tillgänglighet för en zon"::: 

## <a name="automated-patching-with-managed-maintenance-window"></a>Automatiserad uppdatering med hanterat underhålls fönster

Tjänsten utför automatisk uppdatering av underliggande maskin vara, operativ system och databas motor. Korrigeringen innehåller säkerhets-och program uppdateringar. För MySQL-motorn ingår lägre versions uppgraderingar också som en del av den planerade underhålls versionen. Användare kan konfigurera uppdaterings schema som ska hanteras av systemet eller definiera deras anpassade schema. Under underhålls planen tillämpas korrigeringen och servern kan behöva starta om som en del av korrigerings processen för att slutföra uppdateringen. Med det anpassade schemat kan användarna göra sin förväntad korrigerings cykel och välja en underhålls period med minimal påverkan på verksamheten. I allmänhet följer tjänsten varje månads versions schema som en del av den kontinuerliga integreringen och versionen.

Mer information finns i [planerat underhåll](concepts-maintenance.md) . 

## <a name="automatic-backups"></a>Automatisk säkerhets kopiering

Den flexibla Server tjänsten skapar automatiskt Server säkerhets kopior och lagrar dem i användare som har konfigurerat lokalt redundant eller Geo-redundant lagring. Säkerhets kopieringar kan användas för att återställa servern till alla tidpunkter inom säkerhets kopie perioden för kvarhållning. Standard kvarhållningsperioden för säkerhets kopiering är sju dagar. Kvarhållning kan alternativt konfigureras upp till 35 dagar. Alla säkerhetskopior krypteras med AES 256-bitars kryptering. 

Se [säkerhets kopierings begrepp](concepts-backup-restore.md) för mer information.

## <a name="network-isolation"></a>Nätverksisolering

Du har två nätverks alternativ för att ansluta till din Azure Database for MySQL flexibla Server. Alternativen är **privat åtkomst (VNet-integrering)** och **offentlig åtkomst (tillåtna IP-adresser)**. 

* **Privat åtkomst (VNet-integrering)** – du kan distribuera din flexibla server till [Azure-Virtual Network](../../virtual-network/virtual-networks-overview.md). Virtuella Azure-nätverk tillhandahåller privat och säker nätverkskommunikation. Resurser i ett virtuellt nätverk kan kommunicera via privata IP-adresser.

   Välj alternativet VNet-integrering om du vill ha följande funktioner:
   * Ansluta från Azure-resurser i samma virtuella nätverk till den flexibla servern med hjälp av privata IP-adresser
   * Använd VPN eller ExpressRoute för att ansluta från icke-Azure-resurser till din flexibla Server
   * Ingen offentlig slut punkt

* **Offentlig åtkomst (tillåtna IP-adresser)** – du kan distribuera din flexibla server med en offentlig slut punkt. Den offentliga slut punkten är en DNS-adress som kan matchas offentligt. Frasen "tillåtna IP-adresser" syftar på ett intervall med IP-adresser som du väljer för att ge åtkomst behörighet till servern. Dessa behörigheter kallas **brand Väggs regler**.

Mer information finns i [nätverks koncept](concepts-networking.md) .

## <a name="adjust-performance-and-scale-within-seconds"></a>Justera prestanda och skalning på några sekunder

Den flexibla Server tjänsten är tillgänglig på tre SKU-nivåer: Burst, Generell användning och Minnesoptimerade. Den burst-nivån passar bäst för låg kostnads utveckling och låg concurrency-arbetsbelastningar som inte behöver fullständig beräknings kapacitet kontinuerligt. Generell användning och Minnesoptimerade passar bättre för produktions arbets belastningar som kräver hög concurrency, skalning och förutsägbar prestanda. Du kan bygga din första app på en liten databas under några dollar i månaden och sedan sömlöst justera skalan så att den uppfyller lösningens behov. Lagrings skalningen är online och har stöd för automatisk utökning av lagring. Dynamisk skalbarhet gör att databasen reagerar transparent på resurskrav som ändras snabbt. Du betalar bara för de resurser du förbrukar. 

Mer information finns i [beräknings-och lagrings koncept](concepts-compute-storage.md) .

## <a name="stopstart-server-to-optimize-cost"></a>Stoppa/starta servern för att optimera kostnaderna

Med tjänsten flexibel Server kan du stoppa och starta Server på begäran för att optimera kostnaderna. Faktureringen av beräknings nivån stoppas omedelbart när servern stoppas. Detta kan göra att du kan få avsevärda kostnads besparingar under utveckling, testning och tidsbegränsade förutsägbara produktions arbets belastningar. Servern är kvar i stoppat tillstånd i sju dagar om den inte startas om tidigare. 

Mer information finns i [Server koncept](concept-servers.md) . 

## <a name="enterprise-grade-security-and-privacy"></a>Säkerhet och sekretess i företags klass

Den flexibla Server tjänsten använder FIPS 140-2-validerade kryptografisk modul för lagrings kryptering av data i vila. Data, inklusive säkerhets kopieringar och temporära filer som skapas när frågor körs, krypteras. Tjänsten använder AES 256-bit-chiffer som ingår i Azure Storage-kryptering och nycklarna kan hanteras av systemet (standard). 

Tjänsten krypterar data i rörelse med säkerhet i transport skikt som används som standard. Flexibla servrar stöder bara krypterade anslutningar med hjälp av Transport Layer Security (TLS 1,2) och alla inkommande anslutningar med TLS 1,0 och TLS 1,1 kommer att nekas. 

Mer information finns i [använda krypterade anslutningar till flexibla servrar](/articles/mysql/flexible-server/how-to-connect-tls-ssl.md) .

Flexibla servrar ger fullständig privat åtkomst till servrarna med hjälp av [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) (VNet)-integration. Servrar i Azure Virtual Network kan bara nås och anslutas via privata IP-adresser. Med VNet-integrering nekas offentlig åtkomst och servrar kan inte nås med offentliga slut punkter. 

Mer information finns i [nätverks koncept](concepts-networking.md) .


## <a name="monitoring-and-alerting"></a>Övervakning och avisering

Den flexibla Server tjänsten är utrustad med inbyggda funktioner för prestanda övervakning och avisering. Alla Azure-mått har en frekvens på en minut och varje mått ger 30 dagars historik. Du kan konfigurera aviseringar för måtten. Tjänsten exponerar värd Server mått för att övervaka resursernas användning och gör det möjligt att konfigurera långsamma frågemeddelanden. Med dessa verktyg kan du snabbt optimera dina arbets belastningar och konfigurera servern för bästa prestanda. 

Mer information finns i [övervaknings begrepp](concepts-monitoring.md) .

## <a name="migration"></a>Migrering

Tjänsten kör community-versionen av MySQL. Detta möjliggör fullständig programkompatibilitet och kräver minimal omplanering av kostnader för att migrera befintliga program som utvecklats i MySQL-motorn till en enskild server-tjänst. Migreringen till den enskilda servern kan utföras med hjälp av något av följande alternativ:

- **Dumpa och Återställ** – för offline-migrering, där användarna kan få viss nedtid, dumpa och återställa med hjälp av community-verktyg som mysqldump/dumper kan ge ett snabbast sätt att migrera. Mer information finns i Migrera med dump och Restore. 
- **Azure Database migration service** – för sömlösa och förenklade migreringar till en enskild server med minimal nedtid kan [Azure Database migration service](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online) utnyttjas. 

## <a name="contacts"></a>Kontakter
Om du har frågor eller förslag som du kan behöva för att arbeta med Azure Database for MySQL skickar du ett e-postmeddelande till Azure Database for MySQL-teamet ([ @Ask Azure dB för MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com)). Den här e-postadressen är inte ett alias för teknisk support.

Tänk dessutom på följande kontaktpunkter efter behov:

- Kontakta Azure Support genom att [skicka in ett supportärende från Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Om du vill åtgärda ett problem med ditt konto, skickar du in ett [supportärende](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) i Azure-portalen.
- Om du vill ge feedback eller begära nya funktioner, skapar du ett inlägg via [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql).

## <a name="next-steps"></a>Nästa steg
Nu när du har läst en introduktion till Azure Database for MySQL distributions läge för enskild server är du redo att:

- Skapa din första server. 
  - [Skapa en Azure Database for MySQL flexibel server med Azure Portal](quickstart-create-server-portal.md)
  - [Skapa en Azure Database for MySQL flexibel server med Azure CLI](quickstart-create-server-cli.md)
  - [Hantera en Azure Database for MySQL flexibel server med Azure CLI](how-to-manage-server-portal.md)

- Bygg din första app med önskat språk:
  - [Python](connect-python.md)
  - [Php](connect-php.md)
