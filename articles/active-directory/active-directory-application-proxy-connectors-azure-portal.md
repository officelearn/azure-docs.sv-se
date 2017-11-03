---
title: "Publicera program på separata nätverk och platser med hjälp av kopplingen grupper i Azure AD App Proxy | Microsoft Docs"
description: Beskriver hur du skapar och hanterar grupper av kopplingar i Azure AD Application Proxy.
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: H1Hack27Feb2017; it-pro
ms.openlocfilehash: 1b08a0b376cbcae8522364c9b6ef22e9c0176438
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="publish-applications-on-separate-networks-and-locations-using-connector-groups"></a>Publicera program på separata nätverk och platser med hjälp av connector-grupper
> [!div class="op_single_selector"]
> * [Azure Portal](active-directory-application-proxy-connectors-azure-portal.md)
> * [Klassisk Azure-portal](active-directory-application-proxy-connectors.md)
>

Kunder kan använda Azure AD Application Proxy för fler och fler scenarier och program. Så att vi har gjort App Proxy ännu mer flexibla genom att aktivera flera topologier. Du kan skapa Application Proxy connector grupper så att du kan tilldela specifika kopplingar för att hantera specifika program. Den här funktionen ger mer kontroll och sätt att optimera din Application Proxy-distribution. 

Varje Application Proxy connector har tilldelats en grupp för anslutningen. Alla kopplingar som hör till samma grupp för kopplingen fungerar som en separat enhet för hög tillgänglighet och belastningsutjämning. Alla kopplingar tillhör en grupp för anslutningen. Om du inte skapa grupper, är alla kopplingar i en standardgrupp. Administratören kan skapa nya grupper och tilldela kopplingar till dem i Azure-portalen. 

Alla program som har tilldelats en grupp för anslutningen. Om du inte skapa grupper, tilldelas alla program till en standardgrupp. Men du kan ange varje program för att arbeta med en viss koppling grupp om du organisera dina kontakter i grupper. I det här fallet fungerar endast kopplingar i gruppen programmet på begäran. Den här funktionen är användbart om dina program som finns på olika platser. Du kan skapa kopplingen grupper baserat på plats, så att program hanteras alltid av kopplingar som närmar sig fysiskt dem.

>[!TIP] 
>Om du har en stor Application Proxy-distribution kan inte tilldela alla program i standardgruppen för anslutningen. På så sätt kan nya anslutningar får inte någon live trafik tills du tilldelar dem till en grupp i active-koppling. Den här konfigurationen kan du placera kopplingar i ett inaktivt tillstånd genom att flytta dem till standardgruppen, så att du kan utföra underhåll utan att påverka dina användare.

## <a name="prerequisites"></a>Krav
Om du vill gruppera dina kontakter har se till att du [installerat flera kopplingar](active-directory-application-proxy-enable.md). När du installerar en ny koppling den automatiskt ansluter den **standard** connector grupp.

## <a name="create-connector-groups"></a>Skapa grupper för anslutningstjänsten
Följ dessa steg för att skapa så många connector-grupper som du vill använda. 

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Välj **Azure Active Directory** > **företagsprogram** > **programproxy**.
2. Välj **ny koppling grupp**. Ny koppling bladet visas.

   ![Välj den nya kopplingen gruppen](./media/active-directory-application-proxy-connectors-azure-portal/new-group.png)

3. Namnge din nya grupp koppling och Använd den nedrullningsbara menyn för att välja vilka kopplingar som ingår i den här gruppen.
4. Välj **Spara**.

## <a name="assign-applications-to-your-connector-groups"></a>Tilldela program till connector-grupper
Följ dessa steg för varje program som du har publicerat med Application Proxy. Du kan tilldela ett program till en grupp i anslutningen när du publicerar den eller du kan använda de här stegen för att ändra tilldelning när du vill.   

1. Hanteringspanel för din katalog, Välj **företagsprogram** > **alla program** > program som du vill koppla till en koppling > **Programproxy**.
2. Använd den **Connector grupp** listrutan och välj den grupp som du vill att programmet ska använda.
3. Välj **spara** att tillämpa ändringen.

## <a name="use-cases-for-connector-groups"></a>Användningsfall för koppling grupper 

Kopplingen grupper är användbara för olika scenarier, inklusive:

### <a name="sites-with-multiple-interconnected-datacenters"></a>Webbplatser med flera sammankopplade Datacenter

Många organisationer har ett antal sammankopplade datacenter. I så fall måste vill du behålla så mycket trafik i datacentret som möjligt eftersom länkar mellan datacenter är dyrt och långsamt. Du kan distribuera kopplingar i varje datacenter för att hantera de program som finns inom datacentret. Den här metoden minimerar länkar mellan datacenter och ger en upplevelse som helt transparent för användarna.

### <a name="applications-installed-on-isolated-networks"></a>Installerade program på isolerade nätverk

Program kan finnas i nätverk som inte är en del av de huvudsakliga företagsnätverket. Du kan använda connector grupper för att installera dedikerade kopplingar på isolerade nätverk att också isolera program i nätverket. Detta inträffar vanligtvis när en utomstående leverantör upprätthåller ett visst program för din organisation. 

Kopplingen grupper kan du installera dedikerade kopplingar för nätverken som publicerar endast specifika program, vilket gör det enklare och säkrare att flytta ut programhantering till andra leverantörer.

### <a name="applications-installed-on-iaas"></a>Installerade program på IaaS 

