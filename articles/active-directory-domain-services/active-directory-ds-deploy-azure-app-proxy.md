---
title: 'Azure Active Directory Domain Services: Distribuera Azure Active Directory Application Proxy | Microsoft Docs'
description: Använda Azure AD Application Proxy på Azure Active Directory Domain Services hanterade domäner
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: maheshu
ms.openlocfilehash: 13141549b5b1b77f45dd6fa1c7560b9f9d0e9b72
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2018
---
# <a name="deploy-azure-ad-application-proxy-on-an-azure-ad-domain-services-managed-domain"></a>Distribuera Azure AD Application Proxy på en Azure AD Domain Services-hanterad domän
Azure Active Directory (AD) Application Proxy hjälper dig att stöd för fjärranvändare genom att publicera lokala program som kan nås via internet. Med Azure AD Domain Services kan du nu lift-och-SKIFT äldre program som körs lokalt Azure Infrastructure Services. Du kan sedan publicera dessa program med Azure AD Application Proxy för att tillhandahålla säker fjärråtkomst till användare i din organisation.

Om du har använt Azure AD Application Proxy, mer information om den här funktionen med följande artikel: [ge säker fjärråtkomst till lokala program](../active-directory/manage-apps/application-proxy.md).


## <a name="before-you-begin"></a>Innan du börjar
Om du vill utföra åtgärderna i den här artikeln behöver du:

1. En giltig **Azure-prenumeration**.
2. En **Azure AD-katalog** -antingen synkroniseras med en lokal katalog eller en molnbaserad katalog.
3. En **Azure AD Basic eller Premium-licens** krävs för att använda Azure AD Application Proxy.
4. **Azure AD Domain Services** måste vara aktiverat för Azure AD-katalog. Om du inte gjort det, följer du de uppgifter som beskrivs i den [Kom igång-guiden](active-directory-ds-getting-started.md).

<br>

## <a name="task-1---enable-azure-ad-application-proxy-for-your-azure-ad-directory"></a>Uppgift 1 – Aktivera Azure AD Application Proxy för din Azure AD-katalog
Utför följande steg om du vill aktivera Azure AD Application Proxy för Azure AD-katalogen.

