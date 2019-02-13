---
title: Konfigurera SharePoint Online och Exchange Online för villkorlig åtkomst i Azure Active Directory | Microsoft Docs
description: Lär dig hur du ställer in SharePoint Online och Exchange Online för villkorlig åtkomst i Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 62349fba-3cc0-4ab5-babe-372b3389eff6
ms.service: active-directory
ms.subservice: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/15/2018
ms.author: markvi
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8dad650ac1cc7ab8822925a4a9759b68ee0d0088
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56186020"
---
# <a name="how-to-set-up-sharepoint-online-and-exchange-online-for-azure-active-directory-conditional-access"></a>Instruktioner: Konfigurera SharePoint Online och Exchange Online för villkorlig åtkomst i Azure Active Directory 

Med [villkorlig åtkomst i Azure Active Directory (Azure AD)](overview.md), du kan styra hur användare kommer åt dina appar i molnet. Om du vill använda villkorlig åtkomst för att styra åtkomst till SharePoint och Exchange online måste du:

- Granska om scenariot för villkorlig åtkomst stöds
- Förhindra att klientappar från att kringgå tillämpning av principer för villkorlig åtkomst.   

Den här artikeln förklarar hur du kan lösa båda fallen.


## <a name="what-you-need-to-know"></a>Vad du behöver veta

Du kan använda Azure AD villkorsstyrd åtkomst för att skydda appar när en autentiseringsbegäran kommer från:

- En webbläsare

- Ett klientprogram som använder [modern autentisering](https://support.office.com/article/Using-Office-365-modern-authentication-with-Office-clients-776c0036-66fd-41cb-8928-5495c0f9168a)

- Exchange ActiveSync 

Vissa appar i molnet har också stöd för äldre autentiseringsprotokoll. Detta gäller, till exempel för SharePoint Online och Exchange Online. När ett klientprogram kan använda en äldre autentiseringsprotokoll till en molnapp, kan inte Azure AD framtvinga principer för villkorlig åtkomst på den här åtkomstförsök. Om du vill förhindra att en klientapp kringgår tillämpning av principer, bör du kontrollera om det är möjligt att endast aktivera modern autentisering på de berörda molnapparna. 

Är exempel på klient apps villkorlig åtkomst inte gäller för:

- Office 2010 och tidigare

- Office 2013 när modern autentisering inte är aktiverat



 
## <a name="control-access-to-sharepoint-online"></a>Kontrollera åtkomsten till SharePoint Online

Förutom att modern autentisering, SharePoint Online också har stöd för äldre autentiseringsprotokoll. Om de äldre autentiseringsprotokoll är aktiverade, tillämpas inte principer för villkorlig åtkomst för SharePoint för klienter som inte använder modern autentisering.

Du kan inaktivera äldre autentiseringsprotokoll för åtkomst till SharePoint med hjälp av den **[Set-SPOTenant](https://technet.microsoft.com/library/fp161390.aspx)** cmdlet: 

    Set-SPOTenant -LegacyAuthProtocolsEnabled $false

## <a name="control-access-to-exchange-online"></a>Kontrollera åtkomsten till Exchange Online

När du konfigurerar principer för villkorlig åtkomst för Exchange Online måste du kontrollera följande:

- Exchange ActiveSync

- Äldre autentiseringsprotokoll



### <a name="exchange-activesync"></a>Exchange ActiveSync

Exchange Active Sync har stöd för modern autentisering, finns men det några begränsningar för stöd för scenarier med villkorlig åtkomst:

- Du kan bara konfigurera villkor för enhet-plattformar  

    ![Enhetsplattformar](./media/conditional-access-for-exo-and-spo/05.png)

- Ange krav för multifaktorautentisering stöds inte  

    ![Villkorlig åtkomst](./media/conditional-access-for-exo-and-spo/01.png)

För att effektivt skydda åtkomst till Exchange Online från Exchange ActiveSync, kan du:

- Konfigurera principer för villkorlig åtkomst som stöds genom att följa dessa steg:

    a. Välj bara **Office 365 Exchange Online** som appar i molnet.  

    ![Villkorlig åtkomst](./media/conditional-access-for-exo-and-spo/04.png)

    b. Välj **Exchange Active Sync** som **klientappen**, och välj sedan **tillämpa principen endast på plattformar som stöds**.  

    ![Enhetsplattformar](./media/conditional-access-for-exo-and-spo/03.png)

- Blockera Exchange ActiveSync med hjälp av Active Directory Federation Services (AD FS)-regler.

        @RuleName = "Block Exchange ActiveSync"
        c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"]
        => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "false");




### <a name="legacy-authentication-protocols"></a>Äldre autentiseringsprotokoll

Förutom att modern autentisering, Exchange Online också har stöd för äldre autentiseringsprotokoll. Om äldre autentiseringsprotokoll är aktiverade, tillämpas inte principer för villkorlig åtkomst för Exchange Online för klienter som inte använder modern autentisering.

Du kan inaktivera äldre autentiseringsprotokoll för Exchange Online genom att ange regler för AD FS. Den här blockerar åtkomst från:

- Äldre Office-klienter, till exempel Office 2013 som inte har modern autentisering aktiverad 

- Tidigare versioner av Office


## <a name="set-up-ad-fs-rules"></a>Ställa in regler för AD FS

Du kan använda följande auktoriseringsregler för att aktivera eller blockera trafik på AD FS-nivå. 

### <a name="block-legacy-traffic-from-the-extranet"></a>Blockera äldre trafik från extranätet

Genom att använda följande tre regler: 

- Du aktiverar åtkomst för:
    - Exchange ActiveSync-trafik
    - Webbläsartrafik
    - Trafik för modern autentisering
- Du kan blockera åtkomst för: 
    - Äldre klientprogram från extranätet

**Regel 1:**

    @RuleName = "Allow all intranet traffic"
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "true"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

**Regel 2:**

    @RuleName = "Allow Exchange ActiveSync"
    c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

**Regel 3:**

    @RuleName = "Allow extranet browser and browser dialog traffic"
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "false"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

### <a name="block-legacy-traffic-from-anywhere"></a>Blockera äldre trafik från var som helst

Genom att använda följande tre regler:

- Du aktiverar åtkomst för: 
    - Exchange ActiveSync-trafik
    - Webbläsartrafik
    - Trafik för modern autentisering
- Du kan blockera åtkomst för: 
    - Äldre appar från valfri plats

##### <a name="rule-1"></a>Regel 1
    @RuleName = "Allow all intranet traffic only for browser and modern authentication clients"
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "true"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


##### <a name="rule-2"></a>Regel 2
    @RuleName = "Allow Exchange ActiveSync"
    c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


##### <a name="rule-3"></a>Regel 3
    @RuleName = "Allow extranet browser and browser dialog traffic"
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "false"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

## <a name="next-steps"></a>Nästa steg

Mer information finns i [vad är villkorlig åtkomst i Azure Active Directory](overview.md).

För instruktioner om hur du konfigurerar anspråksregler, se [konfigurera Anspråksregler](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-claim-rules). 






