---
title: Hantera federationscertifikat i Azure AD | Microsoft Docs
description: Lär dig hur du anpassar ett sista giltighetsdatum för federationscertifikat och förnya certifikat som snart upphör att gälla.
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: barbkess
ms.reviewer: jeedes
ms.openlocfilehash: 48f001ab0634451b1e339b346e03f5d15cdbf9c6
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54463855"
---
# <a name="manage-certificates-for-federated-single-sign-on-in-azure-active-directory"></a>Hantera certifikat för federerad enkel inloggning i Azure Active Directory
Den här artikeln innehåller vanliga frågor och information som rör de certifikat som skapas i Azure Active Directory (Azure AD) för att upprätta federerad enkel inloggning (SSO) till dina SaaS-program. Lägg till program från Azure AD-appgalleri eller genom att använda en mall för icke-galleriprogram. Konfigurera programmet med hjälp av alternativet federerad enkel inloggning.

Den här artikeln gäller enbart för appar som är konfigurerade för att använda enkel inloggning för Azure AD via SAML-federation, som visas i följande exempel:

![Azure AD enkel inloggning](./media/manage-certificates-for-federated-single-sign-on/saml_sso.PNG)

## <a name="auto-generated-certificate-for-gallery-and-non-gallery-applications"></a>Automatiskt genererade certifikatet för galleriet och inte är ett galleriprogram
När du lägger till ett nytt program från galleriet och konfigurera en SAML-baserad inloggning, genereras ett certifikat för programmet som är giltig i tre år i Azure AD. Du kan ladda ned det här certifikatet bland de **SAML-signeringscertifikat** avsnittet. För galleriprogram, kan det här avsnittet visar ett alternativ för att hämta certifikat eller metadata, beroende på krav för programmet.

![Azure AD enkel inloggning](./media/manage-certificates-for-federated-single-sign-on/saml_certificate_download.png)

## <a name="customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate"></a>Anpassa ett sista giltighetsdatum för federation-certifikat och förnyar till ett nytt certifikat
Som standard konfigurerade certifikat som upphör att gälla efter tre år. Du kan välja ett annat utgångsdatum för certifikatet genom att utföra följande steg.
Skärmbilderna använder Salesforce för exemplet, men de här stegen kan kopplas till alla federerade SaaS-appar.

