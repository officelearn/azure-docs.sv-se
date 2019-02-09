---
title: Anspråksmedvetna appar – Azure AD App Proxy | Microsoft Docs
description: Så här publicerar du en lokal ASP.NET-program som har stöd för AD FS anspråk för säker fjärråtkomst av användarna.
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
ms.date: 11/08/2018
ms.author: celested
ms.reviewer: japere
ms.openlocfilehash: 3cb1849f9fbf99bcd811761f6b72b0d0cb61945d
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2019
ms.locfileid: "55965060"
---
# <a name="working-with-claims-aware-apps-in-application-proxy"></a>Arbeta med anspråksmedvetna appar i programproxyn
[Anspråksmedvetna appar](https://msdn.microsoft.com/library/windows/desktop/bb736227.aspx) utför en omdirigering till Security Token Service (STS). STS begär autentiseringsuppgifter från användaren i utbyte mot en token och sedan omdirigeras användaren till programmet. Det finns ett antal sätt att aktivera Application Proxy att arbeta med dessa omdirigeringar. Använd den här artikeln för att konfigurera distributionen för anspråksmedvetna appar. 

## <a name="prerequisites"></a>Förutsättningar
Se till att STS som appen anspråksmedvetna omdirigerar till är tillgängliga utanför ditt lokala nätverk. Du kan tillgängliggöra STS genom att exponera den via en proxyserver eller genom att tillåta externa anslutningar. 

## <a name="publish-your-application"></a>Publicera programmet

1. Publicera programmet enligt instruktionerna i [publicera program med Application Proxy](application-proxy-add-on-premises-application.md).
2. Gå till programsidan i portalen och välj **enkel inloggning**.
3. Om du har valt **Azure Active Directory** som din **förautentisering metoden**väljer **Azure AD enkel inloggning inaktiverad** som din **internt Autentiseringsmetod**. Om du har valt **genomströmning** som din **förautentisering metoden**, du behöver inte ändra något.

## <a name="configure-adfs"></a>Konfigurera AD FS

Du kan konfigurera AD FS för anspråksmedvetna appar i ett av två sätt. Först är genom att använda anpassade domäner. Andra är med WS-Federation. 

### <a name="option-1-custom-domains"></a>Alternativ 1: Anpassade domäner

Om alla interna URL: er för dina program är fullständigt kvalificerade domännamn (FQDN), så du kan konfigurera [anpassade domäner](application-proxy-configure-custom-domain.md) för dina program. Använd anpassade domäner för att skapa externa URL: er som är samma som de interna URL: er. När ditt externa URL: er matchar din interna URL: er, fungerar STS-omdirigeringar oavsett om dina användare är lokala eller fjärranslutna. 

### <a name="option-2-ws-federation"></a>Alternativ 2: WS-Federation

1. Öppna AD FS-hantering.
2. Gå till **förlitande Partsförtroenden**, högerklicka på appen som du publicerar med Application Proxy och välj **egenskaper**.  

   ![Förlitande Partsförtroenden högerklickar du på appnamn – skärmbild](./media/application-proxy-configure-for-claims-aware-applications/appproxyrelyingpartytrust.png)  

3. På den **slutpunkter** fliken, under **slutpunktstyp**väljer **WS-Federation**.
4. Under **betrodda URL**, ange den Webbadress du angav i programproxyn under **externa URL: en** och klicka på **OK**.  

   ![Lägg till en slutpunkt - värdet betrodda URL - skärmbild](./media/application-proxy-configure-for-claims-aware-applications/appproxyendpointtrustedurl.png)  

## <a name="next-steps"></a>Nästa steg
* [Aktivera enkel inloggning på](configure-single-sign-on-portal.md) för program som inte är anspråksmedvetna
* [Aktivera ursprungliga klientappar kan interagera med proxy-program](application-proxy-configure-native-client-application.md)


