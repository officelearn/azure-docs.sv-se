---
title: Publicera program på separata nätverk och platser med hjälp av anslutningsapp-grupper i Azure AD App Proxy | Microsoft Docs
description: Beskriver hur du skapar och hanterar grupper över anslutningar i Azure AD-programproxy.
services: active-directory
author: barbkess
manager: daveba
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: barbkess
ms.reviewer: japere
ms.openlocfilehash: 2cc624cf47cddfe12532bdc7bf8cdc9a2858bb49
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54468652"
---
# <a name="publish-applications-on-separate-networks-and-locations-using-connector-groups"></a>Publicera program på separata nätverk och platser med hjälp av anslutningsapp-grupper

Kunder använda Azure AD-programproxy för fler scenarier och program. Så vi har gjort App Proxy ännu mer flexibel genom att aktivera flera topologier. Du kan skapa Application Proxy connector grupper så att du kan tilldela specifika anslutningsappar för att hantera specifika program. Den här funktionen ger dig bättre kontroll och sätt att optimera din Application Proxy-distribution. 

Varje programproxy-kopplingen har tilldelats en anslutningsgrupp. Alla kopplingar som tillhör samma anslutningsgruppen fungerar som en separat enhet för hög tillgänglighet och belastningsutjämning. Alla kopplingar som hör till en anslutningsgrupp. Om du inte skapa grupper, är alla kopplingar i en standardgrupp. Administratören kan skapa nya grupper och tilldela kopplingar till dem i Azure-portalen. 

Alla program som har tilldelats en anslutningsgrupp. Om du inte skapa grupper, tilldelas alla dina program till en standardgrupp. Men du kan ange programmen för att arbeta med en viss koppling grupp om du organisera dina anslutningsprogram i grupper. I det här fallet fungerar endast kopplingar i gruppen programmet på begäran. Den här funktionen är användbar om dina program finns på olika platser. Du kan skapa anslutningsapp-grupper baserat på plats, så att program alltid betjänas av kopplingar som är fysiskt nära de.

>[!TIP] 
>Om du har en stor Application Proxy-distribution kan inte tilldela alla program i standardgruppen för anslutningen. På så sätt kan nya anslutningar inte tar emot live trafik tills du tilldelar dem till en aktiv anslutningsapp-grupp. Den här konfigurationen kan du placera kopplingar i ett inaktivt tillstånd genom att flytta dem till standardgruppen, så att du kan utföra underhåll utan att påverka dina användare.

## <a name="prerequisites"></a>Förutsättningar
Om du vill gruppera dina anslutningar, du måste se till att du [installerade flera kopplingar](application-proxy-add-on-premises-application.md). När du installerar en ny anslutning kan den automatiskt ansluter till den **standard** anslutningsgrupp.

## <a name="create-connector-groups"></a>Skapa anslutningsapp-grupper
Följ dessa steg för att skapa så många anslutningsapp-grupper som du vill ha. 

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Välj **Azure Active Directory** > **företagsprogram** > **programproxy**.
2. Välj **ny anslutningsgrupp**. Ny Anslutningsgrupp bladet visas.

   ![Välj ny anslutningsgrupp](./media/application-proxy-connector-groups/new-group.png)

3. Namnge din ny anslutningsgrupp och sedan använda den nedrullningsbara menyn för att välja vilka kopplingar som tillhör den här gruppen.
4. Välj **Spara**.

## <a name="assign-applications-to-your-connector-groups"></a>Tilldela program till dina anslutningsapp-grupper
Följ dessa steg för varje program som du har publicerat med programproxyn. Du kan tilldela ett program till en anslutningsprogramgrupp när du publicerar den, eller du kan använda de här stegen för att ändra tilldelningen när som helst.   

1. Hanteringspanel för din katalog väljer du **företagsprogram** > **alla program** > program som du vill tilldela till en anslutningsprogramgrupp > **Programproxy**.
2. Använd den **Anslutningsgrupp** går du till menyn och välj den grupp du vill att programmet använder.
3. Välj **spara** tillämpa ändringen.

## <a name="use-cases-for-connector-groups"></a>Användningsområden för anslutningsapp-grupper 

Anslutningsapp-grupper är användbara för olika scenarier, inklusive:

### <a name="sites-with-multiple-interconnected-datacenters"></a>Platser med flera sammankopplade Datacenter

Många organisationer har ett antal sammankopplade datacenter. I detta fall använder vill du behålla så mycket trafik i datacentret som möjligt eftersom mellan datacenter länkar är dyra och långsamma. Du kan distribuera anslutningsappar i varje datacenter som fungerar endast de program som finns i datacentret. Den här metoden minimerar över flera datacenter länkar och ger en helt transparent upplevelse för användarna.

### <a name="applications-installed-on-isolated-networks"></a>Program som har installerats på isolerade nätverk

Program kan finnas i nätverk som inte är en del av de viktigaste företagets nätverket. Du kan använda anslutningsapp-grupper för att installera dedikerade anslutningar på isolerade nätverk att också isolera program till nätverket. Detta inträffar vanligtvis när en utomstående leverantör underhåller ett visst program för din organisation. 

Anslutningsapp-grupper kan du installera dedikerade anslutningar för dessa nätverk som publicerar endast specifika program, vilket gör det enklare och säkrare flytta över programhantering till andra leverantörer.

### <a name="applications-installed-on-iaas"></a>Program som har installerats på IaaS 