1. I den [Azure-portalen](https://aad.portal.azure.com), klickar du på **företagsprogram** i den vänstra rutan och klicka sedan på **nytt program** på den **översikt** sidan:

   ![Öppna guiden för konfiguration av enkel inloggning](./media/manage-certificates-for-federated-single-sign-on/enterprise_application_new_application.png)

2. Sök efter galleri-programmet och välj sedan det program som du vill lägga till. Om du inte hittar programmet som krävs kan du lägga till programmet genom att använda den **icke-galleriprogram** alternativet. Den här funktionen är endast tillgänglig i Azure AD Premium (P1 och P2) SKU: N.

    ![Azure AD enkel inloggning](./media/manage-certificates-for-federated-single-sign-on/add_gallery_application.png)

3. Klicka på den **enkel inloggning** länken i den vänstra rutan och ändra **läge för enkel inloggning** till **SAML-baserad inloggning**. Det här steget genererar ett certifikat för tre år för ditt program.

4. Om du vill skapa ett nytt certifikat klickar du på den **Skapa nytt certifikat** länken i den **SAML-signeringscertifikat** avsnittet.

    ![Generera ett nytt certifikat](./media/manage-certificates-for-federated-single-sign-on/create_new_certficate.png)

5. Den **skapa ett nytt certifikat** länken öppnar kalenderkontrollen. Du kan ange valfritt datum och tid upp till tre år från dagens datum. Valda datum och tid är nytt utgångsdatum och tid för det nya certifikatet. Klicka på **Spara**.

    ![Ladda ned och sedan ladda upp certifikatet](./media/manage-certificates-for-federated-single-sign-on/certifcate_date_selection.PNG)

6. Det nya certifikatet är nu tillgänglig för nedladdning. Klicka på den **certifikat** länken för att hämta den. Certifikatet är nu inte aktiv. Om du vill ska rullas över till det här certifikatet kan du välja den **gör nytt certifikat aktivt** markerar du kryssrutan och klicka på **spara**. Från den tidpunkten börjar Azure AD med hjälp av det nya certifikatet för signering av svaret.

7.  Information om hur du överför certifikatet till din visst SaaS-program, klickar du på den **visa självstudierna för konfiguration av programmet** länk.

## <a name="certificate-expiration-notification-email"></a>E-postmeddelande för certifikatet upphör att gälla

Azure AD skickar ett e-post-meddelande 60, 30 och 7 dagar innan SAML-certifikatet upphör att gälla. Ange den e-postadressen att skicka meddelandet:

- Gå till fältet e-postmeddelande på Azure Active Directory enkel inloggning på programsidan.
- Ange den e-postadress som ska ta emot e-postmeddelande certifikatet upphör att gälla. Som standard använder det här fältet e-postadressen till den administratör som har lagts till programmet.

Du får e-postmeddelandet från aadnotification@microsoft.com. Om du vill undvika e-postmeddelandet kommer att din skräppost plats, måste du lägga till den här e-post till dina kontakter. 

## <a name="renew-a-certificate-that-will-soon-expire"></a>Förnya ett certifikat som snart upphör att gälla
Följande steg för förnyelse resulterar i utan betydande driftavbrott för dina användare. Skärmbilderna i det här avsnittet funktionen Salesforce som ett exempel, men de här stegen kan använda för valfri federerade SaaS-app.

1. På den **Azure Active Directory** programmet **enkel inloggning** sidan, skapa ett nytt certifikat för ditt program. Du kan göra detta genom att klicka på den **Skapa nytt certifikat** länken i den **SAML-signeringscertifikat** avsnittet.

    ![Generera ett nytt certifikat](./media/manage-certificates-for-federated-single-sign-on/create_new_certficate.png)

2. Välj önskad datum och tid för det nya certifikatet och klicka på **spara**. Att välja ett datum som överlappar med det befintliga certifikatet säkerställer att driftavbrott på grund av förfallodatum för certifikat är begränsad. 

3. Om appen kan automatiskt förnyar ett certifikat, anger du det nya certifikatet till aktiv.  Logga in på appen för att kontrollera att den fungerar.

4. Om appen inte automatiskt upphämtning nytt certifikat, men kan hantera mer än ett signeringscertifikat certifikat innan gamla upphör att gälla, ladda upp den nya servern till appen, och sedan gå tillbaka till portalen och blir det aktiva certifikatet. 

5. Om appen kan endast hantera ett certifikat i taget, Välj en driftstörningen, ladda ned det nya certifikatet, överföra den till programmet, gå tillbaka till Azure-portalen och ange det nya certifikatet som aktiv. 
   
6. Om du vill aktivera det nya certifikatet i Azure AD, Välj den **gör nytt certifikat aktivt** markerar du kryssrutan och klicka på den **spara** längst upp på sidan. Detta samlar via det nya certifikatet på Azure AD-sida. Status för certifikatet ändras från **New** till **Active**. Från den tidpunkten börjar Azure AD med hjälp av det nya certifikatet för signering av svaret. 
   
    ![Generera ett nytt certifikat](./media/manage-certificates-for-federated-single-sign-on/new_certificate_download.png)

## <a name="related-articles"></a>Relaterade artiklar
* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](../saas-apps/tutorial-list.md)
* [Programhantering i Azure Active Directory](what-is-application-management.md)
* [Programåtkomst och enkel inloggning med Azure Active Directory](what-is-single-sign-on.md)
* [Felsöka SAML-baserad enkel inloggning](../develop/howto-v1-debug-saml-sso-issues.md)
