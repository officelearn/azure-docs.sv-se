---
title: Domänansluten Azure HDInsight-arkitektur | Microsoft Docs
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
ms.openlocfilehash: 1f51a1fbb38bc27d15b7a45ca4783508d863fee5
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39112635"
---
# <a name="plan-azure-domain-joined-hadoop-clusters-in-hdinsight"></a>Planera Azure-domänanslutna Hadoop-kluster i HDInsight

Standard Azure HDInsight-kluster är en enskild användare-kluster. Det är lämpligt för de flesta företag som har små applikationsteam som att skapa stora arbetsbelastningar. Varje användare kan skapa ett dedikerat kluster på begäran och förstöra det när det inte behövs längre. 

Många företag har flyttat mot en modell där kluster hanteras av IT-team och flera applikationsteam delar kluster. Dessa större företag ha flera användare åtkomst till varje kluster i Azure HDInsight.

HDInsight bygger på populära identitetsprovidern – Active Directory--på ett hanterat sätt. Genom att integrera HDInsight med [Azure Active Directory Domain Services (Azure AD DS)](../../active-directory-domain-services/active-directory-ds-overview.md), du kan komma åt klustren med hjälp av autentiseringsuppgifter för domänen. 

Virtuella datorer (VM) i HDInsight är domänansluten till den angivna domänen. Detta visas alla tjänster som körs på HDInsight (Ambari, Hive-server, Ranger, Spark thrift-server och andra) fungerar sömlöst för autentiserade användare. Administratörer kan sedan skapa stark auktoriseringsprinciper med hjälp av Apache Ranger för att tillhandahålla rollbaserad åtkomstkontroll för resurser i klustret.


## <a name="integrate-hdinsight-with-active-directory"></a>Integrera HDInsight med Active Directory

Hadoop för öppen källkod använder Kerberos för autentisering och säkerhet. Därför är HDInsight-klusternoder anslutna till en domän till en domän som hanteras av Azure AD DS. Säkerhet för Kerberos har konfigurerats för Hadoop-komponenter på klustret. 

För varje komponent i Hadoop skapas automatiskt ett huvudnamn för tjänsten. En motsvarande dator huvudnamn skapas också för varje dator som är ansluten till domänen. För att lagra dessa service och datorn säkerhetsobjekt, måste du ange en organisationsenhet (OU) i domänkontrollanten (Azure AD DS), där dessa säkerhetsobjekt är placerade. 

Sammanfattningsvis, måste du konfigurera en miljö med:

- En Active Directory-domän (hanteras av Azure AD DS).
- Säkert LDAP (LDAPS) aktiverat i Azure AD DS.
- Rätt nätverket från det virtuella nätverket för HDInsight till det Azure AD DS virtuella nätverket, om du väljer separata virtuella nätverk för dessa. En virtuell dator i det virtuella nätverket HDInsight ska ha åtkomst till Azure AD DS via vnet-peering. Om HDInsight och Azure AD DS distribueras i samma virtuella nätverk, anslutningen tillhandahålls automatiskt och ingen ytterligare åtgärd krävs.
- En Organisationsenhet [skapas på Azure AD DS](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md).
- Ett tjänstkonto som har behörighet att:
    - Skapa tjänstens huvudnamn i Organisationsenheten.
    - Ansluta datorer till domänen och skapa huvudnamn för datorn i Organisationsenheten.

I följande skärmbild visas en Organisationsenhet som skapats i contoso.com. Den visar även vissa av tjänstens huvudnamn och datorn säkerhetsobjekt.

![Organisationsenhet för domänanslutna HDInsight-kluster](./media/apache-domain-joined-architecture/hdinsight-domain-joined-ou.png).

## <a name="set-up-different-domain-controllers"></a>Konfigurera olika domänkontrollanter
HDInsight stöder för närvarande endast Azure AD DS som den huvudsakliga domänkontrollant som klustret använder för Kerberos-kommunikation. Men andra avancerade inställningar för Active Directory är möjligt, så länge som en sådan konfiguration leder till att aktivera Azure AD DS för HDInsight-åtkomst.

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services
[Azure AD DS](../../active-directory-domain-services/active-directory-ds-overview.md) tillhandahåller en hanterad domän som är helt kompatibel med Windows Server Active Directory. Microsoft hand tar om hantera, uppdatera och övervakning domän i en konfiguration med hög tillgänglighet (HA). Du kan distribuera ett kluster utan att behöva bekymra dig om hur du underhåller domänkontrollanter. 

Användare, grupper och lösenord synkroniseras från Azure Active Directory (AD Azure). Enkelriktad synkronisering från din Azure AD-instans till Azure AD DS gör det möjligt för användare att logga in till klustret med hjälp av samma företagets autentiseringsuppgifter. 

Mer information finns i [konfigurera domänanslutna HDInsight-kluster med Azure AD DS](./apache-domain-joined-configure-using-azure-adds.md).

### <a name="on-premises-active-directory-or-active-directory-on-iaas-vms"></a>Den lokala Active Directory eller Active Directory på virtuella IaaS-datorer

Om du har en lokal Active Directory-instans eller mer komplexa Active Directory-inställningar för din domän, kan du synkronisera dessa identiteter till Azure AD med hjälp av Azure AD Connect. Sedan kan du aktivera Azure AD DS på den Active Directory-klienten. 

Eftersom Kerberos är beroende av hashvärden för lösenord, måste du [aktivera lösenordshashsynkronisering på Azure AD DS](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md). Om du använder federation med Active Directory Federation Services (AD FS), kan du också konfigurera lösenordshashsynkronisering som en säkerhetskopia om det inte går att AD FS-infrastrukturen. Mer information finns i [aktivera lösenordshashsynkronisering med Azure AD Connect-synkronisering](../../active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md). 

Med hjälp av en lokal Active Directory eller Active Directory på virtuella IaaS-datorer separat, utan Azure AD och Azure AD DS, inte är en konfiguration som stöds för domänanslutna HDInsight-kluster.

## <a name="next-steps"></a>Nästa steg
* [Konfigurera domänanslutna HDInsight-kluster](apache-domain-joined-configure-using-azure-adds.md)
* [Konfigurera Hive-principer för domänanslutna HDInsight-kluster](apache-domain-joined-run-hive.md)
* [Hantera domänanslutna HDInsight-kluster](apache-domain-joined-manage.md) 
