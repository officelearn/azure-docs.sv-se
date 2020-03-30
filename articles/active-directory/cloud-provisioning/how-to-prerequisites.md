---
title: Förutsättningar för Azure AD Connect-molnetablering i Azure AD
description: I den här artikeln beskrivs de förutsättningar och maskinvarukrav du behöver för molnetablering.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45648170f69d513b15e79cdd76f56e66bbc88bfa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332073"
---
# <a name="prerequisites-for-azure-ad-connect-cloud-provisioning"></a>Förutsättningar för Azure AD Connect-molnetablering
Den här artikeln innehåller vägledning om hur du väljer och använder Azure Active Directory (Azure AD) Connect-molnetablering som identitetslösning.



## <a name="cloud-provisioning-agent-requirements"></a>Krav på distributionsagent för moln
Du behöver följande för att kunna använda Azure AD Connect-molnetablering:
    
- Ett globalt administratörskonto för din Azure AD-klient som inte är gästanvändare.
- En lokal server för etableringsagenten med Windows 2012 R2 eller senare.
- Lokala brandväggskonfigurationer.

>[!NOTE]
>Etableringsagenten kan för närvarande endast installeras på engelska servrar. Installera ett engelskt språkpaket på en icke-engelsk server är inte en giltig lösning och kommer att resultera i att agenten inte installeras. 

Resten av dokumentet innehåller steg-för-steg-instruktioner för dessa förutsättningar.

### <a name="in-the-azure-active-directory-admin-center"></a>I administrationscentret för Azure Active Directory

1. Skapa ett globalt administratörskonto endast för molnet på din Azure AD-klientorganisation. På så sätt kan du hantera konfigurationen av din klient om dina lokala tjänster misslyckas eller blir otillgängliga. Läs mer om hur du [lägger till ett globalt administratörskonto för molnet](../active-directory-users-create-azure-portal.md). Avsluta det här steget är viktigt för att säkerställa att du inte blir utelåst från din klient.
1. Lägg till ett eller flera [anpassade domännamn](../active-directory-domains-add-azure-portal.md) i din Azure AD-klientorganisation. Användarna kan logga in med ett av dessa domännamn.

### <a name="in-your-directory-in-active-directory"></a>I din katalog i Active Directory

Kör [IdFix-verktyget](https://docs.microsoft.com/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix) för att förbereda katalogattributen för synkronisering.

### <a name="in-your-on-premises-environment"></a>I din lokala miljö

1. Identifiera en domänansluten värdserver som kör Windows Server 2012 R2 eller senare med minst 4 GB RAM och .NET 4.7.1+ körning.

1. Om det finns en brandvägg mellan servrarna och Azure AD konfigurerar du följande objekt:
   - Se till att agenter kan göra *utgående* begäranden till Azure AD via följande portar:

        | Portnummer | Hur den används |
        | --- | --- |
        | **80** | Hämtar listorna över återkallade certifikat (CRL: er) när TLS/SSL-certifikatet valideras.  |
        | **443** | Hanterar all utgående kommunikation med tjänsten. |
        | **8080** (tillval) | Agenter rapporterar sin status var 10:e minut över port 8080, om port 443 inte är tillgänglig. Den här statusen visas i Azure AD-portalen. |
     
   - Om brandväggen tillämpar regler enligt de ursprungliga användarna öppnar du dessa portar för trafik från Windows-tjänster som körs som en nätverkstjänst.
   - Om du kan ange säkra suffix i brandväggen \*eller proxyn lägger du till anslutningar till .msappproxy.net och \*.servicebus.windows.net. Om inte, tillåt åtkomst till [Azure datacenter IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653), som uppdateras varje vecka.
   - Dina agenter behöver tillgång till login.windows.net och login.microsoftonline.com för första registrering. Öppna brandväggen för dessa webbadresser också.
   - För certifikatvalidering avblockerar du följande webbadresser: mscrl.microsoft.com:80, crl.microsoft.com:80, ocsp.msocsp.com:80\.och www microsoft.com:80. Dessa url:er används för certifikatvalidering med andra Microsoft-produkter, så du kanske redan har dessa url:er blockerade.

### <a name="verify-the-port"></a>Verifiera porten
Om du vill kontrollera att Azure lyssnar på port 443 och att din agent kan kommunicera med den använder du följande URL:

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

Det här testet verifierar att dina agenter kan kommunicera med Azure via port 443. Öppna en webbläsare och gå till föregående URL från servern där agenten är installerad.

![Kontroll av porternas nåbarhet](media/how-to-install/verify2.png)

### <a name="additional-requirements"></a>Ytterligare krav
- [Microsoft .NET Framework 4.7.1](https://www.microsoft.com/download/details.aspx?id=56116) 

#### <a name="tls-requirements"></a>TLS-krav

>[!NOTE]
>TLS (Transport Layer Security) är ett protokoll som möjliggör säker kommunikation. Om du ändrar TLS-inställningarna påverkas hela skogen. Mer information finns i [Uppdatera för att aktivera TLS 1.1 och TLS 1.2 som standardsäkra protokoll i WinHTTP i Windows](https://support.microsoft.com/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi).

Windows-servern som är värd för Azure AD Connect-molnetableringsagenten måste ha TLS 1.2 aktiverat innan du installerar den.

Så här aktiverar du TLS 1.2.

1. Ange följande registernycklar:
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. Starta om servern.


## <a name="next-steps"></a>Nästa steg 

- [Vad är etablering?](what-is-provisioning.md)
- [Vad är Azure AD Connect-molnetablering?](what-is-cloud-provisioning.md)

