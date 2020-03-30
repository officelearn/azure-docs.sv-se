---
title: Vägledning för katastrofåterställning för Avere vFXT för Azure
description: Så här skyddar du data i Avere vFXT för Azure från oavsiktlig borttagning eller avbrott
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: rohogue
ms.openlocfilehash: 28278f76497d6e9d0fee221bb4ef32fe6d369db0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75966654"
---
# <a name="disaster-recovery-guidance-for-avere-vfxt-for-azure"></a>Vägledning för katastrofåterställning för Avere vFXT för Azure

I den här artikeln beskrivs strategier för att skydda ditt Avere vFXT för Azure-arbetsflöde och vägledning för säkerhetskopiering av data så att du kan återställa från olyckor eller avbrott.

Avere vFXT för Azure lagrar tillfälligt data i cacheminnet. Data lagras långsiktigt i backend-lagringssystem – lokala maskinvarusystem, Azure Blob-lagringsbehållare eller båda.

Om du vill skydda dig mot avbrott och eventuell dataförlust bör du tänka på följande fyra områden:

* Skydd mot driftstopp om ett Avere vFXT för Azure-system blir otillgängligt
* Skydda data i klustercachen
* Skydda data i fjärrdyna-lagring av NAS-maskinvara
* Skydda data i azure blob-molnlagring på andra sätt

