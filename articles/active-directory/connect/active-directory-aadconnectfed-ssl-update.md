---
title: "Azure AD Connect: Uppdatera SSL-certifikatet för en grupp i Active Directory Federation Services (AD FS) | Microsoft Docs"
description: "Det här dokumentet beskriver steg för att uppdatera SSL-certifikatet för en AD FS-servergrupp med hjälp av Azure AD Connect."
services: active-directory
keywords: "Azure ad connect, AD FS ssl-uppdatering, uppdatering för AD FS-certifikat, ändra AD FS-certifikat, nya AD FS-certifikat, adfs certifikat, uppdatera AD FS ssl-certifikat, uppdatera ssl-certifikat adfs, konfigurera AD FS ssl-certifikat, AD FS, ssl, certifikat, adfs-tjänsten certifikat för kommunikation, update-federation, konfigurera federation, aad-anslutning"
authors: anandyadavmsft
manager: femila
editor: billmath
ms.assetid: 7c781f61-848a-48ad-9863-eb29da78f53c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: anandy
ms.openlocfilehash: 87807a203d71b3abfe3e93132eb7d0b82b14b4ee
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="update-the-ssl-certificate-for-an-active-directory-federation-services-ad-fs-farm"></a>Uppdatera SSL-certifikatet för en grupp i Active Directory Federation Services (AD FS)

## <a name="overview"></a>Översikt
Den här artikeln beskriver hur du kan använda Azure AD Connect för att uppdatera SSL-certifikatet för en grupp i Active Directory Federation Services (AD FS). Du kan använda Azure AD Connect-verktyget för att enkelt uppdatera SSL-certifikatet för AD FS-servergruppen, även om användaren loggar in vald metod inte är AD FS.

Du kan utföra hela åtgärden för att uppdatera SSL-certifikat för AD FS-servergruppen över alla federation och Webbprogramproxy (WAP) servrar i tre enkla steg:

![Tre steg](./media/active-directory-aadconnectfed-ssl-update/threesteps.png)


