---
title: Azure AD Connect – uppdatera SSL-certifikatet för AD FS-servergrupp | Microsoft Docs
description: Den här dokumentet beskriver steg för att uppdatera SSL-certifikatet för AD FS-servergrupp med hjälp av Azure AD Connect.
services: active-directory
manager: daveba
editor: billmath
ms.assetid: 7c781f61-848a-48ad-9863-eb29da78f53c
ms.service: active-directory  
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/09/2018
ms.subservice: hybrid
author: billmath
ms.custom: seohack1
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39ac0e9cf11a0c6c212c4beadb6635ad2b6b056d
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2019
ms.locfileid: "56734718"
---
# <a name="update-the-ssl-certificate-for-an-active-directory-federation-services-ad-fs-farm"></a>Uppdatera SSL-certifikatet för en grupp i Active Directory Federation Services (AD FS)

## <a name="overview"></a>Översikt
Den här artikeln beskrivs hur du kan använda Azure AD Connect för att uppdatera SSL-certifikatet för en Active Directory Federation Services (AD FS)-servergrupp. Du kan använda Azure AD Connect-verktyget för att enkelt uppdatera SSL-certifikatet för AD FS-servergruppen, även om användaren loggar in vald metod inte AD FS.

Du kan utföra hela åtgärden för att uppdatera SSL-certifikat för AD FS-servergrupp över alla federation och Webbprogramproxy (WAP)-servrar i tre enkla steg:

![Tre steg](./media/how-to-connect-fed-ssl-update/threesteps.png)


