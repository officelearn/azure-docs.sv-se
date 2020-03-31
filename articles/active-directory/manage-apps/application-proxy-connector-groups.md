---
title: Publicera appar i separata nätverk via anslutningsgrupper - Azure AD
description: Beskriver hur du skapar och hanterar grupper av kopplingar i Azure AD Application Proxy.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22fa1de0a0e3bb91480212381e07b17875bf0bf4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74275568"
---
# <a name="publish-applications-on-separate-networks-and-locations-using-connector-groups"></a>Publicera program i separata nätverk och platser med hjälp av anslutningsgrupper

Kunder använder Azure AD:s programproxy för fler och fler scenarier och program. Så vi har gjort App Proxy ännu mer flexibel genom att möjliggöra fler topologier. Du kan skapa application proxy-anslutningsgrupper så att du kan tilldela specifika kopplingar för att betjäna specifika program. Den här funktionen ger dig mer kontroll och sätt att optimera distributionen av programproxy.

Varje Programproxykoppling tilldelas en kopplingsgrupp. Alla kopplingar som tillhör samma kopplingsgrupp fungerar som en separat enhet för hög tillgänglighet och belastningsutjämning. Alla kopplingar tillhör en kopplingsgrupp. Om du inte skapar grupper finns alla kopplingar i en standardgrupp. Din administratör kan skapa nya grupper och tilldela kopplingar till dem i Azure-portalen.

Alla program tilldelas en kopplingsgrupp. Om du inte skapar grupper tilldelas alla program till en standardgrupp. Men om du ordnar kopplingarna i grupper kan du ställa in varje program så att det fungerar med en viss kopplingsgrupp. I det här fallet tjänar endast kopplingarna i den gruppen programmet på begäran. Den här funktionen är användbar om dina program finns på olika platser. Du kan skapa kopplingsgrupper baserat på plats, så att program alltid betjänas av kopplingar som är fysiskt nära dem.

> [!TIP]
> Om du har en stor application proxy-distribution ska du inte tilldela några program till standardkopplingsgruppen. På så sätt får nya kopplingar ingen direkttrafik förrän du tilldelar dem till en aktiv kopplingsgrupp. Med den här konfigurationen kan du också placera kopplingar i inaktivt läge genom att flytta tillbaka dem till standardgruppen, så att du kan utföra underhåll utan att påverka användarna.

## <a name="prerequisites"></a>Krav

Om du vill gruppera kopplingarna måste du se till att du [har installerat flera kopplingar](application-proxy-add-on-premises-application.md). När du installerar en ny koppling **Default** sammanfogas den automatiskt standardkopplingsgruppen för anslutningsappar.

## <a name="create-connector-groups"></a>Skapa kopplingsgrupper

Använd de här stegen om du vill skapa så många kopplingsgrupper du vill.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **Azure Active Directory** > **Enterprise-programprogramproxy** > **Application proxy**.
1. Välj **Ny kopplingsgrupp**. Bladet Ny kopplingsgrupp visas.

   ![Visar skärmen för att välja en ny kopplingsgrupp](./media/application-proxy-connector-groups/new-group.png)

1. Ge den nya kopplingsgruppen ett namn och använd sedan rullgardinsmenyn för att välja vilka kopplingar som hör till den här gruppen.
1. Välj **Spara**.

## <a name="assign-applications-to-your-connector-groups"></a>Tilldela program till dina anslutningsgrupper

Följ dessa steg för varje program som du har publicerat med Programproxy. Du kan tilldela ett program till en kopplingsgrupp när du först publicerar det, eller så kan du använda de här stegen för att ändra tilldelningen när du vill.

1. Välj **Enterprise-program** > **Alla program** > det program som du vill tilldela en anslutningsgrupp > Application **Proxy**i katalogen.
1. Använd listrutan **Kopplingsgrupp** för att välja den grupp som du vill att programmet ska använda.
1. Välj **Spara** om du vill tillämpa ändringen.

## <a name="use-cases-for-connector-groups"></a>Användningsfall för kopplingsgrupper

Anslutningsgrupper är användbara för olika scenarier, bland annat:

### <a name="sites-with-multiple-interconnected-datacenters"></a>Platser med flera sammankopplade datacenter

Många organisationer har ett antal sammankopplade datacenter. I det här fallet vill du behålla så mycket trafik i datacentret som möjligt eftersom länkar mellan datacenter är dyra och långsamma. Du kan distribuera kopplingar i varje datacenter för att endast betjäna de program som finns i datacentret. Den här metoden minimerar länkar mellan datacenter och ger en helt transparent upplevelse för dina användare.

### <a name="applications-installed-on-isolated-networks"></a>Program installerade i isolerade nätverk

Program kan finnas i nätverk som inte ingår i huvudnätverket. Du kan använda anslutningsgrupper för att installera dedikerade anslutningsappar i isolerade nätverk för att även isolera program till nätverket. Detta händer vanligtvis när en tredjepartsleverantör underhåller ett specifikt program för din organisation.

Med anslutningsgrupper kan du installera dedikerade anslutningsappar för de nätverk som bara publicerar specifika program, vilket gör det enklare och säkrare att lägga ut programhantering på entreprenad till tredjepartsleverantörer.

### <a name="applications-installed-on-iaas"></a>Program installerade på IaaS

För program som är installerade på IaaS för molnåtkomst tillhandahåller anslutningsgrupper en gemensam tjänst för att skydda åtkomsten till alla appar. Anslutningsgrupper skapar inte ytterligare beroende av företagets nätverk eller fragmenterar appupplevelsen. Anslutningar kan installeras på alla molndatacenter och endast betjänar program som finns i nätverket. Du kan installera flera anslutningsappar för att uppnå hög tillgänglighet.

