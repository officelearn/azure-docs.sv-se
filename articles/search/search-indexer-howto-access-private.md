---
title: Indexerare som använder säkra resurser via privata slut punkter
titleSuffix: Azure Cognitive Search
description: Guide som beskriver hur du konfigurerar privata slut punkter för indexerare för att kommunicera med säkra resurser
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/07/2020
ms.openlocfilehash: 94763cee852893057348f8eea1fa74fa742f62a1
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/29/2020
ms.locfileid: "91534734"
---
# <a name="accessing-secure-resources-via-private-endpoints"></a>Åtkomst till säkra resurser via privata slut punkter

Azure-resurser (till exempel lagrings konton som används som data källor) kan konfigureras så att de bara kan nås från en speciell lista över virtuella nätverk. De kan också konfigureras för att inte tillåta åtkomst till "offentligt nätverk".
Kunder kan begära Azure Kognitiv sökning att skapa en (utgående) [privat slut punkts anslutning](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) för att säkert komma åt data från sådana data källor via indexerare.

## <a name="shared-private-link-resources-management-apis"></a>Delade privat länk resurser hanterings-API: er

Privata slut punkter som skapas av Azure Kognitiv sökning vid kund förfrågan, för att komma åt "säkra" resurser kallas *delade privata länk resurser*. Kunden är "delning"-åtkomst till en resurs (till exempel ett lagrings konto) som har Aktiver ATS till [tjänsten Azure Private Link](https://azure.microsoft.com/services/private-link/).

Azure Kognitiv sökning erbjuder via Sök hanterings-API: et, möjligheten att [skapa eller uppdatera delade privata länk resurser](https://docs.microsoft.com/rest/api/searchmanagement/sharedprivatelinkresources/createorupdate). Du kommer att använda det här API: et tillsammans med andra delade administrations-API: er för *privat länk* för att konfigurera åtkomst till en säker resurs från en Azure kognitiv sökning-indexerare.

Anslutningar för privata slut punkter till vissa resurser kan bara skapas via för hands versionen av API för Sök hantering ( `2020-08-01-Preview` ), som anges med taggen "Preview" i tabellen nedan. Resurser utan "för hands version"-taggen kan skapas via både för hands versions-API: et och GA API ( `2020-08-01` )

Här följer en lista över de Azure-resurser som utgående privata slut punkter kan skapas från Azure Kognitiv sökning. `groupId` som anges i tabellen nedan måste användas exakt (Skift läges känsligt) i API: et för att skapa en delad privat länk resurs.

| Azure-resurs | Grupp-ID |
| --- | --- |
| Azure Storage-BLOB (eller) ADLS gen 2 | `blob`|
| Azure Storage-tabeller | `table`|
| Azure Cosmos DB-SQL API | `Sql`|
| Azure SQL Database | `sqlServer`|
| Azure Database for MySQL (för hands version) | `mysqlServer`|
| Azure Key Vault | `vault` |
| Azure Functions (för hands version) | `sites` |

Listan över Azure-resurser för vilka utgående anslutningar för privata slut punkter stöds kan också frågas via den [lista över API: er som stöds](https://docs.microsoft.com/rest/api/searchmanagement/privatelinkresources/listsupported).

I den här hand boken används en blandning av [ARMClient](https://github.com/projectkudu/ARMClient) och [Postman](https://www.postman.com/) för att demonstrera REST API samtal.

> [!NOTE]
> I den här guiden antar vi att namnet på Sök tjänsten är __contoso-search__ som finns i resurs gruppen __contoso__ för en prenumeration med prenumerations-ID __00000000-0000-0000-0000-000000000000__. Resurs-ID för den här Sök tjänsten kommer att `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search`

I resten av hand boken visas hur tjänsten __contoso-search__ kan konfigureras så att dess indexerare kan komma åt data från det säkra lagrings kontot `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-storage`

## <a name="securing-your-storage-account"></a>Skydda ditt lagrings konto

Konfigurera lagrings kontot så att [endast åtkomst från vissa undernät tillåts](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-a-virtual-network). Om du markerar det här alternativet och lämnar inställningen tom via Azure Portal, innebär det att ingen trafik från något virtuellt nätverk tillåts.

   ![Virtual Network åtkomst](media\search-indexer-howto-secure-access\storage-firewall-noaccess.png "Virtual Network åtkomst")

> [!NOTE]
> Den [betrodda Microsoft Service-metoden](https://docs.microsoft.com/azure/storage/common/storage-network-security#trusted-microsoft-services) kan användas för att kringgå virtuella nätverk eller IP-begränsningar för ett sådant lagrings konto och kan göra det möjligt för Sök tjänsten att komma åt data i lagrings kontot enligt beskrivningen i [Guide](search-indexer-howto-access-trusted-service-exception.md). Men när du använder den här metoden kommunikation mellan Azure Kognitiv sökning och lagrings kontot sker via den offentliga IP-adressen för lagrings kontot, över det säkra Microsoft stamnät nätverket.

## <a name="step-1-create-a-shared-private-link-resource-to-the-storage-account"></a>Steg 1: skapa en delad privat länk resurs till lagrings kontot

Gör följande API-anrop för att begära Azure-Kognitiv sökning för att skapa en utgående privat slut punkts anslutning till lagrings kontot

`armclient PUT https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01 create-pe.json`

Innehållet i `create-pe.json` filen (som representerar begär ande texten till API: et) är följande:

```json
{
      "name": "blob-pe",
      "properties": {
        "privateLinkResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-storage",
        "groupId": "blob",
        "requestMessage": "please approve"
      }
}
```

Ett `202 Accepted` svar returneras vid lyckad-processen att skapa en utgående privat slut punkt är en tids krävande åtgärd (asynkron). Det innebär att du distribuerar följande resurser –

1. En privat slut punkt som allokerats med en privat IP-adress i ett `"Pending"` tillstånd. Den privata IP-adressen hämtas från det adress utrymme som allokerats till det virtuella nätverket för Sök tjänstens speciella körnings miljö för privat indexerare. Vid godkännande av den privata slut punkten kommer all kommunikation från Azure Kognitiv sökning till lagrings kontot från den privata IP-adressen och en säker kanal för privata länkar.
2. En privat DNS-zon för resurs typen, baserat på `groupId` . Detta säkerställer att alla DNS-sökningar till den privata resursen använder den IP-adress som är kopplad till den privata slut punkten.

Se till att ange rätt `groupId` för den typ av resurs som du skapar den privata slut punkten för. Eventuella matchnings fel resulterar i ett svarsmeddelande som inte lyckades.

Precis som alla asynkrona Azure-åtgärder `PUT` returnerar anropet ett `Azure-AsyncOperation` huvud värde som kommer att se ut så här:

`"Azure-AsyncOperation": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01"`

Denna URI kan avsökas regelbundet för att hämta status för åtgärden. Vi rekommenderar att vänta tills den delade privata länkens resurs åtgärds status har nått ett Terminal-tillstånd (det vill säga `succeeded` ) innan du fortsätter.

`armclient GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01"`

```json
{
    "status": "running" | "succeeded" | "failed"
}
```

## <a name="step-2a-approve-the-private-endpoint-connection-for-the-storage-account"></a>Steg 2a: Godkänn den privata slut punkts anslutningen för lagrings kontot

> [!NOTE]
> I det här avsnittet används Azure Portal för att gå igenom godkännande flödet för en privat slut punkt till lagringen. [REST API](https://docs.microsoft.com/rest/api/storagerp/privateendpointconnections) som är tillgängliga via Storage Resource Provider (RP) kan också användas i stället.
>
> Andra leverantörer, till exempel CosmosDB, Azure SQL Server osv., erbjuder också liknande RP-API: er för att hantera privata slut punkts anslutningar.

Gå till fliken "**anslutningar för privata slut punkter**" i lagrings kontot på Azure Portal. Det bör finnas en begäran om en privat slut punkts anslutning, med begär ande meddelandet från det tidigare API-anropet (när den asynkrona åtgärden har __slutförts__).

   ![Godkännande av privat slut punkt](media\search-indexer-howto-secure-access\storage-privateendpoint-approval.png "Godkännande av privat slut punkt")

Välj den privata slut punkt som skapades av Azure Kognitiv sökning (Använd kolumnen "privat slut punkt" för att identifiera den privata slut punkts anslutningen med det namn som anges i föregående API) och välj Godkänn, med ett lämpligt meddelande (meddelandet är inte signifikant). Se till att den privata slut punkts anslutningen visas på följande sätt (den kan var som helst från 1-2 minuter för att status ska uppdateras på portalen)

![Privat slut punkt godkänd](media\search-indexer-howto-secure-access\storage-privateendpoint-after-approval.png "Privat slut punkt godkänd")

När den privata slut punkts anslutnings förfrågan har godkänts innebär det att trafiken *kan* flöda genom den privata slut punkten. När den privata slut punkten har godkänts skapar Azure-Kognitiv sökning nödvändiga mappningar för DNS-zon i den DNS-zon som skapats för den.

## <a name="step-2b-query-the-status-of-the-shared-private-link-resource"></a>Steg 2b: fråga efter status för den delade privata länk resursen

 Kontrol lera att den delade privata länk resursen har uppdaterats efter godkännande genom att hämta dess status via [Get-API: et](https://docs.microsoft.com/rest/api/searchmanagement/sharedprivatelinkresources/get).

`armclient GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01`

Om `properties.provisioningState` resursen är `Succeeded` och `properties.status` är `Approved` , innebär det att den delade privata länk resursen fungerar och indexerare kan konfigureras för att kommunicera över den privata slut punkten.

```json
{
      "name": "blob-pe",
      "properties": {
        "privateLinkResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-storage",
        "groupId": "blob",
        "requestMessage": "please approve",
        "status": "Approved",
        "resourceRegion": null,
        "provisioningState": "Succeeded"
      }
}

```

## <a name="step-3-configure-indexer-to-run-in-the-private-environment"></a>Steg 3: Konfigurera indexeraren att köras i den privata miljön

> [!NOTE]
> Det här steget kan utföras även innan den privata slut punkts anslutningen godkänns. Tills den privata slut punkts anslutningen har godkänts, kommer alla indexerare som försöker kommunicera med en säker resurs (till exempel lagrings kontot) att bli i ett tillfälligt haveri tillstånd. Det går inte att skapa nya indexerare. Så snart anslutningen till den privata slut punkten har godkänts kommer indexerare att kunna komma åt det privata lagrings kontot.

1. [Skapa en data källa](https://docs.microsoft.com/rest/api/searchservice/create-data-source) som pekar på det säkra lagrings kontot och en lämplig behållare i lagrings kontot. Nedan visas den här begäran som utförs via Postman.
![Skapa datakälla](media\search-indexer-howto-secure-access\create-ds.png "Skapa data Källa")

2. [Skapa ett index](https://docs.microsoft.com/rest/api/searchservice/create-index) på samma sätt och eventuellt [skapa en färdigheter](https://docs.microsoft.com/rest/api/searchservice/create-skillset) med hjälp av REST API.

3. [Skapa en indexerare](https://docs.microsoft.com/rest/api/searchservice/create-indexer) som pekar på data källan, indexet och färdigheter som skapats ovan. Tvinga också indexeraren att köras i den privata körnings miljön genom att ange konfigurations egenskapen indexerare `executionEnvironment` till `"Private"` .
![Skapa indexerare](media\search-indexer-howto-secure-access\create-idr.png "Skapa indexerare")

Indexeraren bör skapas och du bör göra förlopps indexering av innehåll från lagrings kontot över den privata slut punkts anslutningen. Status för indexeraren kan övervakas via [indexerings status-API: et](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status).

> [!NOTE]
> Om du redan har befintliga indexerare kan du bara uppdatera dem via parkera- [API: et](https://docs.microsoft.com/rest/api/searchservice/create-indexer) för att ställa in på `executionEnvironment` `"Private"` .

## <a name="troubleshooting-issues"></a>Felsökning av problem

- När du skapar en indexerare, om det inte går att skapa ett fel meddelande som liknar "autentiseringsuppgifter för data källa är ogiltiga", innebär det att antingen den privata slut punkts anslutningen inte har *godkänts* eller inte fungerar.
Hämta statusen för den delade privata länk resursen med hjälp av [Get-API: et](https://docs.microsoft.com/rest/api/searchmanagement/sharedprivatelinkresources/get). Om den har *godkänts* kontrollerar du `properties.provisioningState` om resursen. Om det är det `Incomplete` innebär det att vissa av de underliggande beroendena för resursen inte kunde etablera-utfärda `PUT` begäran till "återskapa" den delade privata länk resurs som bör åtgärda problemet. Ett omgodkännande kan vara nödvändigt – kontrol lera statusen för resursen en gång till för att verifiera.
- Om indexeraren har skapats utan att ange dess kan `executionEnvironment` det hända att indexeraren lyckas, men körnings historiken visar att indexeraren körs Miss lyckas. Du bör [Uppdatera indexeraren](https://docs.microsoft.com/rest/api/searchservice/update-indexer) för att ange körnings miljön.
- Om indexeraren har skapats utan att du har angett `executionEnvironment` och den körs utan problem, innebär det att Azure kognitiv sökning har beslutat att dess körnings miljö är den Sök tjänst som är speciell "privat"-miljö. Detta kan dock ändras baserat på en mängd olika faktorer (resurser som förbrukas av indexeraren, belastningen på Sök tjänsten och så vidare) och kan inte köras vid ett senare tillfälle – vi rekommenderar starkt att du ställer in `executionEnvironment` så att `"Private"` det inte kommer att Miss klart i framtiden.
- [Kvoter och gränser](search-limits-quotas-capacity.md) avgör hur många delade privata länk resurser som kan skapas och är beroende av SKU: n för Sök tjänsten.

## <a name="next-steps"></a>Nästa steg

Läs mer om privata slut punkter:

- [Vad är privata slut punkter?](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)
- [DNS-konfigurationer som behövs för privata slut punkter](https://docs.microsoft.com/azure/private-link/private-endpoint-dns)