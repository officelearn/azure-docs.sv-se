---
title: Autentiseringsproblem i Azure HDInsight
description: Autentiseringsproblem i Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 11/08/2019
ms.openlocfilehash: 26eec9cdd327ceb51e72deb1d6f40d585ce368fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75896128"
---
# <a name="authentication-issues-in-azure-hdinsight"></a>Autentiseringsproblem i Azure HDInsight

I den här artikeln beskrivs felsökningssteg och möjliga lösningar för problem när du interagerar med Azure HDInsight-kluster.

På säkra kluster som backas upp av Azure Data Lake (Gen1 eller Gen2), när domänanvändare loggar in på klustertjänsterna via HDI Gateway (som att logga in på Apache Ambari-portalen), försöker HDI-gatewayen först hämta en OAuth-token från Azure Active Directory (Azure AD) först och hämta sedan en Kerberos-biljett från Azure AD DS. Autentisering kan misslyckas i något av dessa steg. Den här artikeln syftar till att felsöka några av dessa frågor.

När autentiseringen misslyckas uppmanas du att ange autentiseringsuppgifter. Om du avbryter den här dialogrutan skrivs felmeddelandet ut. Här är några av de vanligaste felmeddelandena:

## <a name="invalid_grant-or-unauthorized_client-50126"></a>invalid_grant eller unauthorized_client, 50126

### <a name="issue"></a>Problem

Inloggning misslyckas för federerade användare med felkod 50126 (inloggning lyckas för molnanvändare). Felmeddelandet liknar:

```
Reason: Bad Request, Detailed Response: {"error":"invalid_grant","error_description":"AADSTS70002: Error validating credentials. AADSTS50126: Invalid username or password\r\nTrace ID: 09cc9b95-4354-46b7-91f1-efd92665ae00\r\n Correlation ID: 4209bedf-f195-4486-b486-95a15b70fbe4\r\nTimestamp: 2019-01-28 17:49:58Z","error_codes":[70002,50126], "timestamp":"2019-01-28 17:49:58Z","trace_id":"09cc9b95-4354-46b7-91f1-efd92665ae00","correlation_id":"4209bedf-f195-4486-b486-95a15b70fbe4"}
```

### <a name="cause"></a>Orsak

Azure AD-felkod 50126 innebär att `AllowCloudPasswordValidation` principen inte har angetts av klienten.

### <a name="resolution"></a>Lösning

Företagsadministratören för Azure AD-klienten bör aktivera Azure AD för att använda lösenordshÃ¤nde för ADFS-stödda användare.  Använd `AllowCloudPasswordValidationPolicy` den som visas i artikeln [Använd Enterprise Security Package i HDInsight](../domain-joined/apache-domain-joined-architecture.md).

---

## <a name="invalid_grant-or-unauthorized_client-50034"></a>invalid_grant eller unauthorized_client, 50034

### <a name="issue"></a>Problem

Inloggningen misslyckas med felkod 50034. Felmeddelandet liknar:

```
{"error":"invalid_grant","error_description":"AADSTS50034: The user account Microsoft.AzureAD.Telemetry.Diagnostics.PII does not exist in the 0c349e3f-1ac3-4610-8599-9db831cbaf62 directory. To sign into this application, the account must be added to the directory.\r\nTrace ID: bbb819b2-4c6f-4745-854d-0b72006d6800\r\nCorrelation ID: b009c737-ee52-43b2-83fd-706061a72b41\r\nTimestamp: 2019-04-29 15:52:16Z", "error_codes":[50034],"timestamp":"2019-04-29 15:52:16Z","trace_id":"bbb819b2-4c6f-4745-854d-0b72006d6800", "correlation_id":"b009c737-ee52-43b2-83fd-706061a72b41"}
```

### <a name="cause"></a>Orsak

Användarnamnet är felaktigt (finns inte). Användaren använder inte samma användarnamn som används i Azure-portalen.

### <a name="resolution"></a>Lösning

Använd samma användarnamn som fungerar i portalen.

---

## <a name="invalid_grant-or-unauthorized_client-50053"></a>invalid_grant eller unauthorized_client, 50053

### <a name="issue"></a>Problem

Användarkontot är utelåst, felkod 50053. Felmeddelandet liknar:

