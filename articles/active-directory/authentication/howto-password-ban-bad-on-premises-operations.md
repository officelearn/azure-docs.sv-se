---
title: Aktivera lokalt Azure AD-lösenordsskydd
description: Lär dig hur du aktiverar Azure AD-lösenordsskydd för en lokal Active Directory Domain Services-miljö
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 03/05/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: d00a8b84477226b68913f95c5121bbbdfc2eb09d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263821"
---
# <a name="enable-on-premises-azure-active-directory-password-protection"></a>Aktivera lokalt lösenordsskydd för Azure Active Directory

Användare skapar ofta lösenord som använder vanliga lokala ord som en skola, ett idrottslag eller en känd person. Dessa lösenord är lätta att gissa, och svaga mot ordlistebaserade attacker. För att framtvinga starka lösenord i din organisation ger Azure Active Directory (Azure AD) lösenordsskydd en global och anpassad lista över förbjudna lösenord. En begäran om lösenordsändring misslyckas om det finns en matchning i listan över förbjudna lösenord.

För att skydda din lokala AD DS-miljö (Active Directory Domain Services) kan du installera och konfigurera Azure AD Password Protection så att den fungerar med din on-prem DC. Den här artikeln visar hur du aktiverar Azure AD-lösenordsskydd för din lokala miljö.

Mer information om hur Azure AD-lösenordsskydd fungerar i en lokal miljö finns i [Så här framtvingar du Azure AD-lösenordsskydd för Windows Server Active Directory](concept-password-ban-bad-on-premises.md).

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln visar hur du aktiverar Azure AD-lösenordsskydd för din lokala miljö. Innan du slutför den här artikeln [installerar och registrerar du proxytjänsten för Azure AD Password Protection och DC-agenter](howto-password-ban-bad-on-premises-deploy.md) i din lokala AD DS-miljö.

## <a name="enable-on-premises-password-protection"></a>Aktivera lokalt lösenordsskydd

1. Logga in på [Azure-portalen](https://portal.azure.com) och bläddra till **Azure Active Directory** > **Security** > **Authentication metoder** > **Lösenordsskydd**.
1. Ange alternativet för **Aktivera lösenordsskydd i Active Directory** i Windows Server till *Ja*.

    När den här inställningen är inställd på *Nej*går alla distribuerade Azure AD Password Protection DC-agenter in i ett quiescent-läge där alla lösenord accepteras som de är. Inga valideringsaktiviteter utförs och granskningshändelser genereras inte.

1. Vi rekommenderar att du först ställer in **läget** på *Granskning*. När du är bekväm med funktionen och påverkan på användarna i organisationen kan du växla **läget** till *Påtvingat*. Mer information finns i följande avsnitt om [driftsätt](#modes-of-operation).
1. När du är klar väljer du **Spara**.

    [![](media/howto-password-ban-bad-on-premises-operations/enable-configure-custom-banned-passwords-cropped.png "Enable on-premises password protection under Authentication Methods in the Azure portal")](media/howto-password-ban-bad-on-premises-operations/enable-configure-custom-banned-passwords.png#lightbox)

## <a name="modes-of-operation"></a>Lägen för åtgärden

När du aktiverar lokalt Azure AD-lösenordsskydd kan du använda antingen *granskningsläge* eller *framtvingningsläge.* Vi rekommenderar att den första distributionen och testningen alltid startar i granskningsläge. Poster i händelseloggen bör sedan övervakas för att förutse om några befintliga operativa processer skulle störas när *framtvingningsläge* är aktiverat.

### <a name="audit-mode"></a>Granskningsläge

*Granskningsläget* är avsett som ett sätt att köra programvaran i ett "tänk om"-läge. Varje Azure AD Password Protection DC-agenttjänst utvärderar ett inkommande lösenord enligt den aktiva principen.

Om den aktuella principen är konfigurerad för att vara i granskningsläge resulterar "felaktiga" lösenord i händelseloggmeddelanden men bearbetas och uppdateras. Detta är den enda skillnaden mellan granskning och framtvingningsläge. Alla andra åtgärder körs på samma sätt.

### <a name="enforced-mode"></a>Påtvingat läge

*Påtvingat* läge är avsett som den slutliga konfigurationen. Precis som när i granskningsläge utvärderar varje Azure AD Password Protection DC-agenttjänst inkommande lösenord enligt den aktiva principen. När tvingande läge är aktiverat avvisas dock ett lösenord som anses vara osäkert enligt principen.

När ett lösenord avvisas i tvingande läge av AZURE AD Password Protection DC-agenten ser en slutanvändare ett liknande fel som de skulle se om deras lösenord avvisades av traditionell lokal lösenordskomplexitetsförsändelse. En användare kan till exempel se följande traditionella felmeddelande på inloggningsskärmen för Windows eller ändra lösenord:

*"Det gick inte att uppdatera lösenordet. Värdet som anges för det nya lösenordet uppfyller inte domänens längd-, komplexitets- eller historikkrav."*

Detta meddelande är bara ett exempel på flera möjliga resultat. Det specifika felmeddelandet kan variera beroende på vilken programvara eller scenario som försöker ange ett osäkert lösenord.

Berörda slutanvändare kan behöva arbeta med sin IT-personal för att förstå de nya kraven och välja säkra lösenord.

> [!NOTE]
> Azure AD Password Protection har ingen kontroll över det specifika felmeddelande som visas av klientdatorn när ett svagt lösenord avvisas.

## <a name="next-steps"></a>Nästa steg

Information om hur du anpassar listan över förbjudna lösenord för din organisation finns i [Konfigurera den anpassade listan över förbjudna lösenord för azure AD-lösenordsskydd](tutorial-configure-custom-password-protection.md).

Information om hur du övervakar händelser med on-prem finns i [Övervaka azure AD-lösenordsskydd för prem](howto-password-ban-bad-on-premises-monitor.md).
