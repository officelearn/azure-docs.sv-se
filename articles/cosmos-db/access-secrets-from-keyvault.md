---
title: Använd Key Vault för att lagra och komma åt nycklar för Azure Cosmos DB | Microsoft Docs
description: Använda Azure Key Vault för att lagra och komma åt Azure Cosmos DB-anslutningssträngen, nycklar, URI'S.
services: cosmos-db
author: rafats
manager: kfile
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: rafats
ms.openlocfilehash: b090c1593b49bec4f51fea8d498860e8af8b2f4b
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44053092"
---
# <a name="use-key-vault-to-store-and-access-azure-cosmos-db-keys"></a>Använd Key Vault för att lagra och komma åt Azure Cosmos DB-nycklar

När du använder Azure Cosmos DB för dina program kan du komma åt databasen, samlingar, dokument med hjälp av slutpunkt URI och nyckeln i appens konfigurationsfilen.  Det är dock inte säkert att placera nycklar och URL: en direkt i programkoden eftersom de är tillgängliga i klartext för alla användare. Du vill se till att URI och nycklar är tillgänglig men via en säker mekanism. Det här är där Azure Key Vault kan hjälpa dig att säkert lagra och hantera programhemligheter.

Följande steg krävs för att lagra och läsa åtkomstnycklar för Azure Cosmos DB från Key Vault:

* Skapa en Key Vault-lösning  
* Lägg till nycklar för Azure Cosmos DB-åtkomst till Key Vault  
* Skapa en Azure-webbprogram  
* Registrera programmet och beviljar behörigheter att läsa Key Vault  


## <a name="create-a-key-vault"></a>Skapa en Key Vault-lösning

1. Logga in på [Azure-portalen](https://portal.azure.com/).  
2. Välj **skapa en resurs > Säkerhet > Key Vault**.  
3. Ange följande information i avsnittet **Skapa nyckelvalv** avsnittet Ange följande information:  
   * **Namn:** ange ett unikt namn för Key Vault.  
   * **Prenumeration:** Välj den prenumeration som du ska använda.  
   * Under **Resursgrupp** väljer du **Skapa ny** och anger ett resursgruppsnamn.  
   * Välj en plats i rullgardinsmenyn plats.  
   * Lämna andra alternativ till sina standardinställningar.  
4. När du har angett den här informationen väljer du **Skapa**.  

## <a name="add-azure-cosmos-db-access-keys-to-the-key-vault"></a>Lägg till nycklar för Azure Cosmos DB-åtkomst till Key Vault.
1. Gå till det Nyckelvalv som du skapade i föregående steg, öppna den **hemligheter** fliken.  
2. Välj **+ generera/importera**, 

   * Välj **manuell** för **Uppladdningsalternativ**.
   * Ange en **namn** för din hemlighet
   * Ange anslutningssträngen för ditt Cosmos DB-konto i den **värdet** fält. Välj sedan **skapa**.

   ![Skapa en hemlighet](./media/access-secrets-from-keyvault/create-a-secret.png)

4. När hemligheten har skapats kan du öppna den och kopiera den ** hemlig identifierare som är i följande format. Du använder den här identifieraren i nästa avsnitt. 

   `https://<Key_Vault_Name>.vault.azure.net/secrets/<Secret _Name>/<ID>`

## <a name="create-an-azure-web-application"></a>Skapa en Azure-webbprogram

1. Skapa en Azure-webbprogram eller du kan ladda ned appen från den [GitHub-lagringsplatsen](https://github.com/Azure/azure-cosmosdb-dotnet/tree/master/Demo/keyvaultdemo). Det är ett enkelt MVC-program.  

2. Packa upp programmet som laddats ned och öppna den **HomeController.cs** fil. Uppdatera hemliga-ID i följande rad:

   `var secret = await keyVaultClient.GetSecretAsync("<Your Key Vault’s secret identifier>")`

3. **Spara** filen **skapa** lösningen.  
4. Sedan distribuera programmet till Azure. Högerklicka på projektet och välj **publicera**. Skapa en ny app service profil (du kan kalla appen WebAppKeyVault1) och välj **publicera**.   

5. När programmet har distribuerats. Gå till webbappen som du har distribuerat från Azure-portalen och aktivera den **hanterad tjänstidentitet** av det här programmet.  

   ![Hanterad tjänstidentitet](./media/access-secrets-from-keyvault/turn-on-managed-service-identity.png)

Om du ska köra programmet nu, visas följande felmeddelande när du inte har gett behörighet för det här programmet i Key Vault.

![Appen distribueras utan åtkomst](./media/access-secrets-from-keyvault/app-deployed-without-access.png)

## <a name="register-the-application--grant-permissions-to-read-the-key-vault"></a>Registrera programmet och beviljar behörigheter att läsa Key Vault

I det här avsnittet ska du registrera programmet med Azure Active Directory och ge behörigheterna för programmet att läsa Key Vault. 

1. Gå till Azure-portalen, öppnar den **Key Vault** du skapade i föregående avsnitt.  

2. Öppna **åtkomstprinciper**väljer **+ Lägg till ny** hitta webbappen som du distribuerat, välja behörigheter och välj **OK**.  

   ![Lägg till åtkomstprincip](./media/access-secrets-from-keyvault/add-access-policy.png)

Om du kör programmet, kan du nu läsa hemligheten från Key Vault.

![Appen distribueras med hemlighet](./media/access-secrets-from-keyvault/app-deployed-with-access.png)
 
På samma sätt kan du lägga till en användare för att få åtkomst till key Vault. Du måste lägga till dig själv till Key Vault genom att välja **åtkomstprinciper** och sedan bevilja alla behörigheter som du behöver att köra programmet från Visual studio. När det här programmet körs på skrivbordet, tar din identitet.

## <a name="next-steps"></a>Nästa steg

* Konfigurera en brandvägg för Azure Cosmos DB finns i [stöd i brandväggen](firewall-support.md) artikeln.
* För att konfigurera tjänstslutpunkt för virtuellt nätverk, se [säker åtkomst med hjälp av VNet-tjänstslutpunkt](vnet-service-endpoint.md) artikeln.
