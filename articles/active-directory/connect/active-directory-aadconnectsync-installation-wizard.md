---
title: Guiden Installera köra Azure AD Connect | Microsoft Docs
description: Beskriver hur installationsguiden fungerar andra gången du kör den.
keywords: Installationsguiden för Azure AD Connect kan du konfigurera inställningar för underhåll den andra gången som du kör den
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: d800214e-e591-4297-b9b5-d0b1581cc36a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: a33301904cff9a5a396eb6f641b7e3bc6b3e9801
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34592542"
---
# <a name="azure-ad-connect-sync-running-the-installation-wizard-a-second-time"></a>Azure AD Connect-synkronisering: guiden Installera körs en gång
Första gången du kör installationsguiden för Azure AD Connect vägleder den dig igenom hur du konfigurerar din installation. Om du kör installationsguiden igen erbjuder alternativ för underhåll.

Du hittar guiden i start-menyn med namnet **Azure AD Connect**.

![Start-menyn](./media/active-directory-aadconnectsync-installation-wizard/startmenu.png)

När du startar guiden, visas en sida med dessa alternativ:

![Sida med en lista över ytterligare aktiviteter](./media/active-directory-aadconnectsync-installation-wizard/additionaltasks.png)

Om du har installerat AD FS med Azure AD Connect måste ha du ytterligare alternativ. Ytterligare alternativ som du har för AD FS finns dokumenterade i [AD FS management](active-directory-aadconnect-federation-management.md#manage-ad-fs).

Välj en av uppgifter och klicka på **nästa** att fortsätta.

> [!IMPORTANT]
> När du har installationsguiden öppna har alla åtgärder i Synkroniseringsmotorn avbrutits. Kontrollera att du stänger installationsguiden så snart du har slutfört konfigurationsändringarna.
>
>

## <a name="view-current-configuration"></a>Visa aktuell konfiguration
Det här alternativet ger dig en snabb överblick över dina för tillfället konfigurerade alternativ.

![Sida med en lista över alla alternativ och deras tillstånd](./media/active-directory-aadconnectsync-installation-wizard/viewconfig.png)

Klicka på **föregående** att gå tillbaka. Om du väljer **avsluta**, du stänger installationsguiden.

## <a name="customize-synchronization-options"></a>Anpassa synkroniseringsalternativ
Det här alternativet används för att göra ändringar i konfigurationen för synkronisering. Du ser en delmängd av alternativ från installationssökvägen anpassad konfiguration. Det visas här alternativet även om du har använt Snabbinstallation först.

* [Lägga till flera kataloger](active-directory-aadconnect-get-started-custom.md#connect-your-directories). För att ta bort en katalog, se [ta bort en koppling](active-directory-aadconnectsync-service-manager-ui-connectors.md#delete).
* [Ändra domän och Organisationsenhet filtrering](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering).
* Ta bort gruppfiltrering.
* [Ändra valfria funktioner](active-directory-aadconnect-get-started-custom.md#optional-features).

Andra alternativ från den första installationen kan inte ändras och är inte tillgängliga. Dessa alternativ är:

* Ändra attributet som ska användas för userPrincipalName och sourceAnchor.
* Ändra anslutande metod för objekt från en annan skog.
* Aktivera gruppbaserade filtrering.

## <a name="refresh-directory-schema"></a>Uppdatera katalogschema
Det här alternativet används om du har ändrat schemat i en av dina lokala AD DS-skogar. Du kan till exempel har installerat Exchange eller uppgraderas till en Windows Server 2012-schemat med enhetsobjekt. I det här fallet måste du instruera Azure AD Connect att läsa schemat igen från AD DS och uppdatera sin cache. Den här åtgärden återskapar också regler för synkronisering. Om du lägger till Exchange-schemat som exempel har Sync-regler för Exchange lagts till konfigurationen.

När du väljer det här alternativet visas alla kataloger i konfigurationen. Du kan behålla standardinställningen och uppdatera alla skogar eller avmarkera vissa av dessa.

![Sida med en lista över alla kataloger i miljön](./media/active-directory-aadconnectsync-installation-wizard/refreshschema.png)

## <a name="configure-staging-mode"></a>Konfigurera mellanlagringsläge
Det här alternativet kan du aktivera och inaktivera mellanlagringsläge på servern. Mer information om mellanlagrade läge och hur de används finns i [Operations](active-directory-aadconnectsync-operations.md#staging-mode).

Alternativet visar om mellanlagring är aktiverat eller inaktiverat:  
![Alternativ som visas också det aktuella tillståndet för mellanlagringsläge](./media/active-directory-aadconnectsync-installation-wizard/stagingmodecurrentstate.png)

Välj det här alternativet om du vill ändra tillståndet och markera eller avmarkera kryssrutan.  
![Alternativ som visas också det aktuella tillståndet för mellanlagringsläge](./media/active-directory-aadconnectsync-installation-wizard/stagingmodeenable.png)

## <a name="change-user-sign-in"></a>Ändra användarinloggning
Det här alternativet kan du ändra metoden för användare till och från synkronisering av lösenords-hash, direktautentisering eller federation. Du kan inte ändra till **konfigurerar inte**.

Mer information om det här alternativet, se [användarinloggning](active-directory-aadconnect-user-signin.md#changing-the-user-sign-in-method).

## <a name="next-steps"></a>Nästa steg
* Mer information om av konfigurationsmodellen som Azure AD Connect-synkronisering i [förstå deklarativ etablering](active-directory-aadconnectsync-understanding-declarative-provisioning.md).

**Översiktsavsnitt**

* [Azure AD Connect-synkronisering: Förstå och anpassa synkronisering](active-directory-aadconnectsync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md)