1. Logga in som administratör i den [Azure-portalen](http://portal.azure.com).

2. Klicka på **Azure Active Directory** så att översikt över directory. Klicka på **företagsprogram**.

    ![Välj Azure AD-katalog](./media/app-proxy/app-proxy-enable-start.png)
3. Klicka på **programproxy**. Om du inte har en Azure AD Basic eller Azure AD Premium-prenumeration kan se du ett alternativ för att aktivera en utvärderingsversion. Växla **aktivera Application Proxy?** till **aktivera** och på **spara**.

    ![Aktivera appen Proxy](./media/app-proxy/app-proxy-enable-proxy-blade.png)
4. Hämta kopplingen, klicka på den **Connector** knappen.

    ![Hämta connector](./media/app-proxy/app-proxy-enabled-download-connector.png)
5. Acceptera licensvillkoren och sekretesspolicyn avtalet på hämtningssidan, och klicka på den **hämta** knappen.

    ![Bekräfta hämtning](./media/app-proxy/app-proxy-enabled-confirm-download.png)


## <a name="task-2---provision-domain-joined-windows-servers-to-deploy-the-azure-ad-application-proxy-connector"></a>Uppgift 2 – etablera domänanslutna Windows-servrar för att distribuera Azure AD Application Proxy connector
Du behöver domänanslutna Windows Server virtuella datorer som du installerar Azure AD Application Proxy connector. För vissa program, kan du etablera flera servrar där kopplingen är installerad. Det här distributionsalternativet ger dig större tillgänglighet och hjälper dig att hantera större belastningar för autentisering.

Etablera anslutningstjänstservrarna på samma virtuella nätverk (eller ett virtuellt nätverk anslutet/peerkoppla) som du har aktiverat din Azure AD Domain Services-hanterad domän. På liknande sätt kan måste de servrar som hyser program som du publicerar via Application Proxy installeras på samma virtuella Azure-nätverket.

Följ de uppgifter som beskrivs i artikel med rubriken för att etablera anslutningsservrar [Anslut en Windows-dator till en hanterad domän](active-directory-ds-admin-guide-join-windows-vm.md).


## <a name="task-3---install-and-register-the-azure-ad-application-proxy-connector"></a>Uppgift 3 – Installera och registrera Azure AD Application Proxy Connector
Tidigare etableras en virtuell dator med Windows Server och anslutna till den hanterade domänen. I det här steget installera Azure AD Application Proxy connector på den virtuella datorn.

1. Kopiera installationspaketet anslutningen till den virtuella datorn som du installerar Azure AD Web Application Proxy connector.

2. Kör **AADApplicationProxyConnectorInstaller.exe** på den virtuella datorn. Acceptera licensvillkoren för programvara.

    ![Acceptera villkoren för installation](./media/app-proxy/app-proxy-install-connector-terms.png)
3. Under installationen uppmanas du att registrera anslutningsverktyget med programproxyn för din Azure AD-katalog.
    * Ange din **autentiseringsuppgifter för global administratör för Azure AD**. Autentiseringsuppgifterna för klienten som du är global administratör för kan skilja sig från dina Microsoft Azure-autentiseringsuppgifter.
    * Administratörskontot som används för att registrera anslutningsverktyget måste tillhöra samma katalog som du har aktiverat tjänsten Application Proxy. Till exempel om klientdomänen är contoso.com, administratören vara admin@contoso.com eller ett annat giltigt alias för domänen.
    * Om Förbättrad säkerhetskonfiguration är aktiverad för servern där du installerar anslutningen kan registreringsskärmen blockeras. Följ instruktionerna i felmeddelandet för att tillåta åtkomst. Kontrollera att Förbättrad säkerhetskonfiguration i Internet Explorer är inaktiverat.
    * Om registreringen av anslutningsappen inte lyckas så gå till [Felsöka programproxyn](../active-directory/active-directory-application-proxy-troubleshoot.md).

    ![Anslutning är installerad](./media/app-proxy/app-proxy-connector-installed.png)
4. Om du vill kontrollera anslutningen kör fungerar korrekt, Azure AD Application Proxy Connector felsökaren. Du bör se en lyckad rapport efter felsökaren.

    ![Felsökaren lyckades](./media/app-proxy/app-proxy-connector-troubleshooter.png)
5. Du bör se nyligen installerade koppling som visas på sidan Application proxy i Azure AD-katalogen.

    ![](./media/app-proxy/app-proxy-connector-page.png)

> [!NOTE]
> Du kan välja att installera kopplingar på flera servrar för att garantera hög tillgänglighet för att autentisera program som publicerats via Azure AD Application Proxy. Utför stegen ovan för att installera anslutningen på andra servrar som är anslutna till din hanterade domän.
>
>

## <a name="next-steps"></a>Nästa steg
Du har konfigurerat Azure AD Application Proxy och integreras med din Azure AD Domain Services-hanterad domän.

* **Migrera dina program på virtuella Azure-datorer:** kan du lift-och-SKIFT dina program från lokala servrar till Azure virtuella datorer som är anslutna till din hanterade domän. Detta hjälper dig att ta bort infrastrukturkostnader körs lokalt.

* **Publicera program med Azure AD Application Proxy:** publicera program som körs på din virtuella Azure-datorer med hjälp av Azure AD Application Proxy. Mer information finns i [publicera program med Azure AD Application Proxy](../active-directory/application-proxy-publish-azure-portal.md)


## <a name="deployment-note---publish-iwa-integrated-windows-authentication-applications-using-azure-ad-application-proxy"></a>Obs distribution – publicera IWA (integrerad Windows-autentisering)-program med hjälp av Azure AD Application Proxy
Aktivera enkel inloggning till dina program med integrerad autentisering IWA (Windows) genom att ge Application Proxy Connectors behörighet att personifiera användare, och skicka och ta emot token åt. Konfigurera Kerberos-begränsad delegering (KCD) att bevilja behörigheterna som krävs för åtkomst till resurser på den hanterade domänen-anslutningen. Använda mekanismen resursbaserade KCD på hanterade domäner för ökad säkerhet.


### <a name="enable-resource-based-kerberos-constrained-delegation-for-the-azure-ad-application-proxy-connector"></a>Aktivera resursbaserade Kerberos-begränsad delegering för Azure AD Application Proxy connector
Azure Application Proxy connector ska konfigureras för Kerberos-begränsad delegering (KCD), så den kan personifiera användare på den hanterade domänen. På en hanterad domän i Azure AD Domain Services har inte administratörsbehörighet för domänen. Därför **traditionella kontonivå KCD kan inte konfigureras på en hanterad domän**.

Använda resursbaserade KCD som beskrivs i det här [artikel](active-directory-ds-enable-kcd.md).

> [!NOTE]
> Du måste vara medlem i gruppen AAD DC-administratörer för att administrera hanterade domänen med AD PowerShell-cmdlets.
>
>

Använd Get-ADComputer PowerShell-cmdlet för att hämta inställningarna för den dator där Azure AD Application Proxy connector är installerad.
```powershell
$ConnectorComputerAccount = Get-ADComputer -Identity contoso100-proxy.contoso100.com
```

Därefter, Använd cmdlet Set-ADComputer att ställa in resursbaserade KCD för resursservern.
```powershell
Set-ADComputer contoso100-resource.contoso100.com -PrincipalsAllowedToDelegateToAccount $ConnectorComputerAccount
```

Om du har distribuerat flera Application Proxy-kopplingar på din hanterade domän, måste du konfigurera resursbaserade KCD för varje sådan connector-instans.


## <a name="related-content"></a>Relaterat innehåll
* [Azure AD Domain Services - komma igång-guide](active-directory-ds-getting-started.md)
* [Konfigurera Kerberos-begränsad delegering på en hanterad domän](active-directory-ds-enable-kcd.md)
* [Kerberos-begränsad delegering: översikt](https://technet.microsoft.com/library/jj553400.aspx)
