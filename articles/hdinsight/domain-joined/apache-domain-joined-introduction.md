---
title: En introduktion till Apache Hadoop-säkerhet med Enterprise Security Package
description: Lär dig hur Enterprise Security Package stöder fyra grundläggande faktorer för företagssäkerhet.
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/19/2019
ms.openlocfilehash: deff2aa3cd475a3ed37adc4408a133ed1fd0757e
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62113214"
---
# <a name="an-introduction-to-apache-hadoop-security-with-enterprise-security-package"></a>En introduktion till Apache Hadoop-säkerhet med Enterprise Security Package

Tidigare Azure HDInsight stöds endast en enskild användare: lokal administratör. Det har fungerat bra för mindre programteam eller avdelningar. Som Apache Hadoop-baserade arbetsbelastningar fått populärare inom företagssektorn behovet för stöd för funktioner i företagsklass som flera användare i Active Directory-baserad autentisering och rollbaserad åtkomstkontroll blev allt viktigare. 

Du kan skapa ett HDInsight-kluster med Enterprise Security Package (ESP) och som är ansluten till en Active Directory-domän. Du kan sedan konfigurera en lista över anställda från företaget som kan autentisera via Azure Active Directory för att logga in på HDInsight-klustret. Ingen kan från utanför företaget logga in eller få åtkomst till HDInsight-klustret. 

Enterprise-administratör kan konfigurera rollbaserad åtkomstkontroll (RBAC) för Apache Hive säkerhet med hjälp av [Apache Ranger](https://hortonworks.com/apache/ranger/). Konfigurera RBAC begränsar dataåtkomsten till bara vad som behövs. Slutligen kan administratören granska dataåtkomst för anställda och eventuella ändringar som görs till principer för åtkomstkontroll. Administratören kan sedan få en hög styrningsgrad över företagets resurser.

> [!NOTE]  
> Apache Oozie har nu aktiverats på ESP-kluster. För att komma åt webbgränssnittet för Oozie användare bör aktivera [tunneling](../hdinsight-linux-ambari-ssh-tunnel.md).

Enterprise security består av fyra större pelare: perimetersäkerhet, autentisering, auktorisering och kryptering.

![Fördelarna med Enterprise Security Package HDInsight-kluster i fyra grundläggande faktorer för företagssäkerhet](./media/apache-domain-joined-introduction/hdinsight-domain-joined-four-pillars.png).

## <a name="perimeter-security"></a>Perimetersäkerhet
Perimetersäkerhet i HDInsight uppnås genom virtuella nätverk och Azure VPN Gateway-tjänsten. En företagsadministratör skapa ett ESP-kluster i ett virtuellt nätverk och använda nätverkssäkerhetsgrupper (brandväggsregler) för att begränsa åtkomsten till det virtuella nätverket. Endast de IP-adresser som definieras i de ingående brandväggsreglerna kommer att kunna kommunicera med HDInsight-klustret. Den här konfigurationen ger perimetersäkerhet.

En annan nivå av perimetersäkerhet uppnås genom VPN-Gateway-tjänsten. Gatewayen fungerar som första försvarslinje för varje inkommande begäran till HDInsight-kluster. Den tar emot begäran, validerar den och först därefter vidare begäran skickas till de andra noderna i klustret. På så sätt kan tillhandahåller gatewayen andra namn- och datanoder i klustret perimetersäkerhet.

## <a name="authentication"></a>Autentisering
En företagsadministratör kan skapa ett HDInsight-kluster med ESP i ett [virtuellt nätverk](https://azure.microsoft.com/services/virtual-network/). Alla noder i HDInsight-klustret är anslutna till den domän som hanteras av företaget. Detta uppnås med [Azure Active Directory Domain Services](../../active-directory-domain-services/active-directory-ds-overview.md). 

Med den här konfigurationen kan kan företagets anställda logga in på noderna i klustret med hjälp av sina domänautentiseringsuppgifter. De kan också använda sina domänautentiseringsuppgifter för att autentisera med andra godkända slutpunkter som Apache Ambari Views, ODBC, JDBC, PowerShell och REST API: er att interagera med klustret. Administratören har full kontroll över begränsningen av antalet användare som interagerar med klustret via dessa slutpunkter.

## <a name="authorization"></a>Auktorisering
Bästa praxis som de flesta företag följer att se till att alla medarbetare inte har åtkomst till alla företagsresurser. På samma sätt kan kan administratören definiera rollbaserad principer för åtkomstkontroll för klusterresurserna. 

Administratören kan till exempel konfigurera [Apache Ranger](https://hortonworks.com/apache/ranger/) för att ange åtkomstkontrollprinciper för Hive. Den här funktionen gör att anställda kan komma åt bara så mycket information som de behöver för att utföra sitt arbete. SSH-åtkomst till klustret är också begränsad till endast administratören.

## <a name="auditing"></a>Granskning
Granska all åtkomst till klusterresurserna och data, krävs för att spåra obehörig eller oavsiktlig åtkomst av resurser. Det är lika viktigt som att skydda HDInsight-klusterresurserna från obehöriga användare och skydda data. 

Administratören kan visa och rapportera all åtkomst till HDInsight-klusterresurser och data. Administratören kan också visa och rapportera alla ändringar åtkomstkontrollprinciper som skapats i stöds av Apache Ranger-slutpunkter. 

Ett HDInsight-kluster med ESP använder det välbekanta Apache Ranger-Användargränssnittet för att söka igenom granskningsloggar. På backend-servern använder Ranger [Apache Solr](https://hortonworks.com/apache/solr/) för att lagra och söka igenom loggar.

## <a name="encryption"></a>Kryptering
Skydda data är viktigt för att uppfylla organisationens säkerhets- och krav. Tillsammans med att begränsa åtkomsten till data från obehöriga anställda, bör du krypterar den. 

Båda datalagren för HDInsight-kluster – Azure Blob storage och Azure Data Lake Storage Gen1/Gen2 – stöd för transparent serversidan [kryptering av data](../../storage/common/storage-service-encryption.md) i vila. Skydda HDInsight-kluster fungerar sömlöst med den här funktionen för serversidan kryptering av data i vila.

## <a name="next-steps"></a>Nästa steg

* [Planera för HDInsight-kluster med hjälp av ESP](apache-domain-joined-architecture.md)
* [Konfigurera HDInsight-kluster med ESP](apache-domain-joined-configure.md)
* [Hantera HDInsight-kluster med ESP](apache-domain-joined-manage.md)
* [Konfigurera Apache Hive-principer för HDInsight-kluster med ESP](apache-domain-joined-run-hive.md)
* [Använda SSH med HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined)

