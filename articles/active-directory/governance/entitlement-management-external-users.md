---
title: Styra åtkomsten för externa användare i Azure AD-hantering av rättigheter (för hands version) – Azure Active Directory
description: Lär dig mer om de inställningar som du kan ange för att styra åtkomsten för externa användare i Azure Active Directory rättighets hantering.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 419970985b9531ffab348491730aaf6c00e143b1
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2019
ms.locfileid: "72527111"
---
# <a name="govern-access-for-external-users-in-azure-ad-entitlement-management-preview"></a>Styra åtkomsten för externa användare i Azure AD-hantering av rättigheter (för hands version)

> [!IMPORTANT]
> Azure Active Directory (Azure AD) rättighets hantering är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Hantering av Azure AD-rättigheterna använder [Azure AD Business-to-Business (B2B)](../b2b/what-is-b2b.md) för att samar beta med personer utanför organisationen i en annan katalog. Med Azure AD B2B autentiserar externa användare till sin arbets katalog, men har en representation i din katalog. Representationen i din katalog gör det möjligt för användaren att tilldelas åtkomst till dina resurser.

I den här artikeln beskrivs de inställningar som du kan ange för att styra åtkomsten för externa användare.

## <a name="how-entitlement-management-can-help"></a>Så här kan hantering av rättigheter hjälpa

När du använder [Azure AD B2B](../b2b/what-is-b2b.md) -inbjudan måste du redan känna till e-postadresserna till de externa gäst användare som du vill ta med i resurs katalogen och arbeta med. Detta fungerar bra när du arbetar med ett mindre eller kortsiktigt projekt och du redan känner till alla deltagare, men det är svårare att hantera om du har många användare som du vill arbeta med eller om deltagarna ändras med tiden.  Till exempel kanske du arbetar med en annan organisation och har en kontakt punkt med den organisationen, men över tiden kommer ytterligare användare från organisationen också att ha åtkomst.

Med rättighets hantering kan du definiera en princip som gör det möjligt för användare från organisationer som du anger att kunna begära ett åtkomst paket själv. Du kan ange om godkännande krävs och ett förfallo datum för åtkomsten. Om godkännande krävs kan du också bjuda in en eller flera användare från den externa organisationen till din katalog och ange dem som god kännare – eftersom de förmodligen vet vilka externa användare från deras organisation som behöver åtkomst. När du har konfigurerat åtkomst paketet kan du skicka åtkomst paketets länk till din kontakt person (sponsor) i den externa organisationen. Den kontakten kan dela med andra användare i den externa organisationen, och de kan använda den här länken för att begära åtkomst paketet. Användare från den organisationen som redan har bjudits in till din katalog kan också använda den länken.

När en begäran godkänns, etablerar rättighets hantering användaren med nödvändig åtkomst, vilket kan inkludera inbjudan till användaren om de inte redan finns i katalogen. Azure AD skapar automatiskt ett B2B-gäst konto för dem. Observera att en administratör kan ha tidigare begränsat vilka organisationer som tillåts för samarbete genom att ställa in en [B2B-lista med eller neka](../b2b/allow-deny-list.md) för att tillåta eller blockera inbjudningar till andra organisationer.  Om användaren inte tillåts av listan Tillåt eller blockera kommer de inte att bjudas in.

Eftersom du inte vill att den externa användarens åtkomst till senaste oändligt, anger du ett förfallo datum i principen, till exempel 180 dagar. Efter 180 dagar tar rättighets hanteringen bort all åtkomst som är associerad med det åtkomst paketet, om deras åtkomst inte är utökad. Om användaren som har bjudits in via rättighets hantering inte har några andra åtkomst paket tilldelningar, kommer deras gäst konto att blockeras från att logga in i 30 dagar och sedan tas bort. Detta förhindrar spridning av onödiga konton. Dessa inställningar kan konfigureras enligt beskrivningen i följande avsnitt.

## <a name="how-access-works-for-external-users"></a>Hur åtkomst fungerar för externa användare

Följande diagram och steg ger en översikt över hur externa användare beviljas åtkomst till ett Access-paket.

![Diagram över livs cykeln för externa användare](./media/entitlement-management-external-users/external-users-lifecycle.png)

1. Du skapar ett Access-paket i din katalog som innehåller en princip [för användare som inte finns i din katalog](entitlement-management-access-package-create.md#for-users-not-in-your-directory).

1. Du skickar en [länk till min åtkomst Portal](entitlement-management-access-package-settings.md) till din kontakt i den externa organisationen som de kan dela med sina användare för att begära åtkomst paketet.

1. En extern användare (**begär ande A** i det här exemplet) använder länken min åtkomst Portal för att [begära åtkomst](entitlement-management-request-access.md) till Access-paketet.

1. En god kännare [godkänner begäran](entitlement-management-request-approve.md) (eller också godkänns begäran automatiskt).

1. Begäran försätts i [leverans tillstånd](entitlement-management-process.md).

1. Med hjälp av B2B-ansvars processen skapas ett gäst användar konto i din katalog (**begär ande a (gäst)** i det här exemplet). Om en lista [över tillåtna eller en neka-lista](../b2b/allow-deny-list.md) har definierats tillämpas List inställningen.

1. Gäst användaren tilldelas åtkomst till alla resurser i åtkomst paketet. Det kan ta lite tid innan ändringar görs i Azure AD och till andra Microsoft Online Services eller anslutna SaaS-program. Mer information finns i [när ändringar tillämpas](entitlement-management-access-package-resources.md#when-changes-are-applied).

1. Den externa användaren får ett e-postmeddelande som anger att deras åtkomst har [levererats](entitlement-management-process.md).

1. För att få åtkomst till resurserna kan den externa användaren antingen klicka på länken i e-postmeddelandet eller försöka komma åt någon av katalog resurserna direkt för att slutföra Inbjudnings processen.

1. Beroende på princip inställningarna, som tid steg, går det att tilldela åtkomst paketet för den externa användaren och den externa användarens åtkomst tas bort.

1. Beroende på inställningarna för den externa användarens livs cykel, kommer den externa användaren att blockeras från att logga in och gäst användar kontot tas bort från din katalog när den externa användaren inte längre har några åtkomst paket tilldelningar.

## <a name="manage-the-lifecycle-of-external-users"></a>Hantera livs cykeln för externa användare

Du kan välja vad som händer när en extern användare, som har bjudits in till din katalog via en begäran om åtkomst paket, inte längre ha några åtkomst paket tilldelningar. Detta kan inträffa om användaren låser alla tilldelningar av åtkomst paket eller om den senaste tilldelningen av åtkomst paket upphör att gälla. När en extern användare inte längre har några åtkomst paket tilldelningar blockeras som standard från att logga in till din katalog. Efter 30 dagar tas deras gäst användar konto bort från katalogen.

**Nödvändig roll:** Global administratör eller användar administratör

1. Klicka på **Azure Active Directory** i Azure Portal och klicka sedan på **identitets styrning**.

1. På den vänstra menyn i avsnittet **rättighets hantering** klickar du på **Inställningar**.

1. Klicka på **Redigera**.

    ![Inställningar för att hantera livs cykeln för externa användare](./media/entitlement-management-external-users/settings-external-users.png)

1. I avsnittet **Hantera livs cykel för externa användare** väljer du de olika inställningarna för externa användare.

1. När en extern användare förlorar sin senaste tilldelning till några åtkomst paket, och om du vill blockera dem från att logga in till den här katalogen, ställer du in den **blockerande externa användaren från att logga in på den här katalogen** till **Ja**.

    > [!NOTE]
    > Om en användare är blockerad från att logga in till den här katalogen, kommer användaren inte att kunna begära åtkomst paketet igen eller begära ytterligare åtkomst i den här katalogen. Konfigurera inte blockering av dem från att logga in om de senare behöver begära åtkomst till andra åtkomst paket.

1. När en extern användare förlorar sin senaste tilldelning till några åtkomst paket, om du vill ta bort deras gäst användar konto i den här katalogen, anger du **ta bort extern användare** till **Ja**.

    > [!NOTE]
    > Hantering av rättigheter tar bara bort konton som har bjudits in via rättighets hantering. Observera också att en användare kommer att blockeras från att logga in och tas bort från den här katalogen även om användaren har lagts till i resurser i den här katalogen som inte har åtkomst till paket tilldelningar. Om gästen fanns i den här katalogen innan du får åtkomst till paket tilldelningar, kommer de att vara kvar. Men om gästen har bjudits in via en tilldelning av Access-paket och efter att ha bjudits in till en OneDrive för företag-eller SharePoint Online-webbplats, kommer de fortfarande att tas bort.

1. Om du vill ta bort gäst användar kontot i den här katalogen kan du ange antalet dagar innan det tas bort. Om du vill ta bort gäst användar kontot så fort de förlorar sin senaste tilldelning till alla åtkomst paket, anger du **antalet dagar innan externa användare tas bort från katalogen** till **0**.

1. Klicka på **Save** (Spara).

## <a name="enable-a-catalog-for-external-users"></a>Aktivera en katalog för externa användare

När du skapar en [ny katalog](entitlement-management-catalog-create.md)finns det en inställning för att ge användare från externa kataloger möjlighet att begära åtkomst paket i katalogen. Om du inte vill att externa användare ska ha behörighet att begära åtkomst paket i katalogen ställer du in **aktiverat för externa användare** på **Nej**.

**Nödvändig roll:** Global administratör, användar administratör eller katalog ägare

![Fönstret ny katalog](./media/entitlement-management-shared/new-catalog.png)

Du kan också ändra den här inställningen när du har skapat katalogen.

![Redigera katalog inställningar](./media/entitlement-management-shared/catalog-edit.png)

## <a name="next-steps"></a>Nästa steg

- [För användare som inte är i din katalog](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
- [Skapa och hantera en katalog med resurser](entitlement-management-catalog-create.md)
- [Delegering och roller](entitlement-management-delegate.md)
