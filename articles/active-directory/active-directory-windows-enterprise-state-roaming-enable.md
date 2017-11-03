---
title: "Aktivera Företagsroaming i Azure Active Directory | Microsoft Docs"
description: "Vanliga frågor om Enterprise tillstånd centrala inställningar i Windows-enheter. Enterprise tillstånd centrala ger användarna en enhetlig miljö på sina Windows-enheter och minskar den tid som krävs för att konfigurera en ny enhet."
services: active-directory
keywords: "företagsroaming, windows molnet, hur du aktiverar enterprise tillstånd nätverksväxling"
documentationcenter: 
author: tanning
manager: femila
editor: curtand
ms.assetid: f71d66fd-7f9e-45eb-9cfe-5d989870f8a4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2017
ms.author: markvi
ms.openlocfilehash: 71212d11452d5f263b8621e1f0c13f9edd744618
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="enable-enterprise-state-roaming-in-azure-active-directory"></a>Aktivera enterprise tillståndsväxling i Azure Active Directory
Enterprise tillstånd växling är tillgängliga för en organisation med en Azure AD Premium eller Enterprise Mobility + Security (EMS)-licens. Mer information om hur du skaffa en Azure AD-prenumeration finns i [produktsida för Azure AD](https://azure.microsoft.com/services/active-directory).

När du aktiverar Enterprise tillstånd centrala beviljas automatiskt en licens för kostnadsfria, begränsad användning för Azure Rights Management i din organisation. Den här kostnadsfria prenumerationen är begränsad till kryptera och dekryptera Företagsinställningar och programdata synkroniseras med Enterprise tillstånd nätverksväxling. Du måste ha [en betald prenumeration](https://azure.microsoft.com/pricing/details/active-directory/) att använda alla funktioner i Azure Rights Management.

## <a name="to-enable-enterprise-state-roaming"></a>Aktivera centrala Enterprise-tillstånd

1. Logga in på [administrationscentret för Azure AD](https://aad.portal.azure.com/).

2. Välj **Azure Active Directory** &gt; **enheter** &gt; **Enhetsinställningar**.

3. Välj **kan användarna synkronisera AppData och inställningar mellan enheter**. Mer information finns i [hur du konfigurerar Enhetsinställningar](https://docs.microsoft.com/azure/active-directory/device-management-azure-portal).
  
  ![Bild av enhetsinställning etikett användare kan synkronisera inställningar och AppData på enheter](./media/active-directory-windows-enterprise-state-roaming-enable/device-settings.png)
  
Enheten måste autentiseras med hjälp av en Azure AD-identitet för för en Windows 10-enhet Enterprise tillstånd Roaming-tjänsten. För enheter som är anslutna till Azure AD är användarens primära inloggning identitet sin Azure AD-identitet så krävs ingen ytterligare konfiguration. För enheter som använder lokala Active Directory-IT-administratören måste [ansluta domänanslutna enheter till Azure AD för Windows 10-upplevelser](active-directory-azureadjoin-devices-group-policy.md).

## <a name="data-storage"></a>Datalagring
Enterprise tillstånd centrala data finns i en eller flera [Azure-regioner](https://azure.microsoft.com/regions/) som bäst överensstämmer med värdet för land/region i Azure Active Directory-instans. Enterprise tillstånd centrala data är partitionerad baserat på tre huvudsakliga geografiska områden: Nordamerika, EMEA och APAC. Enterprise tillstånd centrala data för klienten finns lokalt med den geografiska regionen och replikeras inte över regioner.  Till exempel::
Värdet för land/region | sina data har finns i
---------------------|-------------------------
EMEA-land, till exempel ”Frankrike” eller ”Zambia” | en eller i Azure-regioner inom Europa 
Ett Nordamerika land, till exempel ”USA” eller ”Kanada” | en eller flera Azure-regioner inom USA
En APAC land, till exempel ”Australien” eller ”Nya Zeeland” | en eller flera Azure-regioner i Asien
Sydamerika och Antarktis områden | en eller flera Azure-regioner inom USA

Land/region-värdet har angetts som en del av processen för Azure AD-katalogen och kan inte ändras senare. Om du behöver mer information på din plats för datalagring filen en biljett hos [Azure-supporten](https://azure.microsoft.com/support/options/).

## <a name="view-per-user-device-sync-status"></a>Visa synkroniseringsstatus för användarnas enheter
Följ dessa steg om du vill visa en statusrapport för per användare enheten synkronisering.

1. Logga in på [administrationscentret för Azure AD](https://aad.portal.azure.com/).

2. Välj **Azure Active Directory** &gt; **användare och grupper** &gt; **alla användare**.

3. Välj användaren och välj sedan **enheter**.

4. Under **visa**väljer **enheter som synkroniserar inställningar och AppData** att visa synkroniseringsstatus.
  
  ![Bild av enhetsinställning synkronisera data](./media/active-directory-windows-enterprise-state-roaming-enable/sync-status.png)
  
5. Om enheter som synkroniserar för den här användaren finns enheterna som visas här.
  
  ![Bild av kolumner enhetsdata för synkronisering](./media/active-directory-windows-enterprise-state-roaming-enable/device-status-row.png)

## <a name="data-retention"></a>Datakvarhållning
Data som synkroniseras till Azure med hjälp av Enterprise tillstånd centrala bevaras tills den raderas manuellt eller i fråga data bedöms vara inaktuell. 

### <a name="explicit-deletion"></a>Explicit borttagning
Explicit borttagning är när en Azure-administratör tar bort en användare eller en katalog eller annars begär uttryckligen att data som ska tas bort.

* **Ta bort användaren**: när en användare tas bort i Azure AD, användarkontot centrala data tas bort efter 90 till 180 dagar. 
* **Ta bort katalogen**: ta bort en hel katalog i Azure AD är en omedelbar åtgärd. Alla för inställningsdata som är associerade med den katalogen tas bort efter 90 till 180 dagar. 
* **På begäran om borttagning**: om Azure AD-administratör vill ta bort data eller inställningsdata för en viss användare manuellt, kan administratören filen en biljett hos [Azure-supporten](https://azure.microsoft.com/support/). 

### <a name="stale-data-deletion"></a>Inaktuella data tas bort
Data som inte har öppnats i ett år (”kvarhållningsperioden”) kommer att behandlas som föråldrade och kan tas bort från Azure. Kvarhållningsperioden kan ändras, men inte mindre än 90 dagar. Inaktuella data kan vara en specifik uppsättning inställningar för Windows-program eller alla inställningar för en användare. Exempel:

* Om inga enheter åtkomst till en samling med särskilda inställningar (till exempel ett program tas bort från enheten, eller en grupp inställningar, till exempel ”tema” är inaktiverad för alla enheter som en användare), och sedan samlingen blir inaktiv efter kvarhållningsperioden och kan tas bort . 
* Om en användare har inaktiverat synkronisera inställningar på sina enheter, inga inställningsdata kommer att komma åt och alla inställningsdata för den användaren blir inaktuella och kan tas bort efter kvarhållningsperioden. 
* Om Azure AD directory admin inaktiverar Enterprise tillstånd centrala för hela katalogen, sedan alla användare i den directory stoppas synkroniserar inställningar och inställningsdata för alla för alla användare blir inaktuella och kan tas bort efter kvarhållningsperioden. 

### <a name="deleted-data-recovery"></a>Återställa borttagna data
Databevarandeprincip kan inte konfigureras. När data raderas permanent, kan den inte återställas. Dock tas inställningsdata bort från Azure, inte från slutanvändarens enhet. Om alla enheter senare återansluts till tjänsten Enterprise tillstånd Roaming, synkroniseras inställningarna igen och lagras i Azure.

## <a name="related-topics"></a>Relaterade ämnen
* [Företagsroaming](active-directory-windows-enterprise-state-roaming-overview.md)
* [Inställningar och dataroaming vanliga frågor och svar](active-directory-windows-enterprise-state-roaming-faqs.md)
* [Inställningar för princip och MDM för inställningar för synkronisering](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
* [Centrala referens för Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
* [Felsökning](active-directory-windows-enterprise-state-roaming-troubleshooting.md)
