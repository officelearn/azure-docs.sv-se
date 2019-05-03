---
title: Kryptering i vila med Kundhanterade nycklar i Azure Key Vault - Azure Search
description: Tillägg server side encryption över index och synonymmappningar i Azure Search via nycklar som du skapar och hanterar i Azure Key Vault.
author: NatiNimni
manager: jlembicz
ms.author: natinimn
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.custom: ''
ms.openlocfilehash: 987b56a9571fd50f605dbe6fb4112ef857021530
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029182"
---
# <a name="azure-search-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Azure Search-kryptering med Kundhanterade nycklar i Azure Key Vault

Som standard krypterar Azure Search användarinnehåll i vila med [service-hanterade nycklar](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest#data-encryption-models). Du kan komplettera standard kryptering med ett lager för ytterligare kryptering med nycklar som du skapar och hanterar i Azure Key Vault. Den här artikeln vägleder dig genom stegen.

Kryptering för serversidan stöds genom integrering med [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview). Du kan skapa egna krypteringsnycklar och lagra dem i ett nyckelvalv, eller du kan använda Azure Key Vault-API: er för att generera krypteringsnycklar. Du kan också granska nyckelanvändningen med Azure Key Vault. 

Kryptering med Kundhanterade nycklar har konfigurerats på index eller synonymen kartan när objekten skapas och inte på servicenivå sökning. Du kan inte kryptera innehåll som redan finns. 

Du kan använda olika nycklar från olika nyckelvalv. Det innebär att en enda söktjänst kan vara värd för flera krypterade indexes\synonym kartor, var och en krypterad potentiellt med hjälp av en annan kundhanterad nyckel, tillsammans med indexes\synonym maps som inte är krypterade med Kundhanterade nycklar. 

>[!Note]
> **Funktionen tillgänglighet**: Kryptering med Kundhanterade nycklar är en förhandsgranskningsfunktion som inte är tillgänglig för kostnadsfria tjänster. För betaltjänster, det är endast tillgängligt för söktjänster som skapats på eller efter 2019-01-01, med hjälp av den senaste api-versionen (api-version = 2019-05-06-förhandsversion). Det finns för närvarande inget portal stöd för den här funktionen.

## <a name="prerequisites"></a>Nödvändiga komponenter

Följande tjänster används i det här exemplet. 

[Skapa en Azure Search-tjänst](search-create-service-portal.md) eller [hitta en befintlig tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) under din aktuella prenumeration. Du kan använda en kostnadsfri tjänst för den här självstudiekursen.

[Skapa en Azure Key Vault-resurs](https://docs.microsoft.com/azure/key-vault/quick-create-portal#create-a-vault) eller hitta ett befintligt valv i din prenumeration.

[Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) eller [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) används för konfigurationsåtgärder.

[Postman](search-fiddler.md), [Azure PowerShell](search-create-index-rest-api.md) och [Azure Search SDK](https://aka.ms/search-sdk-preview) kan användas för att anropa förhandsversionen av REST API. Det finns ingen portal eller .NET SDK-stöd för Kundhanterade kryptering just nu.

## <a name="1---enable-key-recovery"></a>1 – Aktivera återställning av nyckel

Det här steget är valfritt men rekommenderas. När du har skapat Azure Key Vault-resursen, aktivera **mjuk borttagning** och **Rensa skydd** i det valda nyckelvalvet genom att köra följande PowerShell eller Azure CLI-kommandon:   

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
> På grund av beskaffenhet kryptering med Kundhanterade nycklar funktionen, kommer Azure Search inte att hämta dina data om din Azure Key vault-nyckeln tas bort. Vi rekommenderar att du aktiverar mjuk borttagning och rensa skydd på det valda key vault för att förhindra dataförlust på grund av oavsiktliga borttagningar för Key Vault-nyckeln. Mer information finns i [Azure Key Vault mjuk borttagning](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete).   

## <a name="2---create-a-new-key"></a>2 – Skapa en ny nyckel

Hoppa över det här steget om du använder en befintlig nyckel för att kryptera Azure Search-innehåll.

1. [Logga in på Azure-portalen](https://portal.azure.com) och gå till instrumentpanelen för nyckelvalvet.

1. Välj den **nycklar** från det vänstra navigeringsfönstret och klicka på **+ generera/importera**.

1. I den **skapar du en nyckel** rutan i listan över **alternativ**, välja den metod som du vill använda för att skapa en nyckel. Du kan **generera** en ny nyckel **överför** en befintlig nyckel eller använda **återställa säkerhetskopierade** att välja en säkerhetskopia av en nyckel.

1. Ange en **namn** för din nyckel och du kan också välja andra nyckelegenskaper.

1. Klicka på den **skapa** för att starta distributionen.

Anteckna nyckel-ID – detta är sammansatt från den **Uri-värdet**, **nyckelnamn**, och **nyckelversionen**. Du behöver dessa för att ange ett krypterat index i Azure Search.
 
![Skapa en ny nyckel för nyckelvalvet](./media/search-manage-encryption-keys/create-new-key-vault-key.png "skapa en ny nyckel för nyckelvalvet")

## <a name="3---create-a-service-identity"></a>3 – skapa en tjänstidentitet

Tilldela en identitet till search-tjänsten kan du bevilja behörigheter för Key Vault-åtkomst till din söktjänst. Söktjänsten använder sin identitet för att autentisera med Azure Key vault.

Azure Search har stöd för två sätt för att tilldela identitet: en hanterad identitet eller ett externt hanterad Azure Active Directory-program. 

Använd om möjligt en hanterad identitet. Det är det enklaste sättet för att tilldela en identitet till din söktjänst och bör fungera i de flesta fall. Om du använder flera nycklar för index och synonymmappningar, eller om din lösning finns i en distribuerad arkitektur som diskvalificerar identity-baserad autentisering, med de avancerade [externt hanterad Azure Active Directory metoden](#aad-app)som beskrivs i slutet av den här artikeln.

 I allmänhet är kan en hanterad identitet din söktjänst att autentisera till Azure Key Vault utan att behöva lagra autentiseringsuppgifter i kod. Livscykeln för den här typen av hanterad identitet är kopplad till livscykeln för din search-tjänst som kan bara ha en hanterad identitet. [Mer information om hanterade identiteter](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

1. Skapa en hanterad identitet [logga in toAzure portal](https://portal.azure.com) och öppna instrumentpanelen sökning. 

1. Klicka på **identitet** i det vänstra navigeringsfönstret, ändrar du statusen är **på**, och klicka på **spara**.

![Aktivera en hanterad identitet](./media/search-enable-msi/enable-identity-portal.png "aktivera en hanterade identitet")

## <a name="4---grant-key-access-permissions"></a>4 – bevilja nyckelåtkomst

Om du vill aktivera din söktjänst att använda Key Vault-nyckeln, måste du ge din sökning tjänsten vissa behörigheter för åtkomst.

Behörigheter för åtkomst kan återkallas vid en given tidpunkt. När återkallats, blir alla search index eller synonymen tjänstkarta som använder nyckelvalvet oanvändbara. Återställa behörigheter för Key vault åtkomst vid ett senare tillfälle återställs index\synonym kartan åtkomst. Mer information finns i [säker åtkomst till ett nyckelvalv](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault).

1. [Logga in på Azure-portalen](https://portal.azure.com) och öppna ditt nyckelvalv översiktssidan. 

1. Välj den **åtkomstprinciper** från det vänstra navigeringsfönstret och klicka på **+ Lägg till ny**.

   ![Lägg till ny åtkomstprincip för nyckelvalvet](./media/search-manage-encryption-keys/add-new-key-vault-access-policy.png "Lägg till ny åtkomstprincip för nyckelvalvet")

1. Klicka på **väljer huvudnamn** och välj din Azure Search-tjänst. Du kan söka efter den efter namn eller objekt-ID som visades när du har aktiverat hanterad identitet.

   ![Välj nyckelvalv åtkomst princip huvudnamn](./media/search-manage-encryption-keys/select-key-vault-access-policy-principal.png "väljer nyckelvalv åtkomst princip huvudnamn")

1. Klicka på **Nyckelbehörigheter** och välj *hämta*, *Unwrap Key* och *Wrap Key*. Du kan använda den *Azure Data Lake Storage eller Azure Storage* mall för att snabbt välja behörigheterna som krävs.

   Azure search måste beviljas med följande [åtkomstbehörigheter](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-operations):

   * *Hämta* – gör att din söktjänst att hämta de offentliga delarna av din nyckel i Key Vault
   * *Omsluta nyckel* – gör att din söktjänst till Använd din nyckel för att skydda den interna krypteringsnyckeln
   * *Packa upp nyckeln* – gör att din search-tjänst du använder din nyckel för att packa upp den interna krypteringsnyckeln

   ![Välj nyckelvalv åtkomst princip nyckelns behörigheter](./media/search-manage-encryption-keys/select-key-vault-access-policy-key-permissions.png "Välj nyckelvalv åtkomst princip nyckelns behörigheter")

1. Klicka på **OK** och **spara** principändringar åtkomst.

> [!Important]
> Krypterat innehåll i Azure search är konfigurerad för att använda en specifik Azure Key Vault med ett visst **version**. Om du ändrar nyckeln eller version index eller synonymen kartan måste uppdateras för att använda den nya key\version **innan** tar bort tidigare key\version. När du kommer inte att kunna dekryptera innehållet när nyckelåtkomst går förlorad när du inte kan göra det renderas indexet eller synonymen mappa oanvändbara.   

## <a name="5---encrypt-content"></a>5 – Kryptera innehåll

Skapa ett index eller synonymen karta som krypterats med kundhanterad nyckel ännu inte möjlig med hjälp av Azure-portalen. Använda Azure Search REST API för att skapa sådana index eller synonymen mappningar.

Index- och synonymen mappa stöd för en ny översta **encryptionKey** egenskapen som används för att ange nyckeln. 

Med hjälp av den **nyckelvalv Uri**, **nyckelnamn** och **nyckelversionen** av Key vault-nyckeln kan vi skapa en **encryptionKey** definition:

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
> Ingen av dessa nyckelvalvsinformationen betraktas hemlighet och enkelt kan hämtas genom att bläddra till den relevanta Azure Key Vault sidan i Azure-portalen.

Om du använder ett AAD-program för Key Vault-autentisering istället för att använda en hanterad identitet, lägger du till AAD-programmet **autentiseringsuppgifter för åtkomst** till krypteringsnyckeln: 
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

## <a name="example-index-encryption"></a>Exempel: Index-kryptering
Information om att skapa ett nytt index via REST API hittades på [Create Index (Azure Search Service REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index), där den enda skillnaden är att ange viktiga Krypteringsinformation som en del av indexdefinitionen: 

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
Du kan nu skicka förfrågan om att skapa indexet och sedan börja använda indexet normalt.

## <a name="example-synonym-map-encryption"></a>Exempel: Synonymen kartan kryptering

Information om hur du skapar en ny synonymmappning via REST API finns på [skapa Synonymmappning (Azure Search Service REST API)](https://docs.microsoft.com/rest/api/searchservice/create-synonym-map), där den enda skillnaden är att ange viktiga Krypteringsinformation som en del av avbildningsdefinition synonymen: 

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
Du kan nu skicka synonymen kartan skapa begäran och sedan börja använda det normalt.

>[!Important] 
> Medan **encryptionKey** kan inte läggas till befintliga Azure Search-index eller synonymen mappar, kan det uppdateras genom att ange olika värden för någon av tre nyckelvalvsinformationen (till exempel uppdaterar nyckelns version). När du byter till en ny Key Vault-nyckel eller en ny version, eventuella Azure Search-index eller synonymen karta som använder nyckeln först måste uppdateras för att använda den nya key\version **innan** tar bort tidigare key\version. Om du inte gör det renderas indexet eller synonymmappning bli oanvändbart, eftersom det inte att kunna dekryptera innehåll när viktiga åtkomst går förlorad.   
> Återställa behörigheter för Key vault åtkomst vid ett senare tillfälle att återställa åtkomst till innehåll.

## <a name="aad-app"></a> Avancerat: Använda ett externt hanterad Azure Active Directory-program

När en hanterad identitet inte är möjligt, du kan skapa ett Azure Active Directory-program med en säkerhet huvudnamn för Azure Search-tjänsten. En hanterad identitet är inte användbara under dessa förhållanden:

* Du kan inte direkt bevilja sökningen service åtkomstbehörigheter till Key vault (till exempel om söktjänsten är i en annan Active Directory-klient än Azure Key Vault).

* En enda söktjänst krävs för att vara värd för flera krypterade indexes\synonym kartor, var och en med hjälp av en annan nyckel från en annan Key vault, där varje nyckelvalv måste använda **en annan identitet** för autentisering. Om du använder en annan identitet för att hantera olika nyckelvalv är inte ett krav, bör du använda alternativet hanterad identitet.  

För att hantera dessa topologier, Sök Azure stöder användning av Azure Active Directory (AAD)-program för autentisering mellan din söktjänst och Key Vault.    
Skapa ett AAD-program i portalen:

1. [Skapa ett program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application).

1. [Hämta program-ID och autentiseringsnyckel nyckel](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-application-id-and-authentication-key) som de som måste utföras för att skapa ett index som är krypterade. Du måste ange värden är **program-ID** och **autentiseringsnyckeln**.

>[!Important]
> När du beslutar att använda ett AAD-program för autentisering i stället för en hanterad identitet ska du tänka på som Azure Search inte har behörighet att hantera AAD-programmet för din räkning och det är upp till dig att hantera din AAD-programmet, till exempel periodiska rotation av autentiseringsnyckeln program.
> När du ändrar en AAD-programmet eller dess autentiseringsnyckeln, eventuella Azure Search-index eller synonymen karta som använder programmet först måste uppdateras för att använda det nya programmet ID\key **innan** tar bort det tidigare programmet eller dess auktorisering nyckel, och innan du återkallar ditt Key Vault-åtkomst till den.
> Om du inte gör det renderas indexet eller synonymmappning bli oanvändbart, eftersom det inte att kunna dekryptera innehåll när viktiga åtkomst går förlorad.   

## <a name="next-steps"></a>Nästa steg

Om du inte känner till Azure security-arkitektur, granskar du den [dokumentation om Azure Security](https://docs.microsoft.com/azure/security/), särskilt i den här artikeln:

> [!div class="nextstepaction"]
> [Vilande datakryptering](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)
