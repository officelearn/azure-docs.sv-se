---
title: Anspråksmedvetna appar – Azure AD App Proxy | Microsoft-dokument
description: Så här publicerar du lokala ASP.NET program som accepterar ADFS-anspråk för säker fjärråtkomst av användarna.
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
ms.date: 11/08/2018
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: cbc5c356ea5a542fdc12b11aff236c56b146b3d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68477246"
---
# <a name="working-with-claims-aware-apps-in-application-proxy"></a>Arbeta med anspråksmedvetna appar i Application Proxy
[Anspråksmedvetna appar](https://msdn.microsoft.com/library/windows/desktop/bb736227.aspx) utför en omdirigering till Security Token Service (STS). STS begär autentiseringsuppgifter från användaren i utbyte mot en token och omdirigerar sedan användaren till programmet. Det finns några sätt att aktivera Programproxy för att arbeta med dessa omdirigeringar. Använd den här artikeln för att konfigurera distributionen för anspråksmedvetna appar. 

## <a name="prerequisites"></a>Krav
Kontrollera att STS som den anspråksmedvetna appen omdirigerar till är tillgänglig utanför ditt lokala nätverk. Du kan göra STS tillgänglig genom att exponera den via en proxy eller genom att tillåta externa anslutningar. 

## <a name="publish-your-application"></a>Publicera programmet

1. Publicera din ansökan enligt instruktionerna som beskrivs i [Publicera program med Application Proxy](application-proxy-add-on-premises-application.md).
2. Navigera till programsidan i portalen och välj **Enkel inloggning**.
3. Om du väljer **Azure Active Directory** som **preauthentication-metod**väljer du **Azure AD enkel inloggning inaktiverad** som din **interna autentiseringsmetod**. Om du väljer **Passthrough** som **preauthentication-metod**behöver du inte ändra någonting.

## <a name="configure-adfs"></a>Konfigurera ADFS

Du kan konfigurera ADFS för anspråksmedvetna appar på ett av två sätt. Den första är genom att använda anpassade domäner. Den andra är med WS-Federation. 

### <a name="option-1-custom-domains"></a>Alternativ 1: Anpassade domäner

Om alla interna url:er för dina program är fullständigt kvalificerade domännamn (FQDN) kan du konfigurera [anpassade domäner](application-proxy-configure-custom-domain.md) för dina program. Använd de anpassade domänerna för att skapa externa webbadresser som är desamma som de interna webbadresserna. När dina externa webbadresser matchar dina interna webbadresser fungerar STS-omdirigeringarna oavsett om användarna är lokala eller fjärranslutna. 

### <a name="option-2-ws-federation"></a>Alternativ 2: WS-Federation

1. Öppna ADFS-hantering.
2. Gå till **Förlitande part Trusts**, högerklicka på appen du publicerar med Application Proxy och välj **Egenskaper**.  

   ![Relying Party Trusts högerklicka på appnamn - skärmdump](./media/application-proxy-configure-for-claims-aware-applications/appproxyrelyingpartytrust.png)  

3. Välj **WS-Federation**under **Slutpunktstyp**på fliken **Slutpunkter** .
4. Under **Betrodd URL**anger du webbadressen som du angav i programproxyen under Extern **URL** och klickar på **OK**.  

   ![Lägga till en slutpunkt - ange värdet för betrodd url - skärmbild](./media/application-proxy-configure-for-claims-aware-applications/appproxyendpointtrustedurl.png)  

## <a name="next-steps"></a>Nästa steg
* [Aktivera enkel inloggning](configure-single-sign-on-non-gallery-applications.md) för program som inte är anspråksmedvetna
* [Aktivera inbyggda klientappar för att interagera med proxyprogram](application-proxy-configure-native-client-application.md)


