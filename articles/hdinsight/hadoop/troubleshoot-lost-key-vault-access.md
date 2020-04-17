---
title: Azure HDInsight-kluster med diskkryptering förlorar Key Vault-åtkomst
description: Felsökningssteg och möjliga lösningar för problem när du interagerar med Azure HDInsight-kluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.openlocfilehash: b1d941fbf86d453a56a5157ed988a32173c614fc
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461539"
---
# <a name="scenario-azure-hdinsight-clusters-with-disk-encryption-lose-key-vault-access"></a>Scenario: Azure HDInsight-kluster med diskkryptering förlorar Key Vault-åtkomst

I den här artikeln beskrivs felsökningssteg och möjliga lösningar för problem när du interagerar med Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Aviseringen `The HDInsight cluster is unable to access the key for BYOK encryption at rest`Resource Health Center (RHC) visas för BYOK-kluster (Bring Your Own Key) där klusternoderna har förlorat åtkomsten till kunders Nyckelvalv (KV). Liknande varningar kan också ses på Apache Ambari UI.

## <a name="cause"></a>Orsak

Aviseringen säkerställer att KV är tillgängligt från klusternoderna, vilket säkerställer nätverksanslutningen, KV-hälso- och åtkomstprincipen för den användare som tilldelats hanterad identitet. Den här aviseringen är bara en varning om förestående mäklaravstängning vid omstart av efterföljande nod, klustret fortsätter att fungera tills noderna startas om.

Navigera till Apache Ambari UI för att hitta mer information om aviseringen från **status för diskkrypteringsnyckelvalvet**. Den här varningen innehåller information om orsaken till verifieringsfelet.

## <a name="resolution"></a>Lösning

### <a name="kvaad-outage"></a>Strömavbrott för KV/AAD

Titta på [tillgänglighet och redundanssida](../../key-vault/general/disaster-recovery-guidance.md) för Azure Key Vault och azure-status för mer informationhttps://status.azure.com/

### <a name="kv-accidental-deletion"></a>KV oavsiktlig radering

* Återställ borttagen nyckel på KV för att återställa automatiskt. Mer information finns i [Återställa borttagen nyckel](https://docs.microsoft.com/rest/api/keyvault/recoverdeletedkey).
* Kontakta KV-teamet för att återställa från oavsiktliga borttagningar.

### <a name="kv-access-policy-changed"></a>KV-åtkomstprincipen har ändrats

Återställ åtkomstprinciperna för den användare som tilldelats hanterad identitet som har tilldelats HDI-klustret för åtkomst till KV.

### <a name="key-permitted-operations"></a>Nyckel tillåten verksamhet

För varje nyckel i KV kan du välja en uppsättning tillåtna åtgärder. Se till att du har aktiverat om- och uppbrytningsåtgärder för BYOK-tangenten

### <a name="expired-key"></a>Utgångna nyckel

Om utgångsdatumet har passerats och nyckeln inte roteras återställer du nyckeln från säkerhetskopierings-HSM- eller kontaktar KV-teamet för att rensa utgångsdatumet.

### <a name="kv-firewall-blocking-access"></a>KV-brandvägg blockerar åtkomst

Åtgärda KV-brandväggsinställningarna så att BYOK-klusternoder kan komma åt KV.

### <a name="nsg-rules-on-virtual-network-blocking-access"></a>NSG-regler för åtkomst till blockering av virtuella nätverk

Kontrollera NSG-reglerna som är associerade med det virtuella nätverket som är kopplat till klustret.

## <a name="mitigation-and-prevention-steps"></a>Åtgärder för begränsning och förebyggande

### <a name="kv-accidental-deletion"></a>KV oavsiktlig radering

* Konfigurera Key Vault med [resurslåsuppsättning](../../azure-resource-manager/management/lock-resources.md).
* Säkerhetskopiera nycklar till deras maskinvarusäkerhetsmodul.

### <a name="key-deletion"></a>Borttagning av nyckel

Klustret bör tas bort innan nyckeln tas bort.

### <a name="kv-access-policy-changed"></a>KV-åtkomstprincipen har ändrats

Regelbundet granska och testa åtkomstprinciper.

### <a name="expired-key"></a>Utgångna nyckel

* Säkerhetskopiera nycklar till din HSM.
* Använd en nyckel utan att du har angett förfallodatum.
* Om utgångsdatum måste ställas in roterar du nycklarna före utgångsdatumet.

## <a name="next-steps"></a>Nästa steg

Om du inte såg problemet eller inte kan lösa problemet besöker du någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure Community Support](https://azure.microsoft.com/support/community/).

* Anslut [@AzureSupport](https://twitter.com/azuresupport) med – det officiella Microsoft Azure-kontot för att förbättra kundupplevelsen. Ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en supportbegäran från [Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **Stöd** i menyraden eller öppna **supporthubben Hjälp +.** Mer detaljerad information finns i [Så här skapar du en Azure-supportbegäran](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Åtkomst till prenumerationshantering och faktureringssupport ingår i din Microsoft Azure-prenumeration och teknisk support tillhandahålls via en av [Azure-supportplanerna](https://azure.microsoft.com/support/plans/).
