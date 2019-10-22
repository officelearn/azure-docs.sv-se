---
title: Kryptering – rest med Kundhanterade nycklar i Azure Key Vault (för hands version) – Azure Search
description: Kompletterande kryptering på Server sidan över index och synonymer mappar i Azure Search via nycklar som du skapar och hanterar i Azure Key Vault.
author: NatiNimni
manager: nitinme
ms.author: natinimn
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.custom: ''
ms.openlocfilehash: ce7a8af1416664a3a94b248c95203c8e775e805c
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2019
ms.locfileid: "70182405"
---
# <a name="azure-search-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Azure Search kryptering med Kundhanterade nycklar i Azure Key Vault

> [!Note]
> Kryptering med Kundhanterade nycklar är i för hands version och är inte avsedd för produktions användning. Den [REST API version 2019-05-06 – för hands version](search-api-preview.md) innehåller den här funktionen. Du kan också använda .NET SDK version 8,0-Preview.
>
> Den här funktionen är inte tillgänglig för kostnads fria tjänster. Du måste använda en fakturerbar Sök tjänst som skapats på eller efter 2019-01-01. Det finns för närvarande inget stöd för portalen.

Som standard krypterar Azure Search användar innehåll i vila med [tjänst hanterade nycklar](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest#data-encryption-models). Du kan komplettera standard kryptering med ytterligare ett krypterings lager med hjälp av nycklar som du skapar och hanterar i Azure Key Vault. Den här artikeln vägleder dig genom stegen.

Kryptering på Server sidan stöds genom integrering med [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview). Du kan skapa egna krypterings nycklar och lagra dem i ett nyckel valv, eller så kan du använda Azure Key Vault s API: er för att generera krypterings nycklar. Med Azure Key Vault kan du också granska nyckel användningen. 

Kryptering med Kundhanterade nycklar konfigureras på mappnings nivån index eller synonym när objekten skapas och inte på Sök tjänst nivå. Det går inte att kryptera innehåll som redan finns. 

Du kan använda olika nycklar från olika nyckel valv. Det innebär att en enskild Sök tjänst kan vara värd för flera krypterade indexes\synonym-kartor, varje krypterad potentiellt en annan kundhanterad nyckel, tillsammans med indexes\synonym Maps som inte är krypterade med Kundhanterade nycklar. 

## <a name="prerequisites"></a>Krav

Följande tjänster används i det här exemplet. 

+ [Skapa en Azure Search tjänst](search-create-service-portal.md) eller [hitta en befintlig tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) under din aktuella prenumeration. Du kan använda en kostnads fri tjänst för den här självstudien.

+ [Skapa en Azure Key Vault resurs](https://docs.microsoft.com/azure/key-vault/quick-create-portal#create-a-vault) eller hitta ett befintligt valv under din prenumeration.

+ [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) eller [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) används för konfigurations uppgifter.

+ [Postman](search-get-started-postman.md), [Azure POWERSHELL](search-create-index-rest-api.md) och [Azure Search SDK](https://aka.ms/search-sdk-preview) kan användas för att anropa förhands gransknings REST API. Det finns för närvarande ingen portal eller .NET SDK-stöd för kundhanterad kryptering.

## <a name="1---enable-key-recovery"></a>1 – aktivera nyckel återställning

Det här steget är valfritt men rekommenderas. När du har skapat Azure Key Vault-resursen aktiverar du det **mjuka borttagnings** -och **rensnings skyddet** i det valda nyckel valvet genom att köra följande POWERSHELL-eller Azure CLI-kommandon:   

```powershell
$resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "<vault_name>").ResourceId

$resource.Properties | Add-Member -MemberType NoteProperty -Name "enableSoftDelete" -Value 'true'

$resource.Properties | Add-Member -MemberType NoteProperty -Name "enablePurgeProtection" -Value 'true'

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

```azurecli-interactive
az keyvault update -n <vault_name> -g <resource_group> --enable-soft-delete --enable-purge-protection
```

>[!Note]
> På grund av den stora typen av kryptering med funktionen för Kundhanterade nycklar kommer Azure Search inte att kunna hämta dina data om din Azure Key Vault-nyckel tas bort. För att förhindra data förlust som orsakas av oavsiktliga Key Vault nyckel borttagningar, rekommenderar vi starkt att du aktiverar mjuk borttagning och rensning av skydd på det valda nyckel valvet. Mer information finns i [Azure Key Vault mjuk borttagning](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete).   

## <a name="2---create-a-new-key"></a>2 – Skapa en ny nyckel

Om du använder en befintlig nyckel för att kryptera Azure Search innehåll hoppar du över det här steget.

1. [Logga in på Azure Portal](https://portal.azure.com) och gå till instrument panelen för Key Vault.

1. Välj inställningen **nycklar** i det vänstra navigerings fönstret och klicka på **+ generera/importera**.

1. I fönstret **skapa en nyckel** , i listan med **alternativ**, väljer du den metod som du vill använda för att skapa en nyckel. Du kan **generera** en ny nyckel, **överföra** en befintlig nyckel eller använda **Återställ säkerhets kopia** för att välja en säkerhets kopia av en nyckel.

1. Ange ett **namn** för nyckeln och välj även andra nyckel egenskaper.

1. Klicka på knappen **skapa** för att starta distributionen.

Anteckna nyckel identifieraren – detta består av **nyckel värdets URI**, **nyckel namnet**och **nyckel versionen**. Du behöver dessa för att definiera ett krypterat index i Azure Search.
 
![Skapa en ny nyckel valvs nyckel](./media/search-manage-encryption-keys/create-new-key-vault-key.png "Skapa en ny nyckel valvs nyckel")

## <a name="3---create-a-service-identity"></a>3 – skapa en tjänst identitet

Genom att tilldela en identitet till din Sök tjänst kan du ge Key Vault åtkomst behörighet till din Sök tjänst. Sök tjänsten använder sin identitet för att autentisera med Azure Key Vault.

Azure Search har stöd för två sätt att tilldela identitet: en hanterad identitet eller ett externt hanterat Azure Active Directory-program. 

Använd om möjligt en hanterad identitet. Det är det enklaste sättet att tilldela en identitet till din Sök tjänst och bör fungera i de flesta fall. Om du använder flera nycklar för index och synonymer Maps, eller om din lösning finns i en distribuerad arkitektur som dekvalificerar identitetsbaserade autentisering, använder du den avancerade [externa hanterings Azure Active Directory metoden](#aad-app) som beskrivs i slutet i den här artikeln.

 I allmänhet gör en hanterad identitet att Sök tjänsten kan autentisera till Azure Key Vault utan att lagra autentiseringsuppgifter i kod. Livs cykeln för den här typen av hanterad identitet är kopplad till livs cykeln för din Sök tjänst, som bara kan ha en hanterad identitet. [Läs mer om hanterade identiteter](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

1. Om du vill skapa en hanterad identitet [loggar du in på toAzure-portalen](https://portal.azure.com) och öppnar din instrument panel för Sök tjänsten. 

1. Klicka på **identitet** i det vänstra navigerings fönstret, ändra status till **på**och klicka sedan på **Spara**.

![Aktivera en hanterad identitet](./media/search-enable-msi/enable-identity-portal.png "Aktivera en hanterade-identitet")

## <a name="4---grant-key-access-permissions"></a>4 – bevilja nyckel åtkomst behörigheter

Om du vill att Sök tjänsten ska använda din Key Vault nyckel måste du ge Sök tjänsten vissa åtkomst behörigheter.

Åtkomst behörigheterna kan återkallas vid en angiven tidpunkt. När den har återkallats blir alla Sök tjänst index eller synonym mappningar som använder det nyckel valvet oanvändbara. Att återställa åtkomst behörigheter för Key Vault vid ett senare tillfälle kommer att återställa index\synonym Map-åtkomst. Mer information finns i [säker åtkomst till ett nyckel valv](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault).

1. [Logga in på Azure Portal](https://portal.azure.com) och öppna översikts sidan för Key Vault. 

1. Välj inställningen **åtkomst principer** i det vänstra navigerings fönstret och klicka på **+ Lägg till ny**.

   ![Lägg till ny åtkomst princip för nyckel valv](./media/search-manage-encryption-keys/add-new-key-vault-access-policy.png "Lägg till ny åtkomst princip för nyckel valv")

1. Klicka på **Välj huvud konto** och välj din Azure Search-tjänst. Du kan söka efter den efter namn eller med det objekt-ID som visades när den hanterade identiteten aktiverades.

   ![Välj nyckel valv åtkomst principens huvud namn](./media/search-manage-encryption-keys/select-key-vault-access-policy-principal.png "Välj nyckel valv åtkomst principens huvud namn")

1. Klicka på **nyckel behörigheter** och välj *Hämta*, *packa upp nyckel* och *Radbryt nyckel*. Du kan använda mallen *Azure Data Lake Storage eller Azure Storage* för att snabbt välja de behörigheter som krävs.

   Azure Search måste beviljas med följande [åtkomst behörigheter](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-operations):

   * *Get* -tillåter att Sök tjänsten hämtar offentliga delar av nyckeln i en Key Vault
   * *Wrap-tangenten* – tillåter att din Sök tjänst använder din nyckel för att skydda den interna krypterings nyckeln
   * *Unwrap-nyckel* – tillåter att Sök tjänsten använder din nyckel för att packa upp den interna krypterings nyckeln

   ![Välj nyckel valv åtkomst princip nyckel behörigheter](./media/search-manage-encryption-keys/select-key-vault-access-policy-key-permissions.png "Välj nyckel valv åtkomst princip nyckel behörigheter")

1. Klicka på **OK** och **Spara** ändringarna i åtkomst principen.

> [!Important]
> Krypterat innehåll i Azure Search konfigureras för att använda en speciell Azure Key Vault nyckel med en angiven **version**. Om du ändrar nyckeln eller versionen måste mappningen index eller synonymer uppdateras för att använda den nya key\version **innan** du tar bort föregående key\version. Om du inte gör det kommer index eller synonym mappning inte att kunna användas, men du kan inte dekryptera innehållet när nyckel åtkomsten har gått förlorad.   

## <a name="5---encrypt-content"></a>5 – Kryptera innehåll

Det går inte att skapa ett index eller en synonym mappning som är krypterad med kundhanterad nyckel än att använda Azure Portal. Använd Azure Search REST API för att skapa en sådan index-eller synonym mappning.

Både index och synonym karta stöder en ny **encryptionKey** -egenskap på översta nivån som används för att ange nyckeln. 

Med **Key Vault-URI: n**, **nyckel namnet** och **nyckel versionen** av nyckel valvet, kan vi skapa en **encryptionKey** -definition:

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
Information om hur du skapar ett nytt index via REST API finns i [create index (Azure Search Service REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index), där den enda skillnaden här anger krypterings nyckel informationen som en del av index definitionen: 

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

Information om hur du skapar en ny synonym mappning via REST API finns i [create synonym karta (Azure Search Service REST API)](https://docs.microsoft.com/rest/api/searchservice/create-synonym-map), där den enda skillnaden här anger krypterings nyckel informationen som en del av synonym mappnings definitionen: 

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
> Det går inte att lägga till **encryptionKey** i befintliga Azure Search-index eller synonym mappningar, men det kan uppdateras genom att ange olika värden för någon av de tre nyckel valvs detaljerna (till exempel uppdatering av nyckel versionen). När du ändrar till en ny Key Vault nyckel eller en ny nyckel version måste alla Azure Search-index eller synonym mappning som använder nyckeln först uppdateras för att använda den nya key\version **innan** du tar bort föregående key\version. Om du inte gör det kommer indexet eller synonym mappningen att bli oanvändbar, eftersom den inte kan dekryptera innehållet när nyckel åtkomsten förlorats.   
> Att återställa åtkomst behörigheter för Key Vault vid ett senare tillfälle kommer att återställa innehålls åtkomsten.

## <a name="aad-app"></a>Avancerat: Använd ett externt hanterat Azure Active Directory program

När en hanterad identitet inte är möjlig kan du skapa ett Azure Active Directory-program med ett säkerhets objekt för din Azure Search-tjänst. Mer specifikt är en hanterad identitet inte livskraftig under följande omständigheter:

* Du kan inte direkt bevilja dina åtkomst behörigheter för Sök tjänsten till nyckel valvet (till exempel om Sök tjänsten är i en annan Active Directory klient än Azure Key Vault).

* En enskild Sök tjänst krävs för att vara värd för flera krypterade indexes\synonym-mappningar, var och en använder en annan nyckel från ett annat nyckel valv, där varje nyckel valv måste använda **en annan identitet** för autentisering. Om du inte behöver använda en annan identitet för att hantera olika nyckel valv, bör du överväga att använda alternativet hanterad identitet ovan.  

För att kunna hantera sådana topologier stöder Azure Search användning av Azure Active Directory-program (AAD) för autentisering mellan Sök tjänsten och Key Vault.    
Så här skapar du ett AAD-program i portalen:

1. [Skapa ett program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application).

1. [Hämta program-ID och autentiseringsnyckel](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) som krävs för att skapa ett krypterat index. Värden du måste ange innehåller **program-ID** och **autentiseringsnyckel**.

>[!Important]
> När du bestämmer dig för att använda en AAD-tillämpning av autentisering i stället för en hanterad identitet, bör du tänka på att Azure Search inte har behörighet att hantera ditt AAD-program för din räkning, och det är upp till dig att hantera ditt AAD-program, till exempel regelbunden rotation av programmets autentiseringsnyckel.
> När du ändrar ett AAD-program eller dess autentiseringsnyckel måste du först uppdatera alla Azure Search index eller synonymer som använder programmet för att använda det nya programmet ID\key **innan** du tar bort det tidigare programmet eller dess auktoriseringskod, och Innan du återkallar din Key Vault åtkomst till den.
> Om du inte gör det kommer indexet eller synonym mappningen att bli oanvändbar, eftersom den inte kan dekryptera innehållet när nyckel åtkomsten förlorats.   

## <a name="next-steps"></a>Nästa steg

Om du inte är bekant med Azures säkerhets arkitektur kan du läsa [dokumentationen för Azure-säkerhet](https://docs.microsoft.com/azure/security/)och i synnerhet den här artikeln:

> [!div class="nextstepaction"]
> [Vilande datakryptering](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
