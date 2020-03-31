---
title: Felsöka Aviseringar och meddelanden från Azure Monitor
description: Vanliga problem med Azure Monitor-aviseringar och möjliga lösningar.
author: ofirmanor
ms.author: ofmanor
ms.topic: reference
ms.date: 03/16/2020
ms.subservice: alerts
ms.openlocfilehash: beb47f961c6f24453bd49aa5807c9d801fc199a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132333"
---
# <a name="troubleshooting-problems-in-azure-monitor-alerts"></a>Felsöka problem i Azure Monitor-aviseringar 

I den här artikeln beskrivs vanliga problem i Azure Monitor-aviseringar.

Azure Monitor-aviseringar meddelar dig proaktivt när viktiga villkor hittas i dina övervakningsdata. De gör att du kan identifiera och åtgärda problem innan användarna av ditt system märker dem. Mer information om aviseringar finns [i Översikt över aviseringar i Microsoft Azure](alerts-overview.md).

## <a name="action-or-notification-on-my-alert-did-not-work-as-expected"></a>Åtgärden eller meddelandet på min avisering fungerade inte som förväntat

Om du kan se en avisering som har avlossats i Azure-portalen, men har problem med några av dess åtgärder eller meddelanden, läser du följande avsnitt.

## <a name="did-not-receive-expected-email"></a>Fick inte förväntad e-post

Om du kan se en avisering som har avlossats i Azure-portalen, men inte har fått det e-postmeddelande som du har konfigurerat om den, gör du så här: 

1. **Har e-postmeddelandet undertryckts av en [åtgärdsregel?](alerts-action-rules.md)** 

    Kontrollera genom att klicka på den avfyrade aviseringen i portalen och titta på fliken historik för undertryckta [åtgärdsgrupper:](action-groups.md) 

    ![Historik för regelförtryckshistorik för varningsåtgärd](media/alerts-troubleshoot/history-action-rule.png)

1. **Är typen av åtgärd "E-post Azure Resource Manager-roll"?**

    Den här åtgärden tittar bara på Azure Resource Manager-rolltilldelningar som finns i prenumerationsomfånget och av typen *Användare*.  Kontrollera att du har tilldelat rollen på prenumerationsnivå och inte på resursnivå eller resursgruppsnivå.

1. **Accepterar din e-postserver och postlåda externa e-postmeddelanden?**

    Kontrollera att e-postmeddelanden från dessa tre adresser inte är blockerade:
      - azure-noreply@microsoft.com  
      - azureemail-noreply@microsoft.com
      - alerts-noreply@mail.windowsazure.com

    Det är vanligt att interna e-postlistor eller distributionslistor blockerar e-postmeddelanden från externa e-postadresser. Du måste vitlista ovanstående e-postadresser.  
    Testa genom att lägga till en vanlig e-postadress på arbetet (inte en e-postlista) i åtgärdsgruppen och se om aviseringar kommer till e-postmeddelandet. 

1. **Bearbetades e-postmeddelandet med inkorgsregler eller ett skräppostfilter?** 

    Kontrollera att det inte finns några inkorgsregler som tar bort dessa e-postmeddelanden eller flyttar dem till en sidomapp. Inkorgsregler kan till exempel fånga upp specifika avsändare eller specifika ord i ämnet.

    Kontrollera också:
    
      - skräppostinställningarna för din e-postklient (som Outlook, Gmail)
      - avsändaren gränser / spam inställningar / karantän inställningar för din e-postserver (som Exchange, Office 365, G-suite)
      - inställningarna för din e-säkerhetsapparat, om någon (som Barracuda, Cisco). 

1. **Har du av misstag avslutat prenumerationen från åtgärdsgruppen?** 

    Aviseringsmeddelandena innehåller en länk för att avsluta prenumerationen på åtgärdsgruppen. Så här kontrollerar du om du av misstag har avslutat prenumerationen från den här åtgärdsgruppen:

    1. Öppna åtgärdsgruppen i portalen och kontrollera kolumnen Status:

    ![statuskolumnen för åtgärdsgrupp](media/alerts-troubleshoot/action-group-status.png)

    2. Sök i din e-post efter bekräftelsen för att avsluta prenumerationen:

    ![avsluta prenumerationen på åtgärdsgruppen för aviseringar](media/alerts-troubleshoot/unsubscribe-action-group.png)

    Om du vill prenumerera igen – använd antingen länken i bekräftelsemeddelandet för att avsluta prenumerationen som du har fått, eller ta bort e-postadressen från åtgärdsgruppen och lägg sedan till den igen. 
 
