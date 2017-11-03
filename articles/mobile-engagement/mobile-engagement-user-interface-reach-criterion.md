---
title: "Användargränssnittet för Azure Mobile Engagement - Reach kriterium"
description: "Lär dig hur du använder målobjekt kriterier för att skicka push-kampanjer till en del av dina användare med Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: a4ed03a0-55b1-4dd8-b0bd-c475005afb66
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 803b44721d0ab1ac7b5a8074e18857fc57adb724
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-targeting-criteria-to-send-push-campaigns-to-a-select-subset-of-your-users"></a>Hur du använder målobjekt kriterier för att skicka push-kampanjer till en del av dina användare
Målobjekt för användarna efter specifika villkor med knappen ”villkor” är en av de mest kraftfulla begrepp som hjälper till att du skickar relevanta push-meddelanden i Azure Mobile Engagement att kunderna svarar på i stället för att skicka alla. Du kan begränsa målgruppen utifrån kriterier som standard och simulera push-meddelanden att avgöra hur många som tar emot meddelandet.

**Se även:**

* [UI-dokumentationen – Reach - nya Push-kampanj][Link 27]

## <a name="audience-criteria-can-include"></a>Målgruppen villkor kan omfatta:
* ** Technicals: ** mål baserat på samma teknisk information hittar du i avsnitten Analytics och Övervakare. **Se även:** [UI-dokumentation – Analytics][Link 15], [UI-dokumentation – Övervakare][Link 16]
* **Plats:** program som använder ”realtid plats reporting” med Geobegränsning kan använda geografiska plats som ett villkor för att rikta en målgrupp från GPS-plats. ”Lazy området plats Reporting” anrop också användas för att rikta en målgrupp från mobiltelefonen plats (”realtid plats reporting” och ”Lazy området plats rapportering” måste aktiveras från SDK). **Se även:** [SDK-dokumentationen – iOS - Integration][Link 5], [SDK-dokumentationen - Android - integrering][Link 5]
* **Reach Feedback:** du kan inrikta dig på din målgrupp baserat på deras feedback från tidigare reach-meddelanden via reach feedback från meddelanden, avsökningar och skickar Data. Detta gör det möjligt att bättre mål målgruppen efter två eller tre nå kampanjer än du skulle kunna första gången. Det kan också användas för att filtrera bort användare som redan tagit emot ett meddelande med liknande innehåll genom att ange en kampanj som inte ska skickas till användare som redan tagit emot en specifik tidigare kampanj. Du kan även utesluta användare som ingår en viss kampanj som fortfarande är aktivt tar emot nya push-meddelanden. **Se även:** [UI Documentation - Reach - Push-innehållet][Link 29]
* **Installera spårning:** kan du spåra information baserat på om dina användare installerat appen. **Se även:** [UI-dokumentation – inställningar][Link 20]
* **Användarprofil:** du kan målet baserat på standardanvändare information och du kan målet baserat på den information som anpassad app som du har skapat. Detta omfattar användare som för närvarande är inloggad och användare som har besvarat frågor du har angett dem som i själva appen i stället för bara hur de har svarat på tidigare kampanjer. Alla App-Info har definierats för din app visas i listan.
* Segment: Du kan också mål baserat på segment som du har skapat baserat på specifika användarbeteende som innehåller flera villkor. Alla segment har definierats för din app visas på den här listan. **Se även:** [UI-dokumentation – segment][Link 18]
* **App-Info:** anpassad App Info taggar kan skapas från ”inställningar” för att spåra användarens beteende. **Se även:** [UI-dokumentation – inställningar][Link 20]

## <a name="example"></a>Exempel:
Om du vill skicka ett meddelande endast till de underordnade dina användare som har utfört en åtgärd för köp i appen.

