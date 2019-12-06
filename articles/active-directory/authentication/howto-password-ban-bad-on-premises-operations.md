---
title: Åtgärder och rapporter för lösen ords skydd – Azure Active Directory
description: Azure AD Password Protection efter distributions åtgärder och rapportering
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 24fa03fe12f584e9da32b547f0d8128e5ff28803
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74847736"
---
# <a name="azure-ad-password-protection-operational-procedures"></a>Drift procedurer för Azure AD-lösenords skydd

När du har slutfört [installationen av Azure AD Password Protection](howto-password-ban-bad-on-premises-deploy.md) lokalt finns det ett par objekt som måste konfigureras i Azure Portal.

## <a name="configure-the-custom-banned-password-list"></a>Konfigurera listan anpassat blockerade lösen ord

Följ anvisningarna i artikeln [Konfigurera listan med anpassade förbjudna lösen ord](howto-password-ban-bad-configure.md) för att anpassa listan över förbjudna lösen ord för din organisation.

## <a name="enable-password-protection"></a>Aktivera lösen ords skydd

1. Logga in på [Azure Portal](https://portal.azure.com) och bläddra till **Azure Active Directory**, **autentiseringsmetoder**och **lösen ords skydd**.
1. Ange **Aktivera lösen ords skydd på Windows Server Active Directory** till **Ja**
1. Som vi nämnt i [distributions guiden](howto-password-ban-bad-on-premises-deploy.md#deployment-strategy), rekommenderar vi att du ursprungligen ställer in **läget** på **granskning**
   * När du är van att aktivera funktionen kan du växla mellan **läget** och **verkställa**
1. Klicka på **Spara**

![Aktivera komponenter för lösen ords skydd i Azure AD i Azure Portal](./media/howto-password-ban-bad-on-premises-operations/authentication-methods-password-protection-on-prem.png)

## <a name="audit-mode"></a>Gransknings läge

Gransknings läget är avsett som ett sätt att köra program varan i "Vad händer om"-läge. Varje DC-agenttjänsten utvärderar ett inkommande lösen ord enligt den aktuella aktiva principen. Om den aktuella principen är konfigurerad som gransknings läge resulterar "dåliga" lösen ord i händelse logg meddelanden men godkänns. Detta är den enda skillnaden mellan gransknings-och framtvingande läge. alla andra åtgärder kör samma.

> [!NOTE]
> Microsoft rekommenderar att den första distributionen och testningen alltid startar i gransknings läge. Händelser i händelse loggen bör sedan övervakas för att försöka förutse om befintliga operativa processer skulle störas när tvingande läge är aktiverat.

## <a name="enforce-mode"></a>Framtvinga läge

Tvingande läge är avsett som den slutliga konfigurationen. Som i gransknings läget ovan utvärderar varje DC-agenttjänsten inkommande lösen ord enligt den aktuella aktiva principen. Om tvingande läge är aktiverat, avvisas ett lösen ord som betraktas som oskyddat enligt principen.

När ett lösen ord avvisas i tvingande läge av Azure AD Password Protection DC-agenten, är den synliga påverkan som en slutanvändare sett identisk med vad de skulle se om deras lösen ord avvisades av traditionella lokala lösen ords komplexitet. En användare kan till exempel se följande vanliga fel meddelande på skärmen för Windows logon\change-lösenord:

`Unable to update the password. The value provided for the new password does not meet the length, complexity, or history requirements of the domain.`

Det här meddelandet är bara ett exempel på flera möjliga resultat. Det specifika fel meddelandet kan variera beroende på den faktiska program varan eller det scenario som försöker ange ett oskyddat lösen ord.

Berörda slutanvändare kan behöva arbeta med sin IT-personal för att förstå de nya kraven och kunna välja säkra lösen ord.

> [!NOTE]
> Lösen ords skydd i Azure AD har ingen kontroll över det särskilda fel meddelande som visas av klient datorn när ett svagt lösen ord avvisas.

## <a name="enable-mode"></a>Aktivera läge

Den här inställningen ska vara kvar i standard läget för aktive rad (Yes). Om den här inställningen är inaktive rad (Nej) kommer alla distribuerade Azure AD-quiescent att hamna i ett läge där alla lösen ord godkänns som de är, och inga validerings aktiviteter utförs på samma sätt (till exempel inte ens gransknings händelser genereras).

## <a name="next-steps"></a>Nästa steg

[Övervakning av lösen ords skydd i Azure AD](howto-password-ban-bad-on-premises-monitor.md)
