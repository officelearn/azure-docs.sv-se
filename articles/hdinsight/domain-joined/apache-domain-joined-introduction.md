---
title: HDInsight - domänanslutna HDInsight-kluster – Azure
description: Läs mer ...
services: hdinsight
author: omidm1
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7dc6847d-10d4-4b5c-9c83-cc513cf91965
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: omidm
ms.openlocfilehash: 6c5e32f0ed39ce2e8c1e412dcfc6c04fb0f8bd7a
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34715266"
---
# <a name="an-introduction-to-hadoop-security-with-domain-joined-hdinsight-clusters"></a>En introduktion till Hadoop-säkerhet med domänanslutna HDInsight-kluster

Fram till idag hade Azure HDInsight endast stöd för en enda lokal administratörsanvändare. Det har fungerat bra för mindre programteam eller avdelningar. Stöd för flera användare och rollbaserad åtkomstkontroll blev allt viktigare som Hadoop-baserade arbetsbelastningar med flera popularitet inom företaget, behovet av att funktioner för enterprise-klass som active directory-baserad autentisering. Genom att använda domänanslutna HDInsight-kluster kan du skapa ett HDInsight-kluster som är anslutet till en Active Directory-domän, konfigurera en lista över anställda från företaget som kan autentisera via Azure Active Directory och logga in på HDInsight-kluster. Person utanför företaget kan inte logga in eller få åtkomst till HDInsight-klustret. Enterprise-administratören kan konfigurera rollbaserad åtkomstkontroll för Hive säkerhet med [Apache Ranger](http://hortonworks.com/apache/ranger/), därför att begränsa åtkomsten till data så att endast så mycket som behövs. Slutligen kan administratören granska anställdas dataåtkomst, och eventuella ändringar som görs av principerna för åtkomstkontroll, vilket ger en hög styrningsgrad över företagets resurser.

> [!NOTE]
> De nya funktionerna som beskrivs i den här artikeln är tillgängliga i förhandsversionen endast för följande typer av klustret: Hadoop, Spark och interaktiva frågor.

> [!IMPORTANT]
> Oozie har inte aktiverats på domänanslutna HDInsight.

## <a name="benefits"></a>Fördelar
Företagssäkerhet innehåller fyra huvudsakliga pelare – säkerhet, autentisering, auktorisering och kryptering.

![Domänanslutna HDInsight-kluster gynnar de viktiga delarna](./media/apache-domain-joined-introduction/hdinsight-domain-joined-four-pillars.png).

### <a name="perimeter-security"></a>Perimetersäkerhet
Perimetersäkerhet i HDInsight uppnås med hjälp av virtuella nätverk och Gateway-tjänsten. Idag är en företagsadministratör skapar ett HDInsight-kluster i ett virtuellt nätverk och använda Nätverkssäkerhetsgrupper (brandväggsregler) för att begränsa åtkomsten till det virtuella nätverket. Endast de IP-adresser som definieras i de ingående brandväggsreglerna kan kommunicera med HDInsight-kluster, vilket ger perimetersäkerhet. En annan nivå av perimetersäkerhet uppnås med hjälp av Gateway-tjänsten. Gatewayen är den tjänst som agerar som första försvarslinje för varje inkommande begäran till HDInsight-klustret. Den tar emot begäran, validerar den och skickar först därefter vidare begäran till andra noder i klustret, vilket ger andra namn- och datanoder i klustret perimetersäkerhet.

### <a name="authentication"></a>Autentisering
En enterprise-administratör kan skapa en domänansluten HDInsight-kluster i en [virtuellt nätverk](https://azure.microsoft.com/services/virtual-network/). Noderna i HDInsight-klustret ansluts till den domän som hanteras av företaget. Detta uppnås med hjälp av [Azure Active Directory Domain Services](../../active-directory-domain-services/active-directory-ds-overview.md). Alla noder i klustret är anslutna till en domän som hanteras av företaget. Med den här konfigurationen kan företagets anställda logga in på klusternoderna med hjälp av domänautentiseringsuppgifterna. De kan också använda sina domänautentiseringsuppgifter för att autentisera med andra godkända slutpunkter som Ambari-vyer, ODBC, JDBC, PowerShell och REST API: er att interagera med klustret. Administratören har full kontroll över begränsningen av antalet användare som interagerar med klustret via dessa slutpunkter.

### <a name="authorization"></a>Auktorisering
En bra metod som de flesta företag följer är att alla medarbetare inte har åtkomst till alla företagsresurser. Med den här versionen kan på samma sätt kan administratören definiera rollbaserad principer för åtkomstkontroll för klusterresurserna. Administratören kan till exempel konfigurera [Apache Ranger](http://hortonworks.com/apache/ranger/) för att ange åtkomstkontrollprinciper för Hive. Den här funktionen gör att anställda bara kommer åt just så mycket information som de behöver för att utföra sitt arbete. SSH-åtkomst till klustret är också enbart begränsad till administratören.

### <a name="auditing"></a>Granskning
För att kunna spåra obehörig eller oavsiktlig åtkomst till resurser är det, förutom att skydda HDInsight-klusterresurserna från obehöriga användare, också nödvändigt att skydda data och granska all åtkomst till klusterresurserna och data. Administratören kan visa och rapportera all åtkomst till HDInsight-kluster-resurser och data. Administratören kan också visa och rapportera alla ändringar av åtkomstkontrollprinciperna i slutpunkter som stöds av Apache Ranger. Ett domänanslutet HDInsight-klustret använder det välkända Apache Ranger-användargränssnittet för att söka igenom granskningsloggar. På serversidan använder Ranger [Apache Solr](http://hortonworks.com/apache/solr/) för att lagra och söka igenom loggar.

### <a name="encryption"></a>Kryptering
Att skydda data är viktigt för att uppfylla organisationers säkerhets- och efterlevnadskrav, och i kombination med att begränsa åtkomst till data från obehöriga anställda, bör data också skyddas genom kryptering. Såväl datalagren för HDInsight-kluster som Azure Storage Blob och Azure Data Lake Storage har stöd för transparent [serverdelskryptering av vilande data](../../storage/common/storage-service-encryption.md). Säker HDInsight-kluster fungerar sömlöst med den här servern kryptering av data på rest-funktionen.

## <a name="next-steps"></a>Nästa steg
* [Planera för domänanslutna HDInsight-kluster](apache-domain-joined-architecture.md).
* [Konfigurera domänanslutna HDInsight-kluster](apache-domain-joined-configure.md).
* [Hantera domänanslutna HDInsight-kluster](apache-domain-joined-manage.md).
* [Konfigurera Hive principer för domänanslutna HDInsight-kluster](apache-domain-joined-run-hive.md).
* [Använda SSH med HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).