>[!NOTE]
>Läs mer om certifikat som används av AD FS i [Förstå certifikat som används av AD FS](https://technet.microsoft.com/library/cc730660.aspx).

## <a name="prerequisites"></a>Förutsättningar

* **AD FS-servergrupp**: Se till att AD FS-gruppen är Windows Server 2012 R2-baserad eller senare.
* **Azure AD Connect**: Kontrollera att versionen av Azure AD Connect är 1.1.553.0 eller högre. Du använder aktiviteten **uppdatera AD FS SSL-certifikatet**.

![Uppdatera SSL-aktivitet](./media/how-to-connect-fed-ssl-update/updatessltask.png)

## <a name="step-1-provide-ad-fs-farm-information"></a>Steg 1: Ange information för AD FS-servergrupp

Azure AD Connect försöker att hämta information om AD FS-servergrupp automatiskt av:
1. Fråga Servergruppsinformation från AD FS (Windows Server 2016 eller senare).
2. Refererar till informationen från tidigare körningar som lagras lokalt med Azure AD Connect.

Du kan ändra listan över servrar som visas genom att lägga till eller ta bort servrar för att återspegla den aktuella konfigurationen av AD FS-servergrupp. När du har angetts och visar Azure AD Connect anslutning och aktuell status för SSL-certifikat.

![AD FS-serverinformation](./media/how-to-connect-fed-ssl-update/adfsserverinfo.png)

Om listan innehåller en server som inte längre är en del av AD FS-servergrupp, klickar du på **ta bort** att ta bort servern från listan över servrar i AD FS-gruppen.

![Offline-server i listan](./media/how-to-connect-fed-ssl-update/offlineserverlist.png)

>[!NOTE]
> Ta bort en server från listan över servrar för en AD FS-servergrupp i Azure AD Connect är en lokal åtgärd och uppdaterar informationen för AD FS-servergrupp som Azure AD Connect underhåller lokalt. Azure AD Connect ändra inte konfigurationen på AD FS för att avspegla ändringen.    

## <a name="step-2-provide-a-new-ssl-certificate"></a>Steg 2: Ange ett nytt SSL-certifikat

När du har bekräftat information om AD FS-servergrupp-servrar, frågar Azure AD Connect för det nya SSL-certifikatet. Ange en lösenordsskyddad PFX-certifikat för att fortsätta med installationen.

![SSL-certifikat](./media/how-to-connect-fed-ssl-update/certificate.png)

När du har angett certifikatet går Azure AD Connect igenom ett antal krav. Verifiera certifikat för att kontrollera att certifikatet är korrekt för AD FS-servergrupp:

-   Det är ett jokerteckencertifikat ämne namn/alternativa ämnesnamnet för certifikatet är antingen samma som federationstjänstens namn.
-   Certifikatet är giltigt för mer än 30 dagar.
-   Certifikatkedjan för certifikatet är giltigt.
-   Certifikatet är lösenordsskyddat.

## <a name="step-3-select-servers-for-the-update"></a>Steg 3: Välj server för uppdateringen

Välj de servrar som behöver ha SSL-certifikatet uppdateras i nästa steg. Servrar som är offline kan inte väljas för uppdateringen.

![Välj servrar för att uppdatera](./media/how-to-connect-fed-ssl-update/selectservers.png)

När du har slutfört konfigurationen visar ett meddelande som anger status för uppdateringen och tillhandahåller ett alternativ för att verifiera AD FS-inloggning i Azure AD Connect.

![Konfigurationen är klar](./media/how-to-connect-fed-ssl-update/configurecomplete.png)   

## <a name="faqs"></a>Vanliga frågor och svar

* **Vad bör vara ämnesnamnet för certifikatet för det nya AD FS SSL-certifikatet?**

    Azure AD Connect kontrollerar om ämne namn/alternativa ämnesnamnet för certifikatet innehåller federationstjänstnamnet. Om ditt namn för federationstjänsten är fs.contoso.com, till exempel vara ämne namn/alternativa ämnesnamn fs.contoso.com.  Jokerteckencertifikat accepteras också.

* **Varför måste jag ange autentiseringsuppgifter igen på sidan för WAP-server?**

    Om de autentiseringsuppgifter du anger för att ansluta till AD FS-servrarna inte också har behörighet att hantera WAP-servrarna, frågar Azure AD Connect för autentiseringsuppgifter som har administrativ behörighet på WAP-servrarna.

* **Servern visas som offline. Vad ska jag göra?**

    Azure AD Connect kan inte utföra alla åtgärder om servern är offline. Om servern är en del av AD FS-servergrupp, kontrollerar du anslutningen till servern. När du har löst problemet, trycker du på Uppdatera-ikonen för att uppdatera status i guiden. Om servern var en del av servergruppen tidigare men nu finns inte längre, klickar du på **ta bort** att ta bort den från listan över servrar som Azure AD Connect underhåller. Tar bort servern från listan i Azure AD Connect ändra inte den AD FS-konfigurationen. Om du använder AD FS i Windows Server 2016 eller senare, server finns kvar i inställningarna för konfiguration och kommer att visas igen nästa gång körs uppgiften.

* **Kan jag uppdatera en delmängd av servrarna i servergruppen med det nya SSL-certifikatet?**

    Ja. Du kan köra uppgiften **uppdatera SSL-certifikat** igen för att uppdatera de återstående servrarna. På den **Välj server för SSL-certifikatsuppdatering** kan du sortera listan över servrar på **SSL-utgångsdatum** att enkelt få åtkomst till de servrar som ännu inte har uppdaterats.

* **Jag har tagit bort servern i den tidigare körningen men det är fortfarande visas som offline och listan på sidan för AD FS-servrar. Varför är offline servern kvar även efter att jag har tagit bort?**

    Tar bort servern från listan i Azure AD Connect inte ta bort den i AD FS-konfigurationen. Azure AD Connect refererar till AD FS (Windows Server 2016 eller högre) för information om gruppen. Om servern är kvar i AD FS-konfigurationen, visas den i listan.  

## <a name="next-steps"></a>Nästa steg

- [Azure AD Connect och federation](how-to-connect-fed-whatis.md)
- [Active Directory Federation Services-hantering och anpassning med Azure AD Connect](how-to-connect-fed-management.md)

