---
title: Azure Mobile Engagement - serverdelen integrering
description: "Anslut Azure Mobile Engagement med en SharePoint-serverdel för att skapa kampanjer från SharePoint"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 06297b43-579f-46e6-8a58-961a68f9aa09
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: d49f1094f4c3f170f3618f3e19e42266f9ae8858
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-mobile-engagement---api-integration"></a>Azure Mobile Engagement - API-integrering
I ett automatiserat marknadsföring system sker skapa och aktivera marknadsföringskampanjer också automatiskt. För detta ändamål - Azure Mobile Engagement kan skapa dessa automatiserade marknadsföringskampanjer via API: er samt. 

Kunder använder vanligtvis Mobile Engagement frontend-gränssnitt för att skapa meddelanden/avsökningar etc som en del av deras marknadsföringskampanjer. Men när marknadsföringskampanjer blir mogen finns behov av att använda informationen låst i serverdelssystem (till exempel alla CRM-system eller CMS-system som SharePoint) så att du kan skapa en helt automatiserad pipeline som skapar kampanjer i Mobile Engagement dynamiskt baserat på de data som flödar i från backend-system. 

![][5]

Den här kursen går igenom ett scenario där SharePoint företagsanvändare fyller på en SharePoint-lista med marknadsföring data och en automatiserad process hämtar objekt i listan och ansluter med Mobile Engagement-systemet med tillgängliga REST API: er för att skapa en marknadsföringskampanj från SharePoint-data. 

> [!IMPORTANT]
> I allmänhet kan du använda det här exemplet som utgångspunkt för att förstå hur du anropar Mobile Engagement REST API: er som den innehåller två viktiga aspekter av att anropa API- och autentiseras skickar parametrar. 
> 
> 

## <a name="sharepoint-integration"></a>SharePoint-integrering
1. Här är exempel SharePoint-lista ser ut. **Rubrik**, **kategori**, **NotificationTitle**, **meddelandet** och **URL** används för att skapa ett meddelande. Det finns en kolumn med namnet **IsProcessed** som används av processen exempel automation i form av ett konsolprogram. Du kan antingen köra den här konsolen programmet som en Azure-Webbjobb så att du kan schemalägga det eller direkt kan du använda SharePoint-arbetsflöde för program att skapa och aktivera ett meddelande när ett objekt infogas i SharePoint-listan. I det här exemplet använder vi konsolprogram som går igenom objekten i SharePoint och skapa meddelande i Azure Mobile Engagement för dem och slutligen markerar den **IsProcessed** flaggan vara sant vid skapandet lyckas meddelande.
   
    ![][1]
