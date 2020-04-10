---
title: ta med fil
description: ta med fil
services: notification-hubs
author: sethmanheim
ms.service: notification-hubs
ms.topic: include
ms.date: 02/10/2020
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: a9e8574ea2d7222871c7f065383e6c0c62057dd3
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "81007868"
---
## <a name="generate-the-certificate-signing-request-file"></a>Generera begäran om certifikatsignering

Apple Push Notification Service (APNs) använder certifikat för att autentisera dina push-meddelanden. Följ de här instruktionerna för att skapa nödvändiga push-certifikat för att skicka och ta emot meddelanden. Mer information om de här koncepten finns i den officiella [Apple Push Notification Service](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html)-dokumentationen.

Generera csr-filen (Certificate Signing Request), som Apple använder för att generera ett signerat push-certifikat.

1. Kör Nyckelhanteraren på din Mac. Den kan öppnas från mappen **Verktyg** eller mappen **Andra** på Startplattan.

1. Välj **Nyckelringsåtkomst**, expandera **Certifikatassistenten**och välj sedan **Begär ett certifikat från en certifikatutfärdare**.

    ![Använd nyckelhanterare för att begära ett nytt certifikat](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

   > [!NOTE]
   > Som standard väljer Nyckelringsåtkomst det första objektet i listan. Detta kan vara ett problem om du är i kategorin **Certifikat** och **Apple Worldwide Developer Relations Certification Authority** inte är det första objektet i listan. Kontrollera att du har ett icke-nyckelobjekt, eller så väljs nyckeln **apple worldwide developer relations-certifikatutfärdare** innan du skapar CSR(Certificate Signing Request).

1. Välj din **e-postadress**för användare, ange värdet **För vanligt namn,** se till att du anger **Sparad på disk**och välj sedan **Fortsätt**. Lämna **ca-e-postadressen** tom eftersom den inte krävs.

    ![Nödvändig certifikatsinformation](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-csr-info.png)

1. Ange ett namn på CSR-filen i **Spara som,** välj den plats i **Var**och välj sedan **Spara**.

    ![Välj ett filnamn för certifikatet](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-save-csr.png)

    Den här åtgärden sparar CSR-filen på den valda platsen. Standardplatsen är **Desktop**. Kom ihåg den plats du valde för filen.

Registrera sedan din app med Apple, aktivera push-meddelanden och ladda upp den exporterade kundtjänstrepresentanten för att skapa ett push-certifikat.

## <a name="register-your-app-for-push-notifications"></a>Registrera din app för push-meddelanden

Om du vill skicka push-meddelanden till en iOS-app registrerar du din ansökan hos Apple och registrerar även push-meddelanden.  

1. Om du inte redan har registrerat din app bläddrar du till [iOS-etableringsportalen](https://go.microsoft.com/fwlink/p/?LinkId=272456) i Apple Developer Center. Logga in på portalen med ditt Apple-ID och välj **Identifierare**. Välj **+** sedan för att registrera en ny app.

    ![Sidan för App-ID:n i etableringsportalen för iOS](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids.png)

2. Välj alternativknappen **App-ID** på skärmen **Registrera en ny identifierare.** Välj sedan **Fortsätt**.

    ![iOS-etableringsportal registrerar ny ID-sida](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids-new.png)

3. Uppdatera följande tre värden för den nya appen och välj sedan **Fortsätt:**

   * **Beskrivning**: Skriv ett beskrivande namn för appen.

   * **Bunt-ID:** Ange ett paket-ID för formuläret **Organisationsidentifierare.Produktnamn** som nämns i [appdistributionsguiden](https://help.apple.com/xcode/mac/current/#/dev91fe7130a). *Värdena Organisationsidentifierare* och *produktnamn* måste matcha den organisationsidentifierare och produktnamn som du använder när du skapar Xcode-projektet. I följande skärmbild används **värdet NotificationHubs** som organisationsidentifierare och värdet **GetStarted** används som produktnamn. Kontrollera att **värdet för paketidentifieraren** matchar värdet i Xcode-projektet så att Xcode använder rätt publiceringsprofil.

      ![iOS-etableringsportalregister för app-ID](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-bundle.png)

   * **Push-meddelanden:** Kontrollera alternativet **Push-meddelanden** i avsnittet **Funktioner.**

      ![Formulär för att registrera ett nytt App-ID](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-push.png)

      Den här åtgärden genererar ditt app-ID och begär att du bekräftar informationen. Välj **Fortsätt**och välj sedan **Registrera** för att bekräfta det nya app-ID:t.

      ![Bekräfta nytt app-ID](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-register.png)

      När du har valt **Registrera**visas det nya app-ID:t som ett radobjekt på sidan **Certifikat, identifierare & profiler.**

4. Leta upp det app-ID-radobjekt som du just skapade under **Identifierare**på sidan **Certifikat, identifierar** & profiler och markera dess rad för att visa skärmen **Redigera app-ID-konfigurationen.**

## <a name="creating-a-certificate-for-notification-hubs"></a>Skapa ett certifikat för meddelandehubbar
Ett certifikat krävs för att meddelandehubben ska fungera med **APNS**. Detta kan göras på ett av två sätt:

1. Skapa en **P12** som kan överföras direkt till Meddelandehubben.  
2. Skapa en **.p8** som kan användas för [tokenbaserad autentisering](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-http2-token-authentification) (*den nyare metoden*).

Den nyare metoden har ett antal fördelar (jämfört med att använda certifikat) som dokumenteras i [tokenbaserad (HTTP/2) autentisering för APNS](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-http2-token-authentification). Det har dock tagits åtgärder för båda tillvägagångssätten. 

### <a name="option-1-creating-a-p12-push-certificate-that-can-be-uploaded-directly-to-notification-hub"></a>ALTERNATIV 1: Skapa ett .p12-push-certifikat som kan överföras direkt till Meddelandehubben

1. Bläddra ned till alternativet markerade **push-meddelanden** och välj sedan **Konfigurera** för att skapa certifikatet.

    ![Redigera sidan för App-ID:n](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-edit-appid.png)

2. Fönstret **SSL-certifikat** för Tjänsten Apple Push Notification visas. Välj knappen **Skapa certifikat** under avsnittet **Utveckling SSL-certifikat.**

    ![Skapa certifikat för knappen för App-ID](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

    Skärmen **Skapa ett nytt certifikat** visas.

    > [!NOTE]
    > Den här guiden använder ett utvecklarcertifikat. Du använder samma process när du registrerar ett driftscertifikat. Se bara till att du använder samma certifikattyp när du skickar meddelanden.

3. Välj **Välj fil**, bläddra till den plats där du sparade CSR-filen från den första uppgiften och dubbelklicka sedan på certifikatnamnet för att läsa in den. Välj sedan **Fortsätt**.

4. När portalen har skapat certifikatet väljer du knappen **Hämta.** Spara certifikatet och kom ihåg den plats där det sparas.

    ![Nedladdningssidan för genererade certifikat](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

    Certifikatet hämtas och sparas på datorn i mappen **Nedladdningar.**

    ![Hitta certifikatfilen i mappen Hämtade filer](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [!NOTE]
    > Som standard heter det hämtade utvecklingscertifikatet **aps_development.cer**.

5. Dubbelklicka på det hämtade push-certifikatet **aps_development.cer**. Den här åtgärden installerar det nya certifikatet i nyckelringen enligt följande bild:

    ![Nya certifikat visas i listan med certifikat för nyckelhanteraren](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [!NOTE]
    > Även om namnet i certifikatet kan vara annorlunda, kommer namnet att föregås av **Apple Development iOS Push Services**.

6. I nyckelhanteraren högerklickar du på det nya push-certifikatet som du skapade i **Certifikat**-kategorin. Välj **Exportera**, namnge filen, markera **.p12-formatet** och välj sedan **Spara**.

    ![Exportera certifikatet i p12-format](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)

    Du kan välja att skydda certifikatet med ett lösenord, men det är valfritt. Klicka på **OK** om du vill kringgå skapandet av lösenord. Anteckna filnamnet och platsen dit .p12-certifikatet exporterats. De används för att aktivera autentisering med APN.They are used to enable authentication with APNs.

    > [!NOTE]
    > Filnamnet och platsen .p12 kan skilja sig från vad som visas i den här självstudien.

### <a name="option-2-creating-a-p8-certificate-that-can-be-used-for-token-based-authentication"></a>ALTERNATIV 2: Skapa ett P8-certifikat som kan användas för tokenbaserad autentisering

1. Anteckna följande uppgifter:

    - **Prefix för app-ID** (det här är ett **team-ID)**
    - **Samlings-ID**
    
2. Tillbaka i **certifikat, identifierare & profiler**, klicka på **Nycklar**.

   > [!NOTE]
   > Om du redan har konfigurerat en nyckel för **APNS**kan du återanvända P8-certifikatet som du hämtade direkt efter att det skapades. Om så är fallet kan du ignorera steg **3** till **5**.

3. Klicka **+** på knappen (eller knappen **Skapa en nyckel)** för att skapa en ny nyckel.
4. Ange ett lämpligt **nyckelnamnsvärde,** kontrollera sedan alternativet **Apple Push Notifications service (APNs)** och klicka sedan på **Fortsätt**, följt av **Registrera på** nästa skärm.
5. Klicka på **Hämta** och sedan flytta **.p8-filen** (föregås av *AuthKey_)* till en säker lokal katalog och klicka sedan på **Klar**.

   > [!NOTE] 
   > Se till att hålla p8-filen på en säker plats (och spara en säkerhetskopia). När du har hämtat nyckeln kan den inte hämtas igen eftersom serverkopian tas bort.
  
6. På **tangenter**klickar du på den tangent som du just skapade (eller en befintlig nyckel om du har valt att använda den i stället).
7. Anteckna **värdet nyckel-ID.**
8. Öppna ditt P8-certifikat i ett lämpligt program som du väljer, till exempel [**Visual Studio Code**](https://code.visualstudio.com) och anteckna sedan nyckelvärdet. Det här är värdet mellan **-----BEGIN PRIVATE KEY-----** och **-----END PRIVATE KEY-----** .

    ```
    -----BEGIN PRIVATE KEY-----
    <key_value>
    -----END PRIVATE KEY-----
    ```

    > [!NOTE]
    > Det här är **tokenvärdet** som kommer att användas senare för att konfigurera **Notification Hub**. 

I slutet av dessa steg bör du ha följande information för användning senare i [Konfigurera meddelandehubben med APN-information:](#configure-your-notification-hub-with-apns-information)

- **Team-ID** (se steg 1)
- **Bunt-ID** (se steg 1)
- **Nyckel-ID** (se steg 7)
- **Tokenvärde** d.v.s. nyckelvärdet .p8 (se steg 8)

## <a name="create-a-provisioning-profile-for-the-app"></a>Skapa en etableringsprofil för appen

1. Gå tillbaka till [iOS-etableringsportalen](https://go.microsoft.com/fwlink/p/?LinkId=272456), välj **Certifikat, identifierare & profiler**, välj **+** **Profiler** på den vänstra menyn och välj sedan för att skapa en ny profil. Skärmen **Registrera en ny etableringsprofil** visas.

1. Välj **iOS AppUtveckling** under **Utveckling** som etableringsprofiltyp och välj sedan **Fortsätt**.

    ![Lista med etableringsprofiler](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

1. Välj sedan det app-ID som du skapade i listrutan **App-ID** och välj **Fortsätt**.

    ![Välj App-ID:et](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)

1. I fönstret **Välj certifikat** väljer du det utvecklingscertifikat som du använder för kodsignering och väljer **Fortsätt**. Det här certifikatet är inte det push-certifikat som du skapade. Om det inte finns någon måste du skapa den. Om det finns ett certifikat går du vidare till nästa steg. Så här skapar du ett utvecklingscertifikat om det inte finns något:

    1. Om inga **certifikat är tillgängliga**väljer du Skapa **certifikat**.
    2. I avsnittet **Programvara** väljer du **Apple Development**. Välj sedan **Fortsätt**.
    3. Välj **Arkiv**på skärmen **Skapa ett nytt certifikat** .
    4. Bläddra till certifikatet **För certifikatsigneringsbegäran** som du skapade tidigare, markera det och välj sedan **Öppna**.
    5. Välj **Fortsätt**.
    6. Hämta utvecklingscertifikatet och kom ihåg platsen som det sparas till.

1. Gå tillbaka till sidan **Certifikat, identifierare & profiler,** välj **Profiler** på den **+** vänstra menyn och välj sedan för att skapa en ny profil. Skärmen **Registrera en ny etableringsprofil** visas.

1. Välj det utvecklingscertifikat som du just skapade i fönstret **Välj certifikat.** Välj sedan **Fortsätt**.

1. Välj sedan de enheter som ska användas för testning och välj **Fortsätt**.

1. Slutligen väljer du ett namn för profilen i **Etablering av profilnamn**och väljer **Generera**.

    ![Välj namn på etableringsprofilen](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)

1. När den nya etableringsprofilen skapas väljer du **Hämta**. Kom ihåg var den sparas.

1. Bläddra till platsen för etableringsprofilen och dubbelklicka sedan på den för att installera den på din Xcode-utvecklingsdator.

## <a name="create-a-notification-hub"></a>Skapa en meddelandehubb

I det här avsnittet skapar du en meddelandehubb och konfigurerar autentisering med APN med hjälp av push-certifikatet .p12 eller tokenbaserad autentisering. Om du vill använda en meddelandehubb som du redan har skapat kan du hoppa till steg 5.

[!INCLUDE [notification-hubs-portal-create-new-hub](notification-hubs-portal-create-new-hub.md)]

## <a name="configure-your-notification-hub-with-apns-information"></a>Konfigurera meddelandehubben med APNs-information

Under **Meddelandetjänster**väljer du **Apple (APNS)** och följer sedan lämpliga steg baserat på den metod du valde tidigare i avsnittet [Skapa ett certifikat för meddelandehubbar.](#creating-a-certificate-for-notification-hubs)  

> [!NOTE]
> Använd endast **produktionsläget** för **program** om du vill skicka push-meddelanden till användare som har köpt appen från butiken.

### <a name="option-1-using-a-p12-push-certificate"></a>ALTERNATIV 1: Använda ett .p12-push-certifikat

1. Välj **Certifikat**.

1. Välj filikonen.

1. Markera filen .p12 som du exporterade tidigare och välj sedan **Öppna**.

1. Om det behövs anger du rätt lösenord.

1. Välj **Sandbox**-läge.

    ![Konfigurera APNs-certifikat i Azure-portalen](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-apple-config-cert.png)

1. Välj **Spara**.

### <a name="option-2-using-token-based-authentication"></a>ALTERNATIV 2: Använda tokenbaserad autentisering

1. Välj **token**.
1. Ange följande värden som du har fått tidigare:

    - **Nyckel-ID**
    - **Samlings-ID**
    - **Team-ID**
    - **Token** 

1. Välj **Sandbox**
1. Välj **Spara**. 

Du har nu konfigurerat meddelandehubben med APN.You've now configured your notification hub with APNs. Du har också anslutningssträngarna för att registrera din app och skicka push-meddelanden.
