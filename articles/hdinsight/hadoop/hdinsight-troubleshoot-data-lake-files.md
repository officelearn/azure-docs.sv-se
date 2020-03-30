---
title: Det går inte att komma åt lagringsfiler för DataSjö i Azure HDInsight
description: Det går inte att komma åt lagringsfiler för DataSjö i Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/13/2019
ms.openlocfilehash: 21269f7d5a9ec832a49a613351702dd24be156af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894155"
---
# <a name="unable-to-access-data-lake-storage-files-in-azure-hdinsight"></a>Det går inte att komma åt lagringsfiler för DataSjö i Azure HDInsight

I den här artikeln beskrivs felsökningssteg och möjliga lösningar för problem när du interagerar med Azure HDInsight-kluster.

## <a name="issue-acl-verification-failed"></a>Problem: ACL-verifieringen misslyckades

Ett felmeddelande visas som liknar:

```
LISTSTATUS failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.).
```

### <a name="cause"></a>Orsak

Användaren kan ha återkallat behörigheter för tjänstens huvudnamn (SP) för filer/mappar.

### <a name="resolution"></a>Lösning

1. Kontrollera att SP har x-behörigheter för att gå längs banan. Mer information finns i [Behörigheter](https://hdinsight.github.io/ClusterCRUD/ADLS/adls-create-permission-setup.html). Exempel på dfs-kommando för att kontrollera åtkomsten till filer/mappar i lagringskontot för Datasjö:

    ```
    hdfs dfs -ls /<path to check access>
    ```

1. Ställ in nödvändiga behörigheter för att komma åt sökvägen baserat på den läs-/skrivåtgärd som utförs. Se här för behörigheter som krävs för olika filsystemåtgärder.

---

## <a name="issue-service-principal-certificate-expiry"></a>Problem: Tjänstens huvudcertifikat upphör att gälla

Ett felmeddelande visas som liknar:

```
Token Refresh failed - Received invalid http response: 500
```

### <a name="cause"></a>Orsak

Certifikatet som anges för tjänstens huvudåtkomst kan ha upphört att gälla.

1. SSH i headnode. Kontrollera åtkomsten till lagringskontot med följande dfs-kommando:

    ```
    hdfs dfs -ls /
    ```

1. Bekräfta att felmeddelandet liknar följande:

    ```
    {"stderr": "-ls: Token Refresh failed - Received invalid http response: 500, text = Response{protocol=http/1.1, code=500, message=Internal Server Error, url=http://gw0-abccluster.24ajrd4341lebfgq5unsrzq0ue.fx.internal.cloudapp.net:909/api/oauthtoken}}...
    ```

1. Få en av webbadresserna från `core-site.xml property`  -  `fs.azure.datalake.token.provider.service.urls`.

1. Kör följande curl-kommando för att hämta OAuth-token.

    ```
    curl gw0-abccluster.24ajrd4341lebfgq5unsrzq0ue.fx.internal.cloudapp.net:909/api/oauthtoken
    ```

1. Utdata för ett giltigt tjänsthuvudnamn ska vara ungefär så här:

    ```
    {"AccessToken":"MIIGHQYJKoZIhvcNAQcDoIIGDjCCBgoCAQA…….","ExpiresOn":1500447750098}
    ```

1. Om tjänstens huvudcertifikat har upphört att gälla ser utdata ungefär så här:

    ```
    Exception in OAuthTokenController.GetOAuthToken: 'System.InvalidOperationException: Error while getting the OAuth token from AAD for AppPrincipalId 23abe517-2ffd-4124-aa2d-7c224672cae2, ResourceUri https://management.core.windows.net/, AADTenantId https://login.windows.net/80abc8bf-86f1-41af-91ab-2d7cd011db47, ClientCertificateThumbprint C49C25705D60569884EDC91986CEF8A01A495783 ---> Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException: AADSTS70002: Error validating credentials. AADSTS50012: Client assertion contains an invalid signature. **[Reason - The key used is expired.**, Thumbprint of key used by client: 'C49C25705D60569884EDC91986CEF8A01A495783', Found key 'Start=08/03/2016, End=08/03/2017, Thumbprint=C39C25705D60569884EDC91986CEF8A01A4956D1', Configured keys: [Key0:Start=08/03/2016, End=08/03/2017, Thumbprint=C39C25705D60569884EDC91986CEF8A01A4956D1;]]
    Trace ID: e4d34f1c-a584-47f5-884e-1235026d5000
    Correlation ID: a44d870e-6f23-405a-8b23-9b44aebfa4bb
    Timestamp: 2017-10-06 20:44:56Z ---> System.Net.WebException: The remote server returned an error: (401) Unauthorized.
    at System.Net.HttpWebRequest.GetResponse()
    at Microsoft.IdentityModel.Clients.ActiveDirectory.HttpWebRequestWrapper.<GetResponseSyncOrAsync>d__2.MoveNext()
    ```

1. Alla andra Azure Active Directory-relaterade fel/certifikatrelaterade fel kan identifieras genom att pinga gateway-url:en för att hämta OAuth-token.

1. Om du får följande fel när du försöker komma åt ADLS från HDI-klustret. Kontrollera om certifikatet har upphört att gälla genom att följa stegen ovan.

    ```
    Error: java.lang.IllegalArgumentException: Token Refresh failed - Received invalid http response: 500, text = Response{protocol=http/1.1, code=500, message=Internal Server Error, url=http://clustername.hmssomerandomstringc.cx.internal.cloudapp.net:909/api/oauthtoken}
    ```

### <a name="resolution"></a>Lösning

Skapa ett nytt certifikat eller tilldela befintligt certifikat med följande PowerShell-skript:

```powershell
$clusterName = 'CLUSTERNAME'
$resourceGroupName = 'RGNAME'
$subscriptionId = 'SUBSCRIPTIONID'
$appId = 'APPLICATIONID'
$generateSelfSignedCert = $false
$addNewCertKeyCredential = $true
$certFilePath = 'NEW_CERT_PFX_LOCAL_PATH'
$certPassword = Read-Host "Enter Certificate Password"

if($generateSelfSignedCert)
{
    Write-Host "Generating new SelfSigned certificate"
    
    $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=hdinsightAdlsCert" -KeySpec KeyExchange
    $certBytes = $cert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword);
    $certString = [System.Convert]::ToBase64String($certBytes)
}
else
{

    Write-Host "Reading the cert file from path $certFilePath"

    $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($certFilePath, $certPassword)
    $certString = [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes($certFilePath))
}


Login-AzureRmAccount

if($addNewCertKeyCredential)
{
    Write-Host "Creating new KeyCredential for the app"

    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    
    New-AzureRmADAppCredential -ApplicationId $appId -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore

    Write-Host "Waiting for 30 seconds for the permissions to get propagated"

    Start-Sleep -s 30
}

Select-AzureRmSubscription -SubscriptionId $subscriptionId

Write-Host "Updating the certificate on HDInsight cluster."

Invoke-AzureRmResourceAction `
    -ResourceGroupName $resourceGroupName `
    -ResourceType 'Microsoft.HDInsight/clusters' `
    -ResourceName $clusterName `
    -ApiVersion '2015-03-01-preview' `
    -Action 'updateclusteridentitycertificate' `
    -Parameters @{ ApplicationId = $appId.ToString(); Certificate = $certString; CertificatePassword = $certPassword.ToString() } `
    -Force

```

För att tilldela befintligt certifikat, skapa ett certifikat, ha PFX-filen och lösenordet redo. Associera certifikatet med tjänstens huvudnamn som klustret skapades med och ha AppId redo.

Kör powershell-kommandot när du har ersatt parametrarna med de faktiska värdena.

## <a name="next-steps"></a>Nästa steg

Om du inte såg problemet eller inte kan lösa problemet besöker du någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure Community Support](https://azure.microsoft.com/support/community/).

* Anslut [@AzureSupport](https://twitter.com/azuresupport) med – det officiella Microsoft Azure-kontot för att förbättra kundupplevelsen. Ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en supportbegäran från [Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **Stöd** i menyraden eller öppna **supporthubben Hjälp +.** Mer detaljerad information finns i [Så här skapar du en Azure-supportbegäran](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Åtkomst till prenumerationshantering och faktureringssupport ingår i din Microsoft Azure-prenumeration och teknisk support tillhandahålls via en av [Azure-supportplanerna](https://azure.microsoft.com/support/plans/).
