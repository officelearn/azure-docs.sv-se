---
title: Kryptering – rest med Kundhanterade nycklar
titleSuffix: Azure Cognitive Search
description: Kompletterande kryptering på Server sidan över index och synonymer mappar i Azure Kognitiv sökning att använda nycklar som du skapar och hanterar i Azure Key Vault.
manager: nitinme
author: NatiNimni
ms.author: natinimn
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/01/2020
ms.custom: references_regions
ms.openlocfilehash: 2dc7458dd905ff84455927c81b4ea93765d4f5cb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "88928827"
---
# <a name="configure-customer-managed-keys-for-data-encryption-in-azure-cognitive-search"></a>Konfigurera Kundhanterade nycklar för data kryptering i Azure Kognitiv sökning

Azure Kognitiv sökning krypterar automatiskt indexerat innehåll i vila med [tjänst hanterade nycklar](../security/fundamentals/encryption-atrest.md#azure-encryption-at-rest-components). Om du behöver mer skydd kan du komplettera standard kryptering med ytterligare ett krypterings lager med hjälp av nycklar som du skapar och hanterar i Azure Key Vault. Den här artikeln vägleder dig genom stegen för att konfigurera CMK-kryptering.

CMK-kryptering är beroende av [Azure Key Vault](../key-vault/general/overview.md). Du kan skapa egna krypterings nycklar och lagra dem i ett nyckel valv, eller så kan du använda Azure Key Vault s API: er för att generera krypterings nycklar. Med Azure Key Vault kan du också granska nyckel användningen om du [aktiverar loggning](../key-vault/general/logging.md).  

Kryptering med Kundhanterade nycklar tillämpas på enskilda index eller synonyma mappningar när objekten skapas och anges inte på själva Sök tjänst nivån. Endast nya objekt kan krypteras. Det går inte att kryptera innehåll som redan finns.

Nycklar behöver inte vara i samma nyckel valv. En enskild Sök tjänst kan vara värd för flera krypterade index eller synonyma Maps, var och en krypterad med sina egna Kundhanterade krypterings nycklar som lagras i olika nyckel valv. Du kan också ha index och synonym mappningar i samma tjänst som inte är krypterade med Kundhanterade nycklar. 

## <a name="double-encryption"></a>Dubbel kryptering

För tjänster som skapats efter 1 augusti 2020 och i vissa regioner, innehåller omfånget för CMK-kryptering temporära diskar, vilket ger [fullständig dubbel kryptering](search-security-overview.md#double-encryption), som för närvarande är tillgängligt i följande regioner: 

+ USA, västra 2
+ East US
+ USA, södra centrala
+ US Gov, Virginia
+ US Gov, Arizona

Om du använder en annan region eller en tjänst som skapats före den 1 augusti, är din CMK-kryptering begränsad till enbart data disken, förutom de temporära diskar som används av tjänsten.

## <a name="prerequisites"></a>Förutsättningar

Följande tjänster och tjänster används i det här exemplet. 

+ [Skapa en Azure kognitiv sökning-tjänst](search-create-service-portal.md) eller [hitta en befintlig tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). 

+ [Skapa en Azure Key Vault resurs](../key-vault/secrets/quick-create-portal.md#create-a-vault) eller hitta ett befintligt valv i samma prenumeration som Azure kognitiv sökning. Den här funktionen har ett krav för samma prenumeration.

+ [Azure PowerShell](/powershell/azure/) eller [Azure CLI](/cli/azure/install-azure-cli) används för konfigurations uppgifter.

+ [Postman](search-get-started-postman.md), [Azure POWERSHELL](./search-get-started-powershell.md) och [.NET SDK-förhands granskning](https://aka.ms/search-sdk-preview) kan användas för att anropa REST API som skapar index och synonymer mappningar som innehåller en krypterings nyckel parameter. Det finns inget stöd för portalen för att lägga till en nyckel i index eller synonym Maps för tillfället.

>[!Note]
> På grund av krypterings typen med Kundhanterade nycklar kommer Azure Kognitiv sökning inte att kunna hämta dina data om din Azure Key Vault-nyckel tas bort. För att förhindra data förlust som orsakas av oavsiktliga Key Vault nyckel borttagningar måste du aktivera mjuk borttagning och rensnings skydd i nyckel valvet. Mjuk borttagning är aktiverat som standard, så du kommer bara att stöta på problem om du har inaktiverat det. Rensnings skydd är inte aktiverat som standard, men det krävs för Azure Kognitiv sökning CMK-kryptering. Mer information finns i [mjuk borttagning](../key-vault/general/soft-delete-overview.md) och rensning av [skydds](../key-vault/general/soft-delete-overview.md#purge-protection) översikter.

## <a name="1---enable-key-recovery"></a>1 – aktivera nyckel återställning

Nyckel valvet måste ha aktiverat **skydd mot** **borttagning** och rensning. Du kan ställa in dessa funktioner med hjälp av portalen eller följande PowerShell-eller Azure CLI-kommandon.

### <a name="using-powershell"></a>Använda PowerShell

1. Kör `Connect-AzAccount` för att konfigurera dina Azure-autentiseringsuppgifter.

1. Kör följande kommando för att ansluta till nyckel valvet och Ersätt `<vault_name>` med ett giltigt namn:

   ```powershell
   $resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "<vault_name>").ResourceId
   ```

1. Azure Key Vault skapas med mjuk borttagning aktiverat. Om den är inaktive rad i valvet kör du följande kommando:

   ```powershell
   $resource.Properties | Add-Member -MemberType NoteProperty -Name "enableSoftDelete" -Value 'true'
   ```

1. Aktivera rensnings skydd:

   ```powershell
   $resource.Properties | Add-Member -MemberType NoteProperty -Name "enablePurgeProtection" -Value 'true'
   ```

1. Spara dina uppdateringar:

   ```powershell
   Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
   ```

### <a name="using-azure-cli"></a>Använda Azure CLI

```azurecli-interactive
az keyvault update -n <vault_name> -g <resource_group> --enable-soft-delete --enable-purge-protection
```

## <a name="2---create-a-new-key"></a>2 – Skapa en ny nyckel

Om du använder en befintlig nyckel för att kryptera Azure Kognitiv sökning innehåll hoppar du över det här steget.

1. [Logga in på Azure Portal](https://portal.azure.com) och öppna översikts sidan för Key Vault.

1. Välj inställningen **nycklar** i det vänstra navigerings fönstret och klicka på **+ generera/importera**.

1. I fönstret **skapa en nyckel** , i listan med **alternativ**, väljer du den metod som du vill använda för att skapa en nyckel. Du kan **generera** en ny nyckel, **överföra** en befintlig nyckel eller använda **Återställ säkerhets kopia** för att välja en säkerhets kopia av en nyckel.

1. Ange ett **namn** för nyckeln och välj även andra nyckel egenskaper.

1. Klicka på knappen **skapa** för att starta distributionen.

Anteckna nyckel identifieraren – detta består av **nyckel värdets URI**, **nyckel namnet**och **nyckel versionen**. Du behöver dessa för att definiera ett krypterat index i Azure Kognitiv sökning.
 
![Skapa en ny nyckel valvs nyckel](./media/search-manage-encryption-keys/create-new-key-vault-key.png "Skapa en ny nyckel valvs nyckel")

## <a name="3---create-a-service-identity"></a>3 – skapa en tjänst identitet

Genom att tilldela en identitet till din Sök tjänst kan du ge Key Vault åtkomst behörighet till din Sök tjänst. Sök tjänsten använder sin identitet för att autentisera med Azure Key Vault.

Azure Kognitiv sökning har stöd för två sätt att tilldela identiteter: en hanterad identitet eller ett externt hanterat Azure Active Directory-program. 

Använd om möjligt en hanterad identitet. Det är det enklaste sättet att tilldela en identitet till din Sök tjänst och bör fungera i de flesta fall. Om du använder flera nycklar för index och synonymer Maps, eller om din lösning finns i en distribuerad arkitektur som dekvalificerar identitetsbaserade autentisering, använder du [metoden avancerad extern hantering Azure Active Directory](#aad-app) som beskrivs i slutet av den här artikeln.

 I allmänhet gör en hanterad identitet att Sök tjänsten kan autentisera till Azure Key Vault utan att lagra autentiseringsuppgifter i kod. Livs cykeln för den här typen av hanterad identitet är kopplad till livs cykeln för din Sök tjänst, som bara kan ha en hanterad identitet. [Läs mer om hanterade identiteter](../active-directory/managed-identities-azure-resources/overview.md).

1. [Logga in på Azure Portal](https://portal.azure.com) och öppna översikts sidan för Sök tjänsten. 

1. Klicka på **identitet** i det vänstra navigerings fönstret, ändra status till **på**och klicka sedan på **Spara**.

![Aktivera en hanterad identitet](./media/search-enable-msi/enable-identity-portal.png "Aktivera en hanterade-identitet")

## <a name="4---grant-key-access-permissions"></a>4 – bevilja nyckel åtkomst behörigheter

Om du vill att Sök tjänsten ska använda din Key Vault nyckel måste du ge Sök tjänsten vissa åtkomst behörigheter.

Åtkomst behörigheterna kan återkallas vid en angiven tidpunkt. När den har återkallats blir alla Sök tjänst index eller synonym mappningar som använder det nyckel valvet oanvändbara. Att återställa åtkomst behörigheter för Key Vault vid ett senare tillfälle kommer att återställa index\synonym Map-åtkomst. Mer information finns i [säker åtkomst till ett nyckel valv](../key-vault/general/secure-your-key-vault.md).

1. [Logga in på Azure Portal](https://portal.azure.com) och öppna översikts sidan för Key Vault. 

1. Välj inställningen **åtkomst principer** i det vänstra navigerings fönstret och klicka på **+ Lägg till ny**.

   ![Lägg till ny åtkomst princip för nyckel valv](./media/search-manage-encryption-keys/add-new-key-vault-access-policy.png "Lägg till ny åtkomst princip för nyckel valv")

1. Klicka på **Välj huvud konto** och välj din Azure kognitiv sökning-tjänst. Du kan söka efter den efter namn eller med det objekt-ID som visades när den hanterade identiteten aktiverades.

   ![Välj nyckel valv åtkomst principens huvud namn](./media/search-manage-encryption-keys/select-key-vault-access-policy-principal.png "Välj nyckel valv åtkomst principens huvud namn")

1. Klicka på **nyckel behörigheter** och välj *Hämta*, *packa upp nyckel* och *Radbryt nyckel*. Du kan använda mallen *Azure Data Lake Storage eller Azure Storage* för att snabbt välja de behörigheter som krävs.

   Azure Kognitiv sökning måste beviljas med följande [åtkomst behörigheter](../key-vault/keys/about-keys.md#key-operations):

   * *Get* -tillåter att Sök tjänsten hämtar offentliga delar av nyckeln i en Key Vault
   * *Wrap-tangenten* – tillåter att din Sök tjänst använder din nyckel för att skydda den interna krypterings nyckeln
   * *Unwrap-nyckel* – tillåter att Sök tjänsten använder din nyckel för att packa upp den interna krypterings nyckeln

   ![Välj nyckel valv åtkomst princip nyckel behörigheter](./media/search-manage-encryption-keys/select-key-vault-access-policy-key-permissions.png "Välj nyckel valv åtkomst princip nyckel behörigheter")

1. För **hemliga behörigheter**väljer du *Hämta*.

1. För **certifikat behörigheter**väljer du *Hämta*.

1. Klicka på **OK** och **Spara** ändringarna i åtkomst principen.

> [!Important]
> Krypterat innehåll i Azure Kognitiv sökning konfigureras för att använda en speciell Azure Key Vault nyckel med en angiven **version**. Om du ändrar nyckeln eller versionen måste mappningen index eller synonymer uppdateras för att använda den nya key\version **innan** du tar bort föregående key\version. Om du inte gör det kommer index eller synonym mappning inte att kunna användas, men du kan inte dekryptera innehållet när nyckel åtkomsten har gått förlorad.   

## <a name="5---encrypt-content"></a>5 – Kryptera innehåll

Om du vill lägga till en kundhanterad nyckel på en index-eller synonym karta måste du använda [sök REST API](/rest/api/searchservice/) eller ett SDK. Portalen visar inte synonym kartor eller krypterings egenskaper. När du använder ett giltigt API stöder både index-och synonym Maps en **encryptionKey** -egenskap på översta nivån. 

Använd **Key Vault-URI: n**, **nyckel namnet** och **nyckel versionen** av nyckeln för nyckel valvet och skapa en **encryptionKey** -definition på följande sätt:

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
> Ingen av dessa nyckel valvs uppgifter betraktas som hemliga och kan enkelt hämtas genom att bläddra till den relevanta Azure Key Vault nyckel sidan i Azure Portal.

Om du använder ett AAD-program för Key Vault autentisering i stället för att använda en hanterad identitet, lägger du till AAD-appens **åtkomst uppgifter** till din krypterings nyckel: 
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

## <a name="example-index-encryption"></a>Exempel: index kryptering
Information om hur du skapar ett nytt index via REST API finns i [create index (Azure Kognitiv sökning REST API)](/rest/api/searchservice/create-index), där den enda skillnaden här anger krypterings nyckel informationen som en del av index definitionen: 

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
Nu kan du skicka begäran om att skapa index och sedan börja använda indexet som vanligt.

## <a name="example-synonym-map-encryption"></a>Exempel: synonym kart kryptering

Information om hur du skapar en ny synonym karta via REST API finns på [skapa synonym karta (Azure Kognitiv sökning REST API)](/rest/api/searchservice/create-synonym-map), där den enda skillnaden här anger krypterings nyckel informationen som en del av synonym mappnings definitionen: 

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
Nu kan du skicka en förfrågan om att skapa synonym mappning och sedan börja använda den som vanligt.

>[!Important] 
> Även om **encryptionKey** inte kan läggas till i befintliga Azure kognitiv sökning-index eller synonym Maps, kan det uppdateras genom att ange olika värden för någon av de tre nyckel valvs detaljerna (till exempel uppdatering av nyckel versionen). När du ändrar till en ny Key Vault nyckel eller en ny nyckel version måste alla Azure Kognitiv sökning-index eller synonym mappningar som använder nyckeln först uppdateras för att använda den nya key\version **innan** du tar bort föregående key\version. Om du inte gör det kommer indexet eller synonym mappningen att bli oanvändbar, eftersom den inte kan dekryptera innehållet när nyckel åtkomsten förlorats.   
> Att återställa åtkomst behörigheter för Key Vault vid ett senare tillfälle kommer att återställa innehålls åtkomsten.

## <a name="advanced-use-an-externally-managed-azure-active-directory-application"></a><a name="aad-app"></a> Avancerat: Använd ett externt hanterat Azure Active Directory program

När en hanterad identitet inte är möjlig kan du skapa ett Azure Active Directory-program med ett säkerhets objekt för Azure Kognitiv sökning-tjänsten. Mer specifikt är en hanterad identitet inte livskraftig under följande omständigheter:

* Du kan inte direkt bevilja dina åtkomst behörigheter för Sök tjänsten till nyckel valvet (till exempel om Sök tjänsten är i en annan Active Directory klient än Azure Key Vault).

* En enskild Sök tjänst krävs för att vara värd för flera krypterade indexes\synonym-mappningar, var och en använder en annan nyckel från ett annat nyckel valv, där varje nyckel valv måste använda **en annan identitet** för autentisering. Om du inte behöver använda en annan identitet för att hantera olika nyckel valv, bör du överväga att använda alternativet hanterad identitet ovan.  

För att kunna hantera sådana topologier stöder Azure Kognitiv sökning användning av Azure Active Directory-program (AAD) för autentisering mellan Sök tjänsten och Key Vault.    
Så här skapar du ett AAD-program i portalen:

1. [Skapa ett program i Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md).

1. [Hämta program-ID och autentiseringsnyckel](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) som krävs för att skapa ett krypterat index. Värden du måste ange innehåller **program-ID** och **autentiseringsnyckel**.

>[!Important]
> När du bestämmer dig för att använda en AAD-tillämpning av autentisering i stället för en hanterad identitet, bör du tänka på att Azure Kognitiv sökning inte har behörighet att hantera ditt AAD-program för din räkning, och att det är upp till dig att hantera ditt AAD-program, till exempel regelbunden rotation av appens autentiseringsnyckel.
> När du ändrar ett AAD-program eller dess autentiseringsnyckel, måste alla Azure Kognitiv sökning-index eller synonym mappningar som använder det programmet först uppdateras för att använda det nya programmet ID\key **innan** du tar bort det tidigare programmet eller dess auktoriseringskod, och innan du återkallar din Key Vault åtkomst till det.
> Om du inte gör det kommer indexet eller synonym mappningen att bli oanvändbar, eftersom den inte kan dekryptera innehållet när nyckel åtkomsten förlorats.

## <a name="work-with-encrypted-content"></a>Arbeta med krypterat innehåll

Med CMK-kryptering kommer du att märka svars tider för både indexering och frågor på grund av det extra krypterings-/dekrypterings arbetet. Azure Kognitiv sökning loggar inte krypterings aktivitet, men du kan övervaka nyckel åtkomst via loggning av nyckel valv. Vi rekommenderar att du [aktiverar loggning](../key-vault/general/logging.md) som en del av Key Vault-inställningarna.

Nyckel rotation förväntas ske över tid. När du roterar nycklar är det viktigt att följa den här ordningen:

1. [Avgör vilken nyckel som används av ett index eller en synonym mappning](search-security-get-encryption-keys.md).
1. [Skapa en ny nyckel i Key Vault](../key-vault/keys/quick-create-portal.md), men lämna den ursprungliga nyckeln tillgänglig.
1. [Uppdatera egenskaperna för encryptionKey](/rest/api/searchservice/update-index) på en index-eller synonym mappning så att de använder de nya värdena. Endast objekt som ursprungligen har skapats med den här egenskapen kan uppdateras för att använda ett annat värde.
1. Inaktivera eller ta bort föregående nyckel i nyckel valvet. Övervaka nyckel åtkomst för att verifiera att den nya nyckeln används.

Av prestanda skäl cachelagrar Sök tjänsten nyckeln i upp till flera timmar. Om du inaktiverar eller tar bort nyckeln utan att ange en ny, fortsätter frågorna att fungera temporärt tills cachen upphör att gälla. Men när Sök tjänsten inte kan dekryptera innehåll visas följande meddelande: "åtkomst förbjuden. Den frågeparameter som används kan ha återkallats-försök igen. " 

## <a name="next-steps"></a>Nästa steg

Om du inte är bekant med Azures säkerhets arkitektur kan du läsa [dokumentationen för Azure-säkerhet](../security/index.yml)och i synnerhet den här artikeln:

> [!div class="nextstepaction"]
> [Vilande datakryptering](../security/fundamentals/encryption-atrest.md)