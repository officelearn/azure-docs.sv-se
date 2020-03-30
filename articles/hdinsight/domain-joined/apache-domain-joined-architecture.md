---
title: Azure HDInsight-arkitektur med Enterprise Security Package
description: Lär dig hur du planerar Azure HDInsight-säkerhet med Enterprise Security Package.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: omidm
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/11/2020
ms.openlocfilehash: 452a3b04637126b40aca907178bebd6f74ec4481
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79365804"
---
# <a name="use-enterprise-security-package-in-hdinsight"></a>Använda Enterprise Security Package i HDInsight

Azure HDInsight-standardklustret är ett kluster med en användare. Den är lämplig för de flesta företag som har mindre programteam som bygger stora dataarbetsbelastningar. Varje användare kan skapa ett dedikerat kluster på begäran och förstöra det när det inte behövs längre.

Många företag har gått mot en modell där IT-team hanterar kluster och flera programteam delar kluster. Dessa större företag behöver fleranvändaråtkomst till varje kluster i Azure HDInsight.

HDInsight förlitar sig på en populär identitetsleverantör - Active Directory - på ett hanterat sätt. Genom att integrera HDInsight med [Azure Active Directory Domain Services (Azure AD DS)](../../active-directory-domain-services/overview.md)kan du komma åt klustren med hjälp av dina domänautentiseringsuppgifter.

De virtuella datorerna i HDInsight är domän som är anslutna till din angivna domän. Så, alla tjänster som körs på HDInsight (Apache Ambari, Apache Hive server, Apache Ranger, Apache Spark sparsamhet server och andra) fungerar sömlöst för autentiserade användaren. Administratörer kan sedan skapa starka auktoriseringsprinciper genom att använda Apache Ranger för att tillhandahålla rollbaserad åtkomstkontroll för resurser i klustret.

## <a name="integrate-hdinsight-with-active-directory"></a>Integrera HDInsight med Active Directory

Apache Hadoop med öppen källkod förlitar sig på Kerberos-protokollet för autentisering och säkerhet. Därför ansluts HDInsight-klusternoder med ESP (Enterprise Security Package) till en domän som hanteras av Azure AD DS. Kerberos-säkerhet har konfigurerats för Hadoop-komponenterna i klustret.

Följande saker skapas automatiskt:

- Ett servicehuvudnamn för varje Hadoop-komponent
- Ett maskinhuvudnamn för varje dator som är ansluten till domänen
- En organisationsenhet (OU) för varje kluster för att lagra dessa service- och maskinhuvudnamn

Sammanfattningsvis måste du ställa in en miljö med:

- En Active Directory-domän (hanteras av Azure AD DS). **Domännamnet måste vara 39 tecken eller mindre för att fungera med Azure HDInsight.**
- Säker LDAP (LDAPS) aktiverad i Azure AD DS.
- Korrekt nätverksanslutning från det virtuella HDInsight-nätverket till det virtuella Azure AD DS-nätverket, om du väljer separata virtuella nätverk för dem. En virtuell dator i det virtuella HDInsight-nätverket bör ha en siktlinje till Azure AD DS via virtuell nätverks peering. Om HDInsight och Azure AD DS distribueras i samma virtuella nätverk tillhandahålls anslutningen automatiskt och inga ytterligare åtgärder behövs.

## <a name="set-up-different-domain-controllers"></a>Konfigurera olika domänkontrollanter

HDInsight stöder för närvarande endast Azure AD DS som huvuddomänkontrollant som klustret använder för Kerberos-kommunikation. Men andra komplexa Active Directory-inställningar är möjliga, så länge en sådan konfiguration leder till att aktivera Azure AD DS för HDInsight-åtkomst.

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services

[Azure AD DS](../../active-directory-domain-services/overview.md) tillhandahåller en hanterad domän som är helt kompatibel med Active Directory i Windows Server. Microsoft tar hand om att hantera, korrigera och övervaka domänen i en högtillgänge (HA) setup. Du kan distribuera klustret utan att behöva behöva underhålla domänkontrollanter.

