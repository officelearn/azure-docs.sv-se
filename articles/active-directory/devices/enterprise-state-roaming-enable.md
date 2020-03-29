---
title: Aktivera enterprise tillståndsväxling i Azure Active Directory
description: Vanliga frågor och svar om roaminginställningar för företagtillstånd i Windows-enheter.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8c44d6266f5ea8cdd4f75d0449cb49852e71c905
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672402"
---
# <a name="enable-enterprise-state-roaming-in-azure-active-directory"></a>Aktivera enterprise tillståndsväxling i Azure Active Directory

Företagsstat Roaming är tillgängligt för alla organisationer med en Azure AD Premium- eller Enterprise Mobility + Security (EMS)-licens. Mer information om hur du skaffar en Azure AD-prenumeration finns på [produktsidan för Azure AD](https://azure.microsoft.com/services/active-directory).

När du aktiverar Roaming i Företagstat beviljas din organisation automatiskt en kostnadsfri licens för Azure Rights Management-skydd från Azure Rights Management-skydd från Azure Information Protection. Den här kostnadsfria prenumerationen är begränsad till att kryptera och dekryptera företagsinställningar och programdata som synkroniseras av Företagsstat Roaming. Du måste ha [en betald prenumeration](https://azure.microsoft.com/pricing/details/information-protection/) för att kunna använda alla funktioner i Azure Rights Management-tjänsten.

> [!NOTE]
> Den här artikeln gäller den HTML-baserade webbläsaren Microsoft Edge Legacy som lanserades med Windows 10 i juli 2015. Artikeln gäller inte den nya Microsoft Edge Chromium-baserade webbläsaren som släpptes den 15 januari 2020. Mer information om synkroniseringsbeteendet för den nya Microsoft Edge finns i artikeln [Microsoft Edge Sync](/deployedge/microsoft-edge-enterprise-sync).

## <a name="to-enable-enterprise-state-roaming"></a>Så här aktiverar du Roaming i företagstillstånd

1. Logga in på [Azure AD admin center](https://aad.portal.azure.com/).
1. Välj **Azure Active Directory** &gt; **Devices** &gt; **Företagstillstånd Roaming**.
1. Välj **Användare kan synkronisera inställningar och appdata mellan enheter**. Mer information finns i [hur du konfigurerar enhetsinställningar](/azure/active-directory/device-management-azure-portal).
  
   ![bild av enhetsinställningen med etiketten Användare kan synkronisera inställningar och appdata mellan enheter](./media/enterprise-state-roaming-enable/device-settings.png)
  
För att en Windows 10-enhet ska kunna använda tjänsten Företagstillståndsroamering måste enheten autentisera med hjälp av en Azure AD-identitet. För enheter som är anslutna till Azure AD är användarens primära inloggningsidentitet deras Azure AD-identitet, så ingen ytterligare konfiguration krävs. För enheter som använder lokal Active Directory måste IT-administratören [konfigurera hybrid-Azure Active Directory-anslutna enheter](hybrid-azuread-join-manual-steps.md). 

## <a name="data-storage"></a>Datalagring

Roamingdata för företagtillstånd finns i en eller flera [Azure-regioner](https://azure.microsoft.com/regions/) som bäst stämmer överens med värdet för land/region i Azure Active Directory-instansen. Centrala företagsdata är uppdelade baserat på tre större geografiska regioner: Nordamerika, EMEA och APAC. Roamingdata för företag för klienten är lokalt lokaliserade med den geografiska regionen och replikeras inte mellan regioner.  Ett exempel:

| Värde för land/region | har sina uppgifter värd i |
| -------------------- | ------------------------ |
| Ett EMEA-land/-region som Frankrike eller Zambia | En eller flera av Azure-regionerna i Europa |
| Ett nordamerikanskt land/en region som USA eller Kanada | En eller flera av Azure-regionerna i USA |
| Ett APAC-land/-region som Australien eller Nya Zeeland | En eller flera av Azure-regionerna i Asien |
| Sydamerikanska och Antarktis regioner | En eller flera Azure-regioner i USA |

Värdet för land/region anges som en del av Azure AD-katalogens skapandeprocess och kan inte ändras senare. Om du behöver mer information om din lagringsplats för datafiler kan du lämna in en biljett med [Azure-support](https://azure.microsoft.com/support/options/).

## <a name="view-per-user-device-sync-status"></a>Visa synkroniseringsstatus per användare

Följ dessa steg för att visa en synkroniseringsstatusrapport per användare.

1. Logga in på [Azure AD admin center](https://aad.portal.azure.com/).
1. Välj **Azure Active Directory** &gt; **-användare** &gt; **alla användare**.
1. Markera användaren och välj sedan **Enheter**.
1. Under **Visa**väljer du **Enheter som synkroniserar inställningar och appdata** för att visa synkroniseringsstatus.
  
   ![bild av enhetssynkroniseringsdatainställning](./media/enterprise-state-roaming-enable/sync-status.png)
  
1. Om det finns enheter som synkroniseras för den här användaren visas enheterna som visas här.
  
   ![bild av kolumndata för enhetssynkronisering](./media/enterprise-state-roaming-enable/device-status-row.png)

## <a name="data-retention"></a>Datakvarhållning

Data som synkroniseras med Microsoft-molnet med Hjälp av Företagsstatsroaming behålls tills den tas bort manuellt eller tills data i fråga bedöms vara inaktuella. 

### <a name="explicit-deletion"></a>Explicit radering

Explicit borttagning är när en Azure-administratör tar bort en användare eller en katalog eller på annat sätt uttryckligen begär att data ska tas bort.

* **Borttagning av användare**: När en användare tas bort i Azure AD tas roamingdata för användarkontot bort efter 90 till 180 dagar. 
* **Katalogborttagning:** Ta bort en hel katalog i Azure AD är en omedelbar åtgärd. Alla inställningsdata som är associerade med katalogen tas bort efter 90 till 180 dagar. 
* **Vid borttagning av begäran**: Om Azure AD-administratören vill ta bort en viss användares data eller inställningsdata manuellt kan administratören lämna in en biljett med [Azure-stöd](https://azure.microsoft.com/support/). 

### <a name="stale-data-deletion"></a>Borttagning av inaktuella data

Data som inte har använts på ett år ("kvarhållningsperioden") behandlas som inaktuella och kan tas bort från Microsoft-molnet. Kvarhållningsperioden kan komma att ändras men kommer inte att vara kortare än 90 dagar. De inaktuella data kan vara en specifik uppsättning Windows/programinställningar eller alla inställningar för en användare. Ett exempel:

* Om inga enheter har åtkomst till en viss inställningssamling (till exempel tas ett program bort från enheten eller en inställningsgrupp som "Tema" är inaktiverad för alla en användares enheter), blir samlingen inaktuell efter kvarhållningsperioden och kan tas bort . 
* Om en användare har inaktiverat synkronisering av inställningar på alla sina enheter kommer ingen av inställningsdata att nås och alla inställningsdata för den användaren blir inaktuella och kan tas bort efter lagringsperioden. 
* Om Azure AD-katalogadministratören inaktiverar Roaming i företagstillstånd för hela katalogen kommer alla användare i den katalogen att sluta synkronisera inställningar och alla inställningsdata för alla användare blir inaktuella och kan tas bort efter kvarhållningsperioden. 

### <a name="deleted-data-recovery"></a>Borttagen dataåterställning

Datalagringsprincipen kan inte konfigureras. När data har tagits bort permanent kan de inte återställas. Inställningsdata tas dock bara bort från Microsoft-molnet, inte från slutanvändarens enhet. Om någon enhet senare återansluter till tjänsten Företagstillstånd synkroniseras och lagras inställningarna igen i Microsoft-molnet.

## <a name="next-steps"></a>Nästa steg

* [Översikt över enterprise tillståndsväxling](enterprise-state-roaming-overview.md)
* [Vanliga frågor och svar om inställningar och dataväxling](enterprise-state-roaming-faqs.md)
* [Grupprincip- och MDM-inställningar för inställningar synkroniseras](enterprise-state-roaming-group-policy-settings.md)
* [Referens för Windows 10-växlingsinställningar](enterprise-state-roaming-windows-settings-reference.md)
* [Troubleshooting](enterprise-state-roaming-troubleshooting.md) (Felsökning)