För program som är installerade på IaaS för molnåtkomst anger anslutningsapp-grupper du en common service för att säkra åtkomst till alla appar. Anslutningsappgrupper inte skapa ytterligare beroende på företagets nätverk eller Fragmentera app-upplevelse. Kopplingar kan installeras på varje datacenter i molnet och fungerar bara program som finns i nätverket. Du kan installera flera kopplingar för att uppnå hög tillgänglighet.

Ta som exempel en organisation som har flera virtuella datorer som är anslutna till sina egna IaaS finns virtuellt nätverk. Om du vill låta de anställda använder dessa program, är dessa privata nätverk anslutna till företagsnätverket med hjälp av plats-till-plats-VPN. Detta ger en bra upplevelse för anställda som finns lokalt. Men det kanske inte är perfekt för fjärranslutna anställda, eftersom den kräver ytterligare lokal infrastruktur för att dirigera åtkomst, som du ser i diagrammet nedan:

![AzureAD IaaS-nätverk](./media/application-proxy-connector-groups/application-proxy-iaas-network.png)
  
Du kan aktivera en common service att säkra åtkomst till alla program utan att skapa ytterligare beroende på företagets nätverk med Azure AD Application Proxy connector grupper:

![AzureAD IaaS flera Molnleverantörer](./media/application-proxy-connector-groups/application-proxy-multiple-cloud-vendors.png)

### <a name="multi-forest--different-connector-groups-for-each-forest"></a>Flera skogar – olika anslutningsapp-grupper för varje skog

De flesta kunder som har distribuerat Application Proxy använder affärsmodellen single-sign-on (SSO) genom att utföra Kerberos-begränsad delegering (KCD). Kopplingens datorer måste vara ansluten till en domän som kan delegera användare till programmet för att uppnå detta. KCD stöder mellan skogar funktioner. Men för företag som har olika miljöer med Multi-Forest utan förtroende mellan dem, en enda anslutning kan inte användas för alla skogar. 

I det här fallet kan specifika anslutningsappar distribueras per skog, och ange att leverera program som har publicerats så att den stöder enbart användare av den specifika skogen. Varje anslutningsgrupp representerar en annan skog. När klienten och de flesta av upplevelsen är enhetligt för alla skogar, kan användare tilldelas till skogen program med hjälp av Azure AD-grupper.
 
### <a name="disaster-recovery-sites"></a>Katastrofåterställningsplatser

Det finns två olika metoder som du kan använda med en plats för disaster recovery (DR), beroende på hur dina webbplatser implementeras:

* Om din DR-plats har skapats i aktivt-aktivt läge där det är precis som den huvudsakliga platsen och har samma nätverks- och AD-inställningar, kan du skapa kopplingar på DR-plats i anslutningsgruppen samma som den huvudsakliga platsen. Detta gör att Azure AD för att identifiera redundansväxlingar för dig.
* Om din DR-plats som är separat från den huvudsakliga platsen, kan du skapa en annan anslutningsgrupp i DR-plats, och antingen (1) har säkerhetskopieringsprogram eller 2) manuellt använda ett annat befintligt program till anslutningsgrupp DR efter behov.
 
### <a name="serve-multiple-companies-from-a-single-tenant"></a>Hantera flera företag från en enda klient

Det finns många olika sätt att implementera en modell där en enda tjänstleverantör distribuerar och underhåller Azure AD-relaterade tjänster för flera företag. Anslutningsappgrupper hjälpa administratören att särskilja kopplingar och program i olika grupper. Ett sätt som är lämplig för små företag, är att ha en enda Azure AD-klient och olika företag har sina egna domännamn och nätverk. Detta gäller även för M & A scenarier och situationer där en enskild IT-avdelning har flera företag regler eller företag skäl. 

## <a name="sample-configurations"></a>Exempel-konfigurationer

Några exempel som du kan implementera är följande anslutningsapp-grupper.
 
### <a name="default-configuration--no-use-for-connector-groups"></a>Standardkonfigurationen – ingen användning för anslutningsapp-grupper

Om du inte använder anslutningsapp-grupper, konfigurationen skulle se ut så här:

![AzureAD ingen Anslutningsapp-grupper](./media/application-proxy-connector-groups/application-proxy-sample-config-1.png)
 
Den här konfigurationen är tillräckligt för mindre distributioner och tester. Det fungerar också bra om din organisation har en fast nätverkstopologi.
 
### <a name="default-configuration-and-an-isolated-network"></a>Standardkonfigurationen och ett isolerat nätverk

Den här konfigurationen är en utveckling av standardvärdet, där det finns en viss app som körs i ett isolerat nätverk, till exempel IaaS virtuellt nätverk: 

![AzureAD ingen Anslutningsapp-grupper](./media/application-proxy-connector-groups/application-proxy-sample-config-2.png)
 
### <a name="recommended-configuration--several-specific-groups-and-a-default-group-for-idle"></a>Rekommenderade konfiguration – flera specifika grupper och en standardgrupp för inaktiv

Den rekommenderade konfigurationen för stora och komplexa organisationer är att ha anslutningsgrupp standard som en grupp som betjänar inte alla program och används för inaktiva eller nyligen installerade kopplingar. Alla program som hanteras med hjälp av anpassade anslutningsapp-grupper. På så sätt kan alla komplexitet scenarier som beskrivs ovan.

I exemplet nedan har två datacenter, A och B, med två kopplingar som betjänar varje plats i företaget. Varje plats har olika program som körs på den. 

![AzureAD ingen Anslutningsapp-grupper](./media/application-proxy-connector-groups/application-proxy-sample-config-3.png)
 
## <a name="next-steps"></a>Nästa steg

* [Förstå Azure AD Application Proxy-anslutningar](application-proxy-connectors.md)
* [Aktivera enkel inloggning](what-is-single-sign-on.md)


