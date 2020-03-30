---
title: Azure AD Connect – Uppdatera TLS/SSL-certifikatet för en AD FS-servergrupp | Microsoft-dokument
description: I det här dokumentet beskrivs stegen för att uppdatera TLS/SSL-certifikatet för en AD FS-servergrupp med hjälp av Azure AD Connect.
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
ms.openlocfilehash: 8cc768162d98402fe52b52b2826a9dbf2840a581
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331745"
---
# <a name="update-the-tlsssl-certificate-for-an-active-directory-federation-services-ad-fs-farm"></a>Uppdatera TLS/SSL-certifikatet för en AD FS-servergrupp (Active Directory Federation Services)

## <a name="overview"></a>Översikt
I den här artikeln beskrivs hur du kan använda Azure AD Connect för att uppdatera TLS/SSL-certifikatet för en AD FS-servergrupp (Active Directory Federation Services). Du kan använda Azure AD Connect-verktyget för att enkelt uppdatera TLS/SSL-certifikatet för AD FS-servergruppen även om den valda användarens inloggningsmetod inte är AD FS.

Du kan utföra hela åtgärden med att uppdatera TLS/SSL-certifikatet för AD FS-servergruppen för alla WAP-servrar (Federation and Web Application Proxy) i tre enkla steg:

![Tre steg](./media/how-to-connect-fed-ssl-update/threesteps.png)


