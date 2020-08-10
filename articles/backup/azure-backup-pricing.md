---
title: Priser för Azure Backup
description: Lär dig hur du beräknar kostnader för budgetering Azure Backup prissättning.
ms.topic: conceptual
ms.date: 06/16/2020
ms.openlocfilehash: cdb3dc756e1ee7e32453acd7246952c84abebaf7
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2020
ms.locfileid: "88035764"
---
# <a name="azure-backup-pricing"></a>Priser för Azure Backup

Läs mer om Azure Backup prissättning på sidan för [Azure Backup priser](https://azure.microsoft.com/pricing/details/backup/).

## <a name="download-detailed-estimates-for-azure-backup-pricing"></a>Hämta detaljerade uppskattningar för Azure Backup priser

Om du vill beräkna dina kostnader för budgetering eller kostnads jämförelse kan du ladda ned den detaljerade [Azure Backup pris uppskattningen](https://aka.ms/AzureBackupCostEstimates).  

### <a name="what-does-the-estimator-contain"></a>Vad innehåller uppskattningen?

I Azure Backup kostnads uppskattnings bladet finns ett alternativ för att uppskatta alla möjliga arbets belastningar som du vill säkerhetskopiera med Azure Backup. Följande arbets belastningar är:

- Virtuella Azure-datorer
- Lokala servrar
- SQL i virtuella Azure-datorer
- SAP HANA på virtuella Azure-datorer
- Azure Files-resurser

## <a name="estimate-costs-for-backing-up-azure-vms-or-on-premises-servers"></a>Beräkna kostnader för säkerhets kopiering av virtuella Azure-datorer eller lokala servrar

För att beräkna kostnaderna för att säkerhetskopiera virtuella Azure-datorer eller lokala servrar med Azure Backup behöver du följande parametrar:

- Storleken på de virtuella datorer eller lokala servrar som du försöker säkerhetskopiera
  - Ange den "använda storleken" på diskar eller servrar som krävs för säkerhets kopiering

- Antal servrar med den storleken

- Vad är den förväntade mängden data omsättning på dessa servrar?<br>
  Omsättning syftar på mängden data som ska ändras. Om du till exempel har en virtuell dator med 200 GB data som ska säkerhets kopie ras och 10 GB IT-ändringar varje dag, är den dagliga omsättningen 5%.

  - Högre omsättning innebär att du säkerhetskopierar mer data

  - Välj **låg** eller **måttlig** för fil servrar och **hög** om du kör databaser

  - Om du vet din **omsättning%** kan du använda alternativet **Ange ditt eget%**

- Välj säkerhets kopierings princip

  - Hur länge förväntar du dig att behålla "dagliga" säkerhets kopieringar? (i dagar)

  - Hur länge förväntar du dig att behålla "veckovis" säkerhets kopieringar? (i veckor)

  - Hur länge förväntar du dig att behålla "månatliga" säkerhets kopior? (i månader)

  - Hur lång tid förväntar du dig att spara "årliga" säkerhets kopior? (i år)

  - Hur lång tid kommer du att behålla "omedelbar Restore-ögonblicksbilder"? (1-5 dagar)

    - Med det här alternativet kan du återställa från så långt tillbaka till sju dagar på ett snabbt sätt med hjälp av ögonblicks bilder som lagras på diskar.

- **Valfri** – selektiv säkerhets kopiering av disk

  - Om du använder alternativet **selektiv säkerhets kopiering av disk** när du säkerhetskopierar virtuella Azure-datorer väljer du alternativet **exkludera disk** och anger procent andelen diskar som exkluderas från säkerhets kopian i storlek. Om du till exempel har en virtuell dator som är ansluten till tre diskar med 200 GB som används på varje disk och om du vill undanta två av dem från att säkerhetskopiera, anger du 66,7%.

- **Valfritt** – redundans för lagring av säkerhets kopior

  - Detta anger redundansen för lagrings kontot som dina säkerhets kopierings data hamnar i. Vi rekommenderar att du använder **GRS** för högsta tillgänglighet. Eftersom det säkerställer att en kopia av dina säkerhetskopierade data behålls i en annan region, hjälper det dig att uppfylla flera krav för efterlevnad. Ändra redundansen till **LRS** om du säkerhetskopierar utvecklings-eller test miljöer som inte behöver en säkerhets kopia på företags nivå. Välj alternativet **RAGRS** i bladet om du vill förstå kostnaderna när [återställning mellan regioner](backup-azure-arm-restore-vms.md#cross-region-restore) har Aktiver ATS för dina säkerhets kopieringar.

- **Valfritt** – ändra regional prissättning eller Använd rabatterade kostnader

  - Om du vill kontrol lera dina uppskattningar för en annan region eller rabatterad taxa väljer du **Ja** för alternativet **testa uppskattningar för en annan region?** och anger de priser som du vill köra uppskattningarna med.

## <a name="estimate-costs-for-backing-up-sql-servers-in-azure-vms"></a>Beräkna kostnader för säkerhets kopiering av SQL-servrar i virtuella Azure-datorer

För att beräkna kostnaderna för att säkerhetskopiera SQL-servrar som körs på virtuella Azure-datorer med Azure Backup behöver du följande parametrar:

- Storlek på de SQL-servrar som du försöker säkerhetskopiera

- Antal SQL-servrar med ovanstående storlek

- Vad är den förväntade komprimeringen för dina SQL-servrars säkerhets kopierings data?

  - De flesta Azure Backup kunder ser att säkerhets kopierings data har 80% komprimering jämfört med SQL Server-storleken när SQL-komprimeringen är **aktive rad**.

  - Om du förväntar dig att se en annan komprimering anger du antalet i det här fältet

- Vilken är den förväntade storleken på logg säkerhets kopior?

  - % Anger den dagliga logg storleken som en% av SQL Server-storleken

- Vad är den förväntade mängden daglig data omsättning på dessa servrar?

  - Vanligt vis har databaser "hög" omsättning

  - Om du vet din **omsättning%** kan du använda alternativet **Ange ditt eget%**

- Välj säkerhets kopierings princip

  - Typ av säkerhetskopiering

    - Den mest effektiva principen du kan välja är **daglig differentiellhet** med en veckovis/månatlig/årlig fullständig säkerhets kopiering. Azure Backup kan återställas från differentiella med enkel klickning också.

    - Du kan också välja att ha en princip med en daglig/veckovis/per månads fullständig säkerhets kopiering. Det här alternativet kommer att förbruka lite mer lagrings utrymme än det första alternativet.

  - Hur lång tid förväntar du dig att spara "logg"-säkerhets kopieringar? (i dagar) [7-35]

  - Hur länge förväntar du dig att behålla "dagliga" säkerhets kopieringar? (i dagar)

  - Hur länge förväntar du dig att behålla "veckovis" säkerhets kopieringar? (i veckor)

  - Hur länge förväntar du dig att behålla "månatliga" säkerhets kopior? (i månader)

  - Hur lång tid förväntar du dig att spara "årliga" säkerhets kopior? (i år)

- **Valfritt** – redundans för lagring av säkerhets kopior

  - Detta anger redundansen för lagrings kontot som dina säkerhets kopierings data hamnar i. Vi rekommenderar att du använder **GRS** för högsta tillgänglighet. Eftersom det säkerställer att en kopia av dina säkerhetskopierade data behålls i en annan region, hjälper det dig att uppfylla flera krav för efterlevnad. Ändra redundansen till **LRS** om du säkerhetskopierar utvecklings-eller test miljöer som inte behöver en säkerhets kopia på företags nivå.

- **Valfritt** – ändra regional prissättning eller Använd rabatterade kostnader

  - Om du vill kontrol lera dina uppskattningar för en annan region eller rabatterad taxa väljer du **Ja** för alternativet **testa uppskattningar för en annan region?** och anger de priser som du vill köra uppskattningarna med.

## <a name="estimate-costs-for-backing-up-sap-hana-servers-in-azure-vms"></a>Beräkna kostnaderna för att säkerhetskopiera SAP HANA-servrar i virtuella Azure-datorer

För att beräkna kostnaderna för att säkerhetskopiera SAP HANA servrar som körs på virtuella Azure-datorer med Azure Backup behöver du följande parametrar:

- Total storlek för de SAP HANA-databaser som du försöker säkerhetskopiera. Detta bör vara summan av den fullständiga säkerhets kopierings storleken för varje databas, som rapporteras av SAP HANA.
- Antal SAP HANA-servrar med ovanstående storlek
- Vilken är den förväntade storleken på logg säkerhets kopior?
  
  - Den genomsnittliga logg storleken per dag i procent är en% av den totala storleken på SAP HANA databaser som du säkerhetskopierar på SAP HANA-servern
- Vad är den förväntade mängden daglig data omsättning på dessa servrar?
  - Den genomsnittliga omsättnings storleken per dag i procent är en% av den totala storleken på SAP HANA databaser som du säkerhetskopierar på SAP HANA-servern
  - Vanligt vis har databaser "hög" omsättning
  - Om du vet din **omsättning%** kan du använda alternativet **Ange ditt eget%**
- Välj säkerhets kopierings princip
  - Typ av säkerhetskopiering
    - Den mest effektiva principen du kan välja är **daglig differentiellhet** med en **veckovis/månatlig/årlig** fullständig säkerhets kopiering. Azure Backup kan återställas från differentiella med enkel klickning också.
    - Du kan också välja att ha en princip med en daglig/veckovis/per **månads** fullständig säkerhets kopiering. Det här alternativet kommer att förbruka lite mer lagrings utrymme än det första alternativet.
  - Hur lång tid förväntar du dig att spara "logg"-säkerhets kopieringar? (i dagar) [7-35]
  - Hur länge förväntar du dig att behålla "dagliga" säkerhets kopieringar? (i dagar)
  - Hur länge förväntar du dig att behålla "veckovis" säkerhets kopieringar? (i veckor)
  - Hur länge förväntar du dig att behålla "månatliga" säkerhets kopior? (i månader)
  - Hur lång tid förväntar du dig att spara "årliga" säkerhets kopior? (i år)
- **Valfritt** – redundans för lagring av säkerhets kopior
  
  - Detta anger redundansen för lagrings kontot som dina säkerhets kopierings data hamnar i. Vi rekommenderar att du använder **GRS** för högsta tillgänglighet. Eftersom det säkerställer att en kopia av dina säkerhetskopierade data behålls i en annan region, hjälper det dig att uppfylla flera krav för efterlevnad. Ändra redundansen till **LRS** om du säkerhetskopierar utvecklings-eller test miljöer som inte behöver en säkerhets kopia på företags nivå.
- **Valfritt** – ändra regional prissättning eller Använd rabatterade kostnader
  
  - Om du vill kontrol lera dina uppskattningar för en annan region eller rabatterad taxa väljer du **Ja** för alternativet **testa uppskattningar för en annan region?** och anger de priser som du vill köra uppskattningarna med.
  
## <a name="estimate-costs-for-backing-up-azure-file-shares"></a>Beräkna kostnader för säkerhets kopiering av Azure-filresurser

För att beräkna kostnaderna för att säkerhetskopiera Azure-filresurser med den [ögonblicks bildbaserade säkerhets kopierings lösningen](azure-file-share-backup-overview.md) som erbjuds av Azure Backup behöver du följande parametrar:

- Storlek (**i GB**) för de fil resurser som du vill säkerhetskopiera.

- Om du vill säkerhetskopiera fil resurser uppslag över flera lagrings konton anger du antalet lagrings konton som är värd för fil resurserna med ovanstående storlek.

- Förväntad mängd data omsättning på de fil resurser som du vill säkerhetskopiera. <br>Omsättning syftar på mängden ändring i data och det påverkar direkt storleken för ögonblicks bild lagringen. Om du till exempel har en fil resurs med 200 GB data som ska säkerhets kopie ras och 10 GB IT-ändringar varje dag, är den dagliga omsättningen 5%.
  - Högre omsättning innebär att mängden data som ändras i fil resurs innehållet varje dag är hög och att den stegvisa ögonblicks bilden (endast fångar data ändringar) också skulle vara mer.
  - Välj låg (1%), måttlig (3%) eller hög (5%) baserat på din fil resurs egenskaper och användning.
  - Om du känner till den exakta **omsättningen%** för din fil resurs kan du välja alternativet **Ange ditt eget%** i list rutan. Ange värdena (i%) för varje dag, varje vecka, varje månad och per år omsättning.

- Typ av lagrings konto (standard eller Premium) och inställningen lagrings redundans för det lagrings konto som är värd för den säkerhetskopierade fil resursen. <br>I den aktuella säkerhets kopierings lösningen för Azure-filresurser lagras ögonblicks bilder i samma lagrings konto som den säkerhetskopierade fil resursen. Lagrings kostnaden som är kopplad till ögonblicks bilder faktureras som en del av dina Azure Files-fakturor, baserat på priserna för ögonblicks bilder för konto typen och redundans inställningen för det lagrings konto som är värd för den säkerhetskopierade fil resursen och ögonblicks bilderna.

- Kvarhållning för olika säkerhets kopior
  - Hur länge förväntar du dig att behålla "dagliga" säkerhets kopieringar? (i dagar)
  - Hur länge förväntar du dig att behålla "veckovis" säkerhets kopieringar? (i veckor)
  - Hur länge förväntar du dig att behålla "månatliga" säkerhets kopior? (i månader)
  - Hur lång tid förväntar du dig att spara "årliga" säkerhets kopior? (i år)

  Läs [support matrisen för Azure-filresursen](azure-file-share-support-matrix.md#retention-limits) om du vill ha maximalt antal tillåtna kvarhållning värden i varje kategori.

- **Valfritt** – ändra regional prissättning eller Använd rabatterade kostnader.
  - Standardvärdena som är inställda för kostnad för lagring av ögonblicks bilder per GB och skyddad instans kostnad i uppskattningen är för regionen USA, östra. Om du vill kontrol lera dina uppskattningar för en annan region eller rabatterad taxa väljer du **Ja** för alternativet **testa uppskattningar för en annan region?** och anger de priser som du vill köra uppskattningarna med.

## <a name="next-steps"></a>Nästa steg

[Vad är Azure Backup-tjänsten?](backup-overview.md)
