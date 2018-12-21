---
title: Konfigurera Hybrid Azure Active Directory-anslutningsenheter | Microsoft Docs
description: Lär dig att konfigurera anslutna Azure Active Directory-hybridenheter.
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
ms.topic: tutorial
ms.date: 12/04/2018
ms.author: markvi
ms.reviewer: sandeo
ms.openlocfilehash: c35c16e1414b1287fa891d1ce1f65ca8eff3d2c5
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2018
ms.locfileid: "53434961"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-federated-domains"></a>Självstudie: Konfigurera Azure Active Directory-hybridanslutningar för federerade domäner

På liknande sätt som en användare blir en enhet till en identitet som du vill skydda och också använda för att skydda dina resurser, alltid och överallt. Det kan du göra genom att överföra enheternas identiteter till Azure Active Directory på något av följande sätt:

- Azure Active Directory-anslutning
- Hybrid Azure Active Directory-anslutning
- Azure Active Directory-registrering

När du börjar använda dina enheter med Azure Active Directory maximerar du användarnas produktivitet med enkel inloggning (SSO) mellan dina molnresurser och lokala resurser. Samtidigt kan du skydda tillgången till dina resurser i molnet och lokalt med [villkorad åtkomst](../active-directory-conditional-access-azure-portal.md).

I den här självstudien lär du dig att konfigurera Hybrid Azure Active Directory-anslutning för enheter som federerade med ADFS.

> [!div class="checklist"]
> * Konfigurera Hybrid Azure Active Directory-anslutning
> * Aktivera äldre Windows-enheter
> * Verifiera registreringen
> * Felsöka


## <a name="prerequisites"></a>Nödvändiga komponenter

I den här självstudien förutsätts att du känner till:

-  [Introduktion till enhetshantering i Azure Active Directory](../device-management-introduction.md)

-  [Så här planerar du Azure Active Directory Join-hybridimplementeringen](hybrid-azuread-join-plan.md)

-  [Så här kontrollerar du Azure Active Directory Join-hybriden för dina enheter](hybrid-azuread-join-control.md)



För att kunna konfigurera scenariot i den här självstudien behöver du:

- Windows Server 2012 R2 med AD FS