>[!NOTE]
>Mer information om certifikat som används av AD FS finns [Förstå certifikat som används av AD FS](https://technet.microsoft.com/library/cc730660.aspx).

## <a name="prerequisites"></a>Krav

* **AD FS-servergrupp**: Kontrollera att AD FS-gruppen är baserade på Windows Server 2012 R2 eller senare.
* **Azure AD Connect**: Kontrollera att versionen av Azure AD Connect är 1.1.443.0 eller senare. Du använder aktiviteten **uppdatering AD FS SSL-certifikat**.

![Uppdatera SSL-aktivitet](./media/active-directory-aadconnectfed-ssl-update/updatessltask.png)

## <a name="step-1-provide-ad-fs-farm-information"></a>Steg 1: Ange information för AD FS-servergrupp

Azure AD Connect försöker att hämta information om AD FS-servergruppen automatiskt av:
1. Hämtar grupp-information från AD FS (Windows Server 2016 eller senare).
2. Refererar till informationen från tidigare körs som lagras lokalt med Azure AD Connect.

Du kan ändra listan över servrar som visas genom att lägga till eller ta bort servrar för att återspegla den aktuella konfigurationen av AD FS-servergruppen. Så snart serverinformation anges, visar Azure AD Connect anslutning och aktuell status för SSL-certifikat.

![AD FS-serverinformation](./media/active-directory-aadconnectfed-ssl-update/adfsserverinfo.png)

Om listan innehåller en server som inte längre är en del av AD FS-servergruppen, klickar du på **ta bort** att ta bort servern från listan över servrar i din AD FS-servergrupp.

![Offline-server i listan](./media/active-directory-aadconnectfed-ssl-update/offlineserverlist.png)

>[!NOTE]
> Ta bort en server från listan över servrar för en AD FS-servergrupp i Azure AD Connect är en lokal funktion och uppdateras informationen för AD FS-servergruppen som Azure AD Connect underhåller lokalt. Azure AD Connect ändra inte konfigurationen på AD FS för att avspegla ändringen.    

## <a name="step-2-provide-a-new-ssl-certificate"></a>Steg 2: Ange ett nytt SSL-certifikat

När du har bekräftat information om AD FS-servergruppen, begär Azure AD Connect nytt SSL-certifikat. Ange en lösenordsskyddad PFX-certifikat om du vill fortsätta med installationen.

![SSL-certifikat](./media/active-directory-aadconnectfed-ssl-update/certificate.png)

När du har angett certifikatet går Azure AD Connect igenom ett antal krav. Verifiera certifikat för att säkerställa att certifikatet är korrekt för AD FS-servergrupp:

-   Certifikatämnets namn/alternativa ämnesnamnet för certifikatet är densamma som federationstjänstens namn eller så är ett jokerteckencertifikat.
-   Certifikatet är giltigt för mer än 30 dagar.
-   Certifikatkedjan för certifikatet är giltigt.
-   Certifikatet är lösenordsskyddat.

## <a name="step-3-select-servers-for-the-update"></a>Steg 3: Välj servrar för uppdateringen

Välj de servrar som behöver uppdateras SSL-certifikatet i nästa steg. Servrar som är offline kan inte väljas för uppdateringen.

![Välj servrar för att uppdatera](./media/active-directory-aadconnectfed-ssl-update/selectservers.png)

När du har slutfört konfigurationen av visas Azure AD Connect som anger status för uppdateringen och ger ett alternativ för att verifiera AD FS-inloggning.

![Slutför konfiguration](./media/active-directory-aadconnectfed-ssl-update/configurecomplete.png)   

## <a name="faqs"></a>Vanliga frågor och svar

* **Vad bör vara ämnesnamnet för certifikatet för det nya AD FS SSL-certifikatet?**

    Azure AD Connect kontrollerar om Certifikatämnets namn/alternativa ämnesnamnet för certifikatet innehåller federationstjänstens namn. Om ditt namn för federationstjänsten är fs.contoso.com, måste Certifikatämnets namn/alternativa ämnesnamnet vara fs.contoso.com.  Jokerteckencertifikat också accepteras.

* **Varför uppmanas jag autentiseringsuppgifter igen på sidan WAP?**

    Om de autentiseringsuppgifter du anger för att ansluta till AD FS-servrarna inte har behörighet att hantera WAP-servrar, frågar Azure AD Connect för autentiseringsuppgifter som har administratörsbehörighet på WAP-servrar.

* **Servern visas som offline. Vad ska jag göra?**

    Azure AD Connect kan inte utföra någon åtgärd om servern är offline. Om servern är en del av AD FS-servergrupp, kontrollerar du anslutningen till servern. Tryck på uppdateringsikonen för att uppdatera statusen i guiden när du har löst problemet. Om servern var en del av servergruppen tidigare men nu finns inte längre klickar du på **ta bort** ta bort det från listan över servrar som Azure AD Connect upprätthåller. Tar bort servern från listan i Azure AD Connect ändra inte den AD FS-konfigurationen. Om du använder AD FS i Windows Server 2016 eller senare, server finns kvar i konfigurationsinställningarna och kommer att visas igen nästa gång körs uppgiften.

* **Kan jag uppdatera en delmängd av servrarna i servergruppen med det nya SSL-certifikatet?**

    Ja. Du kan alltid köra uppgiften **uppdatera SSL-certifikat** igen för att uppdatera de återstående servrarna. På den **väljer du servrar för SSL-certifikatet uppdatering** kan du sortera listan över servrar på **SSL utgångsdatum** att enkelt komma åt servrar som inte har uppdaterats ännu.

* **Jag bort servern i den tidigare körningen, men det fortfarande visas som offline och visas på sidan för AD FS-servrar. Varför är offline servern kvar även när du tagit bort det?**

    Tar bort servern från listan i Azure AD Connect inte ta bort den i AD FS-konfigurationen. Azure AD Connect refererar till AD FS (Windows Server 2016 eller senare) för information om gruppen. Om servern är kvar i AD FS-konfigurationen visas i listan.  

## <a name="next-steps"></a>Nästa steg

- [Azure AD Connect och federation](active-directory-aadconnectfed-whatis.md)
- [Active Directory Federation Services-hantering och anpassning med Azure AD Connect](active-directory-aadconnect-federation-management.md)
