---
title: Arkitektur för domänanslutna Azure HDInsight | Microsoft Docs
description: Lär dig hur du planerar för domänansluten HDInsight.
services: hdinsight
documentationcenter: ''
author: omidm1
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7dc6847d-10d4-4b5c-9c83-cc513cf91965
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: ''
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: omidm
ms.openlocfilehash: f4380f5d6ec379d5807f697294623a672bd270ae
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34715249"
---
# <a name="plan-azure-domain-joined-hadoop-clusters-in-hdinsight"></a>Planera Azure-domänanslutna Hadoop-kluster i HDInsight

Standard HDInsight-klustret är ett kluster med en användare. Det passar för de flesta företag som har små applikationsteam som bygger stordataarbetsbelastningar. Varje användare kan ha ett annat kluster dedikerade till sig själv på begäran och förstöras när den inte längre behövs. Men igång många företag går mot en modell där klustren hanteras av IT-team och flera program team resursen kluster. Därför krävs flera användare åtkomst till klustret i Azure HDInsight för dessa större företag.

HDInsight är beroende av den mest populära identitetsleverantören--Active Directory (AD) i ett hanterat sätt. Genom att integrera HDInsight med [Azure Active Directory Domain Services (AAD-DS)](../../active-directory-domain-services/active-directory-ds-overview.md), du kan komma åt kluster med hjälp av autentiseringsuppgifter för domänen. De virtuella datorerna i HDInsight är domänansluten till den angivna domänen, så alla tjänster som körs på HDInsight (Ambari, Hive server Ranger Spark thrift-servern och andra) fungerar sömlöst för den autentiserade användaren. Administratörer kan skapa starka auktoriseringsprinciper som använder Apache Ranger för att tillhandahålla rollbaserad åtkomstkontroll för resurser i klustret.


## <a name="integrate-hdinsight-with-active-directory"></a>Integrera HDInsight med Active Directory

Hadoop för öppen källkod som bygger på Kerberos för att tillhandahålla autentisering och säkerhet. HDInsight-klusternoder är därför domänansluten till en domän som hanteras av AAD-DS. Kerberos-säkerheten är konfigurerad för Hadoop-komponenter på klustret. För varje Hadoop-komponenter skapas ett huvudnamn för tjänsten automatiskt. En motsvarande dator huvudnamn skapas också för varje dator som är ansluten till domänen. För att lagra dessa tjänster och datorer säkerhetsobjekt krävs det att tillhandahålla en organisationsenhet (OU) i domänkontrollanten (AAD-DS), där dessa säkerhetsobjekt är placerade. 

Sammanfattningsvis, måste du ställa in en miljö med:

- En Active Directory-domän (hanteras av AAD-DS)
- Säkert LDAP (LDAPS) aktiverat i AAD-DS.
- God nätverk anslutning från Hdinsight's VNET till AAD-DS-VNET om du väljer du separera Vnet för för dem. En virtuell dator i HDI VNET ska ha en fri AAD-DS med hjälp av VNET-peering. Om både HDI och AAD-DS distribueras i samma virtuella nätverk, anslutningen tillhandahålls automatiskt och ingen ytterligare åtgärd krävs.
- En organisationsenhet (OU) [skapas på AAD-DS](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md)
- Ett konto som har behörighet att:
    - Skapa tjänstens huvudnamn i Organisationsenheten.
    - Ansluta datorer till domänen och skapa dator säkerhetsobjekt i Organisationsenheten.

Följande skärmbild visar en Organisationsenhet som skapats i contoso.com. Några av tjänstens huvudnamn och datorn säkerhetsobjekt som visas i skärmbilden samt.

![Domän ansluten HDInsight-kluster ou](./media/apache-domain-joined-architecture/hdinsight-domain-joined-ou.png).

### <a name="different-domain-controllers-setup"></a>Annan domän domänkontrollanter installationen:
HDInsight stöder för närvarande endast AAD-DS som den huvudsakliga domänkontrollanten att klustret ska kommunicera med Kerberise klustret. Andra avancerade AD-installationer är dock också möjligt så länge det leder till att aktivera AAD-DS för HDI-åtkomst.

- **[Azure Active Directory Domain Services (AAD-DS)](../../active-directory-domain-services/active-directory-ds-overview.md)**: den här tjänsten tillhandahåller en hanterad domän som är helt kompatibel med Windows Server Active Directory. Microsoft hand tar om hantering, uppdatering och övervakning domän i en inställning för hög Available(HA). Du kan distribuera klustret utan att bekymra dig om att hantera domänkontrollanter. Användare, grupper och lösenord synkroniseras från din Azure Active Directory(AAD) [enkelriktad synkronisering från AAD till AAD-DS] att användare kan logga in till klustret med samma företagets autentiseringsuppgifter. Mer information finns i [hur du konfigurerar domänanslutna HDInsight-kluster med AAD-DS](./apache-domain-joined-configure-using-azure-adds.md).
- **Lokala AD eller AD på IaaS-VM**: Om du har en lokal AD eller andra mer komplexa AD inställningarna för din domän som, du kan synkronisera de identiteterna som AAD med hjälp av AD Connect och sedan aktivera AAD-DS på den AD-klient. Eftersom Kerberos är beroende av lösenordshashvärden, behöver du [aktivera lösenordshashsynkronisering på AAD-DS](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md). Om du använder federation med AD-federation Services (ADFS), konfigurera du om du vill lösenordshashsynkronisering som en säkerhetskopia om det inte går att din AD FS-infrastruktur. Mer information finns i [aktivera lösenordshashsynkronisering med AAD Connect sync](../../active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md). Använda lokala AD eller AD på IaaS-VM enbart, är utan AAD och AAD-DS inte en konfiguration som stöds för att ansluta till HDI klustret domän.

## <a name="next-steps"></a>Nästa steg
* [Konfigurera domänanslutna HDInsight-kluster](apache-domain-joined-configure-using-azure-adds.md).
* [Konfigurera Hive principer för domänanslutna HDInsight-kluster](apache-domain-joined-run-hive.md).
* [Hantera domänanslutna HDInsight-kluster](apache-domain-joined-manage.md). 
