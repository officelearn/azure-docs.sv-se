---
title: Hur du konfigurerar hybrid Azure Active Directory-anslutna enheter | Microsoft Docs
description: Lär dig mer om att konfigurera hybrid Azure Active Directory-anslutna enheter.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2018
ms.author: markvi
ms.reviewer: sandeo
ms.openlocfilehash: 36537aacce0180f9b37fb8b49f301fb1f5954d1c
ms.sourcegitcommit: ebb460ed4f1331feb56052ea84509c2d5e9bd65c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/24/2018
ms.locfileid: "42918437"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-managed-domains"></a>Självstudie: Konfigurera hybrid Azure Active Directory-anslutning för hanterade domäner

På liknande sätt till en användare blir en enhet en annan identitet som du vill skydda och även använda för att skydda dina resurser på någon tid och plats. Du kan göra det här målet genom att föra identiteter på dina enheter till Azure AD med någon av följande metoder:

- Azure AD-anslutning
- Hybrid Azure AD-anslutning
- Azure AD-registrering

Genom att ta dina enheter till Azure AD, maximera användarnas produktivitet genom att använda enkel inloggning (SSO) mellan dina molnresurser och lokala resurser. På samma gång, kan du skydda åtkomst till molnet och lokala resurser med [villkorlig åtkomst](../active-directory-conditional-access-azure-portal.md).

Lär dig hur du konfigurerar hybrid Azure AD-anslutning för enheter i hanterade domäner i den här självstudien.

> [!div class="checklist"]
> * Konfigurera hybrid Azure AD-anslutning
> * Aktivera Windows äldre enheter
> * Kontrollera domänanslutna enheter 
> * Felsöka 


## <a name="prerequisites"></a>Förutsättningar

Den här självstudien förutsätter att du är bekant med:
    
-  [Introduktion till hantering av enheter i Azure Active Directory](../device-management-introduction.md)
    
-  [Så här planerar du Azure Active Directory Join-hybridimplementeringen](hybrid-azuread-join-plan.md)

-  [Så här kontrollerar du Azure Active Directory Join-hybriden för dina enheter](hybrid-azuread-join-control.md)
  

Om du vill konfigurera scenariot i den här artikeln, måste den [senaste versionen av Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) (1.1.819.0 eller högre) som ska installeras. 

Kontrollera att Azure AD Connect har synkroniserats datorobjekt av de enheter som du vill ska vara hybrid Azure AD-anslutna till Azure AD. Anslut även om datorobjekt som tillhör specifika organisationsenheter (OU) dessa organisationsenheter måste konfigureras för synkronisering i Azure AD.

Från och med version 1.1.819.0, ger Azure AD Connect dig en guide för att konfigurera hybrid Azure AD-anslutning. Guiden kan du avsevärt förenkla konfigurationsprocessen. Relaterade guiden konfigurerar tjänstanslutningspunkter (SCP) för registrering av enheten.

Konfigurationsstegen i den här artikeln baseras på den här guiden. 

Hybrid Azure AD-anslutning kräver att enheter ska ha åtkomst till följande Microsoft-resurser från i din organisations nätverk:  

- https://enterpriseregistration.windows.net
- https://login.microsoftonline.com
- https://device.login.microsoftonline.com
- https://autologon.microsoftazuread-sso.com (Om du använder eller planerar att använda sömlös enkel inloggning)

Om din organisation kräver åtkomst till Internet via en utgående proxy, kan från och med Windows 10 1709, du konfigurera proxyinställningarna på datorn med hjälp av ett grupprincipobjekt (GPO). Om du använder något som är äldre än Windows 10 1709, måste du implementera Web Proxy Auto-Discovery (WPAD) för att aktivera Windows 10-datorer att göra enhetsregistrering med Azure AD. 

Om din organisation kräver åtkomst till Internet via en autentiserad proxyserver för utgående, måste du se till att din Windows 10-datorer kan autentisera till utgående proxy. Eftersom Windows 10-datorer kör enhetsregistrering med hjälp av datorns kontext, är det nödvändigt att konfigurera utgående proxy-autentisering med hjälp av datorns kontext. Följ med leverantören av utgående proxy på konfigurationskrav. 



## <a name="configure-hybrid-azure-ad-join"></a>Konfigurera hybrid Azure AD-anslutning

Om du vill konfigurera en hybrid Azure AD-anslutning med Azure AD Connect, behöver du:

- Autentiseringsuppgifterna för en global administratör för Azure AD-klienten.  

- Administratörsautentiseringsuppgifterna för var och en av skogarna.


**Konfigurera en hybrid Azure AD-anslutning med Azure AD Connect:**

1. Starta Azure AD Connect och klickar sedan på **konfigurera**.

    ![Välkommen](./media/hybrid-azuread-join-managed-domains/11.png)

2. På den **ytterligare uppgifter** väljer **konfigurera Enhetsalternativ**, och klicka sedan på **nästa**. 

    ![Ytterligare uppgifter](./media/hybrid-azuread-join-managed-domains/12.png)