1. **Har du betygsatts begränsad på grund av många e-postmeddelanden som går till en enda e-postadress?** 

    E-post är [begränsad](alerts-rate-limiting.md) till högst 100 e-postmeddelanden varje timme till varje e-postadress. Om du klarar den här tröskeln tas ytterligare e-postmeddelanden bort.  Kontrollera om du har fått ett meddelande om att din e-postadress tillfälligt har varit begränsad: 
 
   ![E-posthastigheten begränsad](media/alerts-troubleshoot/email-paused.png)

   Om du vill få stora mängder meddelanden utan hastighetsbegränsning kan du överväga att använda en annan åtgärd, till exempel webhook, logikapp, Azure-funktion eller automatiseringskörningar, varav ingen är begränsad. 

## <a name="did-not-receive-expected-sms-voice-call-or-push-notification"></a>Fick inte förväntat SMS, röstsamtal eller push-meddelande

Om du kan se en avskjuten avisering i portalen, men inte fick sms-, röstsamtals- eller push-meddelandet som du har konfigurerat om den, gör du så här: 

1. **Underdunderdades åtgärden av en [åtgärdsregel?](alerts-action-rules.md)** 

    Kontrollera genom att klicka på den avfyrade aviseringen i portalen och titta på fliken historik för undertryckta [åtgärdsgrupper:](action-groups.md) 

    ![Historik för regelförtryckshistorik för varningsåtgärd](media/alerts-troubleshoot/history-action-rule.png)

   Om det var oavsiktligt kan du ändra, inaktivera eller ta bort åtgärdsregeln.
 
1. **SMS / röst: Är ditt telefonnummer korrekt?**

   Kontrollera SMS-åtgärden för stavfel i landskoden eller telefonnumret. 
 
1. **SMS / röst: har du varit begränsad?** 

    SMS- och röstsamtal är begränsade till högst en avisering var femte minut per telefonnummer. Om du klarar den här tröskeln kommer meddelandena att tas bort. 

      - Röstsamtal – kontrollera samtalshistoriken och se om du hade ett annat samtal än Azure under de föregående fem minuterna. 
      - SMS - kontrollera din SMS-historik för ett meddelande som anger att ditt telefonnummer har varit begränsad. 

    Om du vill få stora mängder meddelanden utan hastighetsbegränsning kan du överväga att använda en annan åtgärd, till exempel webhook, logikapp, Azure-funktion eller automatiseringskörningar, varav ingen är begränsad. 
 
1. **SMS: Har du av misstag avregistrerat dig från åtgärdsgruppen?**

    Öppna din SMS-historik och kontrollera om du har valt bort SMS-leverans från denna specifika åtgärdsgrupp (med hjälp av INAKTIVERA action_group_short_name svar) eller från alla åtgärdsgrupper (med stop-svaret). Om du vill prenumerera igen skickar du antingen det aktuella SMS-kommandot (AKTIVERA action_group_short_name eller START) eller tar bort SMS-åtgärden från åtgärdsgruppen och lägger sedan till den igen.  Mer information finns [i SMS-varningsbeteende i åtgärdsgrupper](alerts-sms-behavior.md).

1. **Har du av misstag blockerat aviseringarna på telefonen?**

   Med de flesta mobiltelefoner kan du blockera samtal eller SMS från specifika telefonnummer eller korta koder, eller blockera push-meddelanden från specifika appar (till exempel Azure-mobilappen). Om du vill kontrollera om du av misstag har blockerat aviseringarna på telefonen söker du i dokumentationen som är specifik för telefonens operativsystem och modell eller testar med ett annat telefonnummer och ett annat telefonnummer. 

## <a name="expected-another-type-of-action-to-trigger-but-it-did-not"></a>Förväntade sig att en annan typ av åtgärd skulle utlösas, men det 

Om du kan se en avskjuten avisering i portalen, men dess konfigurerade åtgärd inte utlöstes, gör du så här: 

1. **Underdunderdades åtgärden av en åtgärdsregel?** 

    Kontrollera genom att klicka på den avfyrade aviseringen i portalen och titta på fliken historik för undertryckta [åtgärdsgrupper:](action-groups.md) 

    ![Historik för regelförtryckshistorik för varningsåtgärd](media/alerts-troubleshoot/history-action-rule.png)
 
    Om det var oavsiktligt kan du ändra, inaktivera eller ta bort åtgärdsregeln. 

