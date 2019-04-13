---
title: Hantera federationscertifikat i Azure AD | Microsoft Docs
description: Lär dig hur du anpassar ett sista giltighetsdatum för federationscertifikat och förnya certifikat som snart upphör att gälla.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: celested
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64c2d14a2aa6fc6b53260912b5bead2bd7c01e8d
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2019
ms.locfileid: "59547852"
---
# <a name="manage-certificates-for-federated-single-sign-on-in-azure-active-directory"></a>Hantera certifikat för federerad enkel inloggning i Azure Active Directory

I den här artikeln beskriver vi vanliga frågor och information som rör certifikat som Azure Active Directory (Azure AD) skapar för att upprätta federerad enkel inloggning (SSO) med din programvara som en tjänst (SaaS)-program. Lägg till program från Azure AD-appgalleri eller genom att använda en mall för icke-galleriprogram. Konfigurera programmet med hjälp av alternativet federerad enkel inloggning.

Den här artikeln gäller enbart för appar som är konfigurerade för att använda Azure AD SSO via [Security Assertion Markup Language](https://wikipedia.org/wiki/Security_Assertion_Markup_Language) (SAML) federation.

## <a name="auto-generated-certificate-for-gallery-and-non-gallery-applications"></a>Automatiskt genererade certifikatet för galleriet och inte är ett galleriprogram

När du lägger till ett nytt program från galleriet och konfigurera en SAML-baserad inloggning (genom att välja **enkel inloggning** > **SAML** från översiktssidan program), genererar Azure AD en certifikat för det program som är giltig i tre år. Att hämta det aktiva certifikatet som säkerhetscertifikat (**.cer**) filen, gå tillbaka till sidan (**SAML-baserad inloggning**) och välj en nedladdningslänk i den **SAML-signeringscertifikat** rubrik. Du kan välja mellan raw (binära) certifikatet eller Base64-(base 64-kodad text)-certifikat. För galleriprogram, i det här avsnittet kan visar också en länk för att hämta certifikatet som federationsmetadata XML (en **.xml** fil), beroende på krav för programmet.

![SAML active signeringscertifikat certifikatalternativ för nedladdning](./media/manage-certificates-for-federated-single-sign-on/active-certificate-download-options.png)

Du kan också hämta ett aktiva eller inaktiva certifikat genom att välja den **SAML-signeringscertifikat** rubrikens **redigera** ikon (en penna), som visar den **SAML-signeringscertifikat** sidan. Välj ellipsen (**...** ) bredvid det certifikat som du vill ladda ned och väljer sedan vilken certifikatformatet du vill. Du har ytterligare alternativet att hämta certifikatet i utökad sekretess e-(PEM)-postformat. Det här formatet är identisk med Base64 men med en **.pem** filnamnstillägget, som inte känns igen i Windows som en certifikat-format.

![SAML-signeringscertifikat certifikat hämtningsalternativ (aktiv och inaktiv)](./media/manage-certificates-for-federated-single-sign-on/all-certificate-download-options.png)

## <a name="customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate"></a>Anpassa ett sista giltighetsdatum för federation-certifikat och förnyar till ett nytt certifikat

Som standard konfigurerar Azure ett certifikat upphör att gälla efter tre år när den skapas automatiskt under SAML enkel inloggning för konfigurationen. Eftersom du inte kan ändra datumet då ett certifikat när du har sparat den, behöver du:

1. Skapa ett nytt certifikat med önskat.
2. Spara det nya certifikatet.
3. Ladda ned det nya certifikatet i rätt format.
4. Ladda upp det nya certifikatet till programmet.
5. Aktivera det nya certifikatet i Azure Active Directory-portalen.

Följande två avsnitt hjälpa dig att utföra dessa steg.

### <a name="create-a-new-certificate"></a>Skapa ett nytt certifikat

Först måste du skapa och spara nytt certifikat med ett annat utgångsdatum:

1. Logga in på den [Azure Active Directory-portalen](https://aad.portal.azure.com/). Den **Azure Active Directory Administrationscenter** visas.

2. I den vänstra rutan väljer du **Företagsprogram**. En lista över enterprise-program i ditt konto visas.

3. Välj ett program. En översiktssida för programmet visas.

4. I den vänstra rutan på översiktssidan för program, Välj **enkel inloggning**.

5. Om den **väljer du en metod för enkel inloggning** visas, väljer du **SAML**.

6. I den **ange in enkel inloggning med SAML - förhandsversion** sidan, hitta den **SAML-signeringscertifikat** rubrik- och väljer den **redigera** ikon (en penna). Den **SAML-signeringscertifikat** visas, som visar status (**Active** eller **inaktiv**), utgångsdatum och varje certifikatets tumavtryck (en hash-sträng).

7. Välj **nytt certifikat**. En ny rad visas under listan över certifikat där förfallodatumet som standard exakt tre år efter det aktuella datumet. (Ändringarna inte har sparats ännu, så du kan fortfarande ändra utgångsdatumet.)

8. Hovra över datumkolumnen upphör att gälla i den nya certifikatraden och välj den **Välj datum** ikon (en kalender). En kalenderkontroll visas dagar i en månad med den nya raden aktuella förfallodatum.

9. Använd kalenderkontrollen för att ange ett nytt datum. Du kan ange valfritt datum från aktuellt datum och tre år efter det aktuella datumet.

10. Välj **Spara**. Det nya certifikatet visas nu med statusen **inaktiv**, utgångsdatumet som du valt och ett tumavtryck.

11. Välj den **X** att återgå till den **ange in enkel inloggning med SAML - förhandsversion** sidan.

### <a name="upload-and-activate-a-certificate"></a>Ladda upp och aktivera ett certifikat

Ladda ned det nya certifikatet i rätt format, överför den till programmet och göra den aktiv i Azure Active Directory:

1. Visa instruktioner för programmets ytterligare SAML inloggnings-konfiguration genom att antingen:
   - att välja den **konfigurationsguide** länken för att visa i en separat webbläsarfönster eller flikar, eller
   - Gå till den **konfigurera** rubrik och välja **visa stegvisa instruktioner** att visa i en sidopanelen.

2. Observera Kodningsformatet som krävs för att ladda upp certifikatet i anvisningarna.

3. Följ instruktionerna i den [automatiskt genererade certifikatet för galleriet och inte är ett galleriprogram](#auto-generated-certificate-for-gallery-and-non-gallery-applications) ovan. Det här steget hämtar certifikatet i Kodningsformatet som krävs för överföring av programmet.

4. När du vill förnya till det nya certifikatet går du tillbaka till den **SAML-signeringscertifikat** och välj de tre punkterna i certifikatraden sparade (**...** ) och välj **aktivera certifikatet**. Status för det nya certifikatet ändras till **Active**, och det tidigare aktiva certifikatet ändras till statusen **inaktiv**.

5. Vill du fortsätta följande programmets SAML inloggnings-instruktioner för konfiguration som du visade tidigare, så att du kan ladda upp SAML-signering av certifikat i rätt kodningsformat.

## <a name="add-email-notification-addresses-for-certificate-expiration"></a>Lägg till meddelande e-postadresser för förfallodatum för certifikat

Azure AD skickar ett e-post-meddelande 60, 30 och 7 dagar innan SAML-certifikatet upphör att gälla. Du kan lägga till fler än en e-postadress för att ta emot meddelanden. Om du vill ange de e-postadresser vill du att meddelanden skickas till:

1. I den **SAML-signeringscertifikat** går du till den **meddelande e-postadresser** rubrik. Som standard använder den här rubriken endast e-postadressen till den administratör som har lagts till programmet.

2. Under den sista e-postadressen, skriver du den e-postadress som ska ta emot certifikatets utgångsdatum och tryck sedan på RETUR.

3. Upprepa föregående steg för varje e-postadress som du vill lägga till.

4. För varje e-postadress som du vill ta bort, väljer du den **ta bort** (en Papperskorgen) intill den e-postadressen.

5. Välj **Spara**.

Du får e-postmeddelandet från aadnotification@microsoft.com. Undvik e-postmeddelandet kommer att din skräppost plats genom att lägga till e-postmeddelandet till dina kontakter.

## <a name="renew-a-certificate-that-will-soon-expire"></a>Förnya ett certifikat som snart upphör att gälla

Om det är ett certifikat upphör snart att gälla, kan du förnya den med hjälp av en procedur som resulterar i utan betydande driftavbrott för dina användare. Förnya ett certifikat som går ut:

1. Följ instruktionerna i den [skapa ett nytt certifikat](#create-a-new-certificate) avsnittet ovan, med ett datum som överlappar med det befintliga certifikatet. Detta datum begränsar stilleståndstiden som orsakats av förfallodatum för certifikat.

2. Om programmet kan automatiskt förnyar ett certifikat, anger du det nya certifikatet till aktiv genom att följa dessa steg:
   1. Gå tillbaka till den **SAML-signeringscertifikat** sidan.
   2. Välj ellipsen i certifikatraden sparade (**...** ) och välj sedan **aktivera certifikatet**.
   3. Hoppa över de kommande två stegen.

3. Om appen kan endast hantera ett certifikat i taget, Välj ett driftstopp intervall att utföra nästa steg. (Annars om programmet automatiskt hämtar inte det nya certifikatet men kan hantera flera signeringscertifikat, du kan utföra nästa steg när som helst.)

4. Innan det gamla certifikatet upphör att gälla, följer du anvisningarna i den [ladda upp och aktivera ett certifikat](#upload-and-activate-a-certificate) ovan.

5. Logga in på programmet för att se till att certifikatet fungerar korrekt.

## <a name="related-articles"></a>Relaterade artiklar

* [Självstudier för att integrera SaaS-appar med Azure Active Directory](../saas-apps/tutorial-list.md)
* [Hantering av program med Azure Active Directory](what-is-application-management.md)
* [Enkel inloggning till program i Azure Active Directory](what-is-single-sign-on.md)
* [Felsöka SAML-baserad enkel inloggning till program i Azure Active Directory](../develop/howto-v1-debug-saml-sso-issues.md)
