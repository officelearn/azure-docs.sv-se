---
title: Hur du planerar din implementering för anslutning till Azure Active Directory (Azure AD) | Microsoft Docs
description: Beskriver de steg som krävs för att implementera Azure AD-anslutna enheter i din miljö.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.component: devices
ms.assetid: 81d4461e-21c8-4fdd-9076-0e4991979f62
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/21/2018
ms.author: markvi
ms.reviewer: sandeo
ms.openlocfilehash: be7cded5b9cd2f7a3f33d1de11f36e82f079fc4b
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54448576"
---
# <a name="how-to-plan-your-azure-ad-join-implementation"></a>Anvisningar: Planera implementeringen av Azure AD-anslutningen


Azure AD-anslutning kan du ansluta enheter direkt till Azure AD utan att behöva ansluta till den lokala Active Directory samtidigt som användarna produktiva och skyddade. Azure AD join är färdiga för företag för begränsade distributioner och i skala.   

Den här artikeln ger dig den information du behöver att planera implementeringen av Azure AD join.

 
## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du är bekant med den [introduktionen till enhetshantering i Azure Active Directory](../device-management-introduction.md).



## <a name="plan-your-implementation"></a>Planera implementeringen

Om du vill planera hybridimplementeringen Azure AD, bör du bekanta dig med:

|   |   |
|---|---|
|![Markera][1]|Granska dina scenarier|
|![Markera][1]|Granska din infrastruktur för Identitetshantering|
|![Markera][1]|Utvärdera din enhetshantering|
|![Markera][1]|Förstå överväganden för program och resurser|
|![Markera][1]|Förstå dina alternativ för etablering|
|![Markera][1]|Konfigurera enterprise tillståndsväxling|
|![Markera][1]|Konfigurera villkorlig åtkomst|







## <a name="review-your-scenarios"></a>Granska dina scenarier 

Även om Hybrid Azure AD-anslutning kan vara att föredra för vissa scenarier, kan Azure AD-anslutning du övergången till en molnbaserad modell med Windows. Om du planerar att modernisera din hantering av enheter och enhetsrelaterade IT-kostnader, ger Azure AD join en bra grund för att uppnå dessa mål.  

 
Du bör överväga att Azure AD-anslutning om dina mål uppfyller följande kriterier:

- Du använder Microsoft 365 som programsviten produktivitet för dina användare.

- Du vill hantera enheter med en enhetshanteringslösning i molnet.

- Du vill förenkla enhetsetablering för geografiskt distribuerade användare.

- Du planerar att modernisera din infrastruktur.
 

 

## <a name="review-your-identity-infrastructure"></a>Granska din infrastruktur för Identitetshantering  

Azure AD-anslutning fungerar med både hanterade och federerade miljöer.  


### <a name="managed-environment"></a>Hanterad miljö

En hanterad miljö kan vara distribueras antingen via [Lösenordshashsynkronisering](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization) eller [skicka via autentisering](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-quick-start) med sömlös enkel inloggning.

Dessa scenarier måste inte du konfigurera en federationsserver för autentisering.


### <a name="federated-environment"></a>Federerad miljö

En federerad miljö bör ha en identitetsprovider som har stöd för såväl WS-Trust WS-Fed protokoll:

- **WS-Fed:** Det här protokollet krävs för att ansluta till en enhet till Azure AD.

- **WS-Trust:** Det här protokollet krävs för att logga in på en domänansluten Azure AD-enhet. 

Om din identitetsprovider inte stöder dessa protokoll fungerar inte Azure AD join internt. Från och med Windows 10 1809 kan dina användare kan logga in på en domänansluten Azure AD-enhet med en SAML-baserad identitetsprovider via [web logga in på Windows 10](https://docs.microsoft.com/windows/whats-new/whats-new-windows-10-version-1809#web-sign-in-to-windows-10). För närvarande är webbinloggning en funktion som skrivskyddade.


### <a name="smartcards-and-certificate-based-authentication"></a>Smartkort och certifikatbaserad autentisering

Du kan inte använda smartkort eller certifikatbaserad autentisering för att ansluta enheter till Azure AD. Smartkort kan dock användas för att logga in på Azure AD-anslutna enheter om du har konfigurerat ADFS.

**Rekommendation:** Implementera Windows Hello för företag för stark, lösenord utan autentisering till Windows 10-enheter.


### <a name="user-configuration"></a>Användarkonfiguration

Om du skapar användare i din:

- **En lokal Active Directory**, måste du synkronisera dem till Azure AD via [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis). 

- **Azure AD**, krävs inga ytterligare inställningar.

Lokala UPN-namn som skiljer sig från Azure AD UPN: er inte stöds i Azure AD-anslutna enheter. Om dina användare använder en lokala UPN, bör du planera att växla till sina primära UPN i Azure AD.



## <a name="assess-your-device-management"></a>Utvärdera din enhetshantering

### <a name="supported-devices"></a>Enheter som stöds

Azure AD-anslutning:

- Gäller endast Windows 10-enheter. 

- Gäller inte för tidigare versioner av Windows eller andra operativsystem. Om du har Windows 7/8.1-enheter, måste du uppgradera till Windows 10 för att distribuera Azure AD-anslutning.
 
**Rekommendation:** Använd alltid den senaste versionen av Windows 10 för att dra nytta av uppdaterade funktioner.


### <a name="management-platform"></a>Plattform för hantering

Enhetshantering för Azure AD-anslutna enheter baserat på en MDM-plattformen, till exempel Intune) och MDM CSP: er. Windows 10 har inbyggda MDM-agenten som fungerar med alla kompatibla MDM-lösningar.

