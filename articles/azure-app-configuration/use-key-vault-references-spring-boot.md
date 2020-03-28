---
title: Självstudiekurs för att använda Azure App Configuration Key Vault-referenser i en Java Spring Boot-app | Microsoft-dokument
description: I den här självstudien får du lära dig hur du använder Azure App Configuration's Key Vault-referenser från en Java Spring Boot-app
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
ms.openlocfilehash: b6b6d10165eed331c397e17a18e382b095e1f74f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79216747"
---
# <a name="tutorial-use-key-vault-references-in-a-java-spring-app"></a>Självstudiekurs: Använd Key Vault-referenser i en Java Spring-app

I den här självstudien får du lära dig hur du använder Azure App Configuration-tjänsten tillsammans med Azure Key Vault. Appkonfiguration och Key Vault är kompletterande tjänster som används sida vid sida i de flesta programdistributioner.

Appkonfiguration hjälper dig att använda tjänsterna tillsammans genom att skapa nycklar som referensvärden som lagras i Key Vault. När appkonfiguration skapar sådana nycklar lagras URI:erna för key vault-värden i stället för själva värdena.

Ditt program använder appkonfigurationsklientleverantören för att hämta Key Vault-referenser, precis som för alla andra nycklar som lagras i appkonfiguration. I det här fallet är de värden som lagras i appkonfiguration URI:er som refererar till värdena i nyckelvalvet. De är inte key vault-värden eller autentiseringsuppgifter. Eftersom klientleverantören känner igen nycklarna som Key Vault-referenser används Key Vault för att hämta sina värden.

Ditt program ansvarar för att autentisera korrekt till både appkonfiguration och Nyckelvalv. De två tjänsterna kommunicerar inte direkt.

Den här självstudien visar hur du implementerar Key Vault-referenser i koden. Den bygger på den webbapp som introducerades i snabbstarterna. Innan du fortsätter slutför du [Skapa en Java Spring-app med appkonfiguration](./quickstart-java-spring-app.md) först.