>[!NOTE]
>Mer information om certifikat som används av AD FS finns i [Förstå certifikat som används av AD FS](https://technet.microsoft.com/library/cc730660.aspx).

## <a name="prerequisites"></a>Krav

* **AD FS-servergrupp:** Kontrollera att AD FS-servergruppen är Windows Server 2012 R2-baserad eller senare.
* **Azure AD Connect**: Kontrollera att versionen av Azure AD Connect är 1.1.553.0 eller högre. Du ska använda AD **FS SSL-certifikatet för aktivitetsuppdatering**.

![Uppdatera TLS-uppgift](./media/how-to-connect-fed-ssl-update/updatessltask.png)

## <a name="step-1-provide-ad-fs-farm-information"></a>Steg 1: Ange information om AD FS-jordbruksföretag

Azure AD Connect försöker hämta information om AD FS-servergruppen automatiskt genom att:
1. Frågar servergruppens information från AD FS (Windows Server 2016 eller senare).
2. Refererar till informationen från tidigare körningar, som lagras lokalt med Azure AD Connect.

Du kan ändra listan över servrar som visas genom att lägga till eller ta bort servrarna för att återspegla den aktuella konfigurationen för AD FS-servergruppen. Så snart serverinformationen tillhandahålls visar Azure AD Connect anslutnings- och aktuell TLS/SSL-certifikatstatus.

![INFORMATION om AD FS-servern](./media/how-to-connect-fed-ssl-update/adfsserverinfo.png)

Om listan innehåller en server som inte längre ingår i AD FS-servergruppen klickar du på **Ta bort** om du vill ta bort servern från listan över servrar i AD FS-servergruppen.

![Offlineserver i listan](./media/how-to-connect-fed-ssl-update/offlineserverlist.png)

>[!NOTE]
> Att ta bort en server från listan över servrar för en AD FS-servergrupp i Azure AD Connect är en lokal åtgärd och uppdaterar informationen för AD FS-servergruppen som Azure AD Connect underhåller lokalt. Azure AD Connect ändrar inte konfigurationen på AD FS för att återspegla ändringen.    

## <a name="step-2-provide-a-new-tlsssl-certificate"></a>Steg 2: Ange ett nytt TLS/SSL-certifikat

När du har bekräftat informationen om AD FS-servergruppsservrar frågar Azure AD Connect efter det nya TLS/SSL-certifikatet. Ange ett lösenordsskyddat PFX-certifikat för att fortsätta installationen.

![TLS/SSL-certifikat](./media/how-to-connect-fed-ssl-update/certificate.png)

När du har anger certifikatet går Azure AD Connect igenom en serie förutsättningar. Kontrollera certifikatet för att säkerställa att certifikatet är korrekt för AD FS-jordbruksföretagen:

-   Certifikatets ämnesnamn/alternativa ämnesnamn är antingen samma som federationstjänstnamnet eller ett jokerteckencertifikat.
-   Certifikatet är giltigt i mer än 30 dagar.
-   Certifikatförtroendekedjan är giltig.
-   Certifikatet är lösenordsskyddat.

## <a name="step-3-select-servers-for-the-update"></a>Steg 3: Välj servrar för uppdateringen

I nästa steg väljer du de servrar som behöver uppdateraS till TLS/SSL-certifikatet. Servrar som är offline kan inte väljas för uppdateringen.

![Välj servrar som ska uppdateras](./media/how-to-connect-fed-ssl-update/selectservers.png)

När du har slutfört konfigurationen visar Azure AD Connect meddelandet som anger uppdateringens status och ger ett alternativ för att verifiera AD FS-inloggningen.

![Konfigurationen är klar](./media/how-to-connect-fed-ssl-update/configurecomplete.png)   

## <a name="faqs"></a>Vanliga frågor och svar

* **Vilket ämnesnamn bör certifikatet för det nya AD FS TLS/SSL-certifikatet?**

    Azure AD Connect kontrollerar om certifikatets ämnesnamn/alternativa ämnesnamn innehåller federationstjänstnamnet. Om federationstjänstnamnet till exempel är fs.contoso.com måste ämnesnamnet/det alternativa ämnesnamnet vara fs.contoso.com.  Jokerteckencertifikat accepteras också.

* **Varför bad jag om autentiseringsuppgifter igen på WAP-serversidan?**

    Om de autentiseringsuppgifter du anger för anslutning till AD FS-servrar inte också har behörighet att hantera WAP-servrarna, frågar Azure AD Connect efter autentiseringsuppgifter som har administratörsbehörighet på WAP-servrarna.

* **Servern visas som offline. Vad ska jag göra?**

    Azure AD Connect kan inte utföra någon åtgärd om servern är offline. Om servern ingår i AD FS-servergruppen kontrollerar du anslutningen till servern. När du har löst problemet trycker du på uppdateringsikonen för att uppdatera statusen i guiden. Om servern var en del av servergruppen tidigare men nu inte längre finns klickar du på **Ta bort** för att ta bort den från listan över servrar som Azure AD Connect underhåller. Om du tar bort servern från listan i Azure AD Connect ändras inte själva AD FS-konfigurationen. Om du använder AD FS i Windows Server 2016 eller senare finns servern kvar i konfigurationsinställningarna och visas igen nästa gång aktiviteten körs.

* **Kan jag uppdatera en delmängd av mina servergruppsservrar med det nya TLS/SSL-certifikatet?**

    Ja. Du kan alltid köra **SSL-certifikatet för aktivitetsuppdatering** igen för att uppdatera de återstående servrarna. På sidan **Välj servrar för SSL-certifikatuppdatering** kan du sortera listan över servrar på **SSL-utgångsdatum** för att enkelt komma åt servrarna som inte har uppdaterats ännu.

* **Jag tog bort servern i föregående körning, men det är fortfarande visas som offline och anges på AD FS-servrar sidan. Varför är offline-servern kvar även efter att jag tagit bort den?**

    Om du tar bort servern från listan i Azure AD Connect tas den inte bort i AD FS-konfigurationen. Azure AD Connect refererar till AD FS (Windows Server 2016 eller senare) för all information om servergruppen. Om servern fortfarande finns i AD FS-konfigurationen visas den i listan igen.  

## <a name="next-steps"></a>Nästa steg

- [Azure AD Connect och federation](how-to-connect-fed-whatis.md)
- [Hantering och anpassning av Active Directory Federation Services med Azure AD Connect](how-to-connect-fed-management.md)

