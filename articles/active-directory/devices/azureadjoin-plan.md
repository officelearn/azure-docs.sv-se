---
title: Hur du planerar din implementering för anslutning till Azure Active Directory (Azure AD) | Microsoft Docs
description: Beskriver de steg som krävs för att implementera Azure AD-anslutna enheter i din miljö.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.component: devices
ms.assetid: 81d4461e-21c8-4fdd-9076-0e4991979f62
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/20/2018
ms.author: markvi
ms.reviewer: sandeo
ms.openlocfilehash: fdc8b5db9316e463f8ec46ca5e235ea53cf44265
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2018
ms.locfileid: "52275980"
---
# <a name="how-to-plan-your-azure-ad-join-implementation"></a>Så här: Planera implementeringen Azure AD join


Azure AD-anslutning kan du ansluta enheter direkt till Azure AD utan att behöva ansluta till den lokala Active Directory samtidigt som användarna produktiva och skyddade.  

Den här artikeln förutsätter att du har tidigare erfarenhet av Azure AD och [enhetstillstånd i Azure AD](overview.md). 

 

## <a name="review-your-scenarios"></a>Granska dina scenarier 

Azure AD join är gäller för de flesta scenarier i en organisations inställning, kan det finnas vissa användningsområden där du kanske vill distribuera Hybrid Azure AD-anslutning i stället. Azure AD join är färdiga för företag för distributioner i skala eller ett omfång. 

 
Överväg att Azure AD join utifrån följande kriterier för din organisation:  

- Du planerar eller ha merparten av dina Windows-enheter i Windows 10. 

- Du planerar att flytta till en molndriven distribution och hantering av Windows-enheter. 

- Du har begränsad infrastruktur på plats eller planerar att minska storleken på plats. 

- Du har inte alltför beroende på grupprinciper för att hantera enheter. 

- Du har inte äldre program som förlitar sig på Active Directory-autentisering för datorn. 

- Alla eller valda användare i din organisation behöver inte finnas på företagsnätverket för att komma åt nödvändiga appar och resurser.  

 

Granska dina scenarier baserat på följande utvärderingen  

 

## <a name="assess-infrastructure"></a>Utvärdera infrastruktur  

 

### <a name="users"></a>Användare 