3. På den **översikt** klickar du på **nästa**. 

    ![Översikt](./media/hybrid-azuread-join-managed-domains/13.png)

4. På den **Anslut till Azure AD** ange autentiseringsuppgifterna för en global administratör för Azure AD-klienten.  

    ![Anslut till Azure AD](./media/hybrid-azuread-join-managed-domains/14.png)

5. På den **Enhetsalternativ** väljer **konfigurera Hybrid Azure AD-anslutning**, och klicka sedan på **nästa**. 

    ![Enhetsalternativ](./media/hybrid-azuread-join-managed-domains/15.png)

6. På den **SCP** för varje skog som du vill att Azure AD Connect för att konfigurera SCP: N, utför följande steg och klicka sedan på **nästa**: 

    ![Tjänstanslutningspunkt](./media/hybrid-azuread-join-managed-domains/16.png)

    a. Markera den.

    b. Välj Autentiseringstjänsten.

    c. Klicka på **Lägg till** att ange autentiseringsuppgifterna som företagsadministratör.


7. På den **enhetsoperativsystem** sidan, markera de operativsystem som används av enheter i din Active Directory-miljö och klicka sedan på **nästa**. 

    ![Enhetens operativsystem](./media/hybrid-azuread-join-managed-domains/17.png)


8. På den **redo att konfigurera** klickar du på **konfigurera**. 

    ![Klart att konfigurera](./media/hybrid-azuread-join-managed-domains/19.png)

9. På den **Configuration slutföra** klickar du på **avsluta**. 

    ![Konfigurationen är klar](./media/hybrid-azuread-join-managed-domains/20.png)




## <a name="enable-windows-down-level-devices"></a>Aktivera Windows äldre enheter

Om några av dina domänanslutna enheter är Windows äldre enheter, måste du:

- Uppdatera enhetsinställninga
 
- Konfigurera inställningar för lokalt intranät för registrering av enheten

### <a name="update-device-settings"></a>Uppdatera enhetsinställninga 

Om du vill registrera Windows äldre enheter, måste du se till att enhetsinställningarna så att användarna kan registrera enheter i Azure AD är inställda. Du kan hitta den här inställningen under Azure-portalen:

`Home > [Name of your tenant] > Devices - Device settings`  


    
Följande princip måste anges till **alla**: **användare kan registrera sina enheter med Azure AD**

![Registrera enheter](media/hybrid-azuread-join-managed-domains/23.png)



### <a name="configure-the-local-intranet-settings-for-device-registration"></a>Konfigurera inställningar för lokalt intranät för registrering av enheten

Slutförd hybrid Azure AD join för dina äldre Windows-enheter och för att undvika certifikat anvisningarna när enheter ska autentiseras autentisera till Azure AD som du kan skicka en princip till dina domänanslutna enheter att lägga till följande webbadresser till det lokala intranätet zonen i Internet Explorer:

- `https://device.login.microsoftonline.com`

- `https://autologon.microsoftazuread-sso.com`.

Dessutom kan du behöva aktivera **tillåta uppdateringar till statusfältet via skript** i användarens lokala intranätzonen.

## <a name="verify-the-registration"></a>Kontrollera registreringen

Du kan använda för att verifiera enheten registreringstillståndet i din Azure-klient, den **[Get-MsolDevice](https://docs.microsoft.com/powershell/msonline/v1/get-msoldevice)** cmdlet i den  **[Azure Active Directory PowerShell-modulen](/powershell/azure/install-msonlinev1?view=azureadps-2.0)**.

När du använder den **Get-MSolDevice** cmdlet för att kontrollera service:

- Ett objekt med den **enhets-id** som matchar ID på Windows klient måste finnas.
- Värdet för **DeviceTrustType** måste vara **domänanslutna**. Detta motsvarar den **Hybrid Azure AD-ansluten** tillstånd på enhetssidan i Azure AD-portalen.
- Värdet för **aktiverad** måste vara **SANT** för enheter som används i villkorlig åtkomst. 


**Att kontrollera service:**

1. Öppna **Windows PowerShell** som administratör.

2. Typ `Connect-MsolService` att ansluta till din Azure-klient.  

3. Skriv `get-msoldevice -deviceId <deviceId>`.

6. Kontrollera att **aktiverad** är inställd på **SANT**.





## <a name="troubleshoot-your-implementation"></a>Felsöka din implementering

Om du har problem med att slutföra hybrid Azure AD join för domän anslutna Windows-enheter, se:

- [Felsöka Hybrid Azure AD-anslutning för befintliga Windows-enheter](troubleshoot-hybrid-join-windows-current.md)
- [Felsöka Hybrid Azure AD-anslutning för Windows äldre enheter](troubleshoot-hybrid-join-windows-legacy.md)


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Konfigurera hybrid Azure Active Directory-anslutning för federerade domäner](hybrid-azuread-join-federated-domains.md)
> [konfigurera hybrid Azure Active Directory-koppling manuellt](hybrid-azuread-join-manual-steps.md)

