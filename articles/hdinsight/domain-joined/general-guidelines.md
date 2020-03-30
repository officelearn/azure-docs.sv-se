---
title: Allmänna riktlinjer för företagssäkerhet i Azure HDInsight
description: Några metodtips som bör göra distribution och hantering av enterprise security package enklare.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: be6c1fdc5deb6d541656c198469822dae0a5f7c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77463211"
---
# <a name="enterprise-security-general-information-and-guidelines-in-azure-hdinsight"></a>Allmän information och riktlinjer för företagssäkerhet i Azure HDInsight

När du distribuerar ett säkert HDInsight-kluster finns det några metodtips som bör göra distributionen och klusterhanteringen enklare. Här diskuteras en del allmän information och riktlinjer.

## <a name="use-of-secure-cluster"></a>Användning av säkert kluster

### <a name="recommended"></a>Rekommenderas

* Klustret kommer att användas av flera användare samtidigt.
* Användare har olika åtkomstnivåer till samma data.

### <a name="not-necessary"></a>Inte nödvändigt

* Du kommer bara att köra automatiserade jobb (som ett enda användarkonto), ett standardkluster är tillräckligt bra.
* Du kan göra dataimporten med hjälp av ett standardkluster och använda samma lagringskonto i ett annat säkert kluster där användare kan köra analysjobb.

## <a name="use-of-local-account"></a>Användning av lokalt konto

* Om du använder ett delat användarkonto eller ett lokalt konto blir det svårt att identifiera vem som använde kontot för att ändra konfigurationen eller tjänsten.
* Det är problematiskt att använda lokala konton när användare inte längre ingår i organisationen.

## <a name="ranger"></a>Ranger

### <a name="policies"></a>Principer

* Som standard använder Ranger **Neka** som princip.

* När dataåtkomst görs via en tjänst där auktorisering är aktiverat:
  * Ranger auktorisering plugin anropas och ges kontexten för begäran.
  * Ranger tillämpar de principer som konfigurerats för tjänsten. Om Ranger-principerna misslyckas skjuts åtkomstkontrollen upp till filsystemet. Vissa tjänster som MapReduce kontrollerar bara om filen / mappen ägs av samma användare som skickar begäran. Tjänster som Hive, kontrollera om det finns behörigheter för ägarskap eller lämpliga filsystem (`rwx`).

* För Hive, förutom att ha behörighet att göra Skapa / Uppdatera / `rwx`Ta bort behörigheter, bör användaren ha behörigheter på katalogen på lagring och alla underkataloger.

* Principer kan tillämpas på grupper (att föredra) i stället för individer.

* Ranger authorizer utvärderar alla Ranger-principer för den tjänsten för varje begäran. Den här utvärderingen kan påverka tiden för att acceptera jobbet eller frågan.

### <a name="storage-access"></a>Åtkomst till lagring

* Om lagringstypen är WASB, är ingen OAuth-token inblandad.
* Om Ranger har utfört auktoriseringen sker lagringsåtkomsten med den hanterade identiteten.
* Om Ranger inte utförde någon auktorisering sker lagringsåtkomsten med användarens OAuth-token.

### <a name="hierarchical-name-space"></a>Hierarkiskt namnutrymme

När hierarkiskt namnutrymme inte är aktiverat:

* Det finns inga ärvda behörigheter.
* Endast filsystembehörighet som fungerar är **Storage Data XXXX** RBAC-roll som ska tilldelas användaren direkt i Azure-portalen.

### <a name="default-hdfs-permissions"></a>Standardbehörighet för HDFS

* Som standard har användarna inte åtkomst **/** till mappen på HDFS (de måste vara i rollen lagring blob ägare för att få tillgång till lyckas).
* För mellanlagringskatalogen för mapreduce och andra skapas `sticky _wx` en användarspecifik katalog och ges behörigheter. Användare kan skapa filer och mappar under, men kan inte titta på andra objekt.

### <a name="url-auth"></a>URL-auth

Om url-autentiseringen är aktiverad:

* Den config kommer att innehålla vilka prefix som `adl://`omfattas av url auth (som ).
* Om åtkomsten är för den här webbadressen kontrollerar Ranger om användaren finns i listan över tillåtna.
* Ranger kommer inte att kontrollera någon av de finkorniga politik.

## <a name="resource-groups"></a>Resursgrupper

Använd en ny resursgrupp för varje kluster så att du kan skilja mellan klusterresurser.

## <a name="nsgs-firewalls-and-internal-gateway"></a>NSG, brandväggar och intern gateway

* Använd nätverkssäkerhetsgrupper (NSG) för att låsa virtuella nätverk.
* Använd brandväggen för att hantera principer för utgående åtkomst.
* Använd den interna gatewayen som inte är öppen för det offentliga internet.

## <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) är Microsofts molnbaserade identitets- och åtkomsthanteringstjänst.

### <a name="policies"></a>Principer

* Inaktivera principen för villkorlig åtkomst med hjälp av principen IP-adress. Detta kräver att tjänstslutpunkter aktiveras på VNETs där klustren distribueras. Om du använder en extern tjänst för MFA (något annat än AAD) fungerar inte IP-adressprincipen

* `AllowCloudPasswordValidation`krävs för federerade användare. Eftersom HDInsight använder användarnamnet/lösenordet direkt för att hämta token från Azure AD, måste den här principen aktiveras för alla federerade användare.

* Aktivera tjänstslutpunkter om du behöver kringgå villkorlig åtkomst med betrodda IPs.

### <a name="groups"></a>Grupper

* Distribuera alltid kluster med en grupp.
* Använd Azure AD för att hantera gruppmedlemskap (enklare än att försöka hantera de enskilda tjänsterna i klustret).

### <a name="user-accounts"></a>Användarkonton

* Använd ett unikt användarkonto för varje scenario. Använd till exempel ett konto för import, använd ett annat för frågejobb eller andra bearbetningsjobb.
* Använd gruppbaserade Ranger-principer i stället för enskilda principer.
* Ha en plan för hur du hanterar användare som inte längre ska ha åtkomst till kluster.

## <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services

[Azure Active Directory Domain Services](../../active-directory-domain-services/overview.md) (Azure AD DS) tillhandahåller hanterade domäntjänster som domänanslutning, grupprincip, LDAP (Lightweight Directory Access Protocol) och Kerberos/NTLM-autentisering som är helt kompatibel med Active Directory i Windows Server.

Azure AD DS krävs för att säkra kluster ska kunna ansluta till en domän.
HDInsight kan inte vara beroende av lokala domänkontrollanter eller anpassade domänkontrollanter, eftersom det introducerar för många felpunkter, autentiseringsuppgifter, DNS-behörigheter och så vidare. Mer information finns i [Vanliga frågor om Azure AD DS](../../active-directory-domain-services/faqs.md).

### <a name="azure-ad-ds-instance"></a>Azure AD DS-instans

* Skapa instansen `.onmicrosoft.com domain`med . På så sätt kommer det inte att finnas flera DNS-servrar som betjänar domänen.
* Skapa ett självsignerat certifikat för LDAPS och ladda upp det till Azure AD DS.
* Använd ett peer-virtuellt nätverk för att distribuera kluster (när du har ett antal team som distribuerar HDInsight ESP-kluster, kommer detta att vara till hjälp). Detta säkerställer att du inte behöver öppna portar (NSG) i det virtuella nätverket med domänkontrollant.
* Konfigurera DNS för det virtuella nätverket korrekt (Azure AD DS-domännamnet bör matchas utan några hosts-filposter).
* Om du begränsar utgående trafik kontrollerar du att du har läst igenom [brandväggsstödet i HDInsight](../hdinsight-restrict-outbound-traffic.md)

### <a name="properties-synced-from-azure-ad-to-azure-ad-ds"></a>Egenskaper synkroniserade från Azure AD till Azure AD DS

* Azure AD-anslutning synkroniseras från lokal till Azure AD.
* Azure AD DS synkroniseras från Azure AD.

Azure AD DS synkroniserar objekt från Azure AD med jämna mellanrum. Azure AD DS-bladet på Azure-portalen visar synkroniseringsstatusen. Under varje fas av synkronisering kan unika egenskaper hamna i konflikt och döpas om. Var uppmärksam på egenskapsmappningen från Azure AD till Azure AD DS.

Mer information finns i [Azure AD UserPrincipalName population](../../active-directory/hybrid/plan-connect-userprincipalname.md)och hur Azure AD [DS-synkronisering fungerar](../../active-directory-domain-services/synchronization.md).

### <a name="password-hash-sync"></a>Synkronisering av lösenordsh hash

* Lösenord synkroniseras på ett annat sätt än andra objekttyper. Endast icke-reversibelt lösenordshiseringar synkroniseras i Azure AD och Azure AD DS
* Lokal till Azure AD måste aktiveras via AD Connect
* Azure AD till Azure AD DS-synkronisering är automatisk (svarstider är under 20 minuter).
* Lösenords hashar synkroniseras endast när ett ändrat lösenord har ändrats. När du aktiverar synkronisering av lösenord hash synkroniseras inte alla befintliga lösenord automatiskt eftersom de lagras oåterkalleligt. När du ändrar lösenordet synkroniseras lösenordsharna.

### <a name="computer-objects-location"></a>Plats för datorobjekt

Varje kluster är associerat med en enda organisationsenhet. En intern användare etableras i organisationsenheten. Alla noder är domän anslutna till samma organisationsenhet.

### <a name="active-directory-administrative-tools"></a>Administrationsverktyg för Active Directory

Steg om hur du installerar administrationsverktygen för Active Directory på en virtuell Windows Server-dator finns i [Installera hanteringsverktyg](../../active-directory-domain-services/tutorial-create-management-vm.md).

## <a name="troubleshooting"></a>Felsökning

### <a name="cluster-creation-fails-repeatedly"></a>Klusterskapande misslyckas upprepade gånger

Vanligaste orsakerna:

* DNS-konfigurationen är inte korrekt, domänkoppling av klusternoder misslyckas.
* NSG är för restriktiva, vilket förhindrar domänkoppling.
* Hanterad identitet har inte tillräcklig behörighet.
* Klusternamnet är inte unikt på de första sex tecknen (antingen med ett annat live-kluster eller med ett borttaget kluster).

## <a name="next-steps"></a>Nästa steg

* [Konfigurationer av företagssäkerhetspaket med Azure Active Directory Domain Services i HDInsight](./apache-domain-joined-configure-using-azure-adds.md)

* [Synkronisera Azure Active Directory-användare med ett HDInsight-kluster](../hdinsight-sync-aad-users-to-cluster.md).