Om dina användare skapas i den lokala Active Directory, måste de synkroniseras till Azure AD via Azure AD Connect. Mer information om hur du synkroniserar användare finns [vad är Azure AD Connect?](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity#what-is-azure-ad-connect) 

Om dina användare skapas direkt i Azure AD, det krävs inga ytterligare inställningar 

 

Alternativa inloggnings-ID: N stöds inte på Azure AD-anslutna enheter. Användare bör ha ett giltigt UPN i Azure AD.  

 

### <a name="identity-infrastructure"></a>Infrastruktur för Identitetshantering 

Azure AD-anslutning fungerar med både hanterade och federerade miljöer.  

#### <a name="managed-environment"></a>Hanterad miljö 

En hanterad miljö kan distribueras antingen via synkronisering av lösenords-Hash eller Pass via autentisering med sömlös enkel inloggning. <read more here> 

#### <a name="federated-environment"></a>Federerad miljö  

En federerad miljö bör ha en identitetsprovider som har stöd för såväl WS-Trust WS-Fed protokoll. WS-Fed krävs för att ansluta en enhet till Azure AD och WS-Trust krävs för att logga in på en domänansluten Azure AD-enhet. Så om din identitetsprovider inte stöder dem, inte fungerar Azure AD-anslutning. 


#### <a name="smartcards-and-certificate-based-authentication"></a>Smartkort och certifikatbaserad autentisering 

 

För närvarande smartkort och certifikatbaserad autentisering stöds inte i Azure AD så att de inte att fungera på Azure AD-anslutna enheter 

 

### <a name="devices"></a>Enheter 

 

#### <a name="supported-devices"></a>Enheter som stöds 

Azure AD join är exklusivt för Windows 10-enheter. Det är inte giltiga för tidigare versioner av Windows eller andra operativsystem. Om du fortfarande har Windows 7/8.1 måste du uppgradera till Windows 10 för att distribuera Azure AD join 

 

Rekommendation: Använd alltid den senaste versionen av Windows 10 för uppdaterade funktioner 

 

### <a name="applications--other-resources"></a>Program och andra resurser 

Vi rekommenderar att du migrerar dina program från en lokal plats till molnet för bättre användarupplevelse och åtkomstkontroll. Dock Azure AD-anslutna enheter sömlöst kan ge åtkomst till både lokala och molnbaserade program. Mer information finns i [hur SSO till lokala resurser fungerar i Azure AD-anslutna enheter](azuread-join-sso.md).  


Följande är överväganden för annan typ av program och resurser 

 

- **Molnbaserade program:** om ett program har lagts till appgalleriet för Azure AD kan användarna få SSO via Azure AD-anslutna enheter. Ingen ytterligare konfiguration krävs 

 

- **Lokala webbprogram:** om dina appar är anpassade bygger och/eller lokala, du behöver lägga till dem i webbläsarens betrodda platser. Detta aktiverar Windows-integrerad autentisering att fungera och ge användarna en Nej frågar SSO-upplevelse. Om du använder AD FS finns i [kontrollera och hantera enkel inloggning med AD FS](https://docs.microsoft.com/en-us/previous-versions/azure/azure-services/jj151809(v%3dazure.100)) för mer information. Rekommendation: Överväg värd i molnet (till exempel Azure) och integrera med Azure AD för en bättre upplevelse. 

- **Lokala program som förlitar sig på äldre protokoll:** användare få SSO från Azure AD-anslutna enheter om enheten har åtkomst till domänkontrollanten. Rekommendation: Distribuera Azure AD App proxy för att skydda åtkomsten för dessa program. Mer information finns i [varför en bättre lösning är programproxyn?](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy#why-is-application-proxy-a-better-solution) 

- **Lokala nätverksresurser:** användare få SSO till sina nätverksresurser från Azure AD-anslutna enheter när enheten är ansluten till en domänkontrollant. Mer information finns i [hur SSO till lokala resurser fungerar i Azure AD-anslutna enheter](azuread-join-sso.md).

 

- **Skrivare:** distribuera Hybrid cloud Skriv ut för att identifiera skrivare från Azure AD-anslutna enheter. Användare kan också använda de skrivarna UNC-sökvägen för att lägga till dem direkt. Skrivare kan inte identifieras automatiskt i en enda molnmiljö. 

 

- **Lokala program som förlitar sig på datorn autentisering:** dessa program stöds inte i Azure AD-anslutna enheter. Rekommendation: Överväg att ta bort dessa program och flytta till sina moderna alternativ.  

 

### <a name="device-management"></a>Enhetshantering  

 

Enhetshantering för Azure AD-anslutna enheter är främst genom en EMM-plattform (till exempel Intune) och MDM CSP: er. Windows 10 har inbyggd MDM-agenten som fungerar med alla kompatibla EMM-lösningar.  

 

Grupprinciper stöds inte på Azure AD-anslutna enheter som de inte är anslutna till Active Directory.  

 

Utvärdera MDM-princip paritet med grupprinciper med hjälp av den [MDM-analysverktyg för migrering [MMAT](https://channel9.msdn.com/Events/Ignite/Microsoft-Ignite-Orlando-2017/THR3002R). Granska som stöds respektive principer för att fastställa tillämpligheten hos med en EMM-lösning i stället för grupprinciper. Principer som inte stöds kan du överväga att:  

- Krävs principerna som stöds inte för användare eller enheter Azure AD join distribueras till? 

- Gäller principerna som stöds inte i ett moln som drivs av distributionen? 

 

Om din EMM-lösning inte är tillgänglig i Azure AD app-galleriet, du kan lägga till den enligt den process som beskrivs i [Azure Active Directory-integrering med MDM](https://docs.microsoft.com/windows/client-management/mdm/azure-active-directory-integration-with-mdm).

 

Via samhantering användas SCCM för att hantera vissa aspekter av enheterna när principer levereras via din EMM-plattform. Microsoft Intune gör det möjligt för samhantering med SCCM. Mer information finns i [samhantering för Windows 10-enheter](https://docs.microsoft.com/sccm/core/clients/manage/co-management-overview). Kontrollera med leverantören EMM på tillämpliga samhantering scenarier om du använder en EMM-produkt än Intune.  

 

Det finns två breda sätt för att hantera Azure AD-anslutna enheter: 

 

- **Endast MDM** -enheten hanteras exklusivt av en EMM-provider som Intune. Alla principer som levereras som en del av MDM-registreringen. För Azure AD Premium eller EMS-kunder automatisk MDM-registrering som en del av Azure AD-anslutning. 

- **Samhantering** -enheten hanteras samtidigt av EMM-providern och SCCM. Med den här metoden installeras SCCM-agent på en MDM-hanterad enhet att administrera vissa aspekter. 

Rekommendation: Överväg MDM endast management för Azure AD-anslutna enheter.  

 

## <a name="configuration"></a>Konfiguration 

 

Azure AD-anslutning kan konfigureras i Azure AD-portalen baserat på vissa specifika inställningar. Gå till `Devices -> Device settings` och konfigurera följande alternativ:   

- Användare kan ansluta enheter till Azure AD: Ställ in på ”alla” eller ”valda” baserat på omfattningen för din distribution.  

- Ytterligare lokala administratörer på Azure AD-anslutna enheter: inställt på ”valda” och väljer användare som du vill lägga till i gruppen lokala administratörer på alla Azure AD-anslutna enheter som distribueras i din organisation. Mer information finns i [hantera den lokala administratörsgruppen på Azure AD-anslutna enheter](assign-local-admin.md). 

- Kräver Multi-Factor Authentication att ansluta enheter: inställt på ”Ja” om du behöver användare att utföra MFA vid ansluta enheter till Azure AD. För den användare som ansluter enheten till Azure AD med hjälp av MFA, blir enheten 2nd factor. 

Om du vill aktivera tillståndsväxling till Azure AD så att enheter ska kunna synkroniseras deras inställningar, se [vad är enterprise tillståndsväxling?](enterprise-state-roaming-overview.md). Vi rekommenderar att du aktiverar den här inställningen även för Hybrid Azure AD-anslutna enheter 

### <a name="deployment-options"></a>Distributionsalternativ 

 

Azure AD-anslutning kan distribueras via tre olika sätt:  

- **Självbetjäning i OOBE/inställningar** – i självbetjäning-läge, användare gå via Azure AD join Processinställningar antingen under Windows Out Box Experience (OOBE) eller från Windows.  

- **Windows Autopilot** -Windows Autopilot kan före konfiguration av enheter för en jämnare upplevelse i OOBE för Azure AD-anslutning.   

- **Massregistrering** -massregistrering kan en administratör driven Azure AD-koppling genom att använda en massinläsning etablering verktyget för att konfigurera enheter.  


Här är en jämförelse av dessa tre metoder 

 
||Självbetjäning installationen|Windows Autopilot|Massregistrering|
|---|---|---|---|
|Kräv användarinteraktion för att ställa in|Ja|Ja|Nej|
|Kräv IT arbete|Nej|Ja|Ja|
|Tillämpliga flöden|OOBE och inställningar|OOBE endast|OOBE endast|
|Lokal administratörsbehörighet till primär användare|Ja, som standard|Konfigurerbara|Nej|
|Kräv stöd för enheter OEM|Nej|Ja|Nej|
|Versioner som stöds|1511 +|1709 +|1703 +|
 
Välj din distributionsmetod sätt genom att granska tabellen ovan och granska följande överväganden för att börja använda endera metod:  

- Är din användare teknisk smarta gå igenom installationen själva? 

    - Självbetjäning fungerar bäst för dessa användare. Överväg att Windows Autopilot för att förbättra användarupplevelsen.  

- Är användarna remote eller i företagets lokaler? 

    - Självbetjäning eller Autopilot fungerar bäst för fjärranslutna användare för en problemfri installation. 
 
- Föredrar du en användarstyrd eller en administratör-hanterade konfiguration? 

    - Massregistrering fungerar bättre för admin driven distribution konfigurerar enheter innan du skriver till användare.     

- Köper du enheter från 1 – 2 OEM-tillverkare eller har du en spridning av OEM-enheter?  

    - Om du köper från begränsad OEM-tillverkare som stöder även Autopilot kan du dra nytta av närmare integration med Autopilot. 
 

 


## <a name="next-steps"></a>Nästa steg

- [Enhetshantering](overview.md)

