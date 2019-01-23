---
title: Anpassade domäner i Azure AD Application Proxy | Microsoft Docs
description: 'Hantera anpassade domäner i Azure AD-programproxy så att URL: en för appen är detsamma oavsett var dina användare åtkomst till den.'
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/31/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 3d058ac4ce62718cebc20c349d52ebde57c92904
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54476793"
---
# <a name="working-with-custom-domains-in-azure-ad-application-proxy"></a>Arbeta med anpassade domäner i Azure AD Application Proxy

När du publicerar ett program via Azure Active Directory Application Proxy kan skapa du en extern URL för användarna att vända sig när de arbetar via fjärranslutning. Den här URL: en hämtar standarddomän *yourtenant.msappproxy.net*. Till exempel om du har publicerat en app med namnet utgifter och din klient heter Contoso och den externa URL: en är https://expenses-contoso.msappproxy.net. Om du vill använda ditt eget domännamn, konfigurera ett anpassat domännamn för ditt program. 

Vi rekommenderar att du konfigurerar anpassade domäner för dina program när det är möjligt. Några av fördelarna med anpassade domäner är:

- Dina användare får åtkomst till program med samma URL, oavsett om de arbetar i eller utanför nätverket.
- Om alla dina program har samma interna och externa URL: er, och sedan länkar i ett program till en annan fortsätter att fungera även utanför företagsnätverket. 
- Du kontrollerar din företagsanpassning och skapa de URL: er som du vill. 


## <a name="configure-a-custom-domain"></a>Konfigurera ett anpassat domännamn

### <a name="prerequisites"></a>Förutsättningar

Innan du konfigurerar en anpassad domän kan du kontrollera att du har förberett följande krav: 
- En [verifierad domän som har lagts till i Azure Active Directory](../fundamentals/add-custom-domain.md).
- Ett anpassat certifikat för domänen, i form av en PFX-fil. 
- En lokal app [publicerats via programproxy](application-proxy-add-on-premises-application.md).

### <a name="configure-your-custom-domain"></a>Konfigurera din anpassade domän

När du har dessa tre krav som är redo, följer du dessa steg för att konfigurera din anpassade domän:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Gå till **Azure Active Directory** > **företagsprogram** > **alla program** och välj den app som du vill hantera.
3. Välj **programproxy**. 
4. Använd listrutan för att välja din anpassade domän i fältet externa URL: en. Har om du inte ser din domän i listan, sedan den ännu inte verifierats ännu. 
5. Välj **spara**
5. Den **certifikat** fält som har inaktiverats aktiveras. Välj det här fältet. 

   ![Klicka om du vill ladda upp ett certifikat](./media/application-proxy-configure-custom-domain/certificate.png)

   Om du redan har laddat upp ett certifikat för den här domänen visas certifikatfältet certifikatinformationen. 

6. Ladda upp PFX-certifikatet och ange lösenordet för certifikatet. 
7. Välj **spara** att spara dina ändringar. 
8. Lägg till en [DNS-post](../../dns/dns-operations-recordsets-portal.md) som omdirigerar den nya externa URL: en till domänen msappproxy.net. 

>[!TIP] 
>Du behöver bara ladda upp ett certifikat per egen domän. När du överför ett certifikat, kan du välja den anpassade domänen när du publicerar en ny app och inte behöver göra ytterligare konfigurationsinställningar förutom DNS-posten. 

## <a name="manage-certificates"></a>Hantera certifikat

### <a name="certificate-format"></a>Certifikatformatet
Det finns ingen begränsning för certifikat signatur metoder. Elliptic Curve Cryptography (ECC), alternativt namn på CERTIFIKATMOTTAGARE, och andra vanliga typer av certifikat stöds. 

Du kan använda ett jokerteckencertifikat så länge jokertecknet matchar den önskade externa URL: en. 

### <a name="changing-the-domain"></a>Ändra domänen
Alla verifierade domäner visas i listrutan externa URL: en för ditt program. Om du vill ändra domänen du bara uppdatera fältet för programmet. Om domänen som du vill inte finns i listan [Lägg till den som en verifierad domän](../fundamentals/add-custom-domain.md). Om du väljer en domän som inte har en tillhörande certifikat än Följ steg 5 – 7 för att lägga till certifikatet. Kontrollera sedan att du uppdaterar DNS-posten att omdirigera från den nya externa URL: en. 

### <a name="certificate-management"></a>Certifikathantering
Du kan använda samma certifikat för flera program, såvida inte program som delar en extern värd. 

Du får en varning när ett certifikat upphör att gälla om att överföra ett annat certifikat via portalen. Certifikatet har återkallats dina användare visas en säkerhetsvarning vid åtkomst till programmet. Vi utföra inte återkallelsekontroller för certifikat.  Om du vill uppdatera certifikatet för ett visst program, navigerar till programmet och följ steg 5 – 7 för att konfigurera anpassade domäner på publicerade program för att ladda upp ett nytt certifikat. Om det gamla certifikatet inte används av andra program, tas den bort automatiskt. 

Alla certifikathantering är för närvarande via enskilda programsidor så du behöver hantera certifikat i samband med de relevanta program. 

## <a name="next-steps"></a>Nästa steg
* [Aktivera enkel inloggning](application-proxy-configure-single-sign-on-with-kcd.md) till dina publicerade appar med Azure AD-autentisering.
* [Aktivera villkorlig åtkomst](application-proxy-integrate-with-sharepoint-server.md) till dina publicerade appar.
* [Lägga till ett anpassat domännamn i Azure AD](../fundamentals/add-custom-domain.md)


