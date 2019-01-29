---
title: Köra Azure AD Connect installerar guiden | Microsoft Docs
description: Förklarar hur du installationsguiden av andra gången du kör den.
keywords: Installationsguiden för Azure AD Connect kan du konfigurera inställningar för underhåll den andra gången du kör den
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: d800214e-e591-4297-b9b5-d0b1581cc36a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: d485a0c120a8da80f404541e3090e4c76389302d
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55162267"
---
# <a name="azure-ad-connect-sync-running-the-installation-wizard-a-second-time"></a>Azure AD Connect-synkronisering: Köra installationsguiden en andra gång
Första gången du kör installationsguiden för Azure AD Connect, går vi igenom hur du konfigurerar din installation. Om du kör installationsguiden igen erbjuder alternativ för underhåll.

Du hittar guiden Installera i start-menyn med namnet **Azure AD Connect**.

![Start-menyn](./media/how-to-connect-installation-wizard/startmenu.png)

När du startar installationsguiden kan se du en sida med dessa alternativ:

![Sida med en lista över ytterligare uppgifter](./media/how-to-connect-installation-wizard/additionaltasks.png)

Om du har installerat AD FS med Azure AD Connect måste ha du ännu fler alternativ. Ytterligare alternativ som du har för AD FS finns dokumenterade i [AD FS management](how-to-connect-fed-management.md#manage-ad-fs).

Välj en av uppgifterna och klicka på **nästa** att fortsätta.

> [!IMPORTANT]
> När du har installationsguiden öppna pausas alla åtgärder i Synkroniseringsmotorn. Kontrollera att du stänger guiden när du har slutfört konfigurationsändringarna.
>
>

## <a name="view-current-configuration"></a>Visa aktuell konfiguration
Det här alternativet ger en snabb överblick över dina för tillfället konfigurerade alternativ.

![Sida med en lista över alla alternativ och deras tillstånd](./media/how-to-connect-installation-wizard/viewconfig.png)

Klicka på **föregående** gå tillbaka. Om du väljer **avsluta**, du stänger guiden.

## <a name="customize-synchronization-options"></a>Anpassa synkroniseringsalternativ
Det här alternativet används för att göra ändringar i synkroniseringskonfigurationen. Du ser en delmängd av alternativ från installationssökvägen för anpassad konfiguration. Det visas här alternativet även om du ursprungligen använde expressinstallationsfiler.

* [Lägg till flera kataloger](how-to-connect-install-custom.md#connect-your-directories). För att ta bort en katalog, se [ta bort en koppling](how-to-connect-sync-service-manager-ui-connectors.md#delete).
* [Ändra domän och Organisationsenhet filtrering](how-to-connect-install-custom.md#domain-and-ou-filtering).
* Ta bort gruppfiltrering.
* [Ändra valfria funktioner](how-to-connect-install-custom.md#optional-features).

De andra alternativen från den första installationen kan inte ändras och är inte tillgängliga. Dessa alternativ är:

* Ändra attributet som ska användas för userPrincipalName och sourceAnchor.
* Ändra sammanbinder metod för objekt från en annan skog.
* Aktivera gruppbaserad filtrering.

## <a name="refresh-directory-schema"></a>Uppdatera katalogschema
Det här alternativet används om du har ändrat schemat i någon av dina lokala AD DS-skogar. Du kan till exempel har installerat Exchange eller uppgraderat till en Windows Server 2012-schemat med enhetsobjekt. I det här fallet måste du instruera Azure AD Connect för att läsa schemat igen från AD DS och uppdatera sin cache. Den här åtgärden genererar även om Synkroniseringsregler. Om du lägger till Exchange-schemat som exempel Synkroniseringsregler för Exchange har lagts till i konfigurationen.

När du väljer det här alternativet visas alla kataloger i konfigurationen. Du kan behålla standardinställningen och uppdatera alla skogar eller avmarkera vissa av dem.

![Sida med en lista över alla kataloger i miljön](./media/how-to-connect-installation-wizard/refreshschema.png)

## <a name="configure-staging-mode"></a>Konfigurera mellanlagringsläge
Det här alternativet kan du aktivera och inaktivera mellanlagringsläge på servern. Mer information om mellanlagrade läge och hur de används finns i [Operations](how-to-connect-sync-operations.md#staging-mode).

Alternativet visar om mellanlagring är aktiverat eller inaktiverat:  
![Alternativ som visar också det aktuella tillståndet för mellanlagringsläge](./media/how-to-connect-installation-wizard/stagingmodecurrentstate.png)

Välj det här alternativet om du vill ändra tillståndet och markerar eller avmarkerar kryssrutan.  
![Alternativ som visar också det aktuella tillståndet för mellanlagringsläge](./media/how-to-connect-installation-wizard/stagingmodeenable.png)

## <a name="change-user-sign-in"></a>Ändra användarinloggning
Det här alternativet kan du ändra användarens inloggningsmetod till och från hash-synkronisering av lösenord, direktautentisering eller federation. Du kan inte ändra till **inte konfigurerar**.

Mer information om det här alternativet finns i [användarinloggning](plan-connect-user-signin.md#changing-the-user-sign-in-method).

## <a name="next-steps"></a>Nästa steg
* Mer information om av konfigurationsmodellen som används av Azure AD Connect-synkronisering i [förstå deklarativ etablering](concept-azure-ad-connect-sync-declarative-provisioning.md).

**Översiktsavsnitt**

* [Azure AD Connect-synkronisering: Förstå och anpassa synkronisering](how-to-connect-sync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md)
