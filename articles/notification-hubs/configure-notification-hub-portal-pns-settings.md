---
title: Konfigurera push-meddelanden i Azure Notification Hubs | Microsoft-dokument
description: Lär dig hur du konfigurerar Azure Notification Hubs i Azure-portalen med hjälp av PNS-inställningar (Platform Notification System).
services: notification-hubs
author: sethmanheim
manager: femila
editor: dbradish-microsoft
ms.service: notification-hubs
ms.workload: mobile
ms.topic: quickstart
ms.date: 02/14/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 02/14/2019
ms.openlocfilehash: 78afb124ee1d1ab9b212197fb7a7140f88de9940
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349520"
---
# <a name="quickstart-set-up-push-notifications-in-a-notification-hub"></a>Snabbstart: Konfigurera push-meddelanden i en meddelandehubb

Azure Notification Hubs tillhandahåller en push-motor som är enkel att använda och som skalas ut. Använd Meddelandehubbar för att skicka meddelanden till valfri plattform (iOS, Android, Windows, Baidu) och från vilken backdel som helst (moln eller lokalt). Mer information finns i [Vad är Azure Notification Hubs?](notification-hubs-push-notification-overview.md).

I den här snabbstarten använder du pns-inställningarna (Platform Notification System) i Meddelandehubbar för att konfigurera push-meddelanden på flera plattformar. Snabbstarten visar stegen att ta i Azure-portalen.  [Google Firebase Cloud Messaging](/azure/notification-hubs/configure-notification-hub-portal-pns-settings?tabs=azure-cli#google-firebase-cloud-messaging-fcm) innehåller instruktioner för hur du använder Azure CLI.

Om du inte redan har skapat en meddelandehubb skapar du en nu. Mer information finns [i Skapa en Azure-meddelandehubb i Azure-portalen](create-notification-hub-portal.md) eller Skapa en [Azure-meddelandehubb med Azure CLI](create-notification-hub-azure-cli.md)

## <a name="apple-push-notification-service"></a>Apple Push Notification Service

Så här konfigurerar du Apns (Apple Push Notification Service):

1. På sidan **Meddelandehubben** på sidan Meddelandehubben väljer du **Apple (APNS)** på den vänstra menyn.

1. För **autentiseringsläge**väljer du antingen **Certifikat** eller **Token**.

   a. Om du väljer **Certifikat:**
   * Markera filikonen och välj sedan den *p12-fil* som du vill ladda upp.
   * Ange ett lösenord.
   * Välj **Sandbox**-läge. Om du vill skicka push-meddelanden till användare som köpt appen från butiken väljer du **Produktionsläge.**

     ![Skärmbild av en APNS-certifikatkonfiguration i Azure-portalen](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png)

   b. Om du väljer **Token:**

   * Ange värdena för **nyckel-ID,** **bunt-ID,** **grupp-ID**och **token**.
   * Välj **Sandbox**-läge. Om du vill skicka push-meddelanden till användare som köpt appen från butiken väljer du **Produktionsläge.**

     ![Skärmbild av en APNS-tokenkonfiguration i Azure-portalen](./media/configure-notification-hub-portal-pns-settings/notification-hubs-apple-config-token.png)

Mer information finns i [Push-meddelanden till iOS med hjälp av Azure Notification Hubs](notification-hubs-ios-apple-push-notification-apns-get-started.md).

## <a name="google-firebase-cloud-messaging-fcm"></a>Google Firebase Cloud Messaging (FCM)

# <a name="portal"></a>[Portal](#tab/azure-portal)

Så här konfigurerar du push-meddelanden för Google FCM:

1. På sidan **Meddelandehubben** på sidan Meddelandehubben väljer du **Google (GCM/FCM)** på den vänstra menyn.
2. Klistra in **API-nyckeln** för Google FCM-projektet som du sparade tidigare.
3. Välj **Spara**.

   ![Skärmbild som visar hur du konfigurerar meddelandehubbar för Google FCM](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

När du har slutfört de här stegen anger en avisering att meddelandehubben har uppdaterats. Knappen **Spara** är inaktiverad.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="prerequisites"></a>Krav

Innan du börjar måste du ha följande:

* [Azure CLI](/cli/azure/install-azure-cli) version 2.0.67 eller senare.

* Azure [CLI-tillägget för meddelandehubbar](/cli/azure/ext/notification-hub/notification-hub).
* **API-nyckeln** för ett PROJEKT för Google Firebase Cloud Messaging (FCM).

### <a name="set-up-push-notifications-for-google-fcm"></a>Konfigurera push-meddelanden för Google FCM

1. Använd kommandot [az notification-hub autentiseringsuppgifter gcm update](/cli/azure/ext/notification-hub/notification-hub/credential/gcm#ext-notification-hub-az-notification-hub-credential-gcm-update) för att lägga till google-API-nyckeln i meddelandehubben.

   ```azurecli
   az notification-hub credential gcm update --resource-group spnhubrg --namespace-name spnhubns    --notification-hub-name spfcmtutorial1nhub --google-api-key myKey
   ```

2. Android-appen behöver en anslutningssträng för att ansluta till meddelandehubben.  Använd kommandot [az notification-hub auktoriseringsregel för](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list) att lista tillgängliga åtkomstprinciper.  Använd kommandot [az notification-hub auktoriseringsregellista-nycklar](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) för att hämta anslutningssträngarna för åtkomstprincipen.  Ange **den primäraConnectionString** eller **secondaryConnectionString** i parametern `--query` för att hämta den primära anslutningssträngen direkt.

   ```azurecli
   #list access policies for a notification hub
   az notification-hub authorization-rule list --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --output table

   #list keys and connection strings for a notification hub access policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name myAccessPolicyName --output json

   #get the primaryConnectionString for an access policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name myAccessPolicyName --query primaryConnectionString
   ```

3. Använd kommandot [az notification-hub test-send](/cli/azure/ext/notification-hub/notification-hub#ext-notification-hub-az-notification-hub-test-send) för att testa att skicka meddelanden till Android-appen.

   ```azurecli
   #test with message body
   az notification-hub test-send --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --notification-format gcm --message "my message body"

   #test with JSON string
   az notification-hub test-send --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --notification-format gcm --payload "{\"data\":{\"message\":\"my JSON string\"}}"
   ```

Hämta Azure CLI-referenser för andra plattformar med kommandot [az notification-hub autentiseringsuppgifter.](/cli/azure/ext/notification-hub/notification-hub/credential)

---

Mer information om hur du skickar meddelanden till ett Android-program finns i [Skicka push-meddelanden till Android-enheter med Firebase](notification-hubs-android-push-notification-google-fcm-get-started.md).

## <a name="windows-push-notification-service"></a>Windows Push Meddelande-tjänst

Så här konfigurerar du WNS (Windows Push Notification Service):

1. På sidan **Meddelandehubben** på sidan Meddelandehubben väljer du **Windows (WNS)** på den vänstra menyn.
2. Ange värden för **Package SID** (Paket-SID) och **Security Key** (Säkerhetsnyckel).
3. Välj **Spara**.

   ![Skärmbild som visar rutorna Package SID och Security Key](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

Information finns i [Skicka meddelanden till UWP-appar med hjälp av Azure Notification Hubs](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).

## <a name="microsoft-push-notification-service-for-windows-phone"></a>Microsoft Push Notification Service för Windows Phone

Så här konfigurerar du MICROSOFT Push Notification Service (MPNS) för Windows Phone:

1. På sidan **Meddelandehubben** på sidan Meddelandehubben väljer du **Windows Phone (MPNS)** på den vänstra menyn.
1. Aktivera antingen oautentiserade eller autentiserade push-meddelanden:

   a. Om du vill aktivera oautentiserade push-meddelanden väljer du **Aktivera oautentiserade push** > **Spara**.

      ![Skärmbild som visar hur du aktiverar oautentiserade push-meddelanden](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   b. Så här aktiverar du autentiserade push-meddelanden:
      * Välj **Ladda upp certifikat i**verktygsfältet .
      * Markera filikonen och välj sedan certifikatfilen.
      * Ange lösenordet för certifikatet.
      * Välj **OK**.
      * Välj **Spara**på sidan **Windows Phone (MPNS).**

Mer information finns i [Push-meddelanden till Windows Phone-appar med hjälp av Meddelandehubbar](notification-hubs-windows-mobile-push-notifications-mpns.md).

## <a name="baidu-android-china"></a>Baidu (Android China)

Så här konfigurerar du push-meddelanden för Baidu:

1. På sidan **Meddelandehubben** på sidan Meddelandehubben väljer du **Baidu (Android Kina)** på den vänstra menyn.
2. Ange **Api-nyckeln** som du har fått från Baidu-konsolen i Baidu-molndush-projektet.
3. Ange **den hemliga nyckeln** som du fick från Baidu-konsolen i Baidu-molnets push-projekt.
4. Välj **Spara**.

    ![Skärmbild av Meddelandehubbar som visar Baidu-konfigurationen (Android Kina) för push-meddelanden](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)

När du har slutfört de här stegen anger en avisering att meddelandehubben har uppdaterats. Knappen **Spara** är inaktiverad.

Mer information finns i [Komma igång med meddelandehubbar med Baidu](notification-hubs-baidu-china-android-notifications-get-started.md).

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten lärde du dig hur du konfigurerar inställningar för plattformsmeddelandesystem för en meddelandehubb i Azure-portalen.

Mer information om hur du skickar meddelanden till olika plattformar finns i följande självstudier:

-[Push-meddelanden till iOS-enheter med hjälp av Meddelandehubbar och APNS](notification-hubs-ios-apple-push-notification-apns-get-started.md)
-[Push-meddelanden till Android-enheter med hjälp av Meddelandehubbar och Google FCM](notification-hubs-android-push-notification-google-fcm-get-started.md)
-[Push-meddelanden till en UWP-app som körs på en Push-meddelanden för Windows-enheten](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
-till en Windows Phone[8-app med hjälp av MPNS](notification-hubs-windows-mobile-push-notifications-mpns.md)
-[Push-meddelanden med hjälp av Meddelandehubbar och Baidu-molnfush](notification-hubs-baidu-china-android-notifications-get-started.md)