```
{"error":"unauthorized_client","error_description":"AADSTS50053: You've tried to sign in too many times with an incorrect user ID or password.\r\nTrace ID: 844ac5d8-8160-4dee-90ce-6d8c9443d400\r\nCorrelation ID: 23fe8867-0e8f-4e56-8764-0cdc7c61c325\r\nTimestamp: 2019-06-06 09:47:23Z","error_codes":[50053],"timestamp":"2019-06-06 09:47:23Z","trace_id":"844ac5d8-8160-4dee-90ce-6d8c9443d400","correlation_id":"23fe8867-0e8f-4e56-8764-0cdc7c61c325"}
```

### <a name="cause"></a>Orsak

För många inloggningsförsök med ett felaktigt lösenord.

### <a name="resolution"></a>Lösning

Vänta i 30 minuter eller så, stoppa alla program som kanske försöker autentisera.

---

## <a name="invalid_grant-or-unauthorized_client-50053"></a>invalid_grant eller unauthorized_client, 50053

### <a name="issue"></a>Problem

Lösenordet har upphört att gälla, felkod 50053. Felmeddelandet liknar:

```
{"error":"user_password_expired","error_description":"AADSTS50055: Password is expired.\r\nTrace ID: 241a7a47-e59f-42d8-9263-fbb7c1d51e00\r\nCorrelation ID: c7fe4a42-67e4-4acd-9fb6-f4fb6db76d6a\r\nTimestamp: 2019-06-06 17:29:37Z","error_codes":[50055],"timestamp":"2019-06-06 17:29:37Z","trace_id":"241a7a47-e59f-42d8-9263-fbb7c1d51e00","correlation_id":"c7fe4a42-67e4-4acd-9fb6-f4fb6db76d6a","suberror":"user_password_expired","password_change_url":"https://portal.microsoftonline.com/ChangePassword.aspx"}
```

### <a name="cause"></a>Orsak

Lösenordet har upphört att gälla.

### <a name="resolution"></a>Lösning

Ändra lösenordet i Azure-portalen (på ditt lokala system) och vänta sedan i 30 minuter innan synkroniseringen kommer ikapp.

---

## <a name="interaction_required"></a>interaction_required

### <a name="issue"></a>Problem

Felmeddelandet `interaction_required`får .

### <a name="cause"></a>Orsak

Principen för villkorsstyrd åtkomst eller MFA används för användaren. Eftersom interaktiv autentisering inte stöds ännu, behöver användaren eller klustret undantas från MFA/villkorsstyrd åtkomst. Om du väljer att undanta klustret (IP-adressbaserad `ServiceEndpoints` undantagsprincip) kontrollerar du att AD är aktiverade för det virtuella nätverket.

### <a name="resolution"></a>Lösning

Använd principen för villkorlig åtkomst och undanta HDInisght-kluster från MFA som visas i [Konfigurera ett HDInsight-kluster med Enterprise Security Package med hjälp av Azure Active Directory Domain Services](./apache-domain-joined-configure-using-azure-adds.md).

---

## <a name="sign-in-denied"></a>Logga in nekad

### <a name="issue"></a>Problem

Inloggning nekas.

### <a name="cause"></a>Orsak

För att komma till det här skedet är din OAuth-autentisering inte ett problem, men Kerberos-autentisering är. Om det här klustret backas upp av ADLS har OAuth-inloggningen lyckats innan Kerberos-autentisering försöker. På WASB-kluster försöker inte OAuth-inloggningen. Det kan finnas många orsaker till Kerberos-fel - som om lösenordsharhar är inte synkroniserade, användarkontot utelåst i Azure AD DS och så vidare. Lösenord hashar synkronisering endast när användaren ändrar lösenord. När du skapar Azure AD DS-instansen börjar den synkronisera lösenord som ändras efter att den har skapats. Det synkroniserar inte lösenord som angavs innan starten.

### <a name="resolution"></a>Lösning

Om du tror att lösenorden inte är synkroniserade kan du prova att ändra lösenordet och vänta några minuter med att synkronisera.

Försök att SSH i en Du måste försöka autentisera (kinit) med samma användarautentiseringsuppgifter, från en dator som är ansluten till domänen. SSH i huvudet / kantnoden med en lokal användare och kör sedan kinit.

---

## <a name="kinit-fails"></a>kinit misslyckas

### <a name="issue"></a>Problem

