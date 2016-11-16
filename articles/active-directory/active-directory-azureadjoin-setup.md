---
title: "Konfigurera Azure AD Join för dina användare| Microsoft Docs"
description: "Förklarar hur administratörer kan konfigurera Azure AD Join för din lokala katalog och enhetsregistrering."
services: active-directory
documentationcenter: 
author: femila
manager: swadhwa
editor: 
tags: azure-classic-portal
ms.assetid: bfc5d415-c918-4d8b-afee-b3f41cc28469
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/27/2016
ms.author: femila
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: aaa52cdecc09adb3b7ca53e0c0283d4203b21810


---
# <a name="setting-up-azure-ad-join-in-your-organization"></a>Konfigurera Azure AD Join i din organisation
Innan du konfigurerar Azure Active Directory Join (Azure AD Join) måste du antingen synkronisera din lokala katalog med användare till molnet eller manuellt skapa hanterade konton i Azure AD.

Detaljerade instruktioner för hur du synkroniserar dina lokala användare till Azure AD finns i [Integrera dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md).

Om du vill skapa och hantera användare manuellt i Azure AD läser du [Användarhantering i Azure AD](https://msdn.microsoft.com/library/azure/hh967609.aspx).

## <a name="set-up-device-registration"></a>Konfigurera enhetsregistrering
1. Logga in på Azure-portalen som en administratör.
2. Välj **Active Directory** i det vänstra fönstret.
3. Välj din katalog på fliken **Katalog**.
4. Välj fliken **Konfigurera**.
5. Gå till avsnittet **Enheter**.
6. Ange följande på fliken **Enheter**:  
   * **Högsta antal tillåtna enheter per användare**: Välj det högsta antalet enheter som en användare kan ha i Azure AD.  Om användarna når den här kvoten kan de inte lägga till fler enheter förrän en eller flera av deras befintliga enheter tagits bort.
   * **Kräv Multi-Factor Authentication för att ansluta enheter**: Ange om användarna måste ange en andra autentiseringsfaktor för att ansluta deras enhet till Azure AD. Mer information om Azure Multi-Factor Authentication finns i [Komma igång med Azure Multi-Factor Authentication i molnet](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md).
   * **Användare kan ansluta enheter till Azure AD**: Välj användare och grupper som ska kunna ansluta enheter till Azure AD.
   * **Ytterligare administratörer för Azure AD-anslutna enheter**: Med Azure AD Premium eller Enterprise Mobility Suite (EMS) kan du välja vilka användare som beviljas lokal administratörsbehörighet på enheten. Globala administratörer och enhetsägare beviljas lokal administratörsbehörighet som standard.

<center>![Konfigurera enhetsregistrering](./media/active-directory-azureadjoin/active-directory-aadjoin-configure-devices.png) </center>

När du har konfigurerat Azure AD Join för dina användare kan de ansluta till Azure AD via deras företagsägda eller personliga enheter.

Följande är de tre scenarier som du kan använda om du vill att dina användare ska kunna konfigurera Azure AD Join:

* Användarna ansluter en företagsägd enhet direkt till Azure AD.
* Användarna domänansluter en företagsägd enhet till lokala Active Directory och utökar sedan enheten till Azure AD.
* Användarna lägger till arbets- eller skolkonton till Windows på en personlig enhet

## <a name="additional-information"></a>Ytterligare information
* [Windows 10 för företaget: Sätt att använda enheter för arbete](active-directory-azureadjoin-windows10-devices-overview.md)
* [Utöka molnkapaciteten till Windows 10-enheter via Azure Active Directory Join](active-directory-azureadjoin-user-upgrade.md)
* [Läs mer om användningsscenarier för Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Ansluta domänanslutna enheter till miljöer med Azure AD och Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Konfigurera Azure AD Join](active-directory-azureadjoin-setup.md)




<!--HONumber=Nov16_HO2-->


