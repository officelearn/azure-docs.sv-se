---
title: "Ändra feed för HL7 FHIR resurser - Azure Cosmos DB | Microsoft Docs"
description: "Lär dig hur du ställer in ändringsmeddelanden för HL7 FHIR hälsovård patientjournaler med Azure Logikappar, Azure Cosmos DB och Service Bus."
keywords: HL7 fhir
services: cosmos-db
author: hedidin
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: 0d25c11f-9197-419a-aa19-4614c6ab2d06
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: b-hoedid
ms.openlocfilehash: 7a041e2121a2762af4307d7044437032cce79f05
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="notifying-patients-of-hl7-fhir-health-care-record-changes-using-logic-apps-and-azure-cosmos-db"></a>Meddela patienter HL7 FHIR hälsovård post ändringar med hjälp av Logic Apps och Azure Cosmos DB

Azure MVP Howard Edidin kontaktades nyligen av en sjukvårdsorganisation som du vill lägga till nya funktioner till deras patient portal. De behövs för att skicka meddelanden till patienter när deras hälsa posten har uppdaterats och de behövs patienter för att kunna prenumerera på dessa uppdateringar. 

Den här artikeln beskriver hur ändringen meddelande lösning som har skapats för den här sjukvårdsorganisation med Azure Cosmos DB, Logic Apps och Service Bus-feed. 

