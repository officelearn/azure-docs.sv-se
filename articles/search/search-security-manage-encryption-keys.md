---
title: Kryptering i vila med kundhanterade nycklar
titleSuffix: Azure Cognitive Search
description: Komplettera kryptering på serversidan över index och synonymmappningar i Azure Cognitive Search med hjälp av nycklar som du skapar och hanterar i Azure Key Vault.
manager: nitinme
author: NatiNimni
ms.author: natinimn
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: cb17fe24339ad618229b3456ece15c206f79bdb7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76899945"
---
# <a name="encryption-at-rest-of-content-in-azure-cognitive-search-using-customer-managed-keys-in-azure-key-vault"></a>Krypteringsstöd av innehåll i Azure Cognitive Search med kundhanterade nycklar i Azure Key Vault

Som standard krypterar Azure Cognitive Search indexerat innehåll i vila med [tjänsthanterade nycklar](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest#data-encryption-models). Du kan komplettera standardkryptering med ett extra krypteringslager med hjälp av nycklar som du skapar och hanterar i Azure Key Vault. I den här artikeln får du hjälp med stegen.

Kryptering på serversidan stöds genom integrering med [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview). Du kan skapa dina egna krypteringsnycklar och lagra dem i ett nyckelvalv, eller så kan du använda Azure Key Vault API:er för att generera krypteringsnycklar. Med Azure Key Vault kan du också granska nyckelanvändning. 

Kryptering med kundhanterade nycklar konfigureras på index- eller synonymkartuppnivån när dessa objekt skapas och inte på söktjänstnivå. Du kan inte kryptera innehåll som redan finns. 

Alla nycklar behöver inte finnas i samma nyckelvalv. En enda söktjänst kan vara värd för flera krypterade index eller synonymkartor som krypterats med sina egna kundhanterade krypteringsnycklar lagrade i olika Nyckelvalv.  Du kan också ha index och synonymmappningar i samma tjänst som inte krypteras med kundhanterade nycklar. 

> [!IMPORTANT] 
> Den här funktionen är tillgänglig på [REST API-versionen 2019-05-06](https://docs.microsoft.com/rest/api/searchservice/) och [.NET SDK version 8.0-preview](search-dotnet-sdk-migration-version-9.md). Det finns för närvarande inget stöd för att konfigurera kundhanterade krypteringsnycklar i Azure-portalen. Söktjänsten måste skapas efter januari 2019 och kan inte vara en kostnadsfri (delad) tjänst.

## <a name="prerequisites"></a>Krav

Följande tjänster används i det här exemplet. 

+ [Skapa en Azure Cognitive Search-tjänst](search-create-service-portal.md) eller [hitta en befintlig tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) under din aktuella prenumeration. 

+ [Skapa en Azure Key Vault-resurs](https://docs.microsoft.com/azure/key-vault/quick-create-portal#create-a-vault) eller hitta ett befintligt valv under din prenumeration.

+ [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) eller [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) används för konfigurationsuppgifter.

+ [Postman,](search-get-started-postman.md) [Azure PowerShell](search-create-index-rest-api.md) och [Azure Cognitive Search SDK](https://aka.ms/search-sdk-preview) kan användas för att anropa REST API. Det finns ingen portalsupport för kundhanterad kryptering just nu.

>[!Note]
> På grund av krypteringens natur med funktionen hanterade nycklar för kunder kan Azure Cognitive Search inte hämta dina data om din Azure Key vault-nyckel tas bort. Om du vill förhindra dataförlust orsakad av oavsiktliga nyckelvalvsborttagningar **måste** du aktivera soft delete- och rensningsskydd i Key Vault innan det kan användas. Mer information finns i [Azure Key Vault soft-delete](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete).   

## <a name="1---enable-key-recovery"></a>1 - Aktivera nyckelåterställning

När du har skapat Azure Key Vault-resursen aktiverar du **skydd för mjuk borttagning** och **rensning** i det valda nyckelvalvet genom att köra följande PowerShell- eller Azure CLI-kommandon:   

```powershell
$resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "<vault_name>").ResourceId

$resource.Properties | Add-Member -MemberType NoteProperty -Name "enableSoftDelete" -Value 'true'

$resource.Properties | Add-Member -MemberType NoteProperty -Name "enablePurgeProtection" -Value 'true'

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

```azurecli-interactive
az keyvault update -n <vault_name> -g <resource_group> --enable-soft-delete --enable-purge-protection
```

## <a name="2---create-a-new-key"></a>2 - Skapa en ny nyckel

Om du använder en befintlig nyckel för att kryptera Azure Cognitive Search-innehåll hoppar du över det här steget.

1. [Logga in på Azure-portalen](https://portal.azure.com) och navigera till nyckelvalvets instrumentpanel.

1. Välj inställningen **Nycklar** i det vänstra navigeringsfönstret och klicka på **+ Generera/importera**.

1. Välj den metod som du vill använda för att skapa en nyckel i listan **Alternativ**i fönstret **Skapa en** nyckel i fönstret Skapa en nyckel. Du kan **generera** en ny nyckel, **ladda upp** en befintlig nyckel eller använda **Återställ säkerhetskopiering** för att välja en säkerhetskopia av en nyckel.

1. Ange ett **namn** för nyckeln och välj eventuellt andra nyckelegenskaper.

1. Klicka på knappen **Skapa** för att starta distributionen.

Anteckna nyckelidentifieraren – detta består av **nyckelvärdet Uri**, **nyckelnamnet**och **nyckelversionen**. Du behöver dessa för att definiera ett krypterat index i Azure Cognitive Search.
 
![Skapa en ny nyckelnyckel för nyckelvalv](./media/search-manage-encryption-keys/create-new-key-vault-key.png "Skapa en ny nyckelnyckel för nyckelvalv")

## <a name="3---create-a-service-identity"></a>3 - Skapa en tjänstidentitet

Genom att tilldela en identitet till söktjänsten kan du bevilja behörigheter för Key Vault till söktjänsten. Söktjänsten använder sin identitet för att autentisera med Azure Key-valv.

Azure Cognitive Search stöder två sätt att tilldela identitet: en hanterad identitet eller ett externt hanterat Azure Active Directory-program. 

Använd om möjligt en hanterad identitet. Det är det enklaste sättet att tilldela en identitet till din söktjänst och bör fungera i de flesta scenarier. Om du använder flera nycklar för index och synonymmappningar, eller om din lösning finns i en distribuerad arkitektur som diskvalificerar identitetsbaserad autentisering, använder du den avancerade [externt hanterade Azure Active Directory-metoden](#aad-app) som beskrivs i slutet av den här artikeln.

 I allmänhet gör en hanterad identitet det möjligt för söktjänsten att autentisera till Azure Key Vault utan att lagra autentiseringsuppgifter i kod. Livscykeln för den här typen av hanterad identitet är knuten till livscykeln för söktjänsten, som bara kan ha en hanterad identitet. [Läs mer om hanterade identiteter](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

1. Om du vill skapa en hanterad identitet [loggar du in påAzure-portalen](https://portal.azure.com) och öppnar instrumentpanelen för söktjänsten. 

1. Klicka på **Identitet** i det vänstra navigeringsfönstret, ändra dess status till **På**och klicka på **Spara**.

![Aktivera en hanterad identitet](./media/search-enable-msi/enable-identity-portal.png "Aktivera en manged identitet")

## <a name="4---grant-key-access-permissions"></a>4 - Bevilja behörigheter för nyckelåtkomst

Om du vill att söktjänsten ska kunna använda key vault-nyckeln måste du ge söktjänsten vissa åtkomstbehörigheter.

Åtkomstbehörigheter kan återkallas när som helst. När det har återkallats blir alla söktjänstindex eller synonymmappningar som använder nyckelvalvet oanvändbara. Om du återställer behörigheter för nyckelvalvsåtkomst vid ett senare tillfälle återställs index\synonymmappningsåtkomst. Mer information finns i [Säker åtkomst till ett nyckelvalv](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault).

1. [Logga in på Azure-portalen](https://portal.azure.com) och öppna översiktssidan för nyckelvalvet. 

1. Välj inställningen **Access-principer** i det vänstra navigeringsfönstret och klicka på **+Lägg till ny**.

   ![Lägg till ny princip för nyckelvalvsåtkomst](./media/search-manage-encryption-keys/add-new-key-vault-access-policy.png "Lägg till ny princip för nyckelvalvsåtkomst")

1. Klicka på **Välj huvudnamn** och välj din Azure Cognitive Search-tjänst. Du kan söka efter den efter namn eller efter objekt-ID som visades efter att ha aktiverat hanterad identitet.

   ![Välj huvudprincip för nyckelvalvsåtkomstprincip](./media/search-manage-encryption-keys/select-key-vault-access-policy-principal.png "Välj huvudprincip för nyckelvalvsåtkomstprincip")

1. Klicka på **Nyckelbehörigheter** och välj *Hämta*, *Ta bort nyckel* och *radbrytsnyckel*. Du kan använda Azure *Data Lake Storage eller Azure Storage-mallen* för att snabbt välja de behörigheter som krävs.

   Azure Cognitive Search måste beviljas med följande [åtkomstbehörigheter:](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-operations)

   * *Get* - gör att din söktjänst kan hämta de offentliga delarna av din nyckel i ett Key Vault
   * *Wrap Key* - gör att din söktjänst kan använda din nyckel för att skydda den interna krypteringsnyckeln
   * *Packa upp nyckel* - gör att din söktjänst kan använda din nyckel för att packa upp den interna krypteringsnyckeln

   ![Välj behörigheter för nyckel för nyckel för nyckel för nyckel för nyckel för nyckel för nyckel för nyckel för nyckel för nyckel för nyckel för](./media/search-manage-encryption-keys/select-key-vault-access-policy-key-permissions.png "Välj behörigheter för nyckel för nyckel för nyckel för nyckel för nyckel för nyckel för nyckel för nyckel för nyckel för nyckel för nyckel för")

1. Klicka på **OK** och **Spara** ändringarna i åtkomstprincipen.

> [!Important]
> Krypterat innehåll i Azure Cognitive Search är konfigurerat för att använda en specifik Azure Key Vault-nyckel med en viss **version**. Om du ändrar nyckeln eller versionen måste index- eller synonymkartan uppdateras för att den nya nyckeln\version ska kunna användas **innan** den tidigare nyckelversionen tas bort. Om du inte gör det kan indexet eller synonymkartan vara oanvändbar, eftersom du inte kan dekryptera innehållet när tangentåtkomsten har gått förlorad.   

## <a name="5---encrypt-content"></a>5 - Kryptera innehåll

Det är ännu inte möjligt att skapa en index- eller synonymkarta krypterad med kundhanterad nyckel med Azure-portalen. Använd Azure Cognitive Search REST API för att skapa en sådan index- eller synonymkarta.

Både index- och synonymkartan stöder en ny **krypteringsnyckelegenskap** på den högsta nivån som används för att ange nyckeln. 

Med hjälp av **nyckelvalvet Uri**, **nyckelnamn** och **nyckelversionen** av nyckeln till key vault kan vi skapa en **encryptionKey-definition:**

```json
{
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
  }
}
```
> [!Note] 
> Ingen av dessa viktiga valvinformation anses vara hemlig och kan enkelt hämtas genom att bläddra till relevant Azure Key Vault-nyckelsida i Azure-portalen.

Om du använder ett AAD-program för Key Vault-autentisering i stället för att använda en hanterad identitet lägger du till AAD-programmets **åtkomstautentiseringsuppgifter** till krypteringsnyckeln: 
```json
{
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

## <a name="example-index-encryption"></a>Exempel: Indexkryptering
Information om hur du skapar ett nytt index via REST API kunde hittas på [Create Index (Azure Cognitive Search REST API),](https://docs.microsoft.com/rest/api/searchservice/create-index)där den enda skillnaden här är att ange krypteringsnyckelinformation som en del av indexdefinitionen: 

```json
{
 "name": "hotels",  
 "fields": [
  {"name": "HotelId", "type": "Edm.String", "key": true, "filterable": true},
  {"name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false},
  {"name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene"},
  {"name": "Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
  {"name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
  {"name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true},
  {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true},
  {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true},
  {"name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true},
  {"name": "Location", "type": "Edm.GeographyPoint", "filterable": true, "sortable": true},
 ],
 "encryptionKey": {
   "keyVaultUri": "https://demokeyvault.vault.azure.net",
   "keyVaultKeyName": "myEncryptionKey",
   "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
 }
}
```
Du kan nu skicka begäran om att skapa index och sedan börja använda indexet normalt.

## <a name="example-synonym-map-encryption"></a>Exempel: Synonym kartkryptering

Information om hur du skapar en ny synonymkarta via REST API finns på [Skapa synonymkarta (Azure Cognitive Search REST API),](https://docs.microsoft.com/rest/api/searchservice/create-synonym-map)där den enda skillnaden här är att ange krypteringsnyckelinformation som en del av synonymkartdefinitionen: 

```json
{   
  "name" : "synonymmap1",  
  "format" : "solr",  
  "synonyms" : "United States, United States of America, USA\n
  Washington, Wash. => WA",
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
  }
}
```
Du kan nu skicka begäran om att skapa synonymkarta och sedan börja använda den normalt.

>[!Important] 
> **Krypteringsnyckeln** kan inte läggas till i befintliga Azure Cognitive Search-index eller synonymmappningar, men den kan uppdateras genom att ange olika värden för någon av de tre nyckelvalvsinformationen (till exempel uppdatera nyckelversionen). När du ändrar till en ny Key Vault-nyckel eller en ny nyckelversion måste alla Azure Cognitive Search-index eller synonymmappning som använder nyckeln först uppdateras för att kunna använda den nya key\versionen **innan** den tidigare nyckelversionen tas bort. Om du inte gör det kan indexet eller synonymkartan göras oanvändbar, eftersom den inte kan dekryptera innehållet när tangentåtkomsten har gått förlorad.   
> Om du återställer åtkomstbehörigheter för nyckelvalv vid ett senare tillfälle återställs åtkomsten till innehåll.

## <a name="advanced-use-an-externally-managed-azure-active-directory-application"></a><a name="aad-app"></a>Avancerat: Använda ett externt hanterat Azure Active Directory-program

När en hanterad identitet inte är möjlig kan du skapa ett Azure Active Directory-program med ett säkerhetsobjekt för din Azure Cognitive Search-tjänst. Närmare bestämt är en hanterad identitet inte genomförbar under dessa förhållanden:

* Du kan inte direkt bevilja din söktjänst åtkomstbehörigheter till Nyckelvalvet (till exempel om söktjänsten finns i en annan Active Directory-klient än Azure Key Vault).

* En enda söktjänst krävs för att vara värd för flera krypterade index\synonymkartor, var och en med en annan nyckel än ett annat nyckelvalv, där varje nyckelvalv måste använda **en annan identitet** för autentisering. Om det inte är ett krav att använda en annan identitet för att hantera olika nyckelvalv kan du överväga att använda alternativet hanterad identitet ovan.  

För att hantera sådana topologier stöder Azure Cognitive Search att använda Azure Active Directory (AAD) program för autentisering mellan söktjänsten och Key Vault.    
Så här skapar du ett AAD-program i portalen:

1. [Skapa ett program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application).

1. [Hämta program-ID och autentiseringsnyckel](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) som de kommer att krävas för att skapa ett krypterat index. Värden som du måste ange inkluderar **program-ID** och **autentiseringsnyckel**.

>[!Important]
> När du bestämmer dig för att använda ett AAD-program för autentisering i stället för en hanterad identitet bör du tänka på att Azure Cognitive Search inte har behörighet att hantera ditt AAD-program för din räkning, och det är upp till dig att hantera ditt AAD-program, till exempel periodiska rotation av programautentiseringsnyckeln.
> När du ändrar ett AAD-program eller dess autentiseringsnyckel måste alla Azure Cognitive Search-index eller synonymmappning som använder programmet först uppdateras för att använda den nya program-ID\nyckeln **innan** du tar bort det tidigare programmet eller dess auktoriseringsnyckel och innan du återkallar åtkomsten till Key Vault till det.
> Om du inte gör det kan indexet eller synonymkartan göras oanvändbar, eftersom den inte kan dekryptera innehållet när tangentåtkomsten har gått förlorad.   

## <a name="next-steps"></a>Nästa steg

Om du inte känner till Azure-säkerhetsarkitektur läser du [Azure Security-dokumentationen](https://docs.microsoft.com/azure/security/)och i synnerhet den här artikeln:

> [!div class="nextstepaction"]
> [Datakryptering i vila](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