Kinit misslyckas.

### <a name="cause"></a>Orsak

Varierar.

### <a name="resolution"></a>Lösning

För att kinit ska lyckas `sAMAccountName` måste du veta ditt (det här är kortkontonamnet utan riket). `sAMAccountName`är vanligtvis kontoprefixet (som bob i `bob@contoso.com`). För vissa användare kan det vara annorlunda. Du behöver möjlighet att bläddra / söka `sAMAccountName`i katalogen för att lära dig din .

Sätt att `sAMAccountName`hitta:

* Om du kan logga in på Ambari med den lokala Ambari-administratören kan du titta på listan över användare.

* Om du har en [domän sammanfogad Windows-dator](../../active-directory-domain-services/manage-domain.md)kan du använda standardverktygen för Windows AD för att bläddra. Detta kräver ett fungerande konto i domänen.

* Från huvudnoden kan du använda SAMBA-kommandon för att söka. Detta kräver en giltig Kerberos-session (lyckad kinit). sökning på net-annonser "(userPrincipalName=bob*)"

    Sök / bläddra resultat bör `sAMAccountName` visa dig attributet. Dessutom kan du titta på `pwdLastSet`andra `badPasswordTime` `userPrincipalName` attribut som , , etc. för att se om dessa egenskaper matchar vad du förväntar dig.

---

## <a name="kinit-fails-with-preauthentication-failure"></a>kinit misslyckas med preauthentication misslyckande

### <a name="issue"></a>Problem

Kinit misslyckas med `Preauthentication` fel.

### <a name="cause"></a>Orsak

Felaktigt användarnamn eller lösenord.

### <a name="resolution"></a>Lösning

Kontrollera ditt användarnamn och lösenord. Kontrollera också om det finns andra egenskaper som beskrivs ovan. Om du vill aktivera detaljerad `export KRB5_TRACE=/tmp/krb.log` felsökning körs du från sessionen innan du försöker komma i släktet.

---

## <a name="job--hdfs-command-fails-due-to-tokennotfoundexception"></a>Kommandot Jobb / HDFS misslyckas på grund av TokenNotFoundException

### <a name="issue"></a>Problem

Kommandot Jobb / HDFS `TokenNotFoundException`misslyckas på grund av .

### <a name="cause"></a>Orsak

Den nödvändiga OAuth-åtkomsttoken hittades inte för att jobbet/kommandot skulle lyckas. ADLS/ ABFS-drivrutinen försöker hämta OAuth-åtkomsttoken från autentiseringsuppgifterstjänsten innan lagringsbegäranden skapas. Den här token registreras när du loggar in på Ambari-portalen med samma användare.

### <a name="resolution"></a>Lösning

Se till att du har loggat in på Ambari-portalen en gång via användarnamnet vars identitet används för att köra jobbet.

---

## <a name="error-fetching-access-token"></a>Det gick inte att hämta åtkomsttoken

### <a name="issue"></a>Problem

Användaren får `Error fetching access token`felmeddelande .

### <a name="cause"></a>Orsak

Det här felet uppstår ibland när användare försöker komma åt ADLS Gen2 med hjälp av ÅTKOMSTKONTROLL OCH Kerberos-token har upphört att gälla.

### <a name="resolution"></a>Lösning

* För Azure Data Lake Storage Gen1 rensar du webbläsarens cacheminne och loggar in på Ambari igen.

* För Azure Data Lake Storage `/usr/lib/hdinsight-common/scripts/RegisterKerbWithOauth.sh <upn>` Gen2 körs för användaren som användaren försöker logga in som

---

## <a name="next-steps"></a>Nästa steg

Om du inte såg problemet eller inte kan lösa problemet besöker du någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure Community Support](https://azure.microsoft.com/support/community/).

* Anslut [@AzureSupport](https://twitter.com/azuresupport) med – det officiella Microsoft Azure-kontot för att förbättra kundupplevelsen. Ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en supportbegäran från [Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **Stöd** i menyraden eller öppna **supporthubben Hjälp +.** Mer detaljerad information finns i [Så här skapar du en Azure-supportbegäran](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Åtkomst till prenumerationshantering och faktureringssupport ingår i din Microsoft Azure-prenumeration och teknisk support tillhandahålls via en av [Azure-supportplanerna](https://azure.microsoft.com/support/plans/).
