---
title: 'Azure Active Directory Domain Services: Distribuera Azure AD-programproxy | Microsoft Docs'
description: Använda Azure AD-programproxy på Azure Active Directory Domain Services hanterade domäner
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: iainfou
ms.openlocfilehash: b3fb5d665380bc15b61d3b4b7913a992915f9afb
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2019
ms.locfileid: "68853845"
---
# <a name="deploy-azure-ad-application-proxy-on-an-azure-ad-domain-services-managed-domain"></a>Distribuera Azure AD-programproxy på en Azure AD Domain Services hanterad domän
Azure Active Directory (AD) Application Proxy hjälper dig att stödja fjärran vändare genom att publicera lokala program som ska nås via Internet. Med Azure AD Domain Services kan du nu lyfta upp och flytta äldre program som körs lokalt till Azures infrastruktur tjänster. Du kan sedan publicera dessa program med hjälp av Azure-AD-programproxy för att ge säker fjärråtkomst till användare i din organisation.

Om du är nybörjare på Azure AD-programproxy kan du läsa mer om den här funktionen med följande artikel: [Hur du ger säker fjärråtkomst till lokala program](../active-directory/manage-apps/application-proxy.md).

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Innan du börjar
För att utföra de uppgifter som anges i den här artikeln behöver du:

1. En giltig **Azure-prenumeration**.
2. En **Azure AD-katalog** – antingen synkroniserad med en lokal katalog eller en katalog som endast är molnad.
3. En **Azure AD Basic-eller Premium-licens** krävs för att använda Azure-AD-programproxy.
4. **Azure AD Domain Services** måste vara aktiverat för Azure AD-katalogen. Om du inte har gjort det följer du alla uppgifter som beskrivs i Komma igångs [guiden](create-instance.md).

<br>

## <a name="task-1---enable-azure-ad-application-proxy-for-your-azure-ad-directory"></a>Uppgift 1 – aktivera Azure-AD-programproxy för Azure AD-katalogen
Utför följande steg för att aktivera Azure-AD-programproxy för Azure AD-katalogen.

