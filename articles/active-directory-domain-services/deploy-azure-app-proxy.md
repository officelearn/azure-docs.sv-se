---
title: Distribuera Azure AD-programproxy för Azure AD-domäntjänster | Microsoft-dokument
description: Lär dig hur du ger säker åtkomst till interna program för fjärrarbetare genom att distribuera och konfigurera Azure Active Directory Application Proxy i en hanterad Azure Active Directory Domain Services-domän
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: c1dc5216f758c2dda263e2f61b043dbde5f76604
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655509"
---
# <a name="deploy-azure-ad-application-proxy-for-secure-access-to-internal-applications-in-an-azure-ad-domain-services-managed-domain"></a>Distribuera Azure AD-programproxy för säker åtkomst till interna program i en hanterad Azure AD-domäntjänst

Med Azure AD Domain Services (Azure AD DS) kan du lyfta och flytta äldre program som körs lokalt till Azure. Azure Active Directory (AD) Application Proxy hjälper dig sedan att stödja fjärrarbetare genom att på ett säkert sätt publicera dessa interna program som är en del av en Azure AD DS-hanterad domän så att de kan nås via internet.

Om du inte har tillgång till Azure AD Application Proxy tidigare och vill veta mer läser du Så här ger du [säker fjärråtkomst till interna program](../active-directory/manage-apps/application-proxy.md).

Den här artikeln visar hur du skapar och konfigurerar en Azure AD Application Proxy-anslutningsapp för att ge säker åtkomst till program i en Azure AD DS-hanterad domän.

## <a name="before-you-begin"></a>Innan du börjar

För att kunna slutföra den här artikeln behöver du följande resurser och privilegier:

