---
title: "Ställ in SharePoint Online och Exchange Online för Azure Active Directory för villkorlig åtkomst | Microsoft Docs"
description: "Lär dig hur du ställer in SharePoint Online och Exchange Online för Azure Active Directory för villkorlig åtkomst."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 62349fba-3cc0-4ab5-babe-372b3389eff6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: c527d4511ab2dd3bb349c10c1defb24723fef59a
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2018
---
# <a name="set-up-sharepoint-online-and-exchange-online-for-azure-active-directory-conditional-access"></a>Ställ in SharePoint Online och Exchange Online för Azure Active Directory för villkorlig åtkomst 

Med [villkorlig åtkomst i Azure Active Directory (AD Azure)](active-directory-conditional-access-azure-portal.md), du kan styra hur användare kommer åt dina molnappar. Om du vill använda villkorlig åtkomst för att styra åtkomst till SharePoint och Exchange online måste du:

- Granska om ditt scenario för villkorlig åtkomst stöds
- Förhindra att klientappar från att kringgå tillämpning av principer för villkorlig åtkomst.   

Den här artikeln förklarar hur du kan åtgärda båda fallen.


## <a name="what-you-need-to-know"></a>Vad du behöver veta

Du kan använda villkorlig åtkomst i Azure AD för att skydda molnappar när Autentiseringsförsöket kommer från:

- En webbläsare

- Ett klientprogram som använder [modern autentisering](https://support.office.com/article/Using-Office-365-modern-authentication-with-Office-clients-776c0036-66fd-41cb-8928-5495c0f9168a)

- Exchange ActiveSync 

Vissa molnappar stöder också äldre autentiseringsprotokoll. Detta gäller, till exempel SharePoint Online och Exchange Online. När ett klientprogram kan använda ett äldre autentiseringsprotokoll att komma åt en molnappen, kan Azure AD tillämpa en princip för villkorlig åtkomst på den här åtkomstförsök. Om du vill förhindra att ett klientprogram kringgå tillämpning av principer, bör du kontrollera om det är möjligt att endast aktivera modern autentisering på de berörda molnapparna. 

Är exempel på appar villkorlig åtkomst inte gäller för klient:

- Office 2010 eller tidigare

- Office 2013 när modern autentisering inte är aktiverat



 
## <a name="control-access-to-sharepoint-online"></a>Kontrollera åtkomsten till SharePoint Online

Förutom modern autentisering stöder SharePoint Online också äldre autentiseringsprotokoll. Om de äldre autentiseringsprotokoll är aktiverade, tillämpas inte dina principer för villkorlig åtkomst för SharePoint för klienter som inte använder modern autentisering.

Du kan inaktivera äldre autentiseringsprotokoll för SharePoint-åtkomst med hjälp av den  **[Set SPOTenant](https://technet.microsoft.com/library/fp161390.aspx)**  cmdlet: 

    Set-SPOTenant -LegacyAuthProtocolsEnabled $false

## <a name="control-access-to-exchange-online"></a>Kontrollera åtkomsten till Exchange Online

När du konfigurerar principer för villkorlig åtkomst för Exchange Online måste du granska följande:

- Exchange ActiveSync

- Äldre autentiseringsprotokoll



### <a name="exchange-activesync"></a>Exchange ActiveSync

När Exchange Active Sync stöder modern autentisering, finns det några begränsningar för stöd för scenarier med villkorlig åtkomst:

- Du kan bara konfigurera villkoret plattformar för enheten  

    ![Enhetsplattformar](./media/active-directory-conditional-access-no-modern-authentication/05.png)

- Ange krav för multifaktorautentisering stöds inte  

    ![Villkorlig åtkomst](./media/active-directory-conditional-access-no-modern-authentication/01.png)

För att effektivt skydda åtkomst till Exchange Online från Exchange ActiveSync, kan du:

- Konfigurera en princip för villkorlig åtkomst som stöds genom att följa dessa steg:

    a. Välj bara **Office 365 Exchange Online** som molnappen.  

    ![Villkorlig åtkomst](./media/active-directory-conditional-access-no-modern-authentication/04.png)

    b. Välj **Exchange Active Sync** som **klientappen**, och välj sedan **principen endast tillämpas på plattformar som stöds**.  

    ![Enhetsplattformar](./media/active-directory-conditional-access-no-modern-authentication/03.png)

- Blockera Exchange ActiveSync-med Active Directory Federation Services (AD FS) regler.

        @RuleName = "Block Exchange ActiveSync"
        c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"]
        => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "false");




### <a name="legacy-authentication-protocols"></a>Äldre autentiseringsprotokoll

Förutom modern autentisering stöder Exchange Online också äldre autentiseringsprotokoll. Om äldre autentiseringsprotokoll är aktiverade, tillämpas inte dina principer för villkorlig åtkomst för Exchange Online för klienter som inte använder modern autentisering.

Du kan inaktivera äldre autentiseringsprotokoll för Exchange Online genom att ange regler för AD FS. Blockerar åtkomst från:

- Äldre Office-klienter, till exempel Office 2013 som inte har modern autentisering aktiverad 

- Tidigare versioner av Office


## <a name="set-up-ad-fs-rules"></a>Konfigurera regler för AD FS

Du kan använda följande auktoriseringsregler för att aktivera eller blockera trafik på nivån för AD FS. 

### <a name="block-legacy-traffic-from-the-extranet"></a>Blockera äldre trafiken från extranätet

Genom att använda följande tre regler: 

- Du aktiverar åtkomst för:
    - Exchange ActiveSync-trafik
    - Webbläsartrafik
    - Modern autentiseringstrafik
- Du kan blockera åtkomst för: 
    - Äldre klientappar från extranätet

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

### <a name="block-legacy-traffic-from-anywhere"></a>Blockera äldre trafiken från valfri plats

Genom att använda följande tre regler:

- Du aktiverar åtkomst för: 
    - Exchange ActiveSync-trafik
    - Webbläsartrafik
    - Modern autentiseringstrafik
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

Mer information finns i [villkorlig åtkomst i Azure Active Directory](active-directory-conditional-access-azure-portal.md)