Användare, grupper och lösenord synkroniseras från Azure AD. Enkelriktad synkronisering från din Azure AD-instans till Azure AD DS gör det möjligt för användare att logga in i klustret med samma företagsautentiseringsuppgifter.

Mer information finns i [Konfigurera HDInsight-kluster med ESP med Azure AD DS](./apache-domain-joined-configure-using-azure-adds.md).

### <a name="on-premises-active-directory-or-active-directory-on-iaas-vms"></a>Lokala Active Directory- eller Active Directory-datorer på virtuella IAAS-datorer

Om du har en lokal Active Directory-instans eller mer komplexa Active Directory-inställningar för din domän kan du synkronisera dessa identiteter till Azure AD med hjälp av Azure AD Connect. Du kan sedan aktivera Azure AD DS på den Active Directory-klienten.

Eftersom Kerberos förlitar sig på lösenordshashar måste du [aktivera synkronisering av lösenord hash på Azure AD DS](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md).

Om du använder federation med AD FS (Active Directory Federation Services) måste du aktivera synkronisering av lösenordshã¤nde. (För en rekommenderad installation, se [den här videon](https://youtu.be/qQruArbu2Ew).) Synkronisering av lösenord hash-synkronisering hjälper till med haveriberedskap om din AD FS-infrastruktur misslyckas, och den bidrar också till att ge ett skydd för läckt autentiseringsuppgifter. Mer information finns i [Aktivera synkronisering av lösenordsh hash med Azure AD Connect-synkronisering](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md).

Att använda lokala Active Directory eller Active Directory enbart på virtuella IaaS-datorer, utan Azure AD och Azure AD DS, är inte en konfiguration som stöds för HDInsight-kluster med ESP.

Om federationen används och lösenordsharhar synkroniseras korrekt, men du får autentiseringsfel, kontrollerar du om autentisering av molnlösenord är aktiverat för PowerShell-tjänstens huvudnamn. Om inte, måste du ange en [HRD-princip (Home Realm Discovery)](../../active-directory/manage-apps/configure-authentication-for-federated-users-portal.md) för din Azure AD-klientorganisation. Så här kontrollerar och ställer du in HRD-principen:

1. Installera förhandsversionen av [Azure AD PowerShell-modulen](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2).

   ```powershell
   Install-Module AzureAD
   ```

2. Anslut med autentiseringsuppgifter för global administratör (klientadministratör).

   ```powershell
   Connect-AzureAD
   ```

3. Kontrollera om microsoft Azure PowerShell-tjänstens huvudnamn redan har skapats.

   ```powershell
   Get-AzureADServicePrincipal -SearchString "Microsoft Azure Powershell"
   ```

4. Om den inte finns skapar du tjänstens huvudnamn.

   ```powershell
   $powershellSPN = New-AzureADServicePrincipal -AppId 1950a258-227b-4e31-a9cf-717495945fc2
   ```

5. Skapa och koppla principen till den här tjänstens huvudnamn.

   ```powershell
    # Determine whether policy exists
    Get-AzureADPolicy | Where {$_.DisplayName -eq "EnableDirectAuth"}

    # Create if not exists
    $policy = New-AzureADPolicy `
        -Definition @('{"HomeRealmDiscoveryPolicy":{"AllowCloudPasswordValidation":true}}') `
        -DisplayName "EnableDirectAuth" `
        -Type "HomeRealmDiscoveryPolicy"

    # Determine whether a policy for the service principal exist
    Get-AzureADServicePrincipalPolicy `
        -Id $powershellSPN.ObjectId

    # Add a service principal policy if not exist
    Add-AzureADServicePrincipalPolicy `
        -Id $powershellSPN.ObjectId `
        -refObjectID $policy.ID
   ```

## <a name="next-steps"></a>Nästa steg

- [Konfigurera HDInsight-kluster med ESP](apache-domain-joined-configure-using-azure-adds.md)
- [Konfigurera Apache Hive-principer för HDInsight-kluster med ESP](apache-domain-joined-run-hive.md)
- [Hantera HDInsight-kluster med ESP](apache-domain-joined-manage.md)