Det finns två metoder för hantering av Azure AD-anslutna enheter:

- **Endast MDM** – en enhet hanteras exklusivt av en MDM-provider som Intune. Alla principer som levereras som en del av MDM-registreringen. För Azure AD Premium eller EMS-kunder är MDM-registrering ett automatiserade steg som ingår i en Azure AD-koppling.

- **Samhantering** – en enhet som hanteras av en MDM-providern och SCCM. Med den här metoden installeras SCCM-agent på en MDM-hanterad enhet att administrera vissa aspekter.

Eftersom Azure AD-anslutna enheter är inte ansluten till en lokal Active Directory, grupprinciper stöds inte.


Om du använder grupprinciper, utvärdera dina MDM-principen paritet med hjälp av den [MDM migrering Analysis verktyget (MMAT)](https://github.com/WindowsDeviceManagement/MMAT). 

Granska som stöds respektive principer för att avgöra om du kan använda en MDM-lösning i stället för grupprinciper. För principer som inte stöds, Tänk på följande:

- Krävs stöds inte principerna för Azure AD-anslutna enheter eller användare?

- Gäller principerna som stöds inte i ett moln som drivs av distributionen?

Om din MDM-lösning inte är tillgänglig i Azure AD app-galleriet, du kan lägga till den enligt den process som beskrivs i [Azure Active Directory-integrering med MDM](https://docs.microsoft.com/windows/client-management/mdm/azure-active-directory-integration-with-mdm). 

Du kan använda SCCM via samhantering, för att hantera vissa aspekter av dina enheter när principer levereras via din MDM-plattformen. Microsoft Intune gör det möjligt för samhantering med SCCM. Mer information finns i [samhantering för Windows 10-enheter](https://docs.microsoft.com/sccm/core/clients/manage/co-management-overview). Om du använder en MDM-produkt än Intune, kontakta din MDM-provider på tillämpliga samhantering scenarier.

**Rekommendation:** Överväg att MDM endast management för Azure AD-anslutna enheter.



## <a name="understand-considerations-for-applications-and-resources"></a>Förstå överväganden för program och resurser

Vi rekommenderar att du migrerar program från en lokal plats till molnet för en bättre användare får och åtkomstkontroll. Dock Azure AD-anslutna enheter sömlöst kan ge åtkomst till både lokalt och molnet program. Mer information finns i [hur SSO till lokala resurser fungerar i Azure AD-anslutna enheter](azuread-join-sso.md).

I följande avsnitt listas överväganden för olika typer av program och resurser.

### <a name="cloud-based-applications"></a>Molnbaserade program

Om ett program har lagts till i appgalleriet för Azure AD, får användare enkel inloggning via Azure AD-anslutna enheter. Det krävs ingen ytterligare konfiguration. Användare får enkel inloggning på både, Microsoft Edge och Chrome webbläsare. För Chrome, måste du distribuera den [Windows 10-konton tillägget](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji). 

Alla Win32-program som:

- Förlita dig på Web konto Manager (WAM) för tokenbegäranden också få enkel inloggning på Azure AD-anslutna enheter. 

- Förlita dig inte på WAM kan be användarna för autentisering. 


### <a name="on-premises-web-applications"></a>Lokala webbprogram

Om ditt program är anpassade bygger och/eller lokala, du måste lägga till dem till din webbläsares betrodda platser för att:

- Aktivera integrerad Windows-autentisering ska fungera 
- Ge användarna en Nej frågar SSO-upplevelse. 

Om du använder AD FS, se [kontrollera och hantera enkel inloggning med AD FS](https://docs.microsoft.com/previous-versions/azure/azure-services/jj151809(v%3dazure.100)). 

**Rekommendation:** Överväg att värd i molnet (till exempel Azure) och integrera med Azure AD för en bättre upplevelse.

### <a name="on-premises-applications-relying-on-legacy-protocols"></a>Lokala program som förlitar sig på äldre protokoll

Användare få SSO från Azure AD-anslutna enheter om enheten har åtkomst till en domänkontrollant. 

**Rekommendation:** Distribuera [Azure AD App proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) att möjliggöra säker åtkomst för dessa program.


### <a name="on-premises-network-shares"></a>Lokala nätverksresurser

Användarna har enkel inloggning från Azure AD-anslutna enheter när en enhet har åtkomst till en lokal domänkontrollant.

### <a name="printers"></a>Skrivare

För skrivare, måste du distribuera [hybridmoln Skriv ut](https://docs.microsoft.com/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy) för identifiering av skrivare på Azure AD-anslutna enheter. 

Skrivare inte kan identifieras automatiskt i en enda molnmiljö, kan användarna också använda de skrivarna UNC-sökvägen att lägga till dem direkt. 


### <a name="on-premises-applications-relying-on-machine-authentication"></a>Lokala program som förlitar sig på datorn autentisering

Azure AD-anslutna enheter stöder inte lokala program som förlitar sig på datorn autentisering. 

**Rekommendation:** Överväg att ta bort dessa program och flytta till sina moderna alternativ.

### <a name="remote-desktop-services"></a>Fjärrskrivbordstjänster

Anslutning till fjärrskrivbord till en Azure AD-anslutna enheter kräver värddatorn ska vara antingen Azure AD-ansluten eller Hybrid Azure AD-anslutna. Fjärrskrivbord från en frånkopplas eller icke-Windows-enhet stöds inte. Mer information finns i [Anslut till fjärranslutna Azure AD-domänansluten dator](https://docs.microsoft.com/windows/client-management/connect-to-remote-aadj-pc)


## <a name="understand-your-provisioning-options"></a>Förstå dina alternativ för etablering

Du kan etablera Azure AD-anslutning med hjälp av följande metoder:

- **Självbetjäning i OOBE/inställningar** – i självbetjäning-läge, användare gå via Azure AD join Processinställningar antingen under Windows Out Box Experience (OOBE) eller från Windows. Mer information finns i [ansluter din work-enhet till din organisations nätverk](https://docs.microsoft.com/azure/active-directory/user-help/user-help-join-device-on-network). 

- **Windows Autopilot** -Windows Autopilot kan före konfiguration av enheter för en jämnare upplevelse i OOBE att utföra en Azure AD-koppling. Mer information finns i den [översikt över Windows Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot). 

- **Massregistrering** -massregistrering kan en administratör driven Azure AD-koppling genom att använda en massinläsning etablering verktyget för att konfigurera enheter. Mer information finns i [massregistrering för Windows-enheter](https://docs.microsoft.com/intune/windows-bulk-enroll).
 
 


Här är en jämförelse av dessa tre metoder 

 
||Självbetjäning installationen|Windows Autopilot|Massregistrering|
|---|---|---|---|
|Kräv användarinteraktion för att ställa in|Ja|Ja|Nej|
|Kräv IT arbete|Nej|Ja|Ja|
|Tillämpliga flöden|OOBE och inställningar|OOBE endast|OOBE endast|
|Lokal administratörsbehörighet till primär användare|Ja, som standard|Konfigurerbara|Nej|
|Kräv stöd för enheter OEM|Nej|Ja|Nej|
|Versioner som stöds|1511+|1709+|1703+|
 
Välj din distributionsmetod sätt genom att granska tabellen ovan och granska följande överväganden för att börja använda endera metod:  

- Är din användare teknisk smarta gå igenom installationen själva? 

    - Självbetjäning fungerar bäst för dessa användare. Överväg att Windows Autopilot för att förbättra användarupplevelsen.  

- Är användarna remote eller i företagets lokaler? 

    - Självbetjäning eller Autopilot fungerar bäst för fjärranslutna användare för en problemfri installation. 
 
- Föredrar du en användarstyrd eller en administratör-hanterade konfiguration? 

    - Massregistrering fungerar bättre för admin driven distribution konfigurerar enheter innan du skriver till användare.     

- Köper du enheter från 1 – 2 OEM-tillverkare eller har du en spridning av OEM-enheter?  

    - Om du köper från begränsad OEM-tillverkare som stöder även Autopilot kan du dra nytta av närmare integration med Autopilot. 
 

## <a name="configure-your-device-settings"></a>Konfigurera inställningar för enheter

Azure-portalen kan du styra distributionen av Azure AD-anslutna enheter i din organisation. Konfigurera relaterade inställningar på den **Azure Active Directory-sidan**väljer `Devices > Device settings`.

### <a name="users-may-join-devices-to-azure-ad"></a>Användare kan ansluta enheter till Azure AD

Välja alternativet **alla** eller **valda** baserat på omfånget för distributionen och som du vill tillåta för att konfigurera en Azure AD ansluten enhet. 

![Användare kan ansluta enheter till Azure AD](./media/azureadjoin-plan/01.png)

### <a name="additional-local-administrators-on-azure-ad-joined-devices"></a>Ytterligare lokala administratörer på Azure AD-anslutna enheter

Välj **valda** och väljer de användare som du vill lägga till i gruppen lokala administratörer på alla Azure AD-anslutna enheter. 

![Ytterligare lokala administratörer på Azure AD-anslutna enheter](./media/azureadjoin-plan/02.png)


### <a name="require-multi-factor-auth-to-join-devices"></a>Kräv Multi-Factor Authentication att ansluta enheter

Välj **”Ja** om du behöver användare att utföra MFA vid ansluta enheter till Azure AD. För de användare som ansluter enheter till Azure AD med hjälp av MFA, blir själva enheten 2nd factor.

![Kräv Multi-Factor Authentication att ansluta enheter](./media/azureadjoin-plan/03.png)




## <a name="configure-your-mobility-settings"></a>Konfigurera inställningarna för mobility

Innan du kan konfigurera inställningarna mobility måste kanske du lägga till en MDM-provider först.

**Lägga till en MDM-provider**:

1. På den **Azure Active Directory-sidan**i den **hantera** klickar du på `Mobility (MDM and MAM)`. 

2. Klicka på **Lägg till program**.

3. Välj din MDM-provider i listan.

    ![Lägga till ett program](./media/azureadjoin-plan/04.png)

Välj din MDM-provider för att konfigurera relaterade inställningar. 

### <a name="mdm-user-scope"></a>MDM-användaromfattning

Välj **vissa** eller **alla** utifrån omfattningen för din distribution. 

![MDM-användaromfattning](./media/azureadjoin-plan/05.png)

Baserat på ditt omfång kan händer något av följande: 

- **Användaren är i MDM omfång**: Om du har en Azure AD Premium-prenumeration, automatisk MDM-registrering tillsammans med Azure AD-anslutning. Alla begränsade användare måste ha en lämplig licens för din MDM. Om det inte går att MDM-registrering i det här scenariot kan kommer Azure AD join också att återställas.
    
- **Användaren finns inte i MDM omfång**: Om användarna inte i MDM-omfattning, Azure AD-anslutning har slutförts utan någon MDM-registrering. Detta resulterar i en ohanterad enhet.


### <a name="mdm-urls"></a>MDM-URL: er

Det finns tre URL: er som är relaterade till din MDM-konfiguration:

- MDM-användningsvillkors-URL

- Webbadress till MDM-identifiering 

- MDM-kompatibilitets-URL


![Lägga till ett program](./media/azureadjoin-plan/06.png)


Varje URL: en har ett fördefinierat standardvärde. Om de här fälten är tomma, kontakta din MDM-provider för mer information.

### <a name="mam-settings"></a>Inställningar för MAM

MAM gäller inte för Azure AD-anslutning. 


## <a name="configure-enterprise-state-roaming"></a>Konfigurera enterprise tillståndsväxling

Om du vill aktivera tillståndsväxling till Azure AD så att användarna kan synkronisera sina inställningar på enheter, se [aktivera Enterprise tillstånd centrala i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/devices/enterprise-state-roaming-enable). 

**Rekommendationen**: Aktivera den här inställningen även för hybrid Azure AD-anslutna enheter.


## <a name="configure-conditional-access"></a>Konfigurera villkorlig åtkomst

Om du har en MDM-provider som konfigurerats för din Azure AD-anslutna enheter, providern flaggar att enheten följer standard när enheten är i hanteringen. 

![Kompatibel enhet](./media/azureadjoin-plan/46.png)

Du kan använda den här implementeringen till [kräver hanterade enheter för åtkomst till molnet appen med villkorlig åtkomst](../conditional-access/require-managed-devices.md).




## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Ansluta till en ny Windows 10-enhet med Azure AD under första körningen](azuread-joined-devices-frx.md)
> [ansluter din work-enhet till din organisations nätverk](https://docs.microsoft.com/azure/active-directory/user-help/user-help-join-device-on-network)


<!--Image references-->
[1]: ./media/azureadjoin-plan/12.png