Ta som exempel en organisation som har flera virtuella datorer anslutna till sitt eget IaaS-värdbaserade virtuella nätverk. För att anställda ska kunna använda dessa program är dessa privata nätverk anslutna till företagsnätverket med hjälp av plats-till-plats-VPN. Detta ger en bra upplevelse för medarbetare som finns lokalt. Men det kanske inte är idealiskt för fjärranställda, eftersom det kräver ytterligare lokal infrastruktur för att dirigera åtkomst, som du kan se i diagrammet nedan:

![Diagram som illustrerar Azure AD IaaS-nätverket](./media/application-proxy-connector-groups/application-proxy-iaas-network.png)
  
Med Azure AD Application Proxy-anslutningsgrupper kan du aktivera en gemensam tjänst för att skydda åtkomsten till alla program utan att skapa ytterligare beroende av företagets nätverk:

![Azure AD IaaS flera molnleverantörer](./media/application-proxy-connector-groups/application-proxy-multiple-cloud-vendors.png)

### <a name="multi-forest--different-connector-groups-for-each-forest"></a>Multi-skog – olika kopplingsgrupper för varje skog

De flesta kunder som har distribuerat Programproxy använder sina SSO-funktioner (Single-sign-on) genom att utföra KCD (Kerberos Constrained Delegation). För att uppnå detta måste kopplingens datorer anslutas till en domän som kan delegera användarna mot programmet. KCD stöder funktioner över skogen. Men för företag som har olika miljöer med flera skogar utan förtroende mellan dem kan en enda koppling inte användas för alla skogar. 

I det här fallet kan specifika kopplingar distribueras per skog och ställas in för att betjäna program som har publicerats för att endast betjäna användarna av den specifika skogen. Varje kopplingsgrupp representerar en annan skog. Innehavaren och den mesta upplevelsen är enhetlig för alla skogar, men användare kan tilldelas sina skogsprogram med Azure AD-grupper.

### <a name="disaster-recovery-sites"></a>Platser för katastrofåterställning

Det finns två olika tillvägagångssätt som du kan ta med en DR-plats (Disaster Recovery), beroende på hur dina webbplatser implementeras:

* Om DR-platsen är inbyggd i aktivt aktivt läge där den är precis som huvudplatsen och har samma nätverks- och AD-inställningar kan du skapa anslutningsapparna på DR-platsen i samma anslutningsgrupp som huvudplatsen. Detta gör det möjligt för Azure AD att identifiera redundans för dig.
* Om DR-platsen är skild från huvudplatsen kan du skapa en annan anslutningsgrupp på DR-platsen, och antingen 1) har säkerhetskopieringsprogram eller 2) manuellt vidarekoppla det befintliga programmet till DR-anslutningsgruppen efter behov.

### <a name="serve-multiple-companies-from-a-single-tenant"></a>Betjäna flera företag från en enda hyresgäst

Det finns många olika sätt att implementera en modell där en enda tjänsteleverantör distribuerar och underhåller Azure AD-relaterade tjänster för flera företag. Anslutningsgrupper hjälper administratören att segregera kopplingarna och programmen i olika grupper. Ett sätt, som är lämpligt för små företag, är att ha en enda Azure AD-klient samtidigt som de olika företagen har sitt eget domännamn och nätverk. Detta gäller även för M&A scenarier och situationer där en enda IT-division betjänar flera företag av reglerings- eller affärsskäl.

## <a name="sample-configurations"></a>Exempel på konfigurationer

Några exempel som du kan implementera är följande kopplingsgrupper.

### <a name="default-configuration--no-use-for-connector-groups"></a>Standardkonfiguration – ingen användning för kopplingsgrupper

Om du inte använder kopplingsgrupper ser konfigurationen ut så här:

![Exempel på Azure AD No Connector Groups](./media/application-proxy-connector-groups/application-proxy-sample-config-1.png)

Den här konfigurationen är tillräcklig för små distributioner och tester. Det kommer också att fungera bra om din organisation har en platt nätverkstopologi.

### <a name="default-configuration-and-an-isolated-network"></a>Standardkonfiguration och ett isolerat nätverk

Den här konfigurationen är en utveckling av standardkonfigurationen, där det finns en specifik app som körs i ett isolerat nätverk som IaaS virtuella nätverk:

![Exempel på Azure AD No Connector Groups och ett isolerat nätverk](./media/application-proxy-connector-groups/application-proxy-sample-config-2.png)

### <a name="recommended-configuration--several-specific-groups-and-a-default-group-for-idle"></a>Rekommenderad konfiguration – flera specifika grupper och en standardgrupp för inaktiv

Den rekommenderade konfigurationen för stora och komplexa organisationer är att ha standardkopplingsgruppen som en grupp som inte betjänar några program och som används för inaktiva eller nyinstallerade kopplingar. Alla program visas med anpassade anslutningsgrupper. Detta möjliggör all komplexitet i de scenarier som beskrivs ovan.

I exemplet nedan har företaget två datacenter, A och B, med två kopplingar som betjänar varje plats. Varje plats har olika program som körs på den.

![Exempel på företag med 2 datacenter och 2 anslutningar](./media/application-proxy-connector-groups/application-proxy-sample-config-3.png)

## <a name="next-steps"></a>Nästa steg

* [Förstå Azure AD-programproxy-kopplingar](application-proxy-connectors.md)
* [Aktivera enkel inloggning](what-is-single-sign-on.md)
