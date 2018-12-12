---
title: Ändringsfeed för HL7 FHIR-resurser – Azure Cosmos DB
description: Lär dig hur du ställer in ändringsmeddelanden för HL7 FHIR hälsovård patientjournaler med Azure Logic Apps, Azure Cosmos DB och Service Bus.
keywords: HL7 fhir
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/08/2017
ms.author: sngun
ms.openlocfilehash: 5cc6bdfa9c16a6dfbdd0f6c87873a90b2a203169
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53089232"
---
# <a name="notifying-patients-of-hl7-fhir-health-care-record-changes-using-logic-apps-and-azure-cosmos-db"></a>Meddela patienter av HL7 FHIR hälsovård poständringar med Logic Apps och Azure Cosmos DB

Azure MVP Howard Edidin kontaktades nyligen genom inom organisationen som vill lägga till nya funktioner till sina patientportal. De krävs för att skicka meddelanden till patienter när deras hälsotillstånd post uppdaterades och de behövs patienter för att kunna prenumerera på de här uppdateringarna. 

Den här artikeln beskriver ändringsflödet meddelande lösning som har skapats för den här inom organisationen med hjälp av Azure Cosmos DB, Logic Apps och Service Bus. 

## <a name="project-requirements"></a>Projektkrav
- Leverantörer skicka HL7 konsoliderade kliniska dokumentet arkitektur (C-Videostrategin) dokument i XML-format. C-Videostrategin dokument omfatta nästan alla typer av kliniska dokument, inklusive kliniska dokument, till exempel family historik och vaccination poster, samt som administrativa, arbetsflöde och finansiella dokument. 
- C-Videostrategin dokument konverteras till [HL7 FHIR resurser](https://hl7.org/fhir/2017Jan/resourcelist.html) i JSON-format.
- Ändrade FHIR resource dokument skickas via e-post i JSON-format.

## <a name="solution-workflow"></a>Arbetsflödet i en lösning 

På en hög nivå krävs projektet följande arbetsflöde: 
1. Konvertera C-Videostrategin dokument till FHIR resurser.
2. Utför återkommande utlösare avsökningen för den ändrade FHIR resurser. 
2. Anropa en anpassad app FhirNotificationApi att ansluta till Azure Cosmos DB och fråga efter nya eller ändrade dokument.
3. Spara svaret till Service Bus-kö.
4. Avsökning för nya meddelanden i Service Bus-kö.
5. Skicka e-postmeddelanden till patienter med rätt.

## <a name="solution-architecture"></a>Lösningsarkitektur
Denna lösning kräver tre Logic Apps uppfyller kraven ovan och slutföra arbetsflödet lösning. De tre logiska apparna är:
1. **HL7-FHIR-mappning app**: tar emot HL7 C-Videostrategin dokumentet, omvandlar det till FHIR-resurs och sparar den till Azure Cosmos DB.
2. **EHR app**: frågar Azure Cosmos DB FHIR-databasen och sparar svaret på en Service Bus-kö. Den här logikappen använder en [API-app](#api-app) att hämta nya och ändrade dokument.
3. **Processen meddelandeprogram**: skickar ett e-postmeddelande med FHIR resource dokument i brödtexten.

![De tre Logic-appar som används i den här hälsovård HL7 FHIR-lösningen](./media/change-feed-hl7-fhir-logic-apps/health-care-solution-hl7-fhir.png)



### <a name="azure-services-used-in-the-solution"></a>Azure-tjänster används i lösningen

#### <a name="azure-cosmos-db-sql-api"></a>Azure Cosmos DB SQL API
Azure Cosmos DB är lagringsplatsen för FHIR-resurser enligt följande bild.

![Azure Cosmos DB-kontot som används i självstudien HL7 FHIR hälsovård](./media/change-feed-hl7-fhir-logic-apps/account.png)

#### <a name="logic-apps"></a>Logic Apps
Logikappar hanterar arbetsflödesprocessen. De följande skärmbilderna visar Logic-appar som har skapats för den här lösningen. 


1. **HL7-FHIR-mappning app**: ta emot HL7 C-Videostrategin dokumentet och omvandla dem till en FHIR-resurs med hjälp av Enterprise-Integrationspaketet för Logic Apps. Enterprise-Integrationspaketet hanterar mappningen från C-Videostrategin till FHIR resurser.

    ![Logikappen används för att ta emot HL7 FHIR hälso-poster](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-logic-apps-json-transform.png)


2. **EHR app**: fråga Azure Cosmos DB FHIR-databasen och spara svaret på en Service Bus-kö. Koden för appen GetNewOrModifiedFHIRDocuments är under.

    ![Logikappen används för att fråga Azure Cosmos DB](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-logic-apps-api-app.png)

3. **Processen meddelandeprogram**: skicka ett e-postmeddelande med FHIR resource dokument i.

    ![Den Logikapp som skickar patientens e-postmeddelande med HL7 FHIR-resursen i brödtexten](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-logic-apps-send-email.png)

#### <a name="service-bus"></a>Service Bus
Följande bild visar patienterna kö. Egenskapen Taggvärdet används för e-postmeddelandets ämne.

![Service Bus-kö som används i den här HL7 FHIR-självstudien](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-service-bus-queue.png)

<a id="api-app"></a>

#### <a name="api-app"></a>API-app
En API-app som ansluter till Azure Cosmos DB och frågor för nya eller ändrade FHIR dokument efter resurstyp. Den här appen har en kontrollenhet **FhirNotificationApi** med en enda åtgärd **GetNewOrModifiedFhirDocuments**, se [källa för API-app](#api-app-source).

Vi använder den [ `CreateDocumentChangeFeedQuery` ](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdocumentchangefeedquery.aspx) klass från Azure Cosmos DB SQL .NET API. Mer information finns i den [ändringsfeed artikeln](change-feed.md). 

##### <a name="getnewormodifiedfhirdocuments-operation"></a>GetNewOrModifiedFhirDocuments åtgärden

**indata**
- DatabaseId
- CollectionId
- Resurstyp för HL7 FHIR-namn
- Booleskt värde: Börja från början
- Int: Antalet returnerade dokument

**Utdata**
- Lyckades: Statuskod: 200, svar: lista över dokument (JSON-matris)
- Fel: Statuskod: 404, svar ”: inga dokument hittades för”*resursnamn '* resurstyp ”

<a id="api-app-source"></a>

**Källa för API-app**

```csharp

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
                "from Last Run Date or Beginning of Collection creation"
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

Följande bild visar hur swagger användes för att testa den [FhirNotificationApi](#api-app-source).

![Swagger-filen som används för att testa API-app](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-testing-app.png)


### <a name="azure-portal-dashboard"></a>Instrumentpanelen för Azure portal

Följande bild visar alla Azure-tjänster för den här lösningen körs i Azure-portalen.

![Azure-portalen som visar alla tjänster som används i den här HL7 FHIR-självstudien](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-portal.png)


## <a name="summary"></a>Sammanfattning

- Du har lärt dig att Azure Cosmos DB har inbyggd stöd för meddelanden om nya eller ändras dokument och hur enkelt det är att använda. 
- Genom att använda Logic Apps kan du skapa arbetsflöden utan att behöva skriva någon kod.
- Med Azure Service Bus-köer för att hantera fördelningen för HL7 FHIR-dokument.

## <a name="next-steps"></a>Nästa steg
Mer information om Azure Cosmos DB finns i den [startsidan för Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). Mer information om Logic Apps finns i [Logikappar](https://azure.microsoft.com/services/logic-apps/).


