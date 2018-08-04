---
title: 'Azure Active Directory Domain Services: Distribuera Azure Active Directory Application Proxy | Microsoft Docs'
description: Använda Azure AD Application Proxy på Azure Active Directory Domain Services hanterade domäner
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: maheshu
ms.openlocfilehash: 11967e850e9d626edf757526b8ae7d320bad1a4e
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2018
ms.locfileid: "39504353"
---
# <a name="deploy-azure-ad-application-proxy-on-an-azure-ad-domain-services-managed-domain"></a>Distribuera Azure AD Application Proxy på en Azure AD Domain Services-hanterad domän
Azure Active Directory (AD) Application Proxy kan du stödja fjärranvändare genom att publicera lokala program som kan nås via internet. Med Azure AD Domain Services kan du nu – flytta äldre program som körs lokalt till Azure Infrastructure Services. Du kan sedan publicera dessa program med hjälp av Azure AD Application Proxy för att tillhandahålla säker fjärråtkomst till användare i din organisation.

Om du inte har använt Azure AD-programproxyn kan läsa mer om den här funktionen med följande artikel: [att tillhandahålla säker fjärråtkomst till lokala program](../active-directory/manage-apps/application-proxy.md).

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Innan du börjar
Om du vill utföra åtgärderna i den här artikeln behöver du:

1. En giltig **Azure-prenumeration**.
2. En **Azure AD-katalog** -antingen synkroniseras med en lokal katalog eller en molnbaserad katalog.
3. En **Azure AD Basic eller Premium-licens** krävs för att använda Azure AD-programproxyn.
4. **Azure AD Domain Services** måste aktiveras för Azure AD-katalog. Om du inte gjort det, följer du alla uppgifter som beskrivs i den [komma igång-guiden](active-directory-ds-getting-started.md).

<br>

## <a name="task-1---enable-azure-ad-application-proxy-for-your-azure-ad-directory"></a>Uppgift 1 – Aktivera Azure AD Application Proxy för din Azure AD-katalog
Utför följande steg om du vill aktivera Azure AD-programproxyn för Azure AD-katalogen.