* En aktiv Azure-prenumeration.
    * Om du inte har en Azure-prenumeration [skapar du ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* En Azure Active Directory-klient som är associerad med din prenumeration, antingen synkroniserad med en lokal katalog eller en katalog med endast molnet.
    * Om det behövs [skapar du en Azure Active Directory-klientorganisation][create-azure-ad-tenant] eller [associerar en Azure-prenumeration med ditt konto][associate-azure-ad-tenant].
    * En **Azure AD Premium-licens** krävs för att använda Azure AD Application Proxy.
* En hanterad Azure Active Directory Domain Services-domän aktiverad och konfigurerad i din Azure AD-klientorganisation.
    * Om det behövs [kan du skapa och konfigurera en Azure Active Directory Domain Services-instans][create-azure-ad-ds-instance].

## <a name="create-a-domain-joined-windows-vm"></a>Skapa en domänansluten Windows VM

Om du vill dirigera trafik till program som körs i din miljö installerar du azure AD Application Proxy-anslutningskomponenten. Den här Azure AD Application Proxy-anslutningen måste installeras på virtuella Windows Server-datorer (VM) som har anslutits till azure AD DS-hanterad domän. För vissa program kan du distribuera flera servrar som var och en har anslutningen installerad. Det här distributionsalternativet ger dig större tillgänglighet och hjälper till att hantera tyngre autentiseringsbelastningar.

Den virtuella datorn som kör Azure AD Application Proxy-anslutningen måste vara på samma, eller ett peer-inkompatibla, virtuellt nätverk där du har aktiverat Azure AD DS. De virtuella datorer som sedan är värdar för de program som du publicerar med programproxyn måste också distribueras i samma virtuella Azure-nätverk.

Så här skapar du en virtuell dator för Azure AD Application Proxy-anslutningen:

1. [Skapa en anpassad organisationsenhet](create-ou.md). Du kan delegera behörigheter för att hantera den här anpassade organisationsenheten till användare inom azure AD DS-hanterade domänen. De virtuella datorerna för Azure AD Application Proxy och som kör dina program måste vara en del av den anpassade organisationsenheten, inte *standardenheten för AAD DC-datorer.*
1. [Domän ansluta till de virtuella datorerna][create-join-windows-vm], både den som kör Azure AD Application Proxy-anslutningsappen och de som kör dina program, till Azure AD DS-hanterad domän. Skapa dessa datorkonton i den anpassade organisationsenheten från föregående steg.

## <a name="download-the-azure-ad-application-proxy-connector"></a>Ladda ned Azure AD Application Proxy-anslutningsappen

Utför följande steg för att hämta Azure AD Application Proxy-anslutningen. Installationsfilen som du hämtar kopieras till appproxy-datorn i nästa avsnitt.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett användarkonto som har *behörigheter för Enterprise-administratör* i Azure AD.
1. Sök efter och välj **Azure Active Directory** högst upp i portalen och välj sedan **Enterprise-program**.
1. Välj **Programproxy** på menyn till vänster. Om du vill skapa den första kopplingen och aktivera App Proxy markerar du länken för att **hämta en anslutningsapp**.
1. På nedladdningssidan godkänner du licensvillkoren och sekretessavtalet och väljer sedan **Acceptera villkor & Ladda ned**.

    ![Ladda ned Azure AD App Proxy-anslutningsappen](./media/app-proxy/download-app-proxy-connector.png)

## <a name="install-and-register-the-azure-ad-application-proxy-connector"></a>Installera och registrera Azure AD Application Proxy-anslutningsappen

Med en virtuell dator redo att användas som Azure AD Application Proxy-anslutningsappen kopierar och kör nu installationsfilen som hämtats från Azure-portalen.

1. Kopiera installationsfilen för Azure AD Application Proxy-anslutning till den virtuella datorn.
1. Kör installationsfilen, till exempel *AADApplicationProxyConnectorInstaller.exe*. Acceptera licensvillkoren för programvara.
1. Under installationen uppmanas du att registrera anslutningen med programproxyen i din Azure AD-katalog.
   * Ange autentiseringsuppgifterna för en global administratör i din Azure AD-katalog. Azure AD-globala administratörsautentiseringsuppgifter kan skilja sig från dina Azure-autentiseringsuppgifter i portalen

        > [!NOTE]
        > Det globala administratörskonto som används för att registrera kopplingen måste tillhöra samma katalog där du aktiverar tjänsten Programproxy.
        >
        > Om Azure AD-domänen till exempel *är aaddscontoso.com*bör `admin@aaddscontoso.com` den globala administratören vara eller ett annat giltigt alias på den domänen.

   * Om förbättrad säkerhetskonfiguration i Internet Explorer är aktiverat för den virtuella datorn där du installerar anslutningen kan registreringsskärmen blockeras. Om du vill tillåta åtkomst följer du instruktionerna i felmeddelandet eller inaktiverar Förbättrad säkerhet i Internet Explorer under installationsprocessen.
   * Om anslutningsregistreringen misslyckas läser [du Felsöka programproxy](../active-directory/manage-apps/application-proxy-troubleshoot.md).
1. I slutet av installationen visas en anteckning för miljöer med en utgående proxy. Om du vill konfigurera Azure AD Application Proxy-kopplingen så att den `C:\Program Files\Microsoft AAD App Proxy connector\ConfigureOutBoundProxy.ps1`fungerar via den utgående proxyn kör du det medföljande skriptet, till exempel .
1. På sidan Programproxy i Azure-portalen visas den nya anslutningen med statusen *Aktiv*, vilket visas i följande exempel:

    ![Den nya Azure AD Application Proxy-anslutningen som visas som aktiv i Azure-portalen](./media/app-proxy/connected-app-proxy.png)

> [!NOTE]
> Om du vill ge hög tillgänglighet för program som autentiserar via Azure AD Application Proxy kan du installera kopplingar på flera virtuella datorer. Upprepa samma steg i föregående avsnitt för att installera anslutningen på andra servrar som är anslutna till azure AD DS-hanterade domänen.

## <a name="enable-resource-based-kerberos-constrained-delegation"></a>Aktivera resursbaserad Kerberos-begränsad delegering

Om du vill använda enkel inloggning till dina program med integrerad Windows-autentisering (IWA) ger du azure AD Application Proxy-anslutningsapparna behörighet att personifiera användare och skicka och ta emot token för deras räkning. Om du vill bevilja dessa behörigheter konfigurerar du Kerberos-begränsad delegering (KCD) för att ansluta resurser på den Azure AD DS-hanterade domänen. Eftersom du inte har behörighet för domänadministratörer i en Azure AD DS-hanterad domän kan traditionell KCD på kontonivå inte konfigureras på en hanterad domän. Använd i stället resursbaserad KCD.

Mer information finns i [Konfigurera Kerberos-begränsad delegering (KCD) i Azure Active Directory Domain Services](deploy-kcd.md).

> [!NOTE]
> Du måste vara inloggad på ett användarkonto som är medlem i Azure *AD DC-administratörsgruppen* i din Azure AD-klientorganisation för att kunna köra följande PowerShell-cmdletar.
>
> Datorkontona för den virtuella datorn och virtuella program-datorer måste finnas i en anpassad organisationsenhet där du har behörighet att konfigurera resursbaserad KCD. Du kan inte konfigurera resursbaserad KCD för ett datorkonto i behållaren för inbyggda *AAD DC-datorer.*

Använd [Get-ADComputer][Get-ADComputer] för att hämta inställningarna för den dator där Azure AD Application Proxy-anslutningen är installerad. Från din domänledda hanterings-VM och inloggad som användarkonto som är medlem i Azure *AD DC-administratörsgruppen* kör du följande cmdlets.

I följande exempel får du information om datorkontot *appproxy.aaddscontoso.com*. Ange ditt eget datornamn för den virtuella azure AD-programproxyn som konfigurerats i föregående steg.

```powershell
$ImpersonatingAccount = Get-ADComputer -Identity appproxy.aaddscontoso.com
```

För varje programserver som kör apparna bakom Azure AD Application Proxy använder [du Cmdleten Set-ADComputer][Set-ADComputer] PowerShell för att konfigurera resursbaserad KCD. I följande exempel beviljas Azure AD Application Proxy-anslutningsappen behörighet att använda *den appserver.aaddscontoso.com* datorn:

```powershell
Set-ADComputer appserver.aaddscontoso.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

Om du distribuerar flera Azure AD Application Proxy-kopplingar måste du konfigurera resursbaserad KCD för varje kopplingsinstans.

## <a name="next-steps"></a>Nästa steg

Med Azure AD Application Proxy integrerad med Azure AD DS, publicera program för användare att komma åt. Mer information finns i [publicera program med Azure AD Application Proxy](../active-directory/manage-apps/application-proxy-publish-azure-portal.md).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
[Get-ADComputer]: /powershell/module/addsadministration/get-adcomputer
[Set-ADComputer]: /powershell/module/addsadministration/set-adcomputer
