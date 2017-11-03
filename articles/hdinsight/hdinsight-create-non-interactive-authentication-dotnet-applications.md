---
title: Skapa icke-interaktiv autentisering .NET-program i Azure HDInsight | Microsoft Docs
description: "Lär dig hur du skapar icke-interaktiv autentisering Microsoft .NET-program i Azure HDInsight."
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
ms.assetid: 8e32430f-6404-498a-9fcd-f20338d964af
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/21/2017
ms.author: jgao
ms.openlocfilehash: c1aeed8c6fe4f83b170838e4efb9d9c4744fb951
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-non-interactive-authentication-net-hdinsight-application"></a>Skapa en icke-interaktiv autentisering .NET HDInsight-program
Du kan köra programmet Microsoft .NET Azure HDInsight under programmets egen identitet (icke-interaktiv) eller under identiteten för den inloggade användaren för programmet (Interaktiv). Den här artikeln visar hur du skapar en icke-interaktiv autentisering .NET-program att ansluta till Azure och hantera HDInsight. Ett exempel på ett interaktivt program finns i [Anslut till Azure HDInsight](hdinsight-administer-use-dotnet-sdk.md#connect-to-azure-hdinsight). 

Från ditt icke-interaktiv .NET-program behöver du:

* Ditt Azure-prenumeration klient-ID (kallas även en *katalog-ID*). Se [hämta klient-ID](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-tenant-id).
* Azure Active Directory (Azure AD) application klient-ID. Se [skapa ett Azure Active Directory-program](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application) och [hämta ett program-ID](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key).
* Azure AD application hemliga nyckeln. Se [Get programmet autentiseringsnyckel](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key).

## <a name="prerequisites"></a>Krav
* Ett HDInsight-kluster. Finns det [komma igång-självstudiekurs](hdinsight-hadoop-linux-tutorial-get-started.md#create-cluster).

## <a name="assign-a-role-to-the-azure-ad-application"></a>Tilldela en roll till Azure AD-program
Tilldela Azure AD-program en [rollen](../active-directory/role-based-access-built-in-roles.md), för att bevilja behörighet för att utföra åtgärder. Du kan ange omfånget för prenumerationen, resursgruppen eller resursen. Behörigheter ärvs på lägre nivåer i omfånget. (Till exempel lägga till ett program rollen Läsare för en resursgrupp innebär att programmet kan läsa resursgruppen och alla resurser i den.) I kursen får ange du omfånget på resursgruppsnivå. Mer information finns i [använda rolltilldelningar för att hantera åtkomst till resurserna i Azure-prenumeration](../active-directory/role-based-access-control-configure.md).

**Att lägga till rollen som ägare till Azure AD-program**

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj i den vänstra menyn **resursgruppen**.
3. Välj den resursgrupp som har HDInsight-klustret som du ska köra Hive-fråga senare i den här kursen. Om du har ett stort antal resursgrupper kan använda du filtret för att hitta det du vill använda.
4. Välj på menyn resurs grupp **åtkomstkontroll (IAM)**.
5. Under **användare**väljer **Lägg till**.
6. Följ instruktionerna för att lägga till rollen som ägare till Azure AD-program. När du har lagt till rollen programmet visas under **användare**, med rollen som ägare. 

## <a name="develop-an-hdinsight-client-application"></a>Utveckla ett HDInsight-program för klienten

1. Skapa ett C#-konsolprogram
2. Lägg till följande NuGet-paket:

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
* [Skapa ett Azure Active Directory huvudnamn för program och tjänster i Azure portal](../azure-resource-manager/resource-group-create-service-principal-portal.md).
* Lär dig hur du [autentisera ett huvudnamn för tjänsten med Azure Resource Manager](../azure-resource-manager/resource-group-authenticate-service-principal.md).
* Lär dig mer om [Azure rollbaserad åtkomstkontroll (RBAC)](../active-directory/role-based-access-control-configure.md).
