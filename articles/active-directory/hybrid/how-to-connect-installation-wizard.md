---
title: Kör installationsguiden för Azure AD Connect igen | Microsoft-dokument
description: I artikeln beskrivs hur installationsguiden fungerar andra gången du kör den.
keywords: Med installationsguiden för Azure AD Connect kan du konfigurera underhållsinställningar andra gången du kör den
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
ms.topic: conceptual
ms.date: 07/17/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5440c54b01f62b3ad61b355f4c622a31910a65c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261338"
---
# <a name="azure-ad-connect-sync-running-the-installation-wizard-a-second-time"></a>Synkronisering av Azure AD Connect: Kör installationsguiden en andra gång
Första gången du kör installationsguiden för Azure AD Connect går du igenom hur du konfigurerar installationen. Om du kör installationsguiden igen finns det alternativ för underhåll.

>[!IMPORTANT]
>Tänk på att du inte kan köra installationsguiden medan en synkronisering pågår.  Kontrollera att en synkronisering inte körs innan guiden startas.

Du hittar installationsguiden på startmenyn med namnet **Azure AD Connect**.

![Start-menyn](./media/how-to-connect-installation-wizard/startmenu.png)

NÃ¤s nÃ¤ startar installationsguiden visas en sida med följande alternativ:

![Sida med en lista över ytterligare uppgifter](./media/how-to-connect-installation-wizard/additionaltasks.png)

Om du har installerat ADFS med Azure AD Connect har du ännu fler alternativ. De ytterligare alternativ du har för ADFS dokumenteras i [ADFS-hantering](how-to-connect-fed-management.md#manage-ad-fs).

Markera en av aktiviteterna och klicka på **Nästa** för att fortsätta.

> [!IMPORTANT]
> Medan du har installationsguiden öppen avbryts alla åtgärder i synkroniseringsmotorn. Se till att du stänger installationsguiden så fort du har slutfört konfigurationsändringarna.
>
>

## <a name="view-current-configuration"></a>Visa aktuell konfiguration
Med det här alternativet kan du snabbt se vilka alternativ du har konfigurerat.

![Sida med en lista över alla alternativ och deras tillstånd](./media/how-to-connect-installation-wizard/viewconfig.png)

Klicka på **Föregående** om du vill gå tillbaka. Om du väljer **Avsluta**stänger du installationsguiden.

## <a name="customize-synchronization-options"></a>Anpassa synkroniseringsalternativ
Det här alternativet används för att göra ändringar i synkroniseringskonfigurationen. Du ser en delmängd av alternativ från den anpassade installationssökvägen för konfiguration. Du ser det här alternativet även om du använde expressinstallation från början.

* [Lägg till fler kataloger](how-to-connect-install-custom.md#connect-your-directories). En katalog finns i [Ta bort en koppling](how-to-connect-sync-service-manager-ui-connectors.md#delete).
* [Ändra filtrering av domän- och organisationsenhet](how-to-connect-install-custom.md#domain-and-ou-filtering).
* Ta bort gruppfiltrering.
* [Ändra valfria funktioner](how-to-connect-install-custom.md#optional-features).

De andra alternativen från den första installationen kan inte ändras och är inte tillgängliga. Dessa alternativ är:

* Ändra attributet som ska användas för userPrincipalName och sourceAnchor.
* Ändra kopplingsmetoden för objekt från olika skogar.
* Aktivera gruppbaserad filtrering.

## <a name="refresh-directory-schema"></a>Schema för uppdatera katalog
Det här alternativet används om du har ändrat schemat i en av dina lokala AD DS-skogar. Du kan till exempel ha installerat Exchange eller uppgraderat till ett Windows Server 2012-schema med enhetsobjekt. I det här fallet måste du instruera Azure AD Connect att läsa schemat igen från AD DS och uppdatera cacheminnet. Den här åtgärden återskapar också synkroniseringsreglerna. Om du lägger till Exchange-schemat läggs synkroniseringsreglerna för Exchange till i konfigurationen.

När du väljer det här alternativet visas alla kataloger i konfigurationen. Du kan behålla standardinställningen och uppdatera alla skogar eller avmarkera vissa av dem.

![Sida med en lista över alla kataloger i miljön](./media/how-to-connect-installation-wizard/refreshschema.png)

## <a name="configure-staging-mode"></a>Konfigurera mellanlagringsläge
Med det här alternativet kan du aktivera och inaktivera mellanlagringsläge på servern. Mer information om mellanlagringsläge och hur det används finns i [Operationer](how-to-connect-sync-staging-server.md).

Alternativet visar om mellanlagring för närvarande är aktiverat eller inaktiverat:  
![Alternativ som också visar det aktuella läget för mellanlagringsläge](./media/how-to-connect-installation-wizard/stagingmodecurrentstate.png)

Om du vill ändra läget markerar du det här alternativet och markerar eller avmarkerar kryssrutan.  
![Alternativ som också visar det aktuella läget för mellanlagringsläge](./media/how-to-connect-installation-wizard/stagingmodeenable.png)

## <a name="change-user-sign-in"></a>Ändra inloggning för användare
Med det här alternativet kan du ändra inloggningsmetoden för användare till och från lösenordshã¤nde, direktautentisering eller federation. Du kan inte ändra för att **inte konfigurera**.

Mer information om det här alternativet finns [i användarinloggning](plan-connect-user-signin.md#changing-the-user-sign-in-method).

## <a name="next-steps"></a>Nästa steg
* Läs mer om konfigurationsmodellen som används av Azure AD Connect-synkronisering i [Förstå deklarativ etablering](concept-azure-ad-connect-sync-declarative-provisioning.md).

**Avsnitt om översikt**

* [Synkronisering av Azure AD Connect: Förstå och anpassa synkronisering](how-to-connect-sync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md)