- En lokal Active Directory (AD) med en schemanivå på 85 eller senare. Mer information finns i [Upgrade your Active Directory Schema](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-device-based-conditional-access-on-premises#upgrade-your-active-directory-schema) (Uppgradera Active Directory-schemat).

- [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) version 1.1.819.0 eller senare. 
 

Från och med version 1.1.819.0 tillhandahåller Azure AD Connect en guide för konfiguration av Hybrid Azure AD-anslutning. Med guiden kan du förenkla konfigurationsprocessen avsevärt. Relaterad guide:

- Konfigurerar tjänstanslutningspunkter (SCP) för enhetsregistrering

- Säkerhetskopierar ditt befintliga förlitande part-förtroende för Azure AD

- Uppdaterar anspråksreglerna i ditt Azure AD-förtroende

Konfigurationsstegen i den här artikeln baseras på guiden. Om du har en äldre version av Azure AD Connect installerad måste du uppgradera den till 1.1.819 eller senare. Om du inte kan installera den senaste versionen av Azure AD Connect kan du läsa om [hur du konfigurerar enhetsregistrering manuellt](../device-management-hybrid-azuread-joined-devices-setup.md).

Vid Azure AD-hybridkoppling måste enheterna ha åtkomst till följande Microsoft-resurser inifrån organisationens nätverk:  

- https://enterpriseregistration.windows.net
- https://login.microsoftonline.com
- https://device.login.microsoftonline.com
- Din organisations STS (federerade domäner)
- https://autologon.microsoftazuread-sso.com (Om du använder eller planerar att använda enkel inloggning)

Från och med Windows 10 1803 gäller att om den omedelbara Hybrid Azure AD-anslutningen för federerad domäner såsom AD FS misslyckas så förlitar vi oss på Azure AD Connect för att synkronisera det datorobjekt i Azure AD som därefter används för att slutföra enhetsregistreringen för Hybrid Azure AD-anslutningen.

Om din organisation kräver Internetåtkomst via en utgående proxy från och med Windows 10 1709 kan du konfigurera [proxyinställningarna på datorn med hjälp av ett grupprincipobjekt (GPO)](https://blogs.technet.microsoft.com/netgeeks/2018/06/19/winhttp-proxy-settings-deployed-by-gpo/). Om datorn kör en tidigare version av Windows än Windows 10 1709 måste du implementera Web Proxy Auto-Discovery (WPAD) för att datorer med Windows 10 ska kunna utföra enhetsregistrering med Azure AD. 

Om din organisation kräver Internetåtkomst via en autentiserad proxyserver för utgående trafik måste du se till att dina Windows 10-datorer kan autentisera till den utgående proxyn. Eftersom Windows 10-datorer utför enhetsregistrering med maskinkontext måste autentiseringen för den utgående proxyn konfigureras med maskinkontext. Kontrollera konfigurationskraven med leverantören av den utgående proxyn. 


## <a name="configure-hybrid-azure-ad-join"></a>Konfigurera Hybrid Azure Active Directory-anslutning

Om du vill konfigurera en Hybrid Azure AD-anslutning med Azure AD Connect behöver du:

- Autentiseringsuppgifterna för en global administratör för din Azure AD-klientorganisation.  

- Autentiseringsuppgifterna för företagsadministratören för varje skog.

- Autentiseringsuppgifterna för AD FS-administratören. 


**Så här konfigurerar du en Hybrid Azure AD-anslutning med Azure AD Connect:**

1. Starta Azure AD Connect och klicka på **Konfigurera**.

    ![Välkommen](./media/hybrid-azuread-join-federated-domains/11.png)

2. På sidan **Ytterligare uppgifter** väljer du **Konfigurera enhetsalternativ** och klickar på **Nästa**. 

    ![Ytterligare uppgifter](./media/hybrid-azuread-join-federated-domains/12.png)

3. På sidan **Översikt** klickar du på **Nästa**. 

    ![Översikt](./media/hybrid-azuread-join-federated-domains/13.png)

4. På sidan **Anslut till Azure AD** anger du autentiseringsuppgifterna för en global administratör för din Azure AD-klientorganisation och klickar sedan på **Nästa**.   

    ![Anslut till Azure AD](./media/hybrid-azuread-join-federated-domains/14.png)

5. På sidan **Enhetsalternativ** väljer du **Konfigurera Hybrid Azure AD-anslutning** och klickar på **Nästa**. 

    ![Enhetsalternativ](./media/hybrid-azuread-join-federated-domains/15.png)

6. På sidan **SCP** sidan gör du följande och klickar sedan på **Nästa**: 

    ![SCP](./media/hybrid-azuread-join-federated-domains/16.png)

    a. Välj skogen.

    b. Välj autentiseringstjänst. Du måste välja AD FS-server om inte din organisation uteslutande har Windows 10-klienter och du har konfigurerat dator/enhet-synkronisering eller organisationen använder SeamlessSSO.

    c. Klicka på **Lägg till** för att ange autentiseringsuppgifter för företagsadministratör.


7. På sidan **Enhetsoperativsystem** väljer du de operativsystem som används i din Active Directory-miljö och klickar sedan på **Nästa**. 

    ![Enhetsoperativsystem](./media/hybrid-azuread-join-federated-domains/17.png)

8. På sidan **Federationskonfiguration** anger du autentiseringsuppgifterna för din AD FS-administratör och klickar sedan på **Nästa**. 

    ![Federationskonfiguration](./media/hybrid-azuread-join-federated-domains/18.png)

9. På sidan **Klart att konfigurera** klickar du på **Konfigurera**. 

    ![Klart att konfigurera](./media/hybrid-azuread-join-federated-domains/19.png)

10. På sidan **Konfigurationen är klar** klickar du på **Avsluta**. 

    ![Konfigurationen är klar](./media/hybrid-azuread-join-federated-domains/20.png)




## <a name="enable-windows-down-level-devices"></a>Aktivera äldre Windows-enheter

Om några av dina domänanslutna enheter är äldre Windows-enheter måste du:

- Uppdatera enhetsinställningarna
 
- Konfigurera inställningarna för det lokala intranätet för enhetsregistrering

- Kontrollera äldre Windows-enheter 


### <a name="update-device-settings"></a>Uppdatera enhetsinställningarna 

Om du vill registrera äldre Windows-enheter måste du se till att enhetsinställningarna tillåter användarna att registrera enheter i Azure Active Directory. Du hittar den här informationen i Azure-portalen under:

`Home > [Name of your tenant] > Devices - Device settings`  


    
Följande princip måste anges till **Alla**: **Användare kan registrera sina enheter med Azure AD**

![Registrera enheter](./media/hybrid-azuread-join-federated-domains/23.png)


### <a name="configure-the-local-intranet-settings-for-device-registration"></a>Konfigurera inställningarna för det lokala intranätet för enhetsregistrering

För att slutföra Azure AD-hybridanslutningen för dina äldre Windows-enheter och undvika certifikatuppmaningar när enheter ska autentisera till Azure AD kan du skicka en princip till dina domänanslutna enheter för att lägga till följande webbadresser till den lokala intranätzonen i Internet Explorer:

- `https://device.login.microsoftonline.com`

- Din organisations säkerhetstokentjänst (STS – federerade domäner)

- `https://autologon.microsoftazuread-sso.com` (för enkel inloggning, SSO).

Dessutom kan du behöva aktivera **Tillåt uppdateringar i statusfältet via skript** i användarens lokala intranätzon.



### <a name="control-windows-down-level-devices"></a>Kontrollera äldre Windows-enheter 

För att registrera äldre Windows-enheter måste du ladda ned och installera ett Windows Installer-paket (.msi) från Download Center. Klicka [här](hybrid-azuread-join-control.md#control-windows-down-level-devices) om du vill ha mer information. 

## <a name="verify-the-registration"></a>Verifiera registreringen

För verifiering av enhetsregistreringsstatus i din Azure-klientorganisation kan du använda cmdlet:en **[Get-MsolDevice](https://docs.microsoft.com/powershell/msonline/v1/get-msoldevice)** i  **[Azure Active Directory PowerShell-modulen](/powershell/azure/install-msonlinev1?view=azureadps-2.0)**.

Följande gäller när du använder cmdlet:en **Get-MSolDevice** för att kontrollera tjänstinformation:

- Det måste finnas ett objekt med det **enhets-id** som matchar ID:t på Windows-klienten.
- Värdet för **DeviceTrustType** måste vara **Domänansluten**. Detta motsvarar statusen **Hybrid Azure AD-ansluten** på enhetssidan i Azure AD-portalen.
- Värdet för **Aktiverad** måste vara **True** och **DeviceTrustLevel** måste vara **Hanteras** för enheter som används i villkorsstyrd åtkomst. 


**Så här kontrollerar du tjänstinformationen:**

1. Öppna **Windows PowerShell** som administratör.

2. Skriv `Connect-MsolService` för att ansluta till din Azure-klientorganisation.  

3. Skriv `get-msoldevice -deviceId <deviceId>`.

6. Kontrollera att **Aktiverad** är inställd på **SANT**.





## <a name="troubleshoot-your-implementation"></a>Felsöka din implementering

Om du har problem med att slutföra Hybrid Azure AD-anslutningen för domänanslutna Windows-enheter kan du läsa:

- [Felsöka Hybrid Azure AD-anslutningen för aktuella Windows-enheter](troubleshoot-hybrid-join-windows-current.md)
- [Felsöka Hybrid Azure AD-anslutningen för äldre Windows-enheter](troubleshoot-hybrid-join-windows-legacy.md)



## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Konfigurera Hybrid Azure Active Directory-anslutning för hanterade domäner](hybrid-azuread-join-managed-domains.md)
> [Konfigurera Hybrid Azure Active Directory-anslutning manuellt](hybrid-azuread-join-manual-steps.md)




<!--Image references-->
[1]: ./media/active-directory-conditional-access-automatic-device-registration-setup/12.png
