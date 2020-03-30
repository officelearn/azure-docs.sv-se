---
title: Vanliga frågor om Internet Analyzer | Microsoft-dokument
description: Vanliga frågor och svar för Azure Internet Analyzer.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: guide
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: a4a5b058666fab3e9048a7d92726dccd1360ff37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74184256"
---
# <a name="azure-internet-analyzer-faq-preview"></a>Vanliga frågor och svar om Azure Internet Analyzer (förhandsversion)

Detta är vanliga frågor och svar för Azure Internet Analyzer- om du har ytterligare frågor, gå till [feedback-forumet](https://aka.ms/internetAnalyzerFeedbackForum) och skicka din fråga. När en fråga ofta ställs lägger vi till den i den här artikeln så att den kan hittas snabbt och enkelt.

## <a name="how-do-i-participate-in-the-preview"></a>Hur deltar jag i förhandsgranskningen?

Förhandsgranskningen är tillgänglig för utvalda kunder. Om du är intresserad av att gå med i förhandsgranskningen gör du följande:

1. Logga in på [Azure-portalen](https://ms.portal.azure.com).
2. Navigera till sidan **Prenumerationer.**
3. Klicka på den Azure-prenumeration som du planerar att använda Internet Analyzer med.
4. Gå till inställningarna för **resursleverantörer** för prenumerationen.
5. Sök efter **Microsoft.Network** och klicka på knappen **Registrera (eller** **Registrera om).**
![begäran om åtkomst](./media/ia-faq/request-preview-access.png)

6. [Begär godkännande](https://aka.ms/internetAnalyzerContact) genom att ge oss din e-postadress och Azure-prenumerations-ID som användes för att göra åtkomstbegäran.
7. När din begäran har godkänts får du en e-postbekräftelse och kan skapa/uppdatera/ändra Internet Analyzer-resurser från den nyligen tillåtna Azure-prenumerationen.

## <a name="do-i-need-to-embed-the-client-to-run-a-test"></a>Måste jag bädda in klienten för att köra ett test?

Ja, Internet Analyzer-klienten måste installeras i ditt program för att samla in mått som är specifika för användarna. [Läs om hur du installerar klienten.](internet-analyzer-embed-client.md) 

## <a name="do-i-get-billed-for-participating-in-the-preview"></a>Faktureras jag för att delta i förhandsversionen?
Nej, Azure Internet Analyzer är gratis att använda i förhandsversion. Det finns inget servicenivåavtal under förhandsversionen.

## <a name="what-scenarios-is-internet-analyzer-designed-to-address"></a>Vilka scenarier är Internet Analyzer utformad för att ta itu med?

Internet Analyzer är utformat för att ge dig nätverksresultatinsikter baserat på din användarpopulation. För att göra de bästa prestandabesluten för användarna jämför Internet Analyzer prestanda för två Internet-slutpunkter med din distinkta användarpopulation. Internet Analyzer kan svara på en mängd frågor, men några av de vanligaste är:

* Vad är prestandaeffekten av att migrera till molnet? 
    * *Föreslaget test: Anpassad (din nuvarande lokala infrastruktur) jämfört med Azure (alla förkonfigurerade slutpunkter)*
* Vad är värdet av att sätta mina data på kanten kontra i ett datacenter? 
    *  *Föreslaget test: Azure vs. Azure Ytterdörr, Azure vs Azure CDN från Microsoft*
* Vad är prestandafördelarna med Azure Front Door?
    *  *Föreslaget test: Anpassad/ Azure/ CDN jämfört med Azure ytterdörr*
* Vad är prestandafördelarna med Azure CDN från Microsoft? 
    *  *Föreslaget test: Anpassad/ Azure/ AFD vs Azure CDN från Microsoft*
* Hur stack Azure CDN från Microsoft upp? 
    *  *Föreslaget test: Anpassad (annan CDN-slutpunkt) jämfört med Azure CDN från Microsoft*
* Vilket är det bästa molnet för din slutanvändare i varje region? 
    *  *Föreslaget test: Anpassad (annan molntjänst) jämfört med Azure (alla förkonfigurerade slutpunkter)*

## <a name="which-tests-can-i-run-in-preview"></a>Vilka tester kan jag köra i förhandsversion?

Varje test som du skapar i Internet Analyzer består av två slutpunkter – Slutpunkt A och Slutpunkt B. Någon av följande kombinationer kan köras som tester:  
* Två förkonfigurerade slutpunkter,
* En anpassad och en förkonfigurerad slutpunkt, eller
* Två [anpassade slutpunkter](internet-analyzer-custom-endpoint.md) (en anpassad slutpunkt måste finnas i Azure).

Följande förkonfigurerade slutpunkter är tillgängliga under förhandsgranskningen:
* **Azure-regioner**
    * Brasilien, södra
    * Indien, centrala
    * USA, centrala
    * Asien, östra
    * USA, östra
    * Japan, västra
    * Europa, norra
    * Sydafrika North
    * Sydostasien
    * Uae Norra
    * Storbritannien, västra  
    * Europa, västra
    * USA, västra
    * USA, västra 2
* **Kombinationer av flera Azure-regioner**
    * Östra USA, Södra Brasilien
    * Östra USA, Östasien
    * Västeuropa, Södra Brasilien
    * Västeuropa, Sydostasien
    * Västeuropa, UAE North
    * Västra USA, Östra USA
    * Västra USA, Västeuropa
    * Västra USA, UAE North
    * Västeuropa, UAE North, Sydostasien
    * Västra USA, Västeuropa, Östasien
    * Västra USA, Norra Europa, Sydostasien, UAE North, Sydafrika Nord 
* **Azure + Azure Ytterdörr** - distribueras på enstaka eller flera Azure-regionkombinationer som anges ovan
* **Azure + Azure CDN från Microsoft** – distribueras på en enskild kombination av Azure-regioner som anges ovan
* **Azure + Azure Traffic Manager** – distribueras på alla kombinationer av Flera Azure-regioner som anges ovan

## <a name="how-is-internet-analyzer-different-from-other-monitoring-services-provided-by-azure"></a>Hur skiljer sig Internet Analyzer från andra övervakningstjänster som tillhandahålls av Azure?

Internet Analyzer hjälper dig att förstå dina slutanvändares resultat och hjälper till att fatta beslut för att förbättra deras prestanda. Medan andra Azure-övervakningsverktyg ger insikter i dina Azure-tjänster fokuserar Internet Analyzer på att mäta prestanda från på internet för användarna.

## <a name="how-is-measurement-data-handled-by-internet-analyzer"></a>Hur hanteras mätdata av Internet Analyzer?

Azure har [starka säkerhetsprocesser och uppfyller ett brett spektrum av efterlevnadsstandarder](https://azure.microsoft.com/support/trust-center/). Endast du och ditt utsedda team har tillgång till dina data. Microsofts personal kan ha begränsad åtkomst till den endast under särskilda begränsade omständigheter med din vetskap. Det är krypterat under transport och i vila.

## <a name="next-steps"></a>Nästa steg

Mer information finns i [översikten över Internet Analyzer](internet-analyzer-overview.md).