2. Vi använder koden från exemplet *Remote Authentication i SharePoint Online med klienten Object Model* [här](https://code.msdn.microsoft.com/Remote-Authentication-in-b7b6f43c) att autentisera med SharePoint-listan.
3. När autentiseringen är vi gå igenom listobjekt ta reda på nyligen skapade objekt (som kommer att ha **IsProcessed** = false). 
   
         static async void CreateCampaignFromSharepoint()
        {
            using (ClientContext clientContext = ClaimClientContext.GetAuthenticatedContext(targetSharepointSite))
            {
                // Using https://code.msdn.microsoft.com/Remote-Authentication-in-b7b6f43c for authentication     
                Web site = clientContext.Web;
                List list = site.Lists.GetByTitle("VideoContent");
                CamlQuery query = new CamlQuery();
                query.ViewXml = "<View/>";
                ListItemCollection items = list.GetItems(query);
   
                // Load the SharePoint list
                clientContext.Load(list);
                clientContext.Load(items);
                clientContext.ExecuteQuery();
   
                // Loop through the list to go through all the items which are newly added
                foreach (ListItem item in items)
                    if (item["IsProcessed"].ToString() != "Yes")
                    {
                        string name = item["Title"].ToString();
                        string notificationTitle = item["NotificationTitle"].ToString();
                        string notificationMessage = item["Message"].ToString();
                        string category = item["Category"].ToString();
                        string actionURL = ((FieldUrlValue)item["URL"]).Url;
   
                        // Send an HTTP request to create AzME campaign
                        int campaignId = CreateAzMECampaign
                            (name, notificationTitle, notificationMessage, category, actionURL).Result;
   
                        if (campaignId != -1)
                        {
                            // If creating campaign is successful then set this to true
                            item["IsProcessed"] = "Yes";
   
                            // Now try to activate the campaign also
                            await ActivateAzMECampaign(campaignId);
                        }
                        else
                        {
                            item["IsProcessed"] = "Error";
                        }
                        item.Update();
                    }
                clientContext.ExecuteQuery();
            }
        }

## <a name="mobile-engagement-integration"></a>Mobile Engagement-integration
1. När vi har hittat ett objekt som kräver bearbetning - vi extrahera informationen som krävs för att skapa ett meddelande från listobjekt och anropet `CreateAzMECampaign` skapa den och därefter `ActivateAzMECampaign` att aktivera den. Dessa är i stort sett REST API-anrop som är anrop till Mobile Engagement-serverdelen. 
2. Mobile Engagement REST-API: er kräver en **authorization HTTP-huvud för grundläggande autentisering schemat** som består av den `ApplicationId` och `ApiKey` som du får från Azure-portalen. Kontrollera att du använder nyckeln från den **api-nycklar** avsnitt och *inte* från den **sdk nycklar** avsnitt. 
   
   ![][2]
   
       static string CreateAuthZHeader()
       {
           string BasicAuthzHeaderString = "Basic " + EncodeTo64(ApplicationId + ":" + ApiKey);
           return BasicAuthzHeaderString;
       }
   
       static public string EncodeTo64(string toEncode)
       {
           byte[] toEncodeAsBytes = System.Text.ASCIIEncoding.ASCII.GetBytes(toEncode);
           string returnValue = System.Convert.ToBase64String(toEncodeAsBytes);
           return returnValue;
       }  
3. För att skapa ett meddelande typ kampanj - avser den [dokumentationen](https://msdn.microsoft.com/library/azure/mt683750.aspx). Du måste se till att du anger kampanjen `kind` som *meddelande* och [nyttolast](https://msdn.microsoft.com/library/azure/mt683751.aspx) och passerar den som FormUrlEncodedContent. 
   
        static async Task<int> CreateAzMECampaign(string campaignName, string notificationTitle, 
            string notificationMessage, string notificationCategory, string actionURL)
        {
            string createURIFragment = "/reach/1/create";
   
            using (var client = new HttpClient())
            {
                // Add Authorization Header
                client.DefaultRequestHeaders.TryAddWithoutValidation("Authorization", CreateAuthZHeader());
   
                // Create the payload to send the content
                // Reference -> https://msdn.microsoft.com/library/dn913749.aspx
                string data =
                    @"{""name"":""" + campaignName + @"""," + 
                    @"""type"":""only_notif""," + 
                    @"""deliveryTime"":""any"","" + 
                    @""notificationTitle"":""" + notificationTitle + 
                    @""",""notificationMessage"":""" + notificationMessage + 
                    @""",""actionUrl"":""" + actionURL + @"""}";
   
                var content = new FormUrlEncodedContent(new[] 
                {
                    new KeyValuePair<string, string>("kind", "announcement"),
                    new KeyValuePair<string, string>("data", data),
                });
   
                // Send the POST request
                var response = await client.PostAsync(url + createURIFragment, content);
                var responseString = response.Content.ReadAsStringAsync().Result;
                int campaignId = -1;
                if (response.StatusCode.ToString() == "OK")
                {
                    // This is the campaign id
                    campaignId = Convert.ToInt32(responseString);
                    Console.WriteLine("Campaign successfully created with id {0}", campaignId);
                }
                else
                {
                    Console.WriteLine("Campaign creation failed with error - '{0}'", responseString);
                }
                return campaignId;
            }
        }
4. När du har ett meddelande som har skapats visas något som liknar följande i Mobile Engagement-portal (Observera att tillståndet = utkast och aktiverad = ej tillämpligt)
   
    ![][3]
5. `CreateAzMECampaign`skapar ett meddelande kampanj och returnerar dess Id till anroparen. `ActivateAzMECampaign`kräver detta Id som parameter för att aktivera kampanjen. 
   
        static async Task<bool> ActivateAzMECampaign(int campaignId)
        {
            string activateUriFragment = "/reach/1/activate";
            using (var client = new HttpClient())
            {
                // Add Authorization Header
                client.DefaultRequestHeaders.TryAddWithoutValidation("Authorization", CreateAuthZHeader());
   
                var content = new FormUrlEncodedContent(new[] 
                {
                    new KeyValuePair<string, string>("kind", "announcement"),
                    new KeyValuePair<string, string>("id", campaignId.ToString()),
                });
   
                // Send the POST request
                var response = await client.PostAsync(url + activateUriFragment, content);
                var responseString = response.Content.ReadAsStringAsync().Result;
                if (response.StatusCode.ToString() == "OK")
                {
                    Console.WriteLine("Campaign successfully activated");
                    return true;
                }
                else
                {
                    Console.WriteLine("Campaign activation failed with error - '{0}'", responseString);
                    return false;
                }
            }
        }
6. När du har ett meddelande som har aktiverats visas något som liknar följande i Mobile Engagement-portal:
   
    ![][4]
7. Så snart som kampanjen hämtar aktiverat kan börjar alla enheter som uppfyller kriterierna för kampanjen ser meddelanden. 
8. Du ser också att listobjektet markerad med IsProcessed = false har angetts till True när meddelandet kampanjen skapades.  

Det här exemplet skapas en enkel meddelande kampanj anger främst de obligatoriska egenskaperna. Du kan anpassa så mycket som du kan från portalen med hjälp av informationen [här](https://msdn.microsoft.com/library/azure/mt683751.aspx). 

<!-- Images. -->
[1]: ./media/mobile-engagement-sample-backend-integration-sharepoint/sharepointlist.png
[2]: ./media/mobile-engagement-sample-backend-integration-sharepoint/properties.png
[3]: ./media/mobile-engagement-sample-backend-integration-sharepoint/new-announcement.png
[4]: ./media/mobile-engagement-sample-backend-integration-sharepoint/activate-announcement.png
[5]: ./media/mobile-engagement-sample-backend-integration-sharepoint/diagram.png



