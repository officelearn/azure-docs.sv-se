---
title: Krav för Azure AD Connect moln etablering i Azure AD
description: I det här avsnittet beskrivs förutsättningarna och maskin varu kraven för moln etablering.
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
ms.openlocfilehash: f033563bbd7888e53d910773cd1e0c501eaad098
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74997113"
---
# <a name="prerequisites-for-azure-ad-connect-cloud-provisioning"></a>Krav för Azure AD Connect Cloud-etablering
Det här avsnittet innehåller vägledning om hur du väljer och använder Azure AD Connect moln etablering som din identitets lösning.



## <a name="cloud-provisioning-agent-requirements"></a>Krav för moln etablerings agent
Du kommer att behöva följande för att kunna använda Azure AD Connect Cloud-etablering:
    
- ett globalt administratörs konto för din Azure AD-klient
- en lokal server för etablerings agenten med Windows 2012 R2 eller senare
- konfigurationer för lokala brand väggar

Resten av dokumentet innehåller steg-för-steg-instruktioner för dessa krav.

### <a name="in-the-azure-active-directory-admin-center"></a>I Azure Active Directory administrations Center

1. Skapa ett globalt administratörs konto för molnet på Azure AD-klienten. På så sätt kan du hantera konfigurationen av din klient organisation om dina lokala tjänster kraschar eller blir otillgängliga. Lär dig mer om [att lägga till ett globalt administratörs konto för molnet](../active-directory-users-create-azure-portal.md). Att slutföra det här steget är viktigt för att säkerställa att du inte blir utelåst från din klient.
2. Lägg till ett eller flera [anpassade domän namn](../active-directory-domains-add-azure-portal.md) i Azure AD-klienten. Användarna kan logga in med ett av dessa domän namn.

### <a name="in-your-on-premises-environment"></a>I din lokala miljö

1. Identifiera en domänansluten värd server som kör Windows Server 2012 R2 eller senare med minst 4 GB RAM-minne och .NET 4.7.1 + runtime 

2. Om det finns en brand vägg mellan dina servrar och Azure AD konfigurerar du följande objekt:
   - Se till att agenter kan göra *utgående* begär anden till Azure AD över följande portar:

     | Portnummer | Hur den används |
     | --- | --- |
     | **80** | Hämtar listor över återkallade certifikat (CRL) när SSL-certifikatet verifieras |
     | **443** | Hanterar all utgående kommunikation med tjänsten |
     | **8080** (valfritt) | Agenter rapporterar sin status var tionde minut via port 8080, om port 443 inte är tillgänglig. Den här statusen visas på Azure AD-portalen. |
     
     Om brand väggen tillämpar regler enligt de ursprungliga användarna öppnar du portarna för trafik från Windows-tjänster som körs som en nätverks tjänst.
   - Om din brand vägg eller proxy låter dig ange säkra suffix, lägger du till anslutningar i **\*. msappproxy.net** och **\*. ServiceBus.Windows.net**. Om inte, Tillåt åtkomst till [Azure datacenter IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653)som uppdateras varje vecka.
   - Dina agenter behöver åtkomst till **login.Windows.net** och **login.microsoftonline.com** för inledande registrering. Öppna brand väggen för dessa URL: er även.
   - För certifikat validering, avblockera följande URL: er: **mscrl.Microsoft.com:80**, **CRL.Microsoft.com:80**, **OCSP.msocsp.com:80**och **www\.Microsoft.com:80**. Eftersom dessa URL: er används för certifikat validering med andra Microsoft-produkter kan dessa URL: er vara avblockerade.

### <a name="verify-the-port"></a>Verifiera porten
För att verifiera att Azure lyssnar på port 443 och att agenten kan kommunicera med den, kan du använda följande:

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

Det här testet kontrollerar att dina agenter kan kommunicera med Azure via port 443.  Öppna en webbläsare och gå till ovanstående URL från servern där agenten är installerad.
![Tjänster](media/how-to-install/verify2.png)

### <a name="additional-requirements"></a>Ytterligare krav
- [Microsoft .NET Framework-4.7.1](https://www.microsoft.com/download/details.aspx?id=56116) 

#### <a name="tls-requirements"></a>TLS-krav

>[!NOTE]
>Transport Layer Security (TLS) är ett protokoll som möjliggör säker kommunikation.  Att ändra TLS-inställningarna påverkar hela skogen.  Mer information finns i [Uppdatera för att aktivera tls 1,1 och tls 1,2 som standard säkra protokoll i WinHTTP i Windows](https://support.microsoft.com/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi)

Windows Server som ska vara värd för Azure AD Connect moln etablerings agent måste ha TLS 1,2 aktiverat innan du installerar det.

Aktivera TLS 1.2:

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
- [Vad är Azure AD Connect Cloud revisioner?](what-is-cloud-provisioning.md)