För program som installerats på IaaS för molnåtkomst betjäna connector grupper vanliga för säker åtkomst till alla appar. Kopplingen grupper inte skapa ytterligare beroende på företagets nätverk eller Fragmentera app-upplevelse. Kopplingar kan installeras på varje molndatacenter och hantera program som finns i nätverket. Du kan installera flera kopplingar för att uppnå hög tillgänglighet.

Ta ett exempel som en organisation som har flera virtuella datorer som är anslutna till sina egna IaaS värdbaserade virtuella nätverk. Om du vill låta de anställda använder dessa program, är dessa privata nätverk anslutna till företagsnätverket med hjälp av plats-till-plats-VPN. Detta ger en bra upplevelse för medarbetare som är belägna i företagets lokaler. Men det kanske inte är optimal för fjärranslutna anställda, eftersom den kräver ytterligare lokala infrastruktur för att dirigera åtkomst, som du ser i diagrammet nedan:

![AzureAD Iaas-nätverk](./media/application-proxy-publish-apps-separate-networks/application-proxy-iaas-network.png)
  
Du kan aktivera en gemensam tjänst få säker åtkomst till alla program utan att skapa ytterligare beroende på företagets nätverk med Azure AD Application Proxy connector grupper:

![AzureAD Iaas flera molnet leverantörer](./media/application-proxy-publish-apps-separate-networks/application-proxy-multiple-cloud-vendors.png)

### <a name="multi-forest--different-connector-groups-for-each-forest"></a>Flera skogar – olika connector grupper för varje skog

De flesta kunder som har distribuerat Application Proxy använder dess single-sign-on (SSO) funktioner genom att utföra Kerberos-begränsad delegering (KCD). För att uppnå måste kopplingens datorer du vara ansluten till en domän som kan delegera användare till programmet. KCD stöder funktioner för mellan skogar. Men för företag som har olika miljöer med flera skogar utan förtroende mellan dem, en enskild koppling kan inte användas för alla skogar. 

I det här fallet kan specifika kopplingar distribueras per skog och ange att hantera program som publicerats för att hantera användarna för den specifika skogen. Varje koppling grupp representerar en annan skog. När klienten och de flesta av upplevelsen finns unified för alla skogar, kan användare tilldelas till skogen program med hjälp av Azure AD-grupper.
 
### <a name="disaster-recovery-sites"></a>Katastrofåterställningsplatser

Det finns två olika metoder som du kan använda med en plats för disaster recovery (DR), beroende på hur dina platser implementeras:

* Om DR-plats är inbyggd i aktivt-aktivt läge där den exakt som den centrala platsen och har samma nätverks- och AD-inställningar, kan du skapa kopplingar på DR-plats i samma grupp för koppling som den centrala platsen. Detta gör att Azure AD för att identifiera växling vid fel för dig.
* Om webbplatsen DR skiljer sig från den centrala platsen, kan du skapa en annan koppling grupp i DR-plats, och antingen 1) har säkerhetskopieringsprogram eller omdirigera det befintliga programmet till gruppen DR-koppling 2) manuellt vid behov.
 
### <a name="serve-multiple-companies-from-a-single-tenant"></a>Hantera flera företag från en enda klient

Det finns många olika sätt att implementera en modell där en enda leverantör distribuerar och underhåller Azure AD-relaterade tjänster för flera företag. Kopplingen grupper kan administratören särskilja kopplingar och program till olika grupper. Ett sätt som är lämplig för små företag, är att ha en enda Azure AD-klient medan olika företag har sina egna domännamn och nätverk. Detta gäller även för M & A scenarier och situationer där en enskild IT-avdelning hanterar flera företag reglerande eller affärsmässiga skäl. 

## <a name="sample-configurations"></a>Exempel konfigurationer

Vissa exempel som du kan implementera är följande connector grupper.
 
### <a name="default-configuration--no-use-for-connector-groups"></a>Standardkonfigurationen – ingen användning för koppling grupper

Om du inte använder connector grupper, konfigurationen skulle se ut så här:

![AzureAD inga Connector-grupper](./media/application-proxy-publish-apps-separate-networks/application-proxy-sample-config-1.png)
 
Den här konfigurationen är tillräcklig för mindre distributioner och tester. Den fungerar också bra om din organisation har en platt nätverkstopologi.
 
### <a name="default-configuration-and-an-isolated-network"></a>Standardkonfigurationen och ett isolerat nätverk

Den här konfigurationen är en utveckling av standardskrivaren där det finns en viss app som körs i ett isolerat nätverk, till exempel IaaS virtuellt nätverk: 

![AzureAD inga Connector-grupper](./media/application-proxy-publish-apps-separate-networks/application-proxy-sample-config-2.png)
 
### <a name="recommended-configuration--several-specific-groups-and-a-default-group-for-idle"></a>Rekommenderad konfiguration – flera specifika grupper och en standardgrupp för inaktiv

Den rekommenderade konfigurationen för stora och komplexa organisationer är att kopplingen standardgruppen som en grupp som inte fungerar för alla program och används för inaktiva eller nyligen installerade kopplingar. Alla program som hanteras med hjälp av anpassade connector grupper. Detta gör att alla komplexitet scenarier som beskrivs ovan.

I exemplet nedan har två datacenter, A och B, med två kopplingar som fungerar varje plats i företaget. Varje plats har olika program som körs på den. 

![AzureAD inga Connector-grupper](./media/application-proxy-publish-apps-separate-networks/application-proxy-sample-config-3.png)
 
## <a name="next-steps"></a>Nästa steg

* [Förstå Azure AD Application Proxy-kopplingar](application-proxy-understand-connectors.md)
* [Aktivera enkel inloggning](application-proxy-sso-overview.md)


