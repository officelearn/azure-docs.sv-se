---
title: Själv studie kurs om att använda Azure App konfiguration Key Vault referenser i en Java våren Boot-app | Microsoft Docs
description: I den här självstudien får du lära dig att använda Azure App konfigurations Key Vault referenser från en Java våren Boot-app
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 12/16/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: 17d86f25de6eecee535d6f812f4ef0b078a4b6db
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2020
ms.locfileid: "75752517"
---
# <a name="tutorial-use-key-vault-references-in-a-java-spring-app"></a>Självstudie: använda Key Vault referenser i en Java våren-app

I den här självstudien får du lära dig hur du använder Azure App konfigurations tjänsten tillsammans med Azure Key Vault. Konfiguration av appar och Key Vault är kompletterande tjänster som används sida vid sida i de flesta program distributioner.

Med konfiguration av appar kan du använda tjänsterna tillsammans genom att skapa nycklar som refererar till värden som lagras i Key Vault. När app-konfigurationen skapar sådana nycklar lagrar den URI: er för Key Vault värden i stället för själva värdena.

Programmet använder klient leverantören för konfiguration av appar för att hämta Key Vault referenser, precis som för alla andra nycklar som lagras i app-konfigurationen. I det här fallet är de värden som lagras i app-konfigurationen URI: er som refererar till värdena i Key Vault. De är inte Key Vault värden eller autentiseringsuppgifter. Eftersom klient leverantören känner igen nycklarna som Key Vault referenser, använder den Key Vault för att hämta sina värden.

Ditt program ansvarar för att autentisera korrekt för både app-konfigurationen och Key Vault. De två tjänsterna kommunicerar inte direkt.

Den här självstudien visar hur du implementerar Key Vault referenser i din kod. Den bygger på den webbapp som introducerades i snabbstarterna. Innan du fortsätter måste du först [skapa en Java våren-app med app-konfigurationen](./quickstart-java-spring-app.md) .

