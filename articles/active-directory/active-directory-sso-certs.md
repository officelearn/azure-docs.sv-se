---
title: Hantera federationscertifikat i Azure AD | Microsoft Docs
description: "Lär dig hur du anpassar ett sista giltighetsdatum för federationscertifikat och hur du förnyar certifikat som upphör snart att gälla."
services: active-directory
documentationcenter: 
author: jeevansd
manager: mtillman
editor: 
ms.assetid: f516f7f0-b25a-4901-8247-f5964666ce23
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/09/2017
ms.author: jeedes
ms.openlocfilehash: 2247b668584c7bb501043917f98e77c7c5cecfdc
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="manage-certificates-for-federated-single-sign-on-in-azure-active-directory"></a>Hantera certifikat för federerad enkel inloggning i Azure Active Directory
Den här artikeln innehåller vanliga frågor och information som rör certifikat som skapas i Azure Active Directory (Azure AD) för att upprätta federerad enkel inloggning (SSO) till SaaS-program. Lägg till program från appgalleriet för Azure AD eller genom att använda en mall för icke-galleriet program. Konfigurera programmet med hjälp av alternativet federerad enkel inloggning.

Den här artikeln gäller enbart för appar som är konfigurerade för att använda Azure AD enkel inloggning via SAML federation som visas i följande exempel:

![Azure AD-Single Sign-On](./media/active-directory-sso-certs/saml_sso.PNG)

## <a name="auto-generated-certificate-for-gallery-and-non-gallery-applications"></a>Automatiskt genererade certifikatet för galleriet och icke-galleriet program
När du lägger till ett nytt program från galleriet och konfigurerar en SAML-baserade på genererar Azure AD ett certifikat för det program som är giltig i tre år. Du kan hämta certifikatet från den **SAML-signeringscertifikat** avsnitt. Galleriet program, kan det här avsnittet visar ett alternativ för att hämta certifikat eller metadata, beroende på krav för programmet.

![Azure AD enkel inloggning](./media/active-directory-sso-certs/saml_certificate_download.png)

## <a name="customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate"></a>Anpassa ett sista giltighetsdatum för federation-certifikat och distribuera det till ett nytt certifikat
Certifikat är som standard efter tre år. Du kan välja ett annat utgångsdatum för ditt certifikat genom att utföra följande steg.
Skärmbilderna använder Salesforce för enkelhetens exempel, men de här stegen kan kopplas till alla externa SaaS-appar.

1. I den [Azure-portalen](https://aad.portal.azure.com), klickar du på **företagsprogram** i den vänstra rutan och klicka sedan på **nytt program** på den **översikt** sidan:

   ![Öppna guiden för konfiguration av SSO](./media/active-directory-sso-certs/enterprise_application_new_application.png)

2. Sök efter programmet galleriet och välj sedan det program som du vill lägga till. Om du inte hittar programmet kan du lägga till programmet med hjälp av den **icke-galleriet programmet** alternativet. Den här funktionen är endast tillgänglig i Azure AD Premium (P1 och P2) SKU: N.

    ![Azure AD enkel inloggning](./media/active-directory-sso-certs/add_gallery_application.png)

3. Klicka på den **enkel inloggning** länkar i den vänstra rutan och ändrar **läget för enkel inloggning** till **SAML-baserade inloggning**. Det här steget genererar ett tre års certifikat för programmet.

4. Klicka för att skapa ett nytt certifikat i **Skapa nytt certifikat** länken i den **SAML-signeringscertifikat** avsnitt.

    ![Generera ett nytt certifikat](./media/active-directory-sso-certs/create_new_certficate.png)

5. Den **skapa ett nytt certifikat** länken öppnar kalender. Du kan ange ett datum och tid för upp till tre år från dagens datum. Valda datum och tid är den nya datum och tid för det nya certifikatet. Klicka på **Spara**.

    ![Hämta och sedan ladda upp certifikatet](./media/active-directory-sso-certs/certifcate_date_selection.PNG)

6. Det nya certifikatet är nu tillgänglig för nedladdning. Klicka på den **certifikat** länk för att hämta den. Certifikatet är nu inte aktiv. När du vill återställa till det här certifikatet kan välja den **aktivera nya certifikatet** kryssrutan och klicka på **spara**. Från den tidpunkten startar Azure AD med det nya certifikatet för signering svaret.

7.  Information om hur du överför certifikatet till visst SaaS-program, klickar du på den **visa configuration självstudien** länk.

## <a name="renew-a-certificate-that-will-soon-expire"></a>Förnya ett certifikat som upphör snart att gälla
Följande förnyelse ska leda till ingen betydande driftstopp för dina användare. Skärmbilder i avsnittet funktionen Salesforce som ett exempel, men de här stegen kan tillämpas på alla externa SaaS-appar.

1. På den **Azure Active Directory** programmet **enkel inloggning** kan du skapa ett nytt certifikat för ditt program. Du kan göra detta genom att klicka på den **Skapa nytt certifikat** länken i den **SAML-signeringscertifikat** avsnitt.

    ![Generera ett nytt certifikat](./media/active-directory-sso-certs/create_new_certficate.png)

2. Välj önskade datum och tid för det nya certifikatet och klicka på **spara**.

3. Hämta certifikatet i den **SAML signeringscertifikat** alternativet. Ladda upp det nya certifikatet till skärm för enkel inloggning konfiguration av SaaS-program. Information om hur du gör detta för visst SaaS-program, klickar du på den **visa configuration självstudien** länk.
   
4. Om du vill aktivera det nya certifikatet i Azure AD, Välj den **aktivera nya certifikatet** kryssrutan och klicka på den **spara** längst upp på sidan. Detta samlar över det nya certifikatet på Azure AD-sida. Certifikatets status ändras från **ny** till **Active**. Från den tidpunkten startar Azure AD med det nya certifikatet för signering svaret. 
   
    ![Generera ett nytt certifikat](./media/active-directory-sso-certs/new_certificate_download.png)

## <a name="related-articles"></a>Relaterade artiklar
* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Artikelindex för programhantering i Azure Active Directory](active-directory-apps-index.md)
* [Programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md)
* [Felsökning av SAML-baserade enkel inloggning](active-directory-saml-debugging.md)
