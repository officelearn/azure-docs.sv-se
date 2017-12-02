---
title: "Hadoop-säkerhet – domänanslutna HDInsight-kluster – Azure | Microsoft Docs"
description: "Läs mer ..."
services: hdinsight
documentationcenter: 
author: saurinsh
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7dc6847d-10d4-4b5c-9c83-cc513cf91965
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/31/2016
ms.author: saurinsh
ms.openlocfilehash: ef49a6c176017d05a4048145c62fcf8692ffc23b
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2017
---
# <a name="an-introduction-to-hadoop-security-with-domain-joined-hdinsight-clusters"></a>En introduktion till Hadoop-säkerhet med domänanslutna HDInsight-kluster

Fram till idag hade Azure HDInsight endast stöd för en enda lokal administratörsanvändare. Det har fungerat bra för mindre programteam eller avdelningar. I takt med att Hadoop-baserade arbetsbelastningar blev allt populärare inom företagssektorn blev dock behovet av företagsklassfunktioner, som active directory-baserad autentisering, stöd för flera användare och rollbaserad åtkomstkontroll, allt viktigare. Genom att använda domänanslutna HDInsight-kluster kan du skapa ett HDInsight-kluster som är anslutet till en Active Directory-domän, konfigurera en lista över anställda från företaget som kan autentisera via Azure Active Directory och logga in på HDInsight-kluster. Person utanför företaget kan inte logga in eller få åtkomst till HDInsight-klustret. Företagets administratör kan konfigurera rollbaserad åtkomstkontroll för Hive-säkerhet med hjälp av [Apache Ranger](http://hortonworks.com/apache/ranger/), och kan därmed begränsa åtkomsten till data till bara så mycket som behövs. Slutligen kan administratören granska anställdas dataåtkomst, och eventuella ändringar som görs av principerna för åtkomstkontroll, vilket ger en hög styrningsgrad över företagets resurser.

> [!NOTE]
> De nya funktionerna som beskrivs i den här artikeln är bara tillgängliga på följande typer av klustret: Hadoop, Spark och interaktiva frågor. Andra arbetsbelastningar, t.ex HBase, Storm och Kafka, aktiveras i framtida versioner.

> [!IMPORTANT]
> Oozie har inte aktiverats på domänanslutna HDInsight.

## <a name="benefits"></a>Fördelar
Enterprise Security består av fyra huvudsakliga delar – perimetersäkerhet, autentisering, auktorisering och kryptering.

![Domänanslutna HDInsight-kluster gynnar de viktiga delarna](./media/apache-domain-joined-introduction/hdinsight-domain-joined-four-pillars.png).

### <a name="perimeter-security"></a>Perimetersäkerhet
Perimetersäkerhet i HDInsight uppnås med hjälp av virtuella nätverk och Gateway-tjänsten. Idag kan en företagsadministratör skapa HDInsight-kluster i ett virtuellt nätverk och använda nätverkssäkerhetsgrupper (inkommande eller utgående brandväggsregler) för att begränsa åtkomsten till det virtuella nätverket. Endast de IP-adresser som definieras i de ingående brandväggsreglerna kan kommunicera med HDInsight-kluster, vilket ger perimetersäkerhet. En annan nivå av perimetersäkerhet uppnås med hjälp av Gateway-tjänsten. Gatewayen är den tjänst som agerar som första försvarslinje för varje inkommande begäran till HDInsight-klustret. Den tar emot begäran, validerar den och skickar först därefter vidare begäran till andra noder i klustret, vilket ger andra namn- och datanoder i klustret perimetersäkerhet.

### <a name="authentication"></a>Autentisering
En enterprise-administratör kan skapa en domänansluten HDInsight-kluster i en [virtuellt nätverk](https://azure.microsoft.com/services/virtual-network/). Noderna i HDInsight-klustret ansluts till den domän som hanteras av företaget. Detta uppnås med hjälp av [Azure Active Directory Domain Services](../../active-directory-domain-services/active-directory-ds-overview.md). Alla noder i klustret är anslutna till en domän som hanteras av företaget. Med den här konfigurationen kan företagets anställda logga in på klusternoderna med hjälp av domänautentiseringsuppgifterna. De kan också använda sina domänautentiseringsuppgifter för att autentisera sig med andra godkända slutpunkter som Hue, Ambari Views, ODBC, JDBC, PowerShell och REST API:er för att interagera med klustret. Administratören har full kontroll över begränsningen av antalet användare som interagerar med klustret via dessa slutpunkter.

### <a name="authorization"></a>Auktorisering
En bra metod som de flesta företag följer är att alla medarbetare inte har åtkomst till alla företagsresurser. I den här versionen kan administratören på samma sätt definiera principer för rollbaserad åtkomstkontroll för klusterresurserna. Administratören kan till exempel konfigurera [Apache Ranger](http://hortonworks.com/apache/ranger/) för att ange åtkomstkontrollprinciper för Hive. Den här funktionen gör att anställda bara kommer åt just så mycket information som de behöver för att utföra sitt arbete. SSH-åtkomst till klustret är också enbart begränsad till administratören.

### <a name="auditing"></a>Granskning
För att kunna spåra obehörig eller oavsiktlig åtkomst till resurser är det, förutom att skydda HDInsight-klusterresurserna från obehöriga användare, också nödvändigt att skydda data och granska all åtkomst till klusterresurserna och data. Administratören kan visa och rapportera all åtkomst till HDInsight-kluster-resurser och data. Administratören kan också visa och rapportera alla ändringar av åtkomstkontrollprinciperna i slutpunkter som stöds av Apache Ranger. Ett domänanslutet HDInsight-klustret använder det välkända Apache Ranger-användargränssnittet för att söka igenom granskningsloggar. På serversidan använder Ranger [Apache Solr](http://hortonworks.com/apache/solr/) för att lagra och söka igenom loggar.

### <a name="encryption"></a>Kryptering
Att skydda data är viktigt för att uppfylla organisationers säkerhets- och efterlevnadskrav, och i kombination med att begränsa åtkomst till data från obehöriga anställda, bör data också skyddas genom kryptering. Såväl datalagren för HDInsight-kluster som Azure Storage Blob och Azure Data Lake Storage har stöd för transparent [serverdelskryptering av vilande data](../../storage/common/storage-service-encryption.md). Secure HDInsight-kluster fungerar sömlöst med den här funktionen för serverdelskryptering för vilande data.

## <a name="next-steps"></a>Nästa steg
* Om du vill konfigurera ett domänanslutet HDInsight-kluster kan du läsa i [Konfigurera domänanslutna HDInsight-kluster](apache-domain-joined-configure.md).
* Om du vill hantera ett domänanslutet HDInsight-kluster kan du läsa i [Hantera domänanslutna HDInsight-kluster](apache-domain-joined-manage.md).
* Om du vill konfigurera Hive-principer och köra Hive-frågor kan du läsa i [Konfigurera Hive-principer för domänanslutna HDInsight-kluster](apache-domain-joined-run-hive.md).
* För att köra Hive-frågor med SSH på domänanslutna HDInsight-kluster, se [använda SSH med HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).
