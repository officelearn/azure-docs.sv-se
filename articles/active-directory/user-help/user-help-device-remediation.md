---
title: Korrigeringar för felet "Du kan inte komma dit härifrån" - Azure AD
description: Hitta potentiella korrigeringar för varför du får felmeddelandet "Du kan inte komma dit härifrån".
services: active-directory
author: curtand
manager: daveba
ms.assetid: 8ad0156c-0812-4855-8563-6fbff6194174
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: curtand
ms.reviewer: jairoc
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: 2484de4e554d16ba049d206981a44654ede28a3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190032"
---
# <a name="possible-fixes-for-the-you-cant-get-there-from-here-error-message"></a>Möjliga korrigeringar för felmeddelandet "Du kan inte komma dit härifrån"

När du öppnar organisationens interna webbappar eller tjänster kan du få ett felmeddelande som säger att **du inte kan komma dit härifrån**. Det här meddelandet innebär att din organisation har infört en princip som hindrar enheten från att komma åt organisationens resurser. Även om du kan behöva kontakta din Helpdesk för att åtgärda problemet, här är några saker du kan prova först.

## <a name="make-sure-youre-using-a-supported-browser"></a>Kontrollera att du använder en webbläsare som stöds
Om du får meddelandet **Du kan inte komma dit härifrån** där du säger att du försöker komma åt organisationens webbplatser från en webbläsare som inte stöds kontrollerar du vilken webbläsare du kör.

![Felmeddelande om webbläsarstöd](media/user-help-device-remediation/browser-version.png)

För att åtgärda problemet måste du installera och köra en webbläsare som stöds, baserat på operativsystemet. Om du använder Windows 10 är det Microsoft Edge, Internet Explorer och Google Chrome som stöds. Om du använder ett annat operativsystem kan du kontrollera hela listan över [webbläsare som stöds](../conditional-access/concept-conditional-access-conditions.md#supported-browsers).

## <a name="make-sure-youre-using-a-supported-operating-system"></a>Kontrollera att du använder ett operativsystem som stöds
Kontrollera att du kör en version av operativsystemet som stöds, inklusive:

- **Windows-klient.** Windows 7 eller senare.

- **Windows Server.** Windows Server 2008 R2 eller senare.

- **Macos.** macOS X eller senare

- **Android och iOS.** Senaste versionen av Android och iOS mobila operativsystem

För att åtgärda problemet måste du installera och köra ett operativsystem som stöds.

## <a name="make-sure-your-device-is-joined-to-your-network"></a>Kontrollera att enheten är ansluten till nätverket
Om du får meddelandet **Du kan inte komma dit härifrån** om att enheten inte följer organisationens åtkomstprincip kontrollerar du att du har anslutit enheten till organisationens nätverk.

![Felmeddelande om du är i nätverket](media/user-help-device-remediation/network-version.png)

### <a name="to-check-whether-your-device-is-joined-to-your-network"></a>Så här kontrollerar du om enheten är ansluten till nätverket
1. Logga in i Windows med ditt arbets- eller skolkonto. Till exempel alain@contoso.com.

2. Anslut till organisationens nätverk via ett virtuellt privat nätverk (VPN) eller DirectAccess.

3. När du är ansluten trycker du på **Windows-tangenten+L** för att låsa enheten.

4. Lås upp enheten med ditt arbets- eller skolkonto och försök sedan komma åt den problematiska appen eller tjänsten igen.

    Om felmeddelandet **Du inte kan komma dit härifrån** igen väljer du länken Mer **information** och kontaktar sedan helpdesk med informationen.

### <a name="to-join-your-device-to-your-network"></a>Så här ansluter du enheten till nätverket
Om enheten inte är ansluten till organisationens nätverk kan du göra en av två saker:

- **Gå med i din arbetsenhet.** Gå med i din arbetsägda Windows 10-enhet i organisationens nätverk så att du kan komma åt potentiellt begränsade resurser. Mer information och steg-för-steg-instruktioner finns i [Ansluta din arbetsenhet till organisationens nätverk](user-help-join-device-on-network.md).

- **Registrera din personliga enhet för arbete.** Registrera din personliga enhet, vanligtvis en telefon eller surfplatta, i organisationens nätverk. När enheten har registrerats kan den komma åt organisationens begränsade resurser. Mer information och steg-för-steg-instruktioner finns i [Registrera din personliga enhet i organisationens nätverk](user-help-register-device-on-network.md).

## <a name="next-steps"></a>Nästa steg
- [Vad är MyApps-portalen?](active-directory-saas-access-panel-introduction.md)

- [Logga in med telefonen, inte med ditt lösenord](user-help-auth-app-sign-in.md)
