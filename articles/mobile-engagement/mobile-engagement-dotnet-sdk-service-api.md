---
title: 'Med .NET SDK för att komma åt Azure Mobile Engagement Service API: er'
description: 'Beskriver hur du använder Mobile Engagement .NET SDK för att komma åt Azure Mobile Engagement Service API: er'
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: c07728aa-43f2-4238-8b4a-c9eddf9d838b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 99595eb1f9a1eab1db51796632d58df35bf45be6
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2018
---
# <a name="using-net-sdk-to-access-azure-mobile-engagement-service-apis"></a>Med .NET SDK för att komma åt Azure Mobile Engagement Service API: er
> [!IMPORTANT]
> Azure Mobile Engagement upphör på 3/31/2018. Den här sidan tas bort strax efter.
> 

Azure Mobile Engagement uppvisar en uppsättning API: er att hantera enheter, Reach/Push-kampanjer osv. Om du vill arbeta med dessa API: er vi också ge dig en [Swagger-filen](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-mobileengagement/2014-12-01/swagger/mobile-engagement.json) att du kan använda med verktyg för att generera SDK: er för ditt språk. Vi rekommenderar att du använder den [AutoRest](https://github.com/Azure/AutoRest) verktyget att generera din SDK från våra Swagger-fil.

> [!NOTE]
> Tjänsten Azure Mobile Engagement kommer att dras tillbaka i mars 2018 och är för närvarande endast tillgänglig för befintliga kunder. Mer information finns i [Mobile Engagement](https://azure.microsoft.com/en-us/services/mobile-engagement/).

Vi har skapat en .NET-SDK på ett liknande sätt som gör att du kan interagera med dessa API: er med hjälp av en C#-omslutning och du behöver inte göra autentisering token förhandlingen och uppdatera själv.  

Det här exemplet går igenom uppsättningen steg om du vill använda .NET SDK:

1. Först och främst måste du konfigurera autentiseringen för dina API: er med hjälp av Azure Active Directory enligt beskrivningen [här](mobile-engagement-api-authentication.md#authentication). I slutet av de här stegen bör du ha en giltig **SubscriptionId**, **TenantId**, **ApplicationId** och **hemlighet**. 
2. Vi använder en enkel app för Windows-konsolen för att demonstrera arbeta med .NET SDK med scenario för att skapa ett meddelande kampanj. Så öppnar Visual Studio och skapa en **konsolprogram**.   
3. Därefter måste du hämta .NET SDK som är tillgängliga som **bibliotek för Microsoft Azure Engagement** i galleriet Nuget [här](https://www.nuget.org/packages/Microsoft.Azure.Management.Engagement/).
   Om du installerar Nuget från Visual Studio, måste du kontrollera att du har kontroll markerats i **inkludera förhandsversion** alternativet vid sökning efter paketet:
   
    ![][1]
4. I den `Program.cs` lägger du till följande namnområden:
   
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.Management.Engagement;
        using Microsoft.Azure.Management.Engagement.Models;
5. Därefter måste du definiera följande konstanter som vi ska använda för autentisering och interagerar med Mobile Engagement-App som du skapar kampanjen meddelande:
   
        // For authentication
        const string TENANT_ID = "<Your TenantId>";
        const string CLIENT_ID = "<Your Application Id>";
        const string CLIENT_SECRET = "<Your Secret>";
        const string SUBSCRIPTION_ID = "<Your Subscription Id>";
   
        // This is the Azure Resource group concept for grouping together resources 
        //  see here: https://azure.microsoft.com/en-us/documentation/articles/resource-group-portal/
        const string RESOURCE_GROUP = "";
   
        // For Mobile Engagement operations
        // App Collection Name 
        const string APP_COLLECTION_NAME = "";
        // Application Resource Name - make sure you are using the one as specified in the Azure portal (NOT the App Name)
        const string APP_RESOURCE_NAME = "";
6. Definiera den `EngagementManagementClient` variabel som ska användas för att anropa Mobile Engagement SDK-metoder:
   
        static EngagementManagementClient engagementClient; 
7. Lägg till följande till din `Main` metoden:
   
        try
            {
                // Initialize the Engagement SDK to call out APIs. 
                InitEngagementClient().Wait();
   
                // Create a Reach campaign
                CreateCampaign().Wait();
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.InnerException.Message);
                throw ex;
            }
8. Definiera följande metod som tar hand om vid initiering av `EngagementManagementClient` genom att först autentisera och sedan associera med Mobile Engagement-App som du tänker skapa meddelande kampanj:
   
        private static async Task InitEngagementClient()
        {
            var credentials = await ApplicationTokenProvider.LoginSilentAsync(TENANT_ID, CLIENT_ID, CLIENT_SECRET);
            engagementClient = new EngagementManagementClient(credentials) { SubscriptionId = SUBSCRIPTION_ID };
   
            // This is the Azure concept of ResourceGroup
            engagementClient.ResourceGroupName = RESOURCE_GROUP;
   
            // This is your Mobile Engagement App Collection & App Resource Name in which you create the campaign
            engagementClient.AppCollection = APP_COLLECTION_NAME;
            engagementClient.AppName = APP_RESOURCE_NAME;
        }
   
   > [!IMPORTANT]
   > Observera att du behöver använda den **App resursnamnet** som definierats i Azure-hanteringsportalen för parametern AppName. 
   > 
   > 
9. Slutligen definierar metoden CreateCampaign som tar hand om med den tidigare initierad EngagementClient för att skapa en enkel **när som helst** & **endast meddelande** kampanj med en rubrik och meddelande: 
   
        private async static Task CreateCampaign()
        {
            //  Refer to the Announcement Campaign format from here - 
            //      https://msdn.microsoft.com/en-us/library/azure/mt683751.aspx
            // Make sure you are passing all the non-optional parameters
            Campaign parameters = new Campaign(
                name:"WelcomeCampaign",
                notificationTitle: "Welcome", 
                notificationMessage: "Thank you for installing the app!",
                type:"only_notif",
                deliveryTime:"any"
                );
   
            // Refer to the Campaign Kinds from here - https://msdn.microsoft.com/en-us/library/azure/mt683742.aspx
            CampaignStateResult result = 
                await engagementClient.Campaigns.CreateAsync(CampaignKinds.Announcements, parameters);
            Console.WriteLine("Campaign Id '{0}' was created successfully and it is in '{1}' state", result.Id, result.State);
        }
10. Kör konsolapp-och du får följande på kampanjen har skapats:
    
    **Kampanj-Id '159' skapades och det är i tillståndet 'utkast.**

<!-- Images. -->

[1]: ./media/mobile-engagement-dotnet-sdk-service-api/include-prerelease.png
