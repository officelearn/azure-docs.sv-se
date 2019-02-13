---
title: Potential anledningar för felmeddelandet ”du kan inte ta dig dit härifrån” i Azure Active Directory | Microsoft Docs
description: Felsöka de möjliga orsakerna till att du får felmeddelandet ”du kan inte ta dig dit härifrån”.
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: 8ad0156c-0812-4855-8563-6fbff6194174
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: lizross
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: a7cd67a9a7027e8567e470acab006fd09748de54
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56181719"
---
# <a name="potential-reasons-for-the-you-cant-get-there-from-here-error-message"></a>Möjliga orsaker till felmeddelandet ”du kan inte ta dig dit härifrån”
Vid åtkomst till organisationens interna webbprogram eller tjänster, kan du få ett felmeddelande som säger, **du kan inte ta dig dit härifrån**. Det här meddelandet innebär att din organisation har infört en princip som förhindrar enheten från att komma åt din organisations resurser. Medan du kan få behöva kontakta din supportavdelning för att åtgärda problemet, är här några saker som du kan försöka först.

## <a name="make-sure-youre-using-a-supported-browser"></a>Kontrollera att du använder en webbläsare som stöds
Om du får den **du kan inte ta dig dit härifrån** meddelande om att du försöker komma åt din organisations platser från en webbläsare som inte stöds, kontrollera vilken webbläsare du använder.

![Felmeddelande som är relaterade till stöd för webbläsare](media/user-help-device-remediation/browser-version.png)

För att åtgärda problemet måste du installera och kör en webbläsare som stöds, baserat på ditt operativsystem. Om du använder Windows 10 är webbläsarna som stöds Microsoft Edge, Internet Explorer och Google Chrome. Om du använder ett annat operativsystem, kan du kontrollera den fullständiga listan med [webbläsare som stöds](../conditional-access/technical-reference.md#supported-browsers).

## <a name="make-sure-youre-using-a-supported-operating-system"></a>Kontrollera att du använder ett operativsystem som stöds
Se till att du kör en version som stöds av operativsystemet, inklusive:

- **Windows-klient.** Windows 7 eller senare.

- **Windows Server.** Windows Server 2008 R2 eller senare.

- **macOS.** Mac OS X eller senare

- **Android och iOS.** Senaste versionen av Android och iOS mobila operativsystem

För att åtgärda problemet måste du installera och kör ett operativsystem som stöds.

## <a name="make-sure-your-device-is-joined-to-your-network"></a>Kontrollera att enheten är ansluten till nätverket
Om du får den **du kan inte ta dig dit härifrån** meddelande om att enheten är out of compliance med din organisations åtkomstprincip, se till att du har anslutit din enhet till din organisations nätverk.

![Felmeddelande som rör oavsett om du är i nätverket](media/user-help-device-remediation/network-version.png)

### <a name="to-check-whether-your-device-is-joined-to-your-network"></a>Kontrollera om enheten är ansluten till nätverket
1. Logga in på Windows med ditt arbets- eller skolkonto konto. Till exempel alain@contoso.com.

2. Ansluta till företagets nätverk via ett virtuellt privat nätverk (VPN) eller DirectAccess.

3. När du är ansluten, trycker du på **logotyp för Windows tangenten + L** att låsa enheten.

4. Låsa upp enheten med ditt arbets eller skolkonto och försök sedan att komma åt den problematiska appen eller tjänsten igen.

    Om du ser den **du kan inte ta dig dit härifrån** felmeddelande igen och väljer den **mer** länka och sedan kontakta supportavdelningen med information.

### <a name="to-join-your-device-to-your-network"></a>Att ansluta enheten till nätverket
Om enheten inte är ansluten till din organisations nätverk, kan du göra något av följande:

- **Anslut din enhet för arbete.** Anslut Windows 10-enheten ägs av arbetet till din organisations nätverk så att du kan komma åt potentiellt begränsade resurser. Mer information och instruktioner finns i [ansluter din work-enhet till din organisations nätverk](user-help-join-device-on-network.md).

- **Registrera en enhet för arbete.** Registrera en enhet, vanligtvis en telefon eller surfplatta, på din organisations nätverk. När enheten har registrerats kan komma den åt skyddade resurser i din organisation. Mer information och instruktioner finns i [registrera en enhet på din organisations nätverk](user-help-register-device-on-network.md).

## <a name="next-steps"></a>Nästa steg
- [Vad är MyApps-portalen?](active-directory-saas-access-panel-introduction.md)

- [Logga in med telefonen, inte med ditt lösenord](user-help-auth-app-sign-in.md)