Varje Avere vFXT för Azure-kund måste skapa sin egen omfattande katastrofåterställningsplan som innehåller planer för dessa artiklar. Du kan också skapa återhämtning i program som du använder med vFXT-klustret. Läs länkarna i [Nästa steg](#next-steps) för hjälp.

## <a name="protect-against-downtime"></a>Skydda mot driftstopp

Redundans är inbyggt i Avere vFXT för Azure-produkten:

* Klustret är mycket tillgängligt och enskilda klusternoder kan växla över med minimalt avbrott.
* Data som ändras i cacheminnet skrivs regelbundet till backend-kärnfilers (hardware NAS eller Azure Blob) för långsiktig lagring.

Varje Avere vFXT för Azure-kluster måste finnas i en enda tillgänglighetszon, men du kan använda redundanta kluster som finns i olika zoner eller olika regioner för att ge åtkomst snabbt i händelse av ett regionalt avbrott.

Du kan också placera lagringsbehållare i flera regioner om du är orolig för att förlora åtkomst till data. Tänk dock på att transaktioner mellan regioner har högre svarstid och högre kostnad än transaktioner som stannar inom en region.

## <a name="protect-data-in-the-cluster-cache"></a>Skydda data i klustercachen

Cachelagrade data skrivs alltid till kärnfilersna före en vanlig avstängning, men i en okontrollerad avstängning kan ändrade data i cacheminnet gå förlorade.

Om du bara använder klustret för att optimera filläsningar finns det inga ändringar att förlora. Om du också använder klustret för att cachelagra filändringar (skrivningar) bör du överväga om du vill justera kärnfilers [cacheprinciper](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_manage_cache_policies.html)<!-- link to legacy doc --> för att anpassa hur ofta data skrivs till långsiktig lagring.

I allmänhet bör återställningsplanen fokusera på att säkerhetskopiera backend-lagringssystemen, som innehåller mer data och vanligtvis är viktigare för att återupprätta arbetsflödet efter ett fel.

## <a name="protect-data-in-nas-core-filers"></a>Skydda data i NAS-kärnfilers

Använd accepterade metoder för att skydda data som lagras i en lokal NAS-maskinvarukärna filer, inklusive ögonblicksbilder och fullständiga säkerhetskopior som rekommenderas av NAS-leverantören. Haveriberedskap för dessa kärnfilers är utanför ramen för den här artikeln.

## <a name="protect-data-in-azure-blob-storage"></a>Skydda data i Azure Blob-lagring

Avere vFXT för Azure använder lokalt redundant lagring (LRS) för Azure Blob core filers. Det innebär att data i Blob-behållarna automatiskt kopieras för skydd mot tillfälliga maskinvarufel i ett datacenter.

I det här avsnittet får du tips om hur du skyddar dina data i Blob-lagring ytterligare från sällsynta avbrott i hela regionen eller oavsiktliga borttagningar.

Metodtips för att skydda data i Azure Blob-lagring är:

* Kopiera dina kritiska data till ett annat lagringskonto i en annan region ofta (så ofta som bestäms av din haveriberedskapsplan).
* Kontrollera åtkomsten till data på alla målsystem för att förhindra oavsiktlig borttagning eller skada. Överväg att använda [resurslås](../azure-resource-manager/management/lock-resources.md) på datalagring.
* Aktivera funktionen Avere vFXT för [Azure-molnögonblicksbild](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_snapshot_policies.html>) för dina Blob-kärnfilers.

### <a name="copy-avere-vfxt-core-filer-data-to-a-backup-account"></a>Kopiera Avere vFXT core filer data till ett backup-konto

Följ dessa steg för att upprätta en säkerhetskopiering av data i ett annat konto.

1. Om det behövs, generera en ny krypteringsnyckel och lagra den säkert utanför de berörda systemen.

   Om dina data krypteras av Avere vFXT för Azure-kluster bör du generera en ny krypteringsnyckel innan du kopierar data till ett annat lagringskonto. Lagra nyckeln och lösenordet säkert i en anläggning som är säker och som inte påverkas av ett regionalt fel.

   Du måste ange den här nyckeln när du lägger till behållaren i ett kluster - även om du lägger till den i det ursprungliga klustret igen.

   Läs [inställningar för molnkryptering](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_encryption_settings.html>)<!-- link to legacy doc site --> för detaljerad information.

   Om din behållare endast använder Azures inbyggda kryptering kan du hoppa över det här steget.

1. Ta bort kärnfilerna från systemet. Detta tvingar klustret att skriva alla ändrade data till backend-lagringen.

   Även om du måste lägga till kärnan filer efter säkerhetskopieringen, ta bort det är det bästa sättet att garantera att alla data är helt skriven till baksidan. (Alternativet "pausa" kan ibland lämna ändrade data i cacheminnet.) <!-- xxx true? or just metadata? -->

   Anteckna kärnfilernas namn- och korsningsinformation (listad på **sidan Namnrymd** på kontrollpanelen) så att du kan replikera den när du lägger till behållaren igen efter säkerhetskopieringen.

   Använd klusterkontrollpanelen för att ta bort kärnfilerna. [Öppna klusterkontrollpanelen](avere-vfxt-cluster-gui.md) och välj **Core filer** > **Hantera kärnfilers**. Leta reda på det lagringssystem som du vill säkerhetskopiera och använd knappen **Ta bort** för att ta bort det från klustret.

1. Skapa en ny, tom Blob-lagringsbehållare i ett annat lagringskonto i en annan region.

1. Använd ett bekvämt kopieringsverktyg för att kopiera data på kärnfilerna till den nya behållaren. Kopian måste replikera data utan ändringar och utan att störa det egna molnfilsystemformatet som används av Avere vFXT för Azure. Azure-baserade verktyg inkluderar [AzCopy,](../storage/common/storage-use-azcopy-v10.md) [Azure PowerShell](../data-lake-store/data-lake-store-get-started-powershell.md)och [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md).

1. När du har kopierat data till behållaren för säkerhetskopiering lägger du till den ursprungliga behållaren i klustret enligt beskrivningen i [Konfigurera lagring](avere-vfxt-add-storage.md).

   * Använd samma kärnfilernamn och korsningsinformation så att klientarbetsflödena inte behöver ändras.
   * Ange **innehållsvärdet för bucket till** det befintliga dataalternativet.
   * Om behållaren krypterades av klustret måste du ange den aktuella krypteringsnyckeln för dess innehåll. (Det här är nyckeln som du uppdaterade i steg ett.)

För säkerhetskopior efter den första behöver du inte skapa en ny lagringsbehållare. Men överväg att generera en ny krypteringsnyckel varje gång du gör en säkerhetskopia för att se till att du har den aktuella nyckeln lagras på en plats du kommer ihåg.

### <a name="access-a-backup-data-source-during-an-outage"></a>Komma åt en datakälla för säkerhetskopiering under ett avbrott

Så här kommer du åt behållaren för säkerhetskopiering från ett Avere vFXT för Azure-kluster:

1. Om det behövs skapar du ett nytt Avere vFXT för Azure-kluster i en opåverkad region.

   > [!TIP]
   > När du skapar ett Avere vFXT för Azure-kluster kan du spara en kopia av dess skapande mall och parametrar. Om du sparar den här informationen när du skapar det primära klustret kan du använda den för att skapa ett ersättningskluster med samma egenskaper. Klicka på länken **Hämta mall och parametrar** på [sammanfattningssidan.](avere-vfxt-deploy.md#validation-and-purchase) Spara informationen i en fil innan du skapar klustret.

1. Lägg till en ny cloud core filer som pekar på den dubbla Blob-behållaren.

   Se till att ange att målbehållaren redan innehåller data i **inlagens innehållsinställning** för guiden skapa kärnfiler. (Systemet bör varna dig om du av misstag lämnar den här uppsättningen till **Tom**.)  <!-- you can't add a populated volume at cluster creation time via template, only create a fresh one -->

1. Om det behövs uppdaterar du klienter så att de monterar det nya klustret eller nya kärnfiler i stället för originalet. (Om du lägger till ersättningskärvar med samma namn och korsningssökväg som den ursprungliga behållaren behöver du inte uppdatera klientprocesser om du inte behöver montera det nya klustret på en ny IP-adress.)

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du anpassar inställningar för Avere vFXT för Azure finns i [Klusterjustering](avere-vfxt-tuning.md).
* Läs mer om katastrofåterställning och skapande av fjädrande program i Azure:

  * [Azure-återhämtning, tekniska riktlinjer](https://docs.microsoft.com/azure/architecture/framework/resiliency/overview)
  * [Återställa från regionomfattande tjänststörningar](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region)
  * [Haveriberedskap och hög tillgänglighet för Azure-program](<https://docs.microsoft.com/azure/resiliency/resiliency-disaster-recovery-high-availability-azure-applications>)
  <!-- can't find these in the source tree to use relative links -->
