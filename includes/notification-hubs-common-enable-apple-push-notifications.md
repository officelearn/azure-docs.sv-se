---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 72065ce602c6d29255a3500e26d8c6f36a19ebed
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2020
ms.locfileid: "85081556"
---
### <a name="register-your-ios-app-for-push-notifications"></a>Registrera din iOS-app för push-meddelanden

Om du vill skicka push-meddelanden till en iOS-App registrerar du ditt program med Apple och registrerar dig även för push-meddelanden.  

1. Om du inte redan har registrerat din app kan du gå till [iOS-etablerings portalen](https://go.microsoft.com/fwlink/p/?LinkId=272456) i Apple Developer Center. Logga in på portalen med ditt Apple-ID, navigera till **certifikat, identifierare & profiler**och välj sedan **identifierare**. Klicka **+** om du vill registrera en ny app.

    ![Sidan för App-ID:n i etableringsportalen för iOS](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-ios-appids.png)

1. På sidan **Registrera en ny identifierare** väljer du alternativ knappen **app-ID** . Välj sedan **Fortsätt**.

    ![iOS-etablerings Portal Registrera ny ID-sida](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-ios-appids-new.png)

1. Uppdatera följande tre värden för din nya app och välj sedan **Fortsätt**:

   * **Beskrivning**: Ange ett beskrivande namn för din app.

   * **Paket-ID**: Ange ett paket-ID för formatet **com. <organization_identifier>. <product_name>** som anges i [program distributions guiden](https://help.apple.com/xcode/mac/current/#/dev91fe7130a). I följande skärm bild `mobcat` används värdet som ett organisations-ID och **PushDemo** -värdet används som produkt namn.

      ![iOS-etablerings Portal registrera app-ID-sida](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-new-appid-bundle.png)

   * **Push-meddelanden**: Markera alternativet **push-meddelanden** i avsnittet **funktioner** .

      ![Formulär för att registrera ett nytt App-ID](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-new-appid-push.png)

      Den här åtgärden genererar ditt app-ID och begär att du bekräftar informationen. Välj **Fortsätt**och välj **Registrera** för att bekräfta det nya app-ID: t.

      ![Bekräfta nytt app-ID](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-new-appid-register.png)

      När du har valt **Registrera**visas det nya app-ID: t som ett rad objekt på sidan **certifikat, identifierare & profiler** .

1. På sidan **certifikat, identifierare & profiler** , under **identifierare**, letar du upp det app-ID-rads objekt som du skapade. Välj sedan dess rad för att visa skärmen **Redigera appens ID-konfiguration** .

#### <a name="creating-a-certificate-for-notification-hubs"></a>Skapa ett certifikat för Notification Hubs

Ett certifikat krävs för att meddelande hubben ska fungera med **Apple Push-Notification Services (APN)** och kan tillhandahållas på något av följande sätt:

1. [Skapa ett P12 Push-certifikat som kan överföras direkt till Notification Hub](#option-1-creating-a-p12-push-certificate-that-can-be-uploaded-directly-to-notification-hub) (*den ursprungliga metoden*)

1. [Skapa ett P8-certifikat som kan användas för tokenbaserad autentisering](#option-2-creating-a-p8-certificate-that-can-be-used-for-token-based-authentication) (*den nyare och rekommenderade metoden*)

Den nyare metoden har ett antal fördelar som dokumenterats i [tokenbaserad (http/2) autentisering för APN](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-http2-token-authentification). Färre steg krävs, men det är också ett krav på vissa scenarier. Men steg har angetts för båda metoderna eftersom de fungerar i de här självstudierna.

##### <a name="option-1-creating-a-p12-push-certificate-that-can-be-uploaded-directly-to-notification-hub"></a>ALTERNATIV 1: skapa ett P12-Push-certifikat som kan överföras direkt till Notification Hub

1. Kör Nyckelhanteraren på din Mac. Den kan öppnas från mappen **verktyg** eller den **andra** mappen i Start fönstret.

1. Välj **nyckel rings åtkomst**, expandera **certifikat assistenten**och välj sedan **begär ett certifikat från en certifikat utfärdare**.

    ![Använd nyckelhanterare för att begära ett nytt certifikat](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

   > [!NOTE]
   > Som standard väljer åtkomst till nyckel ringar det första objektet i listan. Detta kan vara ett problem om du befinner dig i kategorin **certifikat** och **certifikat utfärdaren Apple Worldwide Developer relation** är inte det första objektet i listan. Kontrol lera att du har ett objekt som inte är ett nyckel objekt, eller att nyckeln för **certifikat utfärdare för Apple Worldwide Developer-relation** är vald, innan du genererar CSR (certifikat signerings förfrågan).

1. Välj din **användar-e-postadress**, ange ditt **eget namn** -värde, kontrol lera att du har angett **Spara på disk**och välj sedan **Fortsätt**. Lämna **ca-e-postadress** tomt eftersom det inte behövs.

    ![Förväntad certifikat information](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-csr-info.png)

1. Ange ett namn på **filen för begäran om certifikat signering (CSR)** i **Spara som**, Välj den plats **där**och välj sedan **Spara**.

    ![Välj ett fil namn för certifikatet](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-save-csr.png)

    Den här åtgärden sparar **CSR-filen** på den valda platsen. Standard platsen är **Skriv bord**. Kom ihåg den plats du valde för filen.

1. Gå tillbaka till sidan **certifikat, identifierare & profiler** i iOS- [etablerings portalen](https://go.microsoft.com/fwlink/p/?LinkId=272456), rulla ned till alternativet kontrollerade push- **meddelanden** och välj sedan **Konfigurera** för att skapa certifikatet.

    ![Redigera sidan för App-ID:n](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-edit-appid.png)

1. Fönstret **TLS/SSL-certifikat för Apple Push Notification Service** visas. Välj knappen **Skapa certifikat** under avsnittet **utvecklings-TLS/SSL-certifikat** .

    ![Skapa certifikat för knappen för App-ID](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

    Skärmen **skapa ett nytt certifikat** visas.

    > [!NOTE]
    > Den här guiden använder ett utvecklarcertifikat. Du använder samma process när du registrerar ett driftscertifikat. Se bara till att du använder samma certifikattyp när du skickar meddelanden.

1. Välj **Välj fil**, bläddra till den plats där du sparade **CSR-filen**och dubbelklicka sedan på certifikat namnet för att läsa in den. Välj sedan **Fortsätt**.

1. När portalen har skapat certifikatet väljer du knappen **Ladda ned** . Spara certifikatet och kom ihåg platsen där det sparades.

    ![Nedladdningssidan för genererade certifikat](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

    Certifikatet laddas ned och sparas på din dator i mappen **hämtade filer** .

    ![Hitta certifikatfilen i mappen Hämtade filer](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [!NOTE]
    > Som standard heter det hämtade utvecklings certifikatet **aps_development. cer**.

1. Dubbelklicka på det hämtade push-certifikatet **aps_development.cer**. Den här åtgärden installerar det nya certifikatet i nyckelringen enligt följande bild:

    ![Nya certifikat visas i listan med certifikat för nyckelhanteraren](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [!NOTE]
    > Även om namnet i ditt certifikat kan vara annorlunda, kommer namnet att föregås av **Apple Development iOS push-tjänster** och ha rätt paket identifierare kopplad till sig.

1. I nyckel rings åtkomst, **kontrollerar**du genom att  +  **Klicka** på det nya push-certifikatet som du skapade i kategorin **certifikat** . Välj **Exportera**, namnge filen, Välj formatet **P12** och välj sedan **Spara**.

    ![Exportera certifikatet i p12-format](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)

    Du kan välja att skydda certifikatet med ett lösen ord, men det är valfritt att ange ett lösen ord. Klicka på **OK** om du vill kringgå lösen ords skapande. Anteckna fil namnet och platsen för det exporterade P12-certifikatet. De används för att aktivera autentisering med APN.

    > [!NOTE]
    > P12-filnamnet och platsen kan vara annorlunda än vad som är bildat i den här självstudien.

##### <a name="option-2-creating-a-p8-certificate-that-can-be-used-for-token-based-authentication"></a>ALTERNATIV 2: skapa ett P8-certifikat som kan användas för tokenbaserad autentisering

1. Anteckna följande information:

    * **App-ID-prefix** (**Team-ID**)
    * **Samlings-ID**

1. Tillbaka i **certifikat, identifierare & profiler**, klickar du på **nycklar**.

   > [!NOTE]
   > Om du redan har konfigurerat en nyckel för **APN**kan du återanvända det P8-certifikat som du laddade ned direkt efter att det har skapats. I så fall kan du ignorera steg **3** till **5**.

1. Klicka på **+** knappen (eller knappen **skapa en nyckel** ) för att skapa en ny nyckel.
1. Ange ett lämpligt **nyckel namn** värde, kontrol lera sedan alternativet för **Apple Push Notifications-tjänsten (APNs)** och klicka sedan på **Fortsätt**, följt av **Registrera** på nästa skärm.
1. Klicka på **Hämta** och flytta **P8** -filen (med *AuthKey_*) till en säker lokal katalog och klicka sedan på **Slutför**.

   > [!NOTE]
   > Se till att behålla din P8-fil på en säker plats (och spara en säkerhets kopia). När du har laddat ned nyckeln kan du inte ladda ned den igen eftersom Server kopian tas bort.
  
1. Klicka på nyckeln som du skapade (eller en befintlig nyckel om du har valt att använda den i stället) på **nycklar**.
1. Anteckna värdet för **nyckel-ID** .
1. Öppna ditt P8-certifikat i ett lämpligt program som du väljer, till exempel [**Visual Studio Code**](https://code.visualstudio.com). Anteckna nyckelvärdet (mellan **-----starta den privata nyckeln-----** och **-----avsluta den privata nyckeln-----**).

    -----STARTA PRIVAT NYCKEL-----  
    <key_value>  
    -----AVSLUTANDE PRIVAT NYCKEL-----

    > [!NOTE]
    > Detta är det **token-värde** som kommer att användas senare för att konfigurera **Notification Hub**.

I slutet av de här stegen bör du ha följande information för att kunna använda senare i [Konfigurera Notification Hub med APN-information](#configure-your-notification-hub-with-apns-information):

* **Team-ID** (se steg 1)
* **Paket-ID** (se steg 1)
* **Nyckel-ID** (se steg 7)
* **Token-värde** (P8-nyckel värde som hämtades i steg 8)

### <a name="create-a-provisioning-profile-for-the-app"></a>Skapa en etableringsprofil för appen

1. Gå tillbaka till [iOS-etablerings portalen](https://go.microsoft.com/fwlink/p/?LinkId=272456), Välj **certifikat, identifierare & profiler**, Välj **profiler** på den vänstra menyn och välj sedan **+** för att skapa en ny profil. Skärmen **Registrera en ny etablerings profil** visas.

1. Välj **utveckling av iOS-appar** under **utveckling** som etablerings profil typ och välj sedan **Fortsätt**.

    ![Lista med etableringsprofiler](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

1. Välj sedan det app-ID som du skapade i list rutan **app-ID** och välj **Fortsätt**.

    ![Välj App-ID:et](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)

1. I fönstret **Välj certifikat** väljer du det utvecklings certifikat som du använder för kod signering och väljer **Fortsätt**.

    > [!NOTE]
    > Det här certifikatet är inte push-certifikatet som du skapade i [föregående steg](#creating-a-certificate-for-notification-hubs). Detta är ditt utvecklings certifikat. Om det inte finns något, måste du skapa det eftersom det är ett [krav](#prerequisites) för den här självstudien. Du kan skapa certifikat för utvecklare i [Apple Developer-portalen](https://developer.apple.com)via [Xcode](https://developer.apple.com/library/archive/documentation/ToolsLanguages/Conceptual/YourFirstAppStoreSubmission/ProvisionYourDevicesforDevelopment/ProvisionYourDevicesforDevelopment.html) eller i [Visual Studio](https://docs.microsoft.com/xamarin/ios/get-started/installation/device-provisioning/).

1. Gå tillbaka till sidan **certifikat, identifierare & profiler** , Välj **profiler** på den vänstra menyn och välj sedan **+** för att skapa en ny profil. Skärmen **Registrera en ny etablerings profil** visas.

1. I fönstret **Välj certifikat** väljer du det utvecklings certifikat som du skapade. Välj sedan **Fortsätt**.

1. Välj sedan de enheter som ska användas för testning och välj **Fortsätt**.

1. Slutligen väljer du ett namn för profilen i **etablerings profil namn**och väljer **generera**.

    ![Välj namn på etableringsprofilen](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)

1. När den nya etablerings profilen har skapats väljer du **Hämta**. Kom ihåg platsen där den sparades.

1. Bläddra till platsen för etablerings profilen och dubbelklicka sedan på den för att installera den på din utvecklings dator.
