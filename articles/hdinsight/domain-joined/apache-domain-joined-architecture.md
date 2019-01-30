---
title: Azure HDInsight-arkitektur med Enterprise Security Package
description: Lär dig hur du planerar HDInsight säkerhet med Enterprise Security Package.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: omidm
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 3237932c66c77f979c4e95798163621e65735bed
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55247161"
---
# <a name="use-enterprise-security-package-in-hdinsight"></a>Använda Enterprise Security Package i HDInsight

Standard Azure HDInsight-kluster är en enskild användare-kluster. Det är lämpligt för de flesta företag som har små applikationsteam som att skapa stora arbetsbelastningar. Varje användare kan skapa ett dedikerat kluster på begäran och förstöra det när det inte behövs längre. 

Många företag har flyttat mot en modell där kluster hanteras av IT-team och flera applikationsteam delar kluster. Dessa större företag ha flera användare åtkomst till varje kluster i Azure HDInsight.

HDInsight bygger på populära identitetsprovidern – Active Directory--på ett hanterat sätt. Genom att integrera HDInsight med [Azure Active Directory Domain Services (Azure AD DS)](../../active-directory-domain-services/active-directory-ds-overview.md), du kan komma åt klustren med hjälp av autentiseringsuppgifter för domänen. 

Virtuella datorer (VM) i HDInsight är domänansluten till den angivna domänen. Alla tjänster som körs på HDInsight (Apache Ambari, server för Apache Hive, Apache Ranger, Apache Spark thrift-server och andra) fungerar därför sömlöst för autentiserade användare. Administratörer kan sedan skapa stark auktoriseringsprinciper med hjälp av Apache Ranger för att tillhandahålla rollbaserad åtkomstkontroll för resurser i klustret.

## <a name="integrate-hdinsight-with-active-directory"></a>Integrera HDInsight med Active Directory

Apache Hadoop för öppen källkod använder Kerberos för autentisering och säkerhet. Därför är HDInsight-klusternoder med Enterprise Security Package (ESP) anslutna till en domän som hanteras av Azure AD DS. Säkerhet för Kerberos har konfigurerats för Hadoop-komponenter på klustret. 

Följande skapas automatiskt:
- ett huvudnamn för tjänsten för varje Hadoop-komponent 
- ett huvudnamn för datorn för varje dator som är ansluten till domänen
- en organisationsenhet (OU) för varje kluster att lagra dessa huvudnamn för tjänsten och datorer 

Sammanfattningsvis, måste du konfigurera en miljö med:

- En Active Directory-domän (hanteras av Azure AD DS).
- Säkert LDAP (LDAPS) aktiverat i Azure AD DS.
- Rätt nätverket från det virtuella nätverket för HDInsight till det Azure AD DS virtuella nätverket, om du väljer separata virtuella nätverk för dessa. En virtuell dator i det virtuella nätverket HDInsight ska ha åtkomst till Azure AD DS via vnet-peering. Om HDInsight och Azure AD DS distribueras i samma virtuella nätverk, anslutningen tillhandahålls automatiskt och ingen ytterligare åtgärd krävs.

## <a name="set-up-different-domain-controllers"></a>Konfigurera olika domänkontrollanter
HDInsight stöder för närvarande endast Azure AD DS som den huvudsakliga domänkontrollant som klustret använder för Kerberos-kommunikation. Men andra avancerade inställningar för Active Directory är möjligt, så länge som en sådan konfiguration leder till att aktivera Azure AD DS för HDInsight-åtkomst.

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services
[Azure AD DS](../../active-directory-domain-services/active-directory-ds-overview.md) tillhandahåller en hanterad domän som är helt kompatibel med Windows Server Active Directory. Microsoft hand tar om hantera, uppdatera och övervakning domän i en konfiguration med hög tillgänglighet (HA). Du kan distribuera ett kluster utan att behöva bekymra dig om hur du underhåller domänkontrollanter. 

Användare, grupper och lösenord synkroniseras från Azure Active Directory (AD Azure). Enkelriktad synkronisering från din Azure AD-instans till Azure AD DS gör det möjligt för användare att logga in till klustret med hjälp av samma företagets autentiseringsuppgifter. 

Mer information finns i [konfigurera HDInsight-kluster med hjälp av Azure AD DS ESP](./apache-domain-joined-configure-using-azure-adds.md).

### <a name="on-premises-active-directory-or-active-directory-on-iaas-vms"></a>Den lokala Active Directory eller Active Directory på virtuella IaaS-datorer

Om du har en lokal Active Directory-instans eller mer komplexa Active Directory-inställningar för din domän, kan du synkronisera dessa identiteter till Azure AD med hjälp av Azure AD Connect. Sedan kan du aktivera Azure AD DS på den Active Directory-klienten. 

Eftersom Kerberos är beroende av hashvärden för lösenord, måste du [aktivera lösenordshashsynkronisering på Azure AD DS](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md). 

Om du använder federation med Active Directory Federation Services (ADFS), måste du aktivera lösenordshashsynkronisering (en rekommenderad konfigurera, se [detta](https://youtu.be/qQruArbu2Ew)) som hjälper även med haveriberedskap om det inte går att din AD FS-infrastruktur och skydd av läckta autentiseringsuppgifter. Mer information finns i [aktivera lösenordshashsynkronisering med Azure AD Connect-synkronisering](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md). 

Använda en lokal Active Directory eller Active Directory på egen hand utan Azure AD och Azure AD DS, IaaS-datorer är inte en konfiguration som stöds för HDInsight-kluster med ESP.

Om federation används och lösenords-hash är synkroniserade correcty, men du får autentiseringsfel,. Kontrollera om powershell tjänstens huvudnamn i molnet lösenord autentisering är aktiverad, om inte, måste du ställa in en [Start sfär identifiering (HRD ) princip](../../active-directory/manage-apps/configure-authentication-for-federated-users-portal.md) för din AAD-klient. Kontrolera och set HRD-princip:

 1. Installera AzureAD powershell-modulen

 ```
  Install-Module AzureAD
 ```

 2. ```Connect-AzureAD``` med hjälp av autentiseringsuppgifterna för en global administratör (Innehavaradministratör)

 3. Kontrollera om det redan har skapats ”Azure powershell” tjänstens huvudnamn

```
 Get-AzureADServicePrincipal -SearchString "1950a258-227b-4e31-a9cf-717495945fc2"
```

 4. Om det inte finns skapar du tjänstens huvudnamn

```
 New-AzureADServicePrincipal -AppId 1950a258-227b-4e31-a9cf-717495945fc2
```

 5. Koppla principen till den här tjänstens huvudnamn: 

```
 $policy = New-AzureADPolicy -Definition @("{"HomeRealmDiscoveryPolicy":{"AllowCloudPasswordValidation":true}}") -DisplayName EnableDirectAuth -Type HomeRealmDiscoveryPolicy

 Add-AzureADServicePrincipalPolicy -Id <Service Principal ID> -refObjectID $policy.ID
```

## <a name="next-steps"></a>Nästa steg

* [Konfigurera HDInsight-kluster med ESP](apache-domain-joined-configure-using-azure-adds.md)
* [Konfigurera Apache Hive-principer för HDInsight-kluster med ESP](apache-domain-joined-run-hive.md)
* [Hantera HDInsight-kluster med ESP](apache-domain-joined-manage.md) 