1. Logga in som administratör i [Azure-portalen](https://portal.azure.com).

2. Klicka på **Azure Active Directory** för att Visa översikt över katalogen. Klicka på **företags program**.

3. Klicka på **Application Proxy**.

4. Klicka på kopplings knappen för att Ladda ned anslutningen.

5. Godkänn licens villkoren och sekretess avtalet på sidan hämtning och klicka på knappen **Ladda ned** .

    ![Bekräfta nedladdning](./media/app-proxy/app-proxy-enabled-confirm-download.png)


## <a name="task-2---provision-domain-joined-windows-servers-to-deploy-the-azure-ad-application-proxy-connector"></a>Uppgift 2 – etablera domänanslutna Windows-servrar för att distribuera Azure AD-programproxy-anslutningen
Du behöver domänanslutna virtuella Windows Server-datorer där du kan installera Azure AD-programproxy-anslutningen. För vissa program kan du välja att etablera flera servrar där anslutningen är installerad. Det här distributions alternativet ger dig större tillgänglighet och hjälper till att hantera kraftigare autentiserings belastningar.

Etablera anslutnings servrarna i samma virtuella nätverk (eller ett anslutet/peer-kopplat virtuellt nätverk), där du har aktiverat din Azure AD Domain Services hanterade domän. På samma sätt måste servrarna som är värdar för de program som du publicerar via programproxyn installeras på samma virtuella Azure-nätverk.

Om du vill etablera anslutnings servrar följer du de uppgifter som beskrivs i artikeln [Anslut en virtuell Windows-dator till en hanterad domän](active-directory-ds-admin-guide-join-windows-vm.md).


## <a name="task-3---install-and-register-the-azure-ad-application-proxy-connector"></a>Uppgift 3 – Installera och registrera Azure AD-programproxy-anslutningen
Tidigare etablerade du en virtuell Windows Server-dator och anslöt den till den hanterade domänen. I den här uppgiften installerar du Azure AD-programproxy-anslutningen på den virtuella datorn.

1. Kopiera anslutnings programmets installations paket till den virtuella dator där du installerar Azure AD Web Application Proxy Connector.

2. Kör **AADApplicationProxyConnectorInstaller. exe** på den virtuella datorn. Godkänn licens villkoren för program varan.

    ![Godkänn villkoren för installation](./media/app-proxy/app-proxy-install-connector-terms.png)
3. Under installationen uppmanas du att registrera anslutningen med programproxyn för Azure AD-katalogen.
   * Ange dina **autentiseringsuppgifter för Azure AD**-programadministratören. Din program administratörs klient kan skilja sig från dina Microsoft Azure autentiseringsuppgifter.
   * Det administratörs konto som används för att registrera anslutningen måste tillhöra samma katalog där du aktiverade tjänsten Application Proxy. Om klient domänen exempelvis är contoso.com, ska administratören vara admin@contoso.com eller något annat giltigt alias i domänen.
   * Om förbättrad säkerhets konfiguration i Internet Explorer är aktive rad för den server där du installerar anslutningen, kan registrerings skärmen blockeras. Följ instruktionerna i fel meddelandet för att tillåta åtkomst. Kontrollera att Förbättrad säkerhetskonfiguration i Internet Explorer är inaktiverat.
   * Om registreringen av anslutningsappen inte lyckas så gå till [Felsöka programproxyn](../active-directory/manage-apps/application-proxy-troubleshoot.md).

     ![Anslutning installerad](./media/app-proxy/app-proxy-connector-installed.png)
4. Se till att kopplingen fungerar som den ska genom att köra fel sökaren för Azure AD-programproxy Connector. Du bör se en lyckad rapport när du har kört fel sökaren.

    ![Fel sökning har slutförts](./media/app-proxy/app-proxy-connector-troubleshooter.png)
5. Du bör se den nyligen installerade anslutningen som visas på sidan Application Proxy i Azure AD-katalogen.

    ![I Azure Portal visas den installerade anslutningen som tillgänglig](./media/app-proxy/app-proxy-connector-page.png)

> [!NOTE]
> Du kan välja att installera anslutningar på flera servrar för att garantera hög tillgänglighet för att autentisera program som publicerats via Azure-AD-programproxy. Utför samma steg som ovan för att installera anslutningen på andra servrar som är anslutna till din hanterade domän.
>
>

## <a name="next-steps"></a>Nästa steg
Du har konfigurerat Azure-AD-programproxy och integrerat det med din Azure AD Domain Services hanterade domän.

* **Migrera dina program till virtuella Azure-datorer:** Du kan lyfta upp och flytta dina program från lokala servrar till Azure virtuella datorer som är anslutna till din hanterade domän. Detta hjälper dig att få bort infrastruktur kostnaderna för att köra lokala servrar.

* **Publicera program med Azure AD-programproxy:** Publicera program som körs på dina virtuella Azure-datorer med hjälp av Azure-AD-programproxy. Mer information finns i [Publicera program med hjälp av Azure AD-programproxy](../active-directory/manage-apps/application-proxy-publish-azure-portal.md)


## <a name="deployment-note---publish-iwa-integrated-windows-authentication-applications-using-azure-ad-application-proxy"></a>Distributions anteckning – publicera IWA-program (Integrated Windows-autentisering) med Azure AD-programproxy
Aktivera enkel inloggning till dina program med hjälp av integrerad Windows-autentisering (IWA) genom att bevilja Application Proxy Connector-behörighet för att personifiera användare och skicka och ta emot token för deras räkning. Konfigurera Kerberos-begränsad delegering (KCD) för anslutningen för att ge de behörigheter som krävs för att få åtkomst till resurser på den hanterade domänen. Använd den Resource-baserade KCD-mekanismen på hanterade domäner för ökad säkerhet.


### <a name="enable-resource-based-kerberos-constrained-delegation-for-the-azure-ad-application-proxy-connector"></a>Aktivera resurs baserad Kerberos-begränsad delegering för Azure AD-programproxy-anslutningen
Azure Application Proxy-anslutningen ska konfigureras för Kerberos-begränsad delegering (KCD), så att den kan personifiera användare på den hanterade domänen. I en Azure AD Domain Services hanterad domän har du inte domän administratörs behörighet. Därför **kan inte traditionella KCD på konto nivå konfigureras på en hanterad domän**.

Använd Resource based KCD enligt beskrivningen i den här [artikeln](deploy-kcd.md).

> [!NOTE]
> Du måste vara medlem i gruppen "AAD DC-administratörer" för att administrera den hanterade domänen med AD PowerShell-cmdletar.
>
>

Använd PowerShell-cmdleten Get-ADComputer för att hämta inställningarna för den dator där Azure AD-programproxy-anslutningen är installerad.
```powershell
$ConnectorComputerAccount = Get-ADComputer -Identity contoso100-proxy.contoso100.com
```

Därefter använder du cmdleten Set-ADComputer för att konfigurera resurs-baserade KCD för resurs servern.
```powershell
Set-ADComputer contoso100-resource.contoso100.com -PrincipalsAllowedToDelegateToAccount $ConnectorComputerAccount
```

Om du har distribuerat flera Application Proxy-kopplingar på din hanterade domän måste du konfigurera resursbaserade KCD för varje sådan anslutnings instans.


## <a name="related-content"></a>Relaterat innehåll
* [Azure AD Domain Services-Komma igång guide](create-instance.md)
* [Konfigurera Kerberos-begränsad delegering på en hanterad domän](deploy-kcd.md)
* [Översikt över Kerberos-begränsad delegering](https://technet.microsoft.com/library/jj553400.aspx)
