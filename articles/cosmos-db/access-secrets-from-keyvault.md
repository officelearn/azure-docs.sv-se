---
title: Använd Key Vault för att lagra och komma åt Azure Cosmos DB-nycklar
description: Använd Azure Key Vault för att lagra och komma åt Azure Cosmos DB-anslutningssträng, nycklar, slutpunkter.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/23/2019
ms.reviewer: sngun
ms.openlocfilehash: 225221635f978e3d70cec4ce7e9d78d6b100b4fd
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618761"
---
# <a name="secure-azure-cosmos-keys-using-azure-key-vault"></a>Skydda Azure Cosmos-nycklar med Azure Key Vault 

>[!IMPORTANT]
> Den rekommenderade lösningen för att komma åt Azure Cosmos DB-nycklar är att använda en [systemtilldelad hanterad identitet](managed-identity-based-authentication.md). Om din tjänst inte kan dra nytta av hanterade identiteter använder du den [cert-baserade lösningen](certificate-based-authentication.md). Om både den hanterade identitetslösningen och den cert-baserade lösningen inte uppfyller dina behov kan du använda nyckelvalvslösningen nedan.

När du använder Azure Cosmos DB för dina program kan du komma åt databasen, samlingarna, dokumenten med hjälp av slutpunkten och nyckeln i appens konfigurationsfil.  Det är dock inte säkert att placera nycklar och URL direkt i programkoden eftersom de är tillgängliga i klartextformat för alla användare. Du vill se till att slutpunkten och nycklarna är tillgängliga, men via en säker mekanism. Med Azure Key Vault kan du då säkert lagra och hantera programhemligheter.

Följande steg krävs för att lagra och läsa Azure Cosmos DB-åtkomstnycklar från Key Vault:

* Skapa en Key Vault-lösning  
* Lägga till Azure Cosmos DB-åtkomstnycklar i Key Vault  
* Skapa ett Azure-webbprogram  
* Registrera & bevilja behörigheter för att läsa Nyckelvalvet  


## <a name="create-a-key-vault"></a>Skapa en Key Vault-lösning

1. Logga in på [Azure Portal](https://portal.azure.com/).  
2. Välj **Skapa en resurs > säkerhet > nyckelvalv**.  
3. Ange följande information i avsnittet **Skapa nyckelvalv** avsnittet Ange följande information:  
   * **Namn:** Ange ett unikt namn för ditt Key Vault.  
   * **Prenumeration:** Välj den prenumeration som du ska använda.  
   * Under **Resursgrupp** väljer du **Skapa ny** och anger ett resursgruppsnamn.  
   * Välj en plats i listrutan Plats.  
   * Lämna andra alternativ till deras standardvärden.  
4. När du har angett den här informationen väljer du **Skapa**.  

## <a name="add-azure-cosmos-db-access-keys-to-the-key-vault"></a>Lägg till Azure Cosmos DB-åtkomstnycklar i Key Vault.
1. Navigera till det nyckelvalv som du skapade i föregående steg och öppna fliken **Hemligheter.**  
2. Välj **+Generera/importera**, 

   * Välj **Manuell** för **uppladdningsalternativ**.
   * Ange ett **namn** för din hemlighet
   * Ange anslutningssträngen för ditt Cosmos DB-konto i fältet **Värde.** Välj sedan **Skapa**.

   ![Skapa en hemlighet](./media/access-secrets-from-keyvault/create-a-secret.png)

4. När hemligheten har skapats öppnar du den och kopierar den **Hemliga identifieraren som är i följande format. Du kommer att använda den här identifieraren i nästa avsnitt. 

   `https://<Key_Vault_Name>.vault.azure.net/secrets/<Secret _Name>/<ID>`

## <a name="create-an-azure-web-application"></a>Skapa ett Azure-webbprogram

1. Skapa ett Azure-webbprogram eller så kan du hämta appen från [GitHub-databasen](https://github.com/Azure/azure-cosmosdb-dotnet/tree/master/Demo/keyvaultdemo). Det är en enkel MVC ansökan.  

2. Packa upp det nedladdade programmet och öppna **HomeController.cs** filen. Uppdatera det hemliga ID:et på följande rad:

   `var secret = await keyVaultClient.GetSecretAsync("<Your Key Vault’s secret identifier>")`

3. **Spara** filen, **Bygg** lösningen.  
4. Distribuera sedan programmet till Azure. Högerklicka på projektet och välj **publicera**. Skapa en ny apptjänstprofil (du kan namnge appen WebAppKeyVault1) och välja **Publicera**.   

5. När programmet har distribuerats. Från Azure-portalen navigerar du till webbapp som du har distribuerat och aktiverar **programmets hanterade tjänstidentitet.**  

   ![Hanterad tjänstidentitet](./media/access-secrets-from-keyvault/turn-on-managed-service-identity.png)

Om du kör programmet nu visas följande fel, eftersom du inte har gett någon behörighet till det här programmet i Key Vault.

![Appen distribueras utan åtkomst](./media/access-secrets-from-keyvault/app-deployed-without-access.png)

## <a name="register-the-application--grant-permissions-to-read-the-key-vault"></a>Registrera & bevilja behörigheter för att läsa Nyckelvalvet

I det här avsnittet registrerar du programmet med Azure Active Directory och ger behörighet för programmet att läsa Key Vault. 

1. Navigera till Azure-portalen och öppna **nyckelvalvet** som du skapade i föregående avsnitt.  

2. Öppna **Åtkomstprinciper**, välj **+Lägg till ny** hitta webbappen du distribuerade, välj behörigheter och välj **OK**.  

   ![Lägg till åtkomstprincip](./media/access-secrets-from-keyvault/add-access-policy.png)

Nu, om du kör programmet, kan du läsa hemligheten från Key Vault.

![Appen distribueras med hemlighet](./media/access-secrets-from-keyvault/app-deployed-with-access.png)
 
På samma sätt kan du lägga till en användare för att komma åt nyckeln Arkiv. Du måste lägga till dig själv i Key Vault genom att välja **Åtkomstprinciper** och sedan bevilja alla behörigheter du behöver för att köra programmet från Visual Studio. När det här programmet körs från skrivbordet tar det din identitet.

## <a name="next-steps"></a>Nästa steg

* Information om hur du konfigurerar en brandvägg för Azure Cosmos DB finns i artikeln [om brandväggsstöd.](firewall-support.md)
* Om du vill konfigurera slutpunkten för virtuella nätverkstjänster läser du [säker åtkomst med hjälp av slutpunktsartikeln för VNet-tjänsten.](vnet-service-endpoint.md)