1. Gå till inställningssidan för program, väljer ”App-info”-menyn och väljer ”nya app-info”
2. Registrera en ny booleskt appinfo som kallas ”inAppPurchase”
3. Göra programmet ange appinfon till ”true” när användaren utför har ett inköp i appen (med hjälp av sendAppInfo (”inAppPurchase”,...) funktionen)
4. Om du inte vill göra det från ditt program, kan du göra det från din serverdel med hjälp av device API)
5. Sedan måste du skapa ditt meddelande med ett kriterium begränsa användarna till användare som har ”inAppPurchase” inställd på ”true”)

> [!NOTE]
> Målobjekt för baserat på kriterier än taggar för app-info kräver Azure Mobile Engagement att samla in information från användarnas enheter innan push skickas och så kan orsaka en fördröjning. Komplexa push-konfiguration som alternativ (t.ex. uppdatera Aktivitetsikoner) kan också vänta med push-meddelanden. Använda en ”en bild” kampanj från Push-API: et är absolut snabbaste metoden i Azure Mobile Engagement. Med bara app info taggar som push villkor för en Reach-kampanj (antingen från API: et nå eller Användargränssnittet) är den snabbaste metoden nästa eftersom taggar för app-info lagras på serversidan. Med andra målobjekt kriterier för en push-kampanj är den mest flexibla men långsammaste push-metoden eftersom Azure Mobile Engagement har fråga enheterna för att kunna skicka kampanjen.

![Reach-Criterion1][29] 

## <a name="criterion-options-apply-to"></a>Alternativ för villkor gäller:
* **Technicals**     
* Inbyggd name: Firmware namn
* Version på inbyggd programvara: version på inbyggd programvara
* Enhetsmodell: enhetsmodell
* Enhetstillverkare: enhetstillverkare
* Programversion: programversion
* Operatör name: operatör namn, Odefinierad
* Operatör land: operatör land, Odefinierad
* Nätverkstyp: typen
* Språk: språk
* Skärmstorlek: skärmstorlek
* **Plats**      
* Senast kända område: land, Region, ort
* Realtid geobegränsning: listan över PoI (namn, åtgärder), cirkulär POI (namn, latitud, long, RADIUS-i mätare)
* **Nå feedback**     
* Meddelande feedback: meddelande, feedback
* Avsöka feedback: avsökning feedback
* Söka svar feedback: söka svar feedback, fråga, val
* Data Push feedback: Data-Push, feedback
* **Installera spårning**     
* Arkiv: Arkiv Odefinierad
* Källa: Källa, Odefinierad
* **Användarprofil**     
* Kön: man eller kvinnligt Odefinierad
* Födelsedatum datum: operatör, datum, Odefinierad
* Anmäl: SANT eller FALSKT, Odefinierad
* **App-Info**      
* Sträng: Sträng, Odefinierad
* Datum: operatör, datum, Odefinierad
* Heltal: operatorn, Odefinierad
* Booleskt värde: true eller false, Odefinierad
* **Segment**    
* Namnet på segment (från listrutan) undantag (target användare som inte är en del av det här segmentet).