1. **Har en webhook inte utlösa?**

    1. **Har käll-IP-adresserna blockerats?**
    
       Vitlista [IP-adresser](action-groups.md#action-specific-information) som webhook anropas från.

    1. **Fungerar webhook-slutpunkten korrekt?**

       Kontrollera att webhook-slutpunkten som du har konfigurerat är korrekt och slutpunkten fungerar korrekt. Kontrollera din webhook loggar eller instrument dess kod så att du kan undersöka (till exempel logga den inkommande nyttolasten). 

    1. **Ringer du Slack eller Microsoft Teams?**  
    Var och en av dessa slutpunkter förväntar sig ett specifikt JSON-format. Följ [dessa instruktioner](action-groups-logic-app.md) för att konfigurera en logikappåtgärd i stället.

    1. **Har din webhook blev svarar inte eller returnerade fel?** 

        Vår timeout-period för ett webhook-svar är 10 sekunder. Webhook-anropet görs på nytt upp till ytterligare två gånger när följande HTTP-statuskoder returneras: 408, 429, 503, 504 eller när HTTP-slutpunkten inte svarar. Det första återförsöket görs efter 10 sekunder. Det andra och sista återförsöket sker efter 100 sekunder. Om det andra återförsöket misslyckas anropas inte slutpunkten igen under 30 minuter för någon åtgärdsgrupp.

## <a name="action-or-notification-happened-more-than-once"></a>Åtgärd eller meddelande har inträffat mer än en gång 

Om du har fått ett meddelande om en avisering (till exempel ett e-postmeddelande eller ett SMS) mer än en gång, eller om aviseringens åtgärd (till exempel webhook eller Azure-funktion) utlöstes flera gånger, gör du så här: 

1. **Är det verkligen samma varning?** 

    I vissa fall avfyras flera liknande aviseringar vid ungefär samma tidpunkt. Så det kan bara verka som samma varning utlöste sina åtgärder flera gånger. En aviseringsregel för aktivitetslogg kan till exempel konfigureras för att avfyra både när en händelse har startats och när den har slutförts (har slutförts eller misslyckats) genom att inte filtrera i händelsestatusfältet. 

    Om du vill kontrollera om dessa åtgärder eller meddelanden kom från olika aviseringar undersöker du aviseringsinformationen, till exempel dess tidsstämpel och antingen varnings-ID eller dess korrelations-ID. Du kan också kontrollera listan över avfyrade aviseringar i portalen. Om så är fallet måste du anpassa varningsregellogiken eller på annat sätt konfigurera aviseringskällan. 

1. **Upprepas åtgärden i flera åtgärdsgrupper?** 

    När en avisering utlöses bearbetas var och en av dess åtgärdsgrupper oberoende av varandra. Så om en åtgärd (till exempel en e-postadress) visas i flera utlösta åtgärdsgrupper, kallas den en gång per åtgärdsgrupp. 

    Kontrollera vilka åtgärdsgrupper som har utlösts genom att kontrollera fliken varningshistorik. Du ser att både åtgärdsgrupper som definieras i aviseringsregeln och åtgärdsgrupper som läggs till i aviseringen efter åtgärdsregler: 

    ![Åtgärd som upprepas i flera åtgärdsgrupper](media/alerts-troubleshoot/action-repeated-multi-action-groups.png)

## <a name="action-or-notification-has-an-unexpected-content"></a>Åtgärd eller meddelande har ett oväntat innehåll

Om du har fått aviseringen, men tror att vissa av dess fält saknas eller är felaktiga, gör du så här: 

1. **Valde du rätt format för åtgärden?** 

    Varje åtgärdstyp (e-post, webhook, etc.) har två format – standardformat, äldre format och [det nyare gemensamma schemaformatet](alerts-common-schema.md). När du skapar en åtgärdsgrupp anger du önskat format per åtgärd – olika åtgärder i åtgärdsgrupperna kan ha olika format. 

    Till exempel för webhook-åtgärd: 

    ![alternativet webhook-åtgärdsschema](media/alerts-troubleshoot/webhook.png)

    Kontrollera om det format som anges på åtgärdsnivå är vad du förväntar dig. Du kan till exempel ha utvecklat kod som svarar på aviseringar (webhook, funktion, logikapp osv.), och förväntar dig ett format, men senare i åtgärden angav du eller en annan person ett annat format.  

    Kontrollera också nyttolastformat (JSON) för [aktivitetsloggvarningar](activity-log-alerts-webhook.md), för [loggsökningsaviseringar](alerts-log-webhook.md) (både Application Insights och log analytics), [för måttaviseringar,](alerts-metric-near-real-time.md#payload-schema)för det [gemensamma varningsschemat](alerts-common-schema-definitions.md)och för de inaktuella [klassiska måttaviseringarna](alerts-webhooks.md).

 
1. **Aktivitetsloggvarningar: Är informationen tillgänglig i aktivitetsloggen?** 

    [Aktivitetsloggaviseringar](activity-log-alerts.md) är aviseringar som baseras på händelser som skrivits till Azure Activity Log, till exempel händelser om att skapa, uppdatera eller ta bort Azure-resurser, tjänsthälso- och resurshälsohändelser eller resultat från Azure Advisor och Azure Policy. Om du har fått en avisering baserat på aktivitetsloggen men vissa fält som du behöver saknas eller är felaktiga kontrollerar du först händelserna i själva aktivitetsloggen. Om Azure-resursen inte skrev de fält som du letar efter i aktivitetslogghändelsen inkluderas inte dessa fält i motsvarande avisering. 

## <a name="action-rule-is-not-working-as-expected"></a>Åtgärdsregeln fungerar inte som förväntat 

Om du kan se en avisering som avfyrades i portalen, men en relaterad åtgärdsregel inte fungerade som förväntat, gör du så här: 

1. **Är åtgärdsregeln aktiverad?** 

    Kontrollera kolumnen status för åtgärdsregel för att kontrollera att rollen för relaterad åtgärd är aktiverad. 

    ![Grafisk](media/alerts-troubleshoot/action-rule-status.png) 

    Om den inte är aktiverad kan du aktivera åtgärdsregeln genom att markera den och klicka på Aktivera. 

1. **Är det en service hälsovarning?** 

    Servicehälsovarningar (övervakartjänst = "Tjänstens hälsotillstånd") påverkas inte av åtgärdsregler. 

1. **Agerade åtgärdsregeln på din avisering?** 

    Kontrollera om åtgärdsregeln har bearbetat aviseringen genom att klicka på den avfyrade aviseringen i portalen och titta på historikfliken.

    Här är ett exempel på åtgärdsregel som undertrycker alla åtgärdsgrupper: 
 
     ![Historik för regelförtryckshistorik för varningsåtgärd](media/alerts-troubleshoot/history-action-rule.png)

    Här är ett exempel på en åtgärdsregel som lägger till en annan åtgärdsgrupp:

    ![Åtgärd som upprepas i flera åtgärdsgrupper](media/alerts-troubleshoot/action-repeated-multi-action-groups.png)
 

1. **Matchar åtgärdsregelomfånget och filtret den avfyrade aviseringen?** 

    Om du tycker att åtgärdsregeln borde ha avfyrat men inte har sparken, eller att den inte borde ha avfyrats men det gjorde det, noggrant undersöka åtgärden regel omfattning och villkor filter kontra egenskaperna för den avfyrade aviseringen. 


## <a name="how-to-find-the-alert-id-of-a-fired-alert"></a>Så här hittar du varnings-ID för en avfyrad varning

När du öppnar ett ärende om en specifik avisering (till exempel – om du inte fick dess e-postmeddelande) måste du ange varnings-ID: t. 

Så här hittar du den:

1. I Azure-portalen navigerar du till listan över avfyrade aviseringar och hittar den specifika aviseringen. Du kan använda filtren för att hitta dem. 

1. Klicka på aviseringen för att öppna varningsinformationen. 

1. Bläddra nedåt i varningsfälten på den första fliken (sammanfattningsfliken) tills du hittar den och kopiera den. Det fältet innehåller också en hjälpknapp för "Kopiera till urklipp" som du kan använda.  

    ![hitta varnings-ID](media/alerts-troubleshoot/get-alert-id.png)

## <a name="problem-creating-updating-or-deleting-action-rules-in-the-azure-portal"></a>Problem med att skapa, uppdatera eller ta bort åtgärdsregler i Azure-portalen

Om du fick ett felmeddelande när du försökte skapa, uppdatera eller ta bort en [åtgärdsregel](alerts-action-rules.md)gör du så här: 

1. **Fick du ett behörighetsfel?**  

    Du bör antingen ha [den inbyggda rollen Övervakningsdeltagare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor)eller de specifika behörigheter som är relaterade till åtgärdsregler och aviseringar.

1. **Har du verifierat åtgärdsregelparametrarna?**  

    Kontrollera [åtgärdsregeldokumentationen](alerts-action-rules.md)eller [åtgärdsregeln PowerShell Set-AzActionRule-](https://docs.microsoft.com/powershell/module/az.alertsmanagement/Set-AzActionRule?view=azps-3.5.0) kommando. 


## <a name="next-steps"></a>Nästa steg
- Om du använder en loggavisering läser du även [Felsökning av loggvarningar](alert-log-troubleshoot.md).
- Gå tillbaka till [Azure-portalen](https://portal.azure.com) för att kontrollera om du har löst problemet med vägledningen ovan 