Du kan använda vilken kodredigerare som helst för att göra stegen i den här självstudien. [Visual Studio Code](https://code.visualstudio.com/) är till exempel en plattformsoberoende kodredigerare som är tillgänglig för operativsystemen Windows, macOS och Linux.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa en appkonfigurationsnyckel som refererar till ett värde som lagras i Key Vault.
> * Få tillgång till värdet av den här nyckeln från ett Java Spring-program.

## <a name="prerequisites"></a>Krav

* Azure-prenumeration - [skapa en gratis](https://azure.microsoft.com/free/)
* Ett [Java Development Kit (Som stöds)](https://docs.microsoft.com/java/azure/jdk) med version 8.
* [Apache Maven](https://maven.apache.org/download.cgi) version 3.0 eller högre.

## <a name="create-a-vault"></a>Skapa ett valv

1. Välj alternativet **Skapa en resurs** i det övre vänstra hörnet av Azure-portalen:

    ![Utdata när skapandet av nyckelvalvet har slutförts](./media/quickstarts/search-services.png)
1. I sökrutan anger du **Nyckelvalv**.
1. Välj **Nyckelvalv** till vänster i resultatlistan.
1. Välj **Lägg till**i **Nyckelvalv.**
1. Till höger i **Skapa nyckelvalv**anger du följande information:
    * Välj **Prenumeration** om du vill välja en prenumeration.
    * Välj **Skapa nytt** i **Resursgrupp**och ange ett resursgruppnamn.
    * I **Key vault-namnet**krävs ett unikt namn. För den här självstudien anger du **Contoso-vault2**.
    * Välj en plats i listrutan **Region.**
1. Lämna de andra alternativen **för skapa nyckelvalv** med sina standardvärden.
1. Välj **Skapa**.

Nu är ditt Azure-konto det enda som har behörighet att komma åt det här nya valvet.

![Utdata när skapandet av nyckelvalvet har slutförts](./media/quickstarts/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Lägga till en hemlighet i Key Vault

Om du vill lägga till en hemlighet i valvet måste du bara ta några ytterligare steg. I det här fallet lägger du till ett meddelande som du kan använda för att testa hämtning av Nyckelvalv. Meddelandet heter **Meddelande**och du lagrar värdet "Hej från Nyckelvalv" i det.

1. På egenskapssidorna För Nyckelvalv väljer du **Hemligheter**.
1. Välj **Generera/importera**.
1. Ange följande värden i fönstret **Skapa ett hemligt:**
    * **Ladda upp alternativ:** Ange **manuell**.
    * **Namn**: Ange **meddelande**.
    * **Värde**: Ange **Hej från Key Vault**.
1. Lämna den andra **Skapa en hemlig** egenskaper med sina standardvärden.
1. Välj **Skapa**.

## <a name="add-a-key-vault-reference-to-app-configuration"></a>Lägga till en referens för Key Vault i appkonfiguration

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Alla resurser**och välj sedan den App Configuration Store-instans som du skapade i snabbstarten.

1. Välj **Konfigurationsutforskaren**.

1. Välj **+ Skapa** > **referens för nyckelvalv**och ange sedan följande värden:
    * **Nyckel**: Välj **/application/config.keyvaultmessage**
    * **Etikett**: Lämna det här värdet tomt.
    * **Prenumeration,** **resursgrupp**och **nyckelvalv:** Ange de värden som motsvarar värdena i nyckelvalvet som du skapade i föregående avsnitt.
    * **Hemlighet:** Välj den hemlighet som heter **Meddelande** som du skapade i föregående avsnitt.

## <a name="connect-to-key-vault"></a>Anslut till Nyckelvalv

1. I den här självstudien använder du ett tjänsthuvudnamn för autentisering till Key Vault. Om du vill skapa det här tjänstens huvudnamn använder du kommandot Azure CLI [az ad sp create-for-rbac:](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)

    ```azurecli
    az ad sp create-for-rbac -n "http://mySP" --sdk-auth
    ```

    Den här åtgärden returnerar en serie nyckel-/värdepar:

    ```console
    {
    "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
    "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
    "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
    "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
    }
    ```

1. Kör följande kommando så att tjänstens huvudnamn kan komma åt nyckelvalvet:

    ```console
    az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get
    ```

1. Kör följande kommando för att hämta ditt objekt-ID och lägg sedan till det i Appkonfiguration.

    ```console
    az ad sp show --id <clientId-of-your-service-principal>
    az role assignment create --role "App Configuration Data Reader" --assignee-object-id <objectId-of-your-service-principal> --resource-group <your-resource-group>
    ```

1. Skapa följande miljövariabler med hjälp av värdena för tjänstens huvudnamn som visades i föregående steg:

    * **AZURE_CLIENT_ID**: *clientId*
    * **AZURE_CLIENT_SECRET**: *clientSecret*
    * **AZURE_TENANT_ID**: *tenantId*

> [!NOTE]
> Dessa Key Vault-autentiseringsuppgifter används bara i ditt program.  Ditt program autentiserar direkt med Key Vault med hjälp av dessa autentiseringsuppgifter utan att involvera tjänsten App Configuration.  Key Vault tillhandahåller autentisering för både ditt program och din appkonfigurationstjänst utan att dela eller exponera nycklar.

## <a name="update-your-code-to-use-a-key-vault-reference"></a>Uppdatera koden för att använda en Referens för Nyckelvalv

1. Öppna *MessageProperties.java*. Lägg till en ny variabel som kallas *keyVaultMessage:*

    ```java
    private String keyVaultMessage;

    public String getKeyVaultMessage() {
        return keyVaultMessage;
    }

    public void setKeyVaultMessage(String keyVaultMessage) {
        this.keyVaultMessage = keyVaultMessage;
    }
    ```

1. Öppna *HelloController.java*. Uppdatera *metoden getMessage* för att inkludera meddelandet som hämtats från Key Vault.

    ```java
    @GetMapping
    public String getMessage() {
        return "Message: " + properties.getMessage() + "\nKey Vault message: " + properties.getKeyVaultMessage();
    }
    ```

1. Skapa en ny fil som heter *AzureCredentials.java* och lägg till koden nedan.

    ```java
    package com.example;

    import com.azure.core.credential.TokenCredential;
    import com.azure.identity.EnvironmentCredentialBuilder;
    import com.microsoft.azure.spring.cloud.config.AppConfigurationCredentialProvider;
    import com.microsoft.azure.spring.cloud.config.KeyVaultCredentialProvider;

    public class AzureCredentials implements AppConfigurationCredentialProvider, KeyVaultCredentialProvider{

        @Override
        public TokenCredential getKeyVaultCredential(String uri) {
            return getCredential();
        }

        @Override
        public TokenCredential getAppConfigCredential(String uri) {
            return getCredential();
        }

        private TokenCredential getCredential() {
            return new EnvironmentCredentialBuilder().build();
        }

    }
    ```

1. Skapa en ny fil med namnet *AppConfiguration.java*. Och lägg till koden nedan.

    ```java
    package com.example;

    import org.springframework.context.annotation.Bean;
    import org.springframework.context.annotation.Configuration;

    @Configuration
    public class AppConfiguration {

        @Bean
        public AzureCredentials azureCredentials() {
            return new AzureCredentials();
        }
    }
    ```

1. Skapa en ny fil i dina resurser META-INF katalog som kallas *spring.factories* och lägga till.

    ```factories
    org.springframework.cloud.bootstrap.BootstrapConfiguration=\
    com.example.AppConfiguration
    ```

1. Bygg din Spring Boot ansökan med Maven och kör den, till exempel:

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```

1. När programmet har körts använder du *curl* för att testa ditt program, till exempel:

      ```shell
      curl -X GET http://localhost:8080/
      ```

    Meddelandet som du angav i App Configuration Store visas. Du ser också meddelandet som du angav i Key Vault.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien skapade du en appkonfigurationsnyckel som refererar till ett värde som lagras i Key Vault. Om du vill veta hur du använder funktionsflaggor i javafjäderprogrammet fortsätter du till nästa självstudiekurs.

> [!div class="nextstepaction"]
> [Hanterad identitetsintegrering](./quickstart-feature-flag-spring-boot.md)