## <a name="project-requirements"></a>Projektkrav för
- Providers skicka HL7 konsoliderade kliniska dokumentet arkitektur (C-CDA) dokument i XML-format. C-CDA dokument omfattar nästan alla typer av kliniska dokument, inklusive kliniska dokument, till exempel family historik och vaccination poster, samt som administrativa, arbetsflöden och ekonomiska dokument. 
- C-CDA dokument konverteras till [HL7 FHIR resurser](http://hl7.org/fhir/2017Jan/resourcelist.html) i JSON-format.
- Ändrade FHIR resurs dokument skickas via e-post i JSON-format.

## <a name="solution-workflow"></a>Lösning för arbetsflöde 

På en hög nivå krävs projektet följande arbetsflödessteg: 
1. Konvertera C CDA dokument till FHIR resurser.
2. Utför återkommande utlösare avsöker för ändrade FHIR resurser. 
2. Anropa en anpassad app FhirNotificationApi att ansluta till Azure Cosmos DB och fråga efter nya eller ändrade dokument.
3. Spara svar om du vill att Service Bus-kö.
4. Avsökning för nya meddelanden i Service Bus-kö.
5. Skicka e-postmeddelanden till patienter.

## <a name="solution-architecture"></a>Lösningsarkitektur
Denna lösning kräver tre Logic Apps att uppfylla kraven ovan och slutföra arbetsflödet lösning. Tre logikappar är:
1. **HL7-FHIR-mappning app**: tar emot HL7 C-CDA dokumentet, omvandlar till FHIR resursen och sedan sparar det på Azure Cosmos DB.
2. **EHR app**: frågar Azure Cosmos DB FHIR databasen och sparar svar till en Service Bus-kö. Den här logikapp använder en [API-app](#api-app) att hämta nya och ändrade dokument.
3. **Processen meddelandeprogram**: skickar ett e-postmeddelande med FHIR resurs dokument i brödtexten.

![Tre Logic Apps som används i den här HL7 FHIR sjukvården lösningen](./media/change-feed-hl7-fhir-logic-apps/health-care-solution-hl7-fhir.png)



### <a name="azure-services-used-in-the-solution"></a>Azure-tjänster används i lösningen

#### <a name="azure-cosmos-db-sql-api"></a>Azure Cosmos DB SQL-API
Azure Cosmos-DB är lagringsplatsen för FHIR resurser som visas i följande bild.

![Azure DB som Cosmos-kontot som används i självstudierna HL7 FHIR sjukvården](./media/change-feed-hl7-fhir-logic-apps/account.png)

#### <a name="logic-apps"></a>Logic Apps
Logic Apps hantera arbetsflödesprocessen. Följande skärmdumpar visar logikappar som skapats för den här lösningen. 


1. **HL7-FHIR-mappning app**: ta emot HL7 C-CDA dokumentet och omvandla dem till en resurs för FHIR använder Enterprise-Integrationspaket för Logic Apps. Enterprise-Integrationspaket hanterar mappning från C-CDA FHIR resurser.

    ![Logikappen som används för att ta emot HL7 FHIR sjukvården poster](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-logic-apps-json-transform.png)


2. **EHR app**: fråga Azure Cosmos DB FHIR databasen och spara svar till en Service Bus-kö. Koden för GetNewOrModifiedFHIRDocuments appen är nedan.

    ![Logikappen som används för att fråga Azure Cosmos DB](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-logic-apps-api-app.png)

3. **Processen meddelandeprogram**: skicka ett e-postmeddelande med FHIR resurs dokument i.

    ![Logikappen som skickar patient e-post med HL7 FHIR resursen i brödtexten](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-logic-apps-send-email.png)

#### <a name="service-bus"></a>Service Bus
Följande bild visar patienterna kön. Taggen egenskapens värde används för ämnet för e-post.

![Service Bus-kö som används i den här självstudiekursen HL7 FHIR](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-service-bus-queue.png)

<a id="api-app"></a>

#### <a name="api-app"></a>API-app
En API-app ansluter till Azure Cosmos DB och frågor om nya eller ändrade FHIR dokument uppdelat efter resurstyp. Det här programmet har en domänkontrollant **FhirNotificationApi** med en åtgärd **GetNewOrModifiedFhirDocuments**, se [källa för API-app](#api-app-source).

Vi använder den [ `CreateDocumentChangeFeedQuery` ](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdocumentchangefeedquery.aspx) klass från Azure Cosmos SQL DB .NET API. Mer information finns i [ändra feed artikel](change-feed.md). 

##### <a name="getnewormodifiedfhirdocuments-operation"></a>GetNewOrModifiedFhirDocuments åtgärden

**Indata**
- DatabaseId
- CollectionId
- Resurstyp för HL7 FHIR namn
- Boolean: Starta från början
- Int: Antal dokument som returneras

**Utdata**
- Lyckades: Statuskod: 200, svar: listan över dokument (JSON-matris)
- Fel: Statuskod: 404, svar ”: inga dokument hittades för”*resursnamnet '* resurstypen ”

<a id="api-app-source"></a>

**Källa för API-appen**

```C#

    using System.Collections.Generic;
    using System.Linq;
    using System.Net;
    using System.Net.Http;
    using System.Threading.Tasks;
    using System.Web.Http;
    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.Client;
    using Swashbuckle.Swagger.Annotations;
    using TRex.Metadata;
    
    namespace FhirNotificationApi.Controllers
    {
        /// <summary>
        ///     FHIR Resource Type Controller
        /// </summary>
        /// <seealso cref="System.Web.Http.ApiController" />
        public class FhirResourceTypeController : ApiController
        {
            /// <summary>
            ///     Gets the new or modified FHIR documents from Last Run Date 
            ///     or create date of the collection
            /// </summary>
            /// <param name="databaseId"></param>
            /// <param name="collectionId"></param>
            /// <param name="resourceType"></param>
            /// <param name="startfromBeginning"></param>
            /// <param name="maximumItemCount">-1 returns all (default)</param>
            /// <returns></returns>
            [Metadata("Get New or Modified FHIR Documents",
                "Query for new or modifed FHIR Documents By Resource Type " +
                "from Last Run Date or Begining of Collection creation"
            )]
            [SwaggerResponse(HttpStatusCode.OK, type: typeof(Task<dynamic>))]
            [SwaggerResponse(HttpStatusCode.NotFound, "No New or Modifed Documents found")]
            [SwaggerOperation("GetNewOrModifiedFHIRDocuments")]
            public async Task<dynamic> GetNewOrModifiedFhirDocuments(
                [Metadata("Database Id", "Database Id")] string databaseId,
                [Metadata("Collection Id", "Collection Id")] string collectionId,
                [Metadata("Resource Type", "FHIR resource type name")] string resourceType,
                [Metadata("Start from Beginning ", "Change Feed Option")] bool startfromBeginning,
                [Metadata("Maximum Item Count", "Number of documents returned. '-1 returns all' (default)")] int maximumItemCount = -1
            )
            {
                var collectionLink = UriFactory.CreateDocumentCollectionUri(databaseId, collectionId);
    
                var context = new DocumentDbContext();  
    
                var docs = new List<dynamic>();
    
                var partitionKeyRanges = new List<PartitionKeyRange>();
                FeedResponse<PartitionKeyRange> pkRangesResponse;
    
                do
                {
                    pkRangesResponse = await context.Client.ReadPartitionKeyRangeFeedAsync(collectionLink);
                    partitionKeyRanges.AddRange(pkRangesResponse);
                } while (pkRangesResponse.ResponseContinuation != null);
    
                foreach (var pkRange in partitionKeyRanges)
                {
                    var changeFeedOptions = new ChangeFeedOptions
                    {
                        StartFromBeginning = startfromBeginning,
                        RequestContinuation = null,
                        MaxItemCount = maximumItemCount,
                        PartitionKeyRangeId = pkRange.Id
                    };
    
                    using (var query = context.Client.CreateDocumentChangeFeedQuery(collectionLink, changeFeedOptions))
                    {
                        do
                        {
                            if (query != null)
                            {
                                var results = await query.ExecuteNextAsync<dynamic>().ConfigureAwait(false);
                                if (results.Count > 0)
                                    docs.AddRange(results.Where(doc => doc.resourceType == resourceType));
                            }
                            else
                            {
                                throw new HttpResponseException(new HttpResponseMessage(HttpStatusCode.NotFound));
                            }
                        } while (query.HasMoreResults);
                    }
                }
                if (docs.Count > 0)
                    return docs;
                var msg = new StringContent("No documents found for " + resourceType + " Resource");
                var response = new HttpResponseMessage
                {
                    StatusCode = HttpStatusCode.NotFound,
                    Content = msg
                };
                return response;
            }
        }
    }
    
```

### <a name="testing-the-fhirnotificationapi"></a>Testa FhirNotificationApi 

Följande bild visar hur swagger användes till att testa den [FhirNotificationApi](#api-app-source).

![Swagger-filen som används för att testa API-appen](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-testing-app.png)


### <a name="azure-portal-dashboard"></a>Azure portalens instrumentpanel

Följande bild visar alla Azure-tjänster för den här lösningen som körs i Azure-portalen.

![Azure portal som visar alla tjänster som används i den här självstudiekursen HL7 FHIR](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-portal.png)


## <a name="summary"></a>Sammanfattning

- Du har lärt dig att Azure Cosmos DB har inbyggda relationstypen för meddelanden om nya eller ändras dokument och hur lätt det är att använda. 
- Du kan skapa arbetsflöden utan att skriva någon kod genom att utnyttja Logic Apps.
- Med Azure Service Bus-köer för att hantera distribution för HL7 FHIR-dokument.

## <a name="next-steps"></a>Nästa steg
Mer information om Azure Cosmos DB finns på [Azure Cosmos DB startsidan](https://azure.microsoft.com/services/cosmos-db/). Mer information om Logic Apps finns i [Logikappar](https://azure.microsoft.com/services/logic-apps/).


