---
title: Viktig information om Azure HDInsight
description: Senaste versionsanteckningar för Azure HDInsight. Få utvecklingstips och information om Hadoop, Spark, R Server, Hive med mera.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/20/2020
ms.openlocfilehash: e5a96d2eb67937ce4eeaa1999d8168e7f5d3d926
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80130194"
---
# <a name="release-notes"></a>Viktig information

Den här artikeln innehåller information om de **senaste** uppdateringarna av Azure HDInsight-versionen. Information om tidigare versioner finns i [HDInsight Release Notes Archive](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Sammanfattning

Azure HDInsight är en av de mest populära tjänsterna bland företagskunder för analys med öppen källkod på Azure.

## <a name="release-date-01092020"></a>Utgivningsdatum: 2020-01-09

Den här versionen gäller både HDInsight 3.6 och 4.0. HDInsight-versionen görs tillgänglig för alla regioner under flera dagar. Utgivningsdatumet här anger det första regionutgivningsdatumet. Om du inte ser nedanstående ändringar väntar du på att versionen ska vara live i din region om några dagar.

> [!IMPORTANT]  
> Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [artikel i HDInsight versioning](hdinsight-component-versioning.md).

## <a name="new-features"></a>Nya funktioner
### <a name="tls-12-enforcement"></a>Tvingande TLS 1.2
TLS (Transport Layer Security) och SSL (Secure Sockets Layer) är kryptografiska protokoll som ger kommunikationssäkerhet över ett datornätverk. Läs mer om [TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0). HDInsight använder TLS 1.2 på offentliga HTTPs-slutpunkter, men TLS 1.1 stöds fortfarande för bakåtkompatibilitet. 

Med den här versionen kan kunder endast anmäla sig till TLS 1.2 för alla anslutningar via slutpunkten för det offentliga klustret. För att stödja detta introduceras den nya egenskapen **minSupportedTlsVersion** och kan anges när klustret skapas. Om egenskapen inte har angett stöder klustret fortfarande TLS 1.0, 1.1 och 1.2, vilket är samma som dagens beteende. Kunder kan ange värdet för den här egenskapen till "1,2", vilket innebär att klustret endast stöder TLS 1.2 och högre. Mer information finns i [Planera ett virtuellt nätverk - Transport layer security](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#transport-layer-security).

### <a name="bring-your-own-key-for-disk-encryption"></a>Ta med din egen nyckel för diskkryptering
Alla hanterade diskar i HDInsight är skyddade med Azure Storage Service Encryption (SSE). Data på dessa diskar krypteras som standard av Microsoft-hanterade nycklar. Från och med den här versionen kan du ta med din egen nyckel (BYOK) för diskkryptering och hantera den med Azure Key Vault. BYOK-kryptering är en konfiguration i ett steg när kluster skapas utan extra kostnad. Registrera bara HDInsight som en hanterad identitet med Azure Key Vault och lägg till krypteringsnyckeln när du skapar ditt kluster. Mer information finns i [Kryptering med kundhanterad nyckeldisk](https://docs.microsoft.com/azure/hdinsight/disk-encryption).

## <a name="deprecation"></a>Utfasning
Inga utfasningar för den här versionen. Se [Kommande ändringar](#upcoming-changes).

## <a name="behavior-changes"></a>Beteendeförändringar
Inga beteendeändringar för den här versionen. Se [Kommande ändringar](#upcoming-changes).

## <a name="upcoming-changes"></a>Kommande förändringar
Följande ändringar kommer att ske i kommande versioner. 

### <a name="a-minimum-4-core-vm-is-required-for-head-node"></a>En minst 4-kärnig virtuell dator krävs för Head Node 
En minst 4-kärnig virtuell dator krävs för head nod för att säkerställa hög tillgänglighet och tillförlitlighet för HDInsight-kluster. Från och med den 6 april 2020 kan kunderna bara välja 4-kärniga eller högre virtuella datorer som head-nod för de nya HDInsight-klustren. Befintliga kluster fortsätter att köras som förväntat. 

### <a name="esp-spark-cluster-node-size-change"></a>ESP Spark-klusternodstorleksändring 
I den kommande versionen ändras den minsta tillåtna nodstorleken för ESP Spark-klustret till Standard_D13_V2. Virtuella datorer i A-serien kan orsaka ESP-klusterproblem på grund av relativt låg cpu- och minneskapacitet. Virtuella datorer i A-serien kommer att inaktiveras för att skapa nya ESP-kluster.

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Flytta till Azure-skalningsuppsättningar för virtuella datorer
HDInsight använder nu virtuella Azure-datorer för att etablera klustret. I den kommande versionen använder HDInsight azure-skalningsuppsättningar för virtuella datorer i stället. Läs mer om Azure-skalningsuppsättningar för virtuella datorer.

### <a name="hbase-20-to-21"></a>HBase 2,0 till 2,1
I den kommande HDInsight 4.0-versionen kommer HBase-versionen att uppgraderas från version 2.0 till 2.1.

## <a name="bug-fixes"></a>Felkorrigeringar
HDInsight fortsätter att göra klustertillförlitlighet och prestandaförbättringar. 

## <a name="component-version-change"></a>Ändring av komponentversion
Ingen ändring av komponentversion för den här versionen. Du kan hitta de aktuella komponentversionerna för HDInsight 4.0 ad HDInsight 3.6 här.

## <a name="known-issues"></a>Kända problem

Från och med den 18 mars 2020 har vissa Azure HDInsight-kunder i Västeuropa eller Norra Europa fått felmeddelanden när hdinsight-kluster skapas eller skalas i dessa regioner. Fel relaterade till det här problemet är:

- Internt serverfel uppstod vid bearbetning av begäran. Försök igen förfrågan eller kontakta supporten.
- Det gick inte att driften av minst en resursdistribution. Vänligen lista distributionsåtgärder för mer information. Se https://aka.ms/DeployOperations för användningsinformation
- User SubscriptionId\<'\>Prenumeration ID ' har inte\<kärnor kvar för att skapa resursens klusternamn>'. \<Obligatoriskt:\>X , Tillgänglig: 0.

Tekniker är medvetna om problemet och undersöker aktivt.

Om du vill ha mer hjälp kan du skapa en [supportbegäran](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).
