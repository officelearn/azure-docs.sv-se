---
title: Hantera federationscertifikat i Azure AD | Microsoft-dokument
description: Lär dig hur du anpassar utgångsdatumet för dina federationscertifikat och hur du förnyar certifikat som snart upphör att gälla.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: mimart
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: de60dc5095ce4ab4d0219a388c445b08f544e1f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77159037"
---
# <a name="manage-certificates-for-federated-single-sign-on-in-azure-active-directory"></a>Hantera certifikat för federerade enstaka inloggning i Azure Active Directory

I den här artikeln täcker vi vanliga frågor och information relaterad till certifikat som Azure Active Directory (Azure AD) skapar för att upprätta federerade enkel inloggning (SSO) till din programvara som en tjänst (SaaS) program. Lägg till program från Azure AD-appgalleriet eller med hjälp av en programmall som inte är galleri. Konfigurera programmet med hjälp av det federerade SSO-alternativet.

Den här artikeln är endast relevant för appar som är konfigurerade för att använda Azure AD SSO via SAML-federation [(Security Assertion Markup Language).](https://wikipedia.org/wiki/Security_Assertion_Markup_Language)

## <a name="auto-generated-certificate-for-gallery-and-non-gallery-applications"></a>Automatiskt genererat certifikat för galleri- och icke-galleriprogram

När du lägger till ett nytt program från galleriet och konfigurerar en SAML-baserad inloggning (genom att välja Enkel > **inloggnings-SAML** från programöversiktssidan) genererar Azure AD ett certifikat för programmet som är giltigt i tre år. **Single sign-on** Om du vill hämta det aktiva certifikatet som en sÃ¤kert**sÃ¤kert fÃ¤rs (.cer)** går du tillbaka till den sidan **(SAML-baserad sign-on)** och väljer en hämtningslänk i rubriken **SAML Signering Certificate.** Du kan välja mellan råcertifikatet (binärt) certifikat eller Base64-certifikatet (bas 64-kodad text). För galleriprogram kan det här avsnittet också visa en länk för att hämta certifikatet som federationsmetadata XML (en **XML-fil),** beroende på programmets krav.

![Hämtningsalternativ för aktiva signeringscertifikat i SAML](./media/manage-certificates-for-federated-single-sign-on/active-certificate-download-options.png)

Du kan också hämta ett aktivt eller inaktivt certifikat genom att välja ikonen **Redigera redigeringsikon** för **SAML-signeringscertifikat** (en penna), som visar sidan **SAML-signeringscertifikat.** Välj ellipsen (**...**) bredvid det certifikat du vill hämta och välj sedan vilket certifikatformat du vill använda. Du har ytterligare möjlighet att ladda ner certifikatet i sekretessförbättrad e-post (PEM) format. Det här formatet är identiskt med Base64 men med filnamnstillägget **.pem,** som inte känns igen i Windows som ett certifikatformat.

![Hämtningsalternativ för hämtning av SAML-signeringscertifikat (aktiva och inaktiva)](./media/manage-certificates-for-federated-single-sign-on/all-certificate-download-options.png)

## <a name="customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate"></a>Anpassa utgångsdatumet för federationscertifikatet och rulla över det till ett nytt certifikat

Som standard konfigurerar Azure ett certifikat som ska upphöra att gälla efter tre år när det skapas automatiskt under SAML-konfiguration med enkel inloggning. Eftersom du inte kan ändra datumet för ett certifikat när du har sparat det måste du:

1. Skapa ett nytt certifikat med önskat datum.
1. Spara det nya certifikatet.
1. Hämta det nya certifikatet i rätt format.
1. Ladda upp det nya certifikatet till programmet.
1. Gör det nya certifikatet aktivt i Azure Active Directory-portalen.

Följande två avsnitt hjälper dig att utföra dessa steg.

### <a name="create-a-new-certificate"></a>Skapa ett nytt certifikat

Skapa och spara först nytt certifikat med ett annat utgångsdatum:

1. Logga in på [Azure Active Directory-portalen](https://aad.portal.azure.com/). Sidan **Azure Active Directory admin center** visas.
1. I den vänstra rutan väljer du **Företagsprogram**. En lista över företagsprogram i ditt konto visas.
1. Välj det berörda programmet. En översiktssida för programmet visas.
1. I den vänstra rutan på sidan för programöversikt väljer du **Enkel inloggning**.
1. Om sidan **Välj en enskild inloggningsmetod** visas väljer du **SAML**.
1. Leta reda på rubriken **SAML-signeringscertifikat** på sidan **Konfigurera enkel inloggning med SAML - Förhandsgranska** och välj ikonen **Redigera** (en penna). Sidan **SAML-signeringscertifikat** visas, som visar status (**Aktiv** eller **Inaktiv**), utgångsdatum och tumavtryck (en hash-sträng) för varje certifikat.
1. Välj **Nytt certifikat**. En ny rad visas under certifikatlistan, där utgångsdatumet är exakt tre år efter det aktuella datumet. (Ändringarna har inte sparats ännu, så du kan fortfarande ändra utgångsdatumet.)
1. På den nya certifikatraden hovrar du över kolumnen för utgångsdatum och väljer ikonen **Välj datum** (en kalender). En kalenderkontroll visas som visar dagarna i en månad för den nya radens aktuella utgångsdatum.
1. Använd kalenderkontrollen för att ange ett nytt datum. Du kan ange valfritt datum mellan det aktuella datumet och tre år efter det aktuella datumet.
1. Välj **Spara**. Det nya certifikatet visas nu med statusen **Inaktiv**, det utgångsdatum som du valde och ett tumavtryck.
1. Välj **X** för att återgå till sidan **Konfigurera enkel inloggning med SAML - Förhandsgranska.**

### <a name="upload-and-activate-a-certificate"></a>Ladda upp och aktivera ett certifikat

Hämta sedan det nya certifikatet i rätt format, ladda upp det till programmet och aktivera det i Azure Active Directory:

1. Visa programmets ytterligare konfigurationsinstruktioner för SAML-inloggning av antingen:

   - Välja **länken för konfigurationsguiden** för att visa i ett separat webbläsarfönster eller en separat flik, eller
   - Gå till **inställningsrubriken** och välja **Visa steg-för-steg-instruktioner** som ska visas i ett sidofält.

1. I instruktionerna bör du ange det kodningsformat som krävs för certifikatöverföringen.
1. Följ instruktionerna i avsnittet [Automatiskt genererat certifikat för galleri- och icke-galleriprogram](#auto-generated-certificate-for-gallery-and-non-gallery-applications) tidigare. I det här steget hämtas certifikatet i det kodningsformat som krävs för överföring av programmet.
1. När du vill gå över till det nya certifikatet går du tillbaka till sidan **SAML-signeringscertifikat** och i den nyligen sparade certifikatraden väljer du ellipsen (**...**) och väljer **Gör certifikatet aktivt**. Statusen för det nya certifikatet ändras till **Aktiv**och det tidigare aktiva certifikatet ändras till statusen **Inaktiv**.
1. Fortsätt att följa programmets KONFIGURATIONSINSTRUKTIONER för SAML som du visade tidigare, så att du kan ladda upp SAML-signeringscertifikatet i rätt kodningsformat.

## <a name="add-email-notification-addresses-for-certificate-expiration"></a>Lägga till e-postmeddelandeadresser för certifikatets förfallodatum

Azure AD skickar ett e-postmeddelande 60, 30 och 7 dagar innan SAML-certifikatet upphör att gälla. Du kan lägga till mer än en e-postadress för att få meddelanden. Om du vill ange den e-postadress som du vill att meddelandena ska skickas till:

1. Gå till **meddelandeadressrubriken** på sidan **SAML-signeringscertifikat.** Som standard använder den här rubriken endast e-postadressen till administratören som lade till programmet.
1. Under den slutliga e-postadressen skriver du den e-postadress som ska ta emot certifikatets utgångsdatum och trycker sedan på Retur.
1. Upprepa föregående steg för varje e-postadress som du vill lägga till.
1. För varje e-postadress som du vill ta bort väljer du ikonen **Ta bort** (en soptunna) bredvid e-postadressen.
1. Välj **Spara**.

Du kommer att få aadnotification@microsoft.commeddelandet e-post från . Om du vill undvika att e-postmeddelandet går till skräppostplatsen lägger du till det här e-postmeddelandet i dina kontakter.

## <a name="renew-a-certificate-that-will-soon-expire"></a>Förnya ett certifikat som snart upphör att gälla

Om ett certifikat håller på att upphöra att gälla kan du förnya det med hjälp av en procedur som inte resulterar i några betydande driftstopp för användarna. Så här förnyar du ett certifikat som löper ut:

1. Följ instruktionerna i avsnittet [Skapa ett nytt certifikat](#create-a-new-certificate) tidigare med ett datum som överlappar det befintliga certifikatet. Det datumet begränsar mängden driftstopp som orsakas av certifikatets förfallodatum.
1. Om programmet automatiskt kan rulla över ett certifikat anger du att det nya certifikatet ska vara aktivt genom att följa dessa steg:
   1. Gå tillbaka till sidan **SAML-signeringscertifikat.**
   1. I den nyligen sparade certifikatraden markerar du ellipsen (**...**) och väljer sedan **Gör certifikatet aktivt**.
   1. Hoppa över de kommande två stegen.

1. Om appen bara kan hantera ett certifikat i taget väljer du ett driftstoppsintervall för att utföra nästa steg. (Annars, om programmet inte automatiskt hämtar det nya certifikatet men kan hantera mer än ett signeringscertifikat, kan du utföra nästa steg när som helst.)
1. Innan det gamla certifikatet upphör att gälla följer du instruktionerna i [avsnittet Ladda upp och aktivera ett certifikatavsnitt](#upload-and-activate-a-certificate) tidigare.
1. Logga in på programmet för att kontrollera att certifikatet fungerar korrekt.

## <a name="related-articles"></a>Relaterade artiklar

- [Självstudier för att integrera SaaS-appar med Azure Active Directory](../saas-apps/tutorial-list.md)
- [Programhantering med Azure Active Directory](what-is-application-management.md)
- [Enkel inloggning till program i Azure Active Directory](what-is-single-sign-on.md)
- [Felsöka SAML-baserad enkel inloggning till program i Azure Active Directory](../azuread-dev/howto-v1-debug-saml-sso-issues.md)