<!--Image references-->
[1]: ./media/mobile-engagement-user-interface-navigation/navigation1.png
[2]: ./media/mobile-engagement-user-interface-home/home1.png
[3]: ./media/mobile-engagement-user-interface-home/home2.png
[4]: ./media/mobile-engagement-user-interface-home/home3.png
[5]: ./media/mobile-engagement-user-interface-home/home4.png
[6]: ./media/mobile-engagement-user-interface-home/home5.png
[7]: ./media/mobile-engagement-user-interface-my-account/myaccount1.png
[8]: ./media/mobile-engagement-user-interface-my-account/myaccount2.png
[9]: ./media/mobile-engagement-user-interface-my-account/myaccount3.png
[10]: ./media/mobile-engagement-user-interface-analytics/analytics1.png
[11]: ./media/mobile-engagement-user-interface-analytics/analytics2.png
[12]: ./media/mobile-engagement-user-interface-analytics/analytics3.png
[13]: ./media/mobile-engagement-user-interface-analytics/analytics4.png
[14]: ./media/mobile-engagement-user-interface-monitor/monitor1.png
[15]: ./media/mobile-engagement-user-interface-monitor/monitor2.png
[16]: ./media/mobile-engagement-user-interface-monitor/monitor3.png
[17]: ./media/mobile-engagement-user-interface-monitor/monitor4.png
[18]: ./media/mobile-engagement-user-interface-reach/reach1.png
[19]: ./media/mobile-engagement-user-interface-reach/reach2.png
[20]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign1.png
[21]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign2.png
[22]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign3.png
[23]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign4.png
[24]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign5.png
[25]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign6.png
[26]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign7.png
[27]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign8.png
[28]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign9.png
[29]: ./media/mobile-engagement-user-interface-reach-criterion/Reach-Criterion1.png
[30]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content1.png
[31]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content2.png
[32]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content3.png
[33]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content4.png
[34]: ./media/mobile-engagement-user-interface-dashboard/dashboard1.png
[35]: ./media/mobile-engagement-user-interface-segments/segments1.png
[36]: ./media/mobile-engagement-user-interface-segments/segments2.png
[37]: ./media/mobile-engagement-user-interface-segments/segments3.png
[38]: ./media/mobile-engagement-user-interface-segments/segments4.png
[39]: ./media/mobile-engagement-user-interface-segments/segments5.png
[40]: ./media/mobile-engagement-user-interface-segments/segments6.png
[41]: ./media/mobile-engagement-user-interface-segments/segments7.png
[42]: ./media/mobile-engagement-user-interface-segments/segments8.png
[43]: ./media/mobile-engagement-user-interface-segments/segments9.png
[44]: ./media/mobile-engagement-user-interface-segments/segments10.png
[45]: ./media/mobile-engagement-user-interface-segments/segments11.png
[46]: ./media/mobile-engagement-user-interface-settings/settings1.png
[47]: ./media/mobile-engagement-user-interface-settings/settings2.png
[48]: ./media/mobile-engagement-user-interface-settings/settings3.png
[49]: ./media/mobile-engagement-user-interface-settings/settings4.png
[50]: ./media/mobile-engagement-user-interface-settings/settings5.png
[51]: ./media/mobile-engagement-user-interface-settings/settings6.png
[52]: ./media/mobile-engagement-user-interface-settings/settings7.png
[53]: ./media/mobile-engagement-user-interface-settings/settings8.png
[54]: ./media/mobile-engagement-user-interface-settings/settings9.png
[55]: ./media/mobile-engagement-user-interface-settings/settings10.png
[56]: ./media/mobile-engagement-user-interface-settings/settings11.png
[57]: ./media/mobile-engagement-user-interface-settings/settings12.png
[58]: ./media/mobile-engagement-user-interface-settings/settings13.png

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[Link 12]: mobile-engagement-user-interface-navigation.md
[Link 13]: mobile-engagement-user-interface-home.md
[Link 14]: mobile-engagement-user-interface-my-account.md
[Link 15]: mobile-engagement-user-interface-analytics.md
[Link 16]: mobile-engagement-user-interface-monitor.md
[Link 17]: mobile-engagement-user-interface-reach.md
[Link 18]: mobile-engagement-user-interface-segments.md
[Link 19]: mobile-engagement-user-interface-dashboard.md
[Link 20]: mobile-engagement-user-interface-settings.md
[Link 21]: mobile-engagement-troubleshooting-guide-analytics.md
[Link 22]: mobile-engagement-troubleshooting-guide-apis.md
[Link 23]: mobile-engagement-troubleshooting-guide-push-reach.md
[Link 24]: mobile-engagement-troubleshooting-guide-service.md
[Link 25]: mobile-engagement-troubleshooting-guide-sdk.md
[Link 26]: mobile-engagement-troubleshooting-guide-sr-info.md
[Link 27]: mobile-engagement-user-interface-reach-campaign.md
[Link 28]: mobile-engagement-user-interface-reach-criterion.md
[Link 29]: mobile-engagement-user-interface-reach-content.md