Du kan använda valfri kod redigerare för att utföra stegen i den här självstudien. Till exempel är [Visual Studio Code](https://code.visualstudio.com/) en plattforms oberoende kod redigerare som är tillgänglig för operativ systemen Windows, MacOS och Linux.

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Skapa en konfigurations nyckel för appen som refererar till ett värde som lagras i Key Vault.
> * Få åtkomst till värdet för den här nyckeln från ett Java våren-program.

## <a name="prerequisites"></a>Krav

Innan du startar den här självstudien installerar du [.net Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-vault"></a>Skapa ett valv

1. Välj alternativet **skapa en resurs** i det övre vänstra hörnet i Azure Portal:

    ![Utdata efter att nyckel valvet har skapats](./media/quickstarts/search-services.png)
1. I rutan Sök anger du **Key Vault**.
1. Välj **nyckel valv** till vänster i listan resultat.
1. I **nyckel valv**väljer du **Lägg till**.
1. Ange följande information till höger i **skapa nyckel valv**:
    - Välj en **prenumeration för att välja en** prenumeration.
    - I **resurs grupp**väljer du **Skapa ny** och anger ett resurs grupp namn.
    - I **Key Vault-namnet**krävs ett unikt namn. I den här självstudien anger du **contoso-vault2**.
    - Välj en plats i list rutan **region** .
1. Lämna de andra alternativen för att **skapa nyckel valv** med standardvärdena.
1. Välj **Skapa**.

I det här läget är ditt Azure-konto det enda som har behörighet att komma åt det här nya valvet.

![Utdata efter att nyckel valvet har skapats](./media/quickstarts/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Lägga till en hemlighet i Key Vault

Om du vill lägga till en hemlighet i valvet behöver du bara utföra några ytterligare steg. I det här fallet lägger du till ett meddelande som du kan använda för att testa Key Vault hämtning. Meddelandet kallas **meddelande**och du lagrar värdet "hej från Key Vault" i det.

1. Välj **hemligheter**på sidan Key Vault egenskaper.
1. Välj **generera/importera**.
1. Ange följande värden i fönstret **skapa en hemlighet** :
    - **Överförings alternativ**: ange **manuell**.
    - **Namn**: Ange ett **meddelande**.
    - **Värde**: ange **Hej från Key Vault**.
1. Lämna den andra **skapa en hemlig** egenskap med standardvärdena.
1. Välj **Skapa**.

## <a name="add-a-key-vault-reference-to-app-configuration"></a>Lägg till en Key Vault referens till app-konfigurationen

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **alla resurser**och välj sedan den instans av app konfigurations arkiv som du skapade i snabb starten.

1. Välj **konfigurations Utforskaren**.

1. Välj **+ skapa** > **Key Vault-referens**och ange sedan följande värden:
    - **Nyckel**: Välj **/Application/config.keyvaultmessage**
    - **Etikett**: lämna värdet tomt.
    - **Prenumeration**, **resurs grupp**och **nyckel valv**: ange värdena som motsvarar värdena i nyckel valvet som du skapade i föregående avsnitt.
    - **Hemlighet**: Välj det hemliga namnet **meddelande** som du skapade i föregående avsnitt.

## <a name="connect-to-key-vault"></a>Anslut till Key Vault

1. I den här självstudien använder du ett huvud namn för tjänsten för autentisering till Key Vault. Om du vill skapa tjänstens huvud namn använder du kommandot Azure CLI [AZ AD SP Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) :

    ```azurecli
    az ad sp create-for-rbac -n "http://mySP" --sdk-auth
    ```

    Den här åtgärden returnerar en serie med nyckel/värde-par:

    ```console
    {
    "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
    "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
    "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
    "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "activeDirectoryGraphResourceId": "https://graph.windows.net/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
    }
    ```

1. Kör följande kommando för att ge tjänstens huvud namn åtkomst till ditt nyckel valv:

    ```
    az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
    ```

1. Skapa följande miljövariabler med värdena för tjänstens huvud namn som visades i föregående steg:

    * **AZURE_CLIENT_ID**: *clientId*
    * **AZURE_CLIENT_SECRET**: *clientSecret*
    * **AZURE_TENANT_ID**: *tenantId*

> [!NOTE]
> Dessa Key Vault autentiseringsuppgifter används endast i ditt program. Programmet autentiseras direkt till Key Vault med dessa autentiseringsuppgifter. De skickas aldrig till appens konfigurations tjänst.

## <a name="update-your-code-to-use-a-key-vault-reference"></a>Uppdatera din kod för att använda en Key Vault referens

1. Öppna *MessageProperties. java*. Lägg till en ny variabel med namnet *keyVaultMessage*:

    ```java
    private String keyVaultMessage;

    public String getKeyVaultMessage() {
        return keyVaultMessage;
    }

    public void setKeyVaultMessage(String keyVaultMessage) {
        this.keyVaultMessage = keyVaultMessage;
    }
    ```

1. Öppna *HelloController. java*. Uppdatera *GetMessage* -metoden för att inkludera det meddelande som hämtats från Key Vault.

    ```java
    @GetMapping
    public String getMessage() {
        return "Message: " + properties.getMessage() + "\nKey Vault message: " + properties.getKeyVaultMessage();
    }
    ```

1. Skapa ditt våren Boot-program med Maven och kör det, till exempel:

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```
1. När programmet har körts använder du *sväng* för att testa programmet, till exempel:

      ```shell
      curl -X GET http://localhost:8080/
      ```
    Du ser meddelandet som du angav i appens konfigurations arkiv. Du ser också meddelandet som du angav i Key Vault.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat en konfigurations nyckel för appen som refererar till ett värde som lagras i Key Vault. Fortsätt till nästa självstudie om du vill lära dig hur du använder funktions flaggor i ditt Java våren-program.

> [!div class="nextstepaction"]
> [Hanterad identitets integrering](./quickstart-feature-flag-spring-boot.md)