1. Logga in som administratör i den [Azure-portalen](http://portal.azure.com).

2. Klicka på **Azure Active Directory** att ta fram översikt över directory. Klicka på **företagsprogram**.

    ![Välj Azure AD-katalog](./media/app-proxy/app-proxy-enable-start.png)
3. Klicka på **programproxy**. Om du inte har en Azure AD Basic eller Azure AD Premium-prenumeration kan se du ett alternativ för att aktivera en utvärderingsversion. Visa/dölj **aktivera programproxyn?** till **aktivera** och klicka på **spara**.

    ![Aktivera App Proxy](./media/app-proxy/app-proxy-enable-proxy-blade.png)
4. Om du vill hämta connector klickar du på den **Connector** knappen.

    ![Hämta connector](./media/app-proxy/app-proxy-enabled-download-connector.png)
5. På sidan download godkänner licensvillkoren och sekretess avtal och klickar på den **hämta** knappen.

    ![Bekräfta download](./media/app-proxy/app-proxy-enabled-confirm-download.png)


## <a name="task-2---provision-domain-joined-windows-servers-to-deploy-the-azure-ad-application-proxy-connector"></a>Uppgift 2 – etablera domänanslutna Windows-servrar för att distribuera Azure AD-programproxy-kopplingen
Du behöver domänansluten Windows Server-datorer där du kan installera Azure AD-programproxy-kopplingen. För vissa program, kan du välja att etablera flera servrar som anslutningen är installerad. Det här distributionsalternativet får du högre tillgänglighet och hjälper dig att hantera större belastningar för autentisering.

Etablera connector-servrar på samma virtuella nätverk (eller ett anslutet/peer-kopplat virtuellt nätverk) som du har aktiverat din hanterade domän i Azure AD Domain Services. På samma sätt kan måste de servrar som hyser program som du publicerar via programproxyn vara installerad på samma Azure-nätverket.

För att etablera anslutningsservrar, Följ stegen som beskrivs i artikeln [ansluta en Windows-dator till en hanterad domän](active-directory-ds-admin-guide-join-windows-vm.md).


## <a name="task-3---install-and-register-the-azure-ad-application-proxy-connector"></a>Uppgift 3 – Installera och registrera Azure AD Application Proxy Connector
Tidigare etablerat en virtuell dator i Windows Server och anslutit till den hanterade domänen. I den här uppgiften ska installera du Azure AD Application Proxy connector på den virtuella datorn.

1. Kopiera installationspaketet för anslutningsappen till den virtuella datorn där du installerar Azure AD Web Application Proxy connector.

2. Kör **AADApplicationProxyConnectorInstaller.exe** på den virtuella datorn. Acceptera licensvillkoren för programvaran.

    ![Acceptera villkoren för installation](./media/app-proxy/app-proxy-install-connector-terms.png)
3. Under installationen uppmanas du att registrera anslutningsverktyget med programproxyn för Azure AD-katalogen.
    * Ange din **autentiseringsuppgifter som global Azure AD**. Autentiseringsuppgifterna för klienten som du är global administratör för kan skilja sig från dina Microsoft Azure-autentiseringsuppgifter.
    * Administratörskonto som används för att registrera anslutningsverktyget måste tillhöra samma katalog där du har aktiverat tjänsten Application Proxy. Till exempel om klient-domänen är contoso.com ska administratören vara admin@contoso.com eller något annat giltigt alias på den domänen.
    * Om Förbättrad säkerhetskonfiguration är aktiverad för servern där du installerar anslutningstjänsten, kan registreringsskärmen blockeras. Följ instruktionerna i felmeddelandet för att tillåta åtkomst. Kontrollera att Förbättrad säkerhetskonfiguration i Internet Explorer är inaktiverat.
    * Om registreringen av anslutningsappen inte lyckas så gå till [Felsöka programproxyn](../active-directory/manage-apps/application-proxy-troubleshoot.md).

    ![Anslutningstjänsten installerad](./media/app-proxy/app-proxy-connector-installed.png)
4. För att säkerställa att anslutningen kör fungerar korrekt, du Azure AD Application Proxy Connector felsökaren. Du bör se en lyckad rapport när du har kört felsökaren.

    ![Felsökare lyckades](./media/app-proxy/app-proxy-connector-troubleshooter.png)
5. Du bör se den nyligen installerade anslutning som listas på sidan Application proxy i Azure AD-katalogen.

    ![](./media/app-proxy/app-proxy-connector-page.png)

> [!NOTE]
> Du kan välja att installera anslutningar på flera servrar för att garantera hög tillgänglighet för att autentisera program som publicerats via Azure AD-programproxyn. Utför du samma steg som anges ovan för att installera anslutningen på andra servrar som är anslutna till din hanterade domän.
>
>

## <a name="next-steps"></a>Nästa steg
Du har konfigurerat Azure AD Application Proxy och integrerad med din hanterade domän i Azure AD Domain Services.

* **Migrera dina program till Azure virtual machines:** kan du – flytta dina program från lokala servrar till Azure-datorer som är anslutna till din hanterade domän. Detta hjälper dig att ta bort infrastrukturkostnader som körs lokalt.

* **Publicera program med Azure AD Application Proxy:** publicera program som körs på virtuella datorer i Azure med hjälp av Azure AD-programproxyn. Mer information finns i [publicera program med Azure AD Application Proxy](../active-directory/manage-apps/application-proxy-publish-azure-portal.md)


## <a name="deployment-note---publish-iwa-integrated-windows-authentication-applications-using-azure-ad-application-proxy"></a>Obs distribution – publicera IWA (integrerad Windows-autentisering)-program med hjälp av Azure AD Application Proxy
Aktivera enkel inloggning för dina program som använder integrerad Windows autentisering (IWA) genom att ge Programproxyns-kopplingar behörighet att personifiera användare, och skicka och ta emot tokens å deras vägnar. Konfigurera Kerberos-begränsad delegering (KCD) att bevilja behörigheterna som krävs för att komma åt resurser i den hanterade domänen-anslutningen. Använd mekanismen Resursbaserad KCD på hanterade domäner för ökad säkerhet.


### <a name="enable-resource-based-kerberos-constrained-delegation-for-the-azure-ad-application-proxy-connector"></a>Aktivera resursbaserade Kerberos-begränsad delegering för Azure AD Application Proxy connector
Azure Application Proxy connector ska konfigureras för Kerberos-begränsad delegering (KCD), så att den kan personifiera användare i den hanterade domänen. På en hanterad domän i Azure AD Domain Services har inte administratörsbehörighet för domänen. Därför **traditionella kontonivå KCD kan inte konfigureras på en hanterad domän**.

Använda Resursbaserad KCD som beskrivs i det här [artikeln](active-directory-ds-enable-kcd.md).

> [!NOTE]
> Du måste vara medlem i gruppen ”AAD DC-administratörer” för att administrera den hanterade domänen med AD PowerShell-cmdlets.
>
>

Använda Get-ADComputer PowerShell-cmdleten för att hämta inställningarna för den dator där Azure AD Application Proxy connector är installerad.
```powershell
$ConnectorComputerAccount = Get-ADComputer -Identity contoso100-proxy.contoso100.com
```

Därefter använda cmdlet Set-ADComputer att ställa in Resursbaserad KCD för resursservern.
```powershell
Set-ADComputer contoso100-resource.contoso100.com -PrincipalsAllowedToDelegateToAccount $ConnectorComputerAccount
```

Om du har distribuerat flera programproxyanslutningar på den hanterade domänen, måste du konfigurera Resursbaserad KCD för varje sådan connector-instans.


## <a name="related-content"></a>Relaterat innehåll
* [Azure AD Domain Services – komma igång-guiden](active-directory-ds-getting-started.md)
* [Konfigurera Kerberos-begränsad delegering på en hanterad domän](active-directory-ds-enable-kcd.md)
* [Kerberos-begränsad delegering översikt](https://technet.microsoft.com/library/jj553400.aspx)
