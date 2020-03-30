---
title: Icke-interaktiv autentisering .NET-program - Azure HDInsight
description: Lär dig hur du skapar icke-interaktiv autentisering Microsoft .NET-program i Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/23/2019
ms.openlocfilehash: 5e6a0586bc750f8972586920c15dbb297295aa20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371281"
---
# <a name="create-a-non-interactive-authentication-net-hdinsight-application"></a>Skapa ett icke-interaktivt autentiseringsprogram för .NET HDInsight

Kör ditt Microsoft .NET Azure HDInsight-program antingen under programmets egen identitet (icke-interaktiv) eller under identiteten för den inloggade användaren av programmet (interaktiv). Den här artikeln visar hur du skapar ett icke-interaktivt autentisering .NET-program för att ansluta till Azure och hantera HDInsight. Ett exempel på ett interaktivt program finns i [Anslut till Azure HDInsight](hdinsight-administer-use-dotnet-sdk.md#connect-to-azure-hdinsight).

Från ditt icke-interaktiva .NET-program behöver du:

* Ditt Azure-prenumerationsklient-ID (kallas även *ett katalog-ID*). Se [Hämta klient-ID](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).
* Azure Active Directory (Azure AD) programklient-ID. Se [Skapa ett Azure Active Directory-program](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) och hämta ett [program-ID](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).
* Hemlig nyckel för Azure AD-programmet. Se [Hämta programautentiseringsnyckel](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).

## <a name="prerequisites"></a>Krav

Ett HDInsight-kluster. Se [komma igång handledning](hadoop/apache-hadoop-linux-tutorial-get-started.md).

## <a name="assign-a-role-to-the-azure-ad-application"></a>Tilldela en roll till Azure AD-programmet

Tilldela ditt Azure AD-program en [roll](../role-based-access-control/built-in-roles.md)för att ge det behörighet att utföra åtgärder. Du kan ange omfånget på nivån för prenumerationen, resursgruppen eller resursen. Behörigheterna ärvs till lägre omfattningsnivåer. Om du till exempel lägger till ett program i rollen Läsare för en resursgrupp kan programmet läsa resursgruppen och eventuella resurser i den. I den här artikeln anger du scopet på resursgruppsnivå. Mer information finns i [Använda rolltilldelningar för att hantera åtkomst till dina Azure-prenumerationsresurser](../role-based-access-control/role-assignments-portal.md).

**Så här lägger du till ägarrollen i Azure AD-programmet**

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Navigera till resursgruppen som har HDInsight-klustret där du ska köra Hive-frågan senare i den här artikeln. Om du har ett stort antal resursgrupper kan du använda filtret för att hitta det du vill ha.
1. Välj **Åtkomstkontroll (IAM) på**resursgruppsmenyn .
1. Välj fliken **Rolltilldelningar** om du vill visa de aktuella rolltilldelningarna.
1. Högst upp på sidan väljer du **+ Lägg till**.
1. Följ instruktionerna för att lägga till ägarrollen i ditt Azure AD-program. När du har lagt till rollen visas programmet under rollen Ägare.

## <a name="develop-an-hdinsight-client-application"></a>Utveckla ett HDInsight-klientprogram

1. Skapa ett C#-konsolprogram
2. Lägg till följande [NuGet-paket:](https://www.nuget.org/)

        Install-Package Microsoft.Azure.Common.Authentication -Pre
        Install-Package Microsoft.Azure.Management.HDInsight -Pre
        Install-Package Microsoft.Azure.Management.Resources -Pre

3. Kör följande kod:

    ```csharp
    using System;
    using System.Security;
    using Microsoft.Azure;
    using Microsoft.Azure.Common.Authentication;
    using Microsoft.Azure.Common.Authentication.Factories;
    using Microsoft.Azure.Common.Authentication.Models;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.HDInsight;
    
    namespace CreateHDICluster
    {
        internal class Program
        {
            private static HDInsightManagementClient _hdiManagementClient;
    
            private static Guid SubscriptionId = new Guid("<Enter your Azure subscription ID>");
            private static string tenantID = "<Enter your tenant ID (also called directory ID)>";
            private static string applicationID = "<Enter your application ID>";
            private static string secretKey = "<Enter the application secret key>";
    
            private static void Main(string[] args)
            {
                var key = new SecureString();
                foreach (char c in secretKey) { key.AppendChar(c); }
    
                var tokenCreds = GetTokenCloudCredentials(tenantID, applicationID, key);
                var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);
    
                var resourceManagementClient = new ResourceManagementClient(subCloudCredentials);
                resourceManagementClient.Providers.Register("Microsoft.HDInsight");
    
                _hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);
    
                var results = _hdiManagementClient.Clusters.List();
                foreach (var name in results.Clusters)
                {
                    Console.WriteLine("Cluster Name: " + name.Name);
                    Console.WriteLine("\t Cluster type: " + name.Properties.ClusterDefinition.ClusterType);
                    Console.WriteLine("\t Cluster location: " + name.Location);
                    Console.WriteLine("\t Cluster version: " + name.Properties.ClusterVersion);
                }
                Console.WriteLine("Press Enter to continue");
                Console.ReadLine();
            }
    
            /// Get the access token for a service principal and provided key.          
            public static TokenCloudCredentials GetTokenCloudCredentials(string tenantId, string clientId, SecureString secretKey)
            {
                var authFactory = new AuthenticationFactory();
                var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };
                var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
                var accessToken =
                    authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never).AccessToken;
    
                return new TokenCloudCredentials(accessToken);
            }
    
            public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(SubscriptionCloudCredentials creds, Guid subId)
            {
                return new TokenCloudCredentials(subId.ToString(), ((TokenCloudCredentials)creds).Token);
            }
        }
    }
    ```

## <a name="next-steps"></a>Nästa steg

* [Skapa ett Azure Active Directory-program och tjänsthuvudnamn i Azure-portalen](../active-directory/develop/howto-create-service-principal-portal.md).
* Lär dig hur du [autentiserar ett tjänsthuvudnamn med Azure Resource Manager](../active-directory/develop/howto-authenticate-service-principal-powershell.md).
* Lär dig mer om [RBAC (Azure Role-Based Access Control).](../role-based-access-control/role-assignments-portal.md)
