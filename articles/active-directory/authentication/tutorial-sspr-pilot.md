---
title: Aktivera en Azure AD SSPR-pilot
description: I den här självstudien aktiverar du självåterställning av lösenord med Azure AD för en pilotgrupp med användare
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6e1095cc2c5937fa5de762f91a9830161b8d2a5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60413941"
---
# <a name="tutorial-complete-an-azure-ad-self-service-password-reset-pilot-roll-out"></a>Självstudier: Utföra en pilotlansering av självbetjäning av lösenordsåterställning för Azure AD

I den här självstudien aktiverar du en pilotlansering av självåterställning av lösenord (SSPR) med Azure AD i din organisation och testar med ett icke-administratörskonto.

Det är viktigt att all testning av självåterställning av lösenord utförs med icke-administratörskonton. Microsoft hanterar principen för lösenordsåterställning för administratörskonton och kräver användning av starkare autentiseringsmetoder. Den här principen tillåter inte användningen av säkerhetsfrågor och svar, och kräver användning av två metoder för återställning.

> [!div class="checklist"]
> * Aktivera lösenordsåterställning via självbetjäning
> * Testa SSPR som användare

## <a name="prerequisites"></a>Nödvändiga komponenter

* Ett konto som global administratör

## <a name="enable-self-service-password-reset"></a>Aktivera lösenordsåterställning via självbetjäning

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett konto som global administratör.
1. Bläddra till **Azure Active Directory** och välj **Lösenordsåterställning**.
1. Börja med en pilotgrupp genom att aktivera självåterställning av lösenord för en delmängd av användarna i din organisation.
   * På sidan **Egenskaper** går du till alternativet **Self Service Password Reset Enabled** (Självåterställning av lösenord aktiverat), väljer **Valda** och väljer en pilotgrupp.
      * Endast medlemmar av den specifika Azure AD-gruppen som du utser kan använda SSPR-funktionen. Vi rekommenderar att du definierar en grupp användare och använder den här inställningen när du distribuerar funktionen för ett ”proof of concept” (POC). Kapsling av säkerhetsgrupper stöds här.
      * Se till att användarna i den grupp du valde har licensierats på rätt sätt.
   * Klicka på **Spara**
1. På sidan **Autentiseringsmetoder**
   * Ange den **antal metoder som krävs för att återställa** till **1**
   * Välj vilka **Metoder som är tillgängliga för användare** som organisationen vill tillåta. Den här självstudien markerar du kryssrutorna till aktivera **e-post**, **mobiltelefon**, **Arbetstelefon**, **mobilapp (förhandsversion)** och  **Kod för mobilapp (förhandsversion)**.
   * Klicka på **Spara**
1. På sidan **Registrering**
   * Välj **Ja** för att **kräva att användare registrerar sig vid inloggning**.
   * Ställ in **Antal dagar innan användare uppmanas att bekräfta sin autentiseringsinformation** på **180**.
   * Klicka på **Spara**
1. På sidan **Meddelanden**
   * Ställ in alternativet **Meddela användare om lösenordsåterställning** på **Ja**.
   * Ställ in **Meddela alla administratörer när andra administratörer återställer sina lösenord** på **Ja**.
1. På sidan **Anpassning**
   * Microsoft rekommenderar att du ställer in **Anpassa helpdesk-länk** till **Ja** och anger antingen en e-postadress eller en URL för den webbplats där användarna kan få ytterligare hjälp från din organisation i fältet **Anpassad e-postadress eller URL till helpdesk**.
   * I den här självstudien lämnar vi **Anpassa helpdesk-länk** inställt på **Nej**.

Självåterställning av lösenord har nu konfigurerats för molnanvändarna i din pilotgrupp.

## <a name="test-sspr-as-a-user"></a>Testa SSPR som användare

Testa självåterställning av lösenord med hjälp av en testanvändare som inte är administratör och som är medlem i pilotgruppen. **Tänk på att om du använder ett konto som har administratörsroller tilldelade så kan autentiseringsmetoderna och numren skilja sig från det som du valde eftersom Microsoft hanterar administratörsprincipen.**

1. Öppna ett nytt webbläsarfönster i InPrivate- eller inkognitoläge.
1. Använd en testanvändare för att registrera dig för självåterställning av lösenord med hjälp av registreringsportalen på [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup).
1. Använd samma testanvändare för att bläddra till portalen för självåterställning av lösenord [https://aka.ms/sspr](https://aka.ms/sspr) och försök att återställa ditt lösenord med hjälp av den information som du angav i föregående steg.
1. Du bör kunna återställa ditt lösenord.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte längre vill använda funktioner som du har konfigurerat i den här självstudien kan du göra följande ändring.

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Bläddra till **Azure Active Directory** och välj **Lösenordsåterställning**.
1. På sidan **Egenskaper** går du till alternativet **Self Service Password Reset Enabled** (Självåterställning av lösenord aktiverat) och väljer **Inget**.
1. Klicka på **Spara**

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du aktiverat självåterställning av lösenord med Azure AD. Fortsätt till nästa självstudie för att se hur en lokal Active Directory Domain Services-infrastruktur kan integreras i självåterställningen av lösenord.

> [!div class="nextstepaction"]
> [Aktivera lokal SSPR-tillbakaskrivningsintegration](tutorial-enable-writeback.md)
