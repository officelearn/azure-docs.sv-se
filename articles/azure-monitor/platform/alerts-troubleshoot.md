---
title: Felsöka Azure Monitor aviseringar och meddelanden
description: Vanliga problem med Azure Monitor aviseringar och möjliga lösningar.
author: ofirmanor
ms.author: ofmanor
ms.topic: reference
ms.date: 03/16/2020
ms.subservice: alerts
ms.openlocfilehash: beb47f961c6f24453bd49aa5807c9d801fc199a3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80132333"
---
# <a name="troubleshooting-problems-in-azure-monitor-alerts"></a>Felsöka problem i Azure Monitor aviseringar 

I den här artikeln beskrivs vanliga problem i Azure Monitor aviseringar.

Azure Monitor aviseringar proaktivt meddela dig när viktiga villkor finns i dina övervaknings data. De gör att du kan identifiera och åtgärda problem innan användarna av systemet ser dem. Mer information om aviseringar finns i [Översikt över aviseringar i Microsoft Azure](alerts-overview.md).

## <a name="action-or-notification-on-my-alert-did-not-work-as-expected"></a>Åtgärden eller meddelandet på aviseringen fungerade inte som förväntat

Om du kan se en utlöst avisering i Azure Portal, men har problem med några av dess åtgärder eller aviseringar, se följande avsnitt.

## <a name="did-not-receive-expected-email"></a>Tog inte emot den förväntade e-postadressen

Om du kan se en utlöst avisering i Azure Portal, men inte fick e-postmeddelandet som du har konfigurerat, följer du dessa steg: 

1. **Ignorerades e-postmeddelandet av en [Åtgärds regel](alerts-action-rules.md)**? 

    Kontrol lera genom att klicka på den utlöst aviseringen i portalen och titta på fliken Historik för undertryckta [Åtgärds grupper](action-groups.md): 

    ![Varnings historik för varnings åtgärds regel](media/alerts-troubleshoot/history-action-rule.png)

1. **Är typen av åtgärd "e-Azure Resource Manager roll"?**

    Den här åtgärden kan bara se Azure Resource Manager roll tilldelningar som finns i prenumerations omfånget, och av typen *användare*.  Kontrol lera att du har tilldelat rollen på prenumerations nivå och inte på resurs nivå eller resurs grupps nivå.

1. **Accepterar e-postservern och post lådan externa e-postmeddelanden?**

    Verifiera att e-postmeddelanden från dessa tre adresser inte blockeras:
      - azure-noreply@microsoft.com  
      - azureemail-noreply@microsoft.com
      - alerts-noreply@mail.windowsazure.com

    Det är vanligt att interna e-postlistor eller distributions listor blockerar e-post från externa e-postadresser. Du måste vitlista ovanstående e-postadresser.  
    Testa genom att lägga till en vanlig e-postadress för arbetet (inte en distributions lista) i åtgärds gruppen och se om aviseringar kommer till e-postmeddelandet. 

1. **Har e-postmeddelandet bearbetats av reglerna för Inkorgen eller ett skräp post filter?** 

    Kontrol lera att det inte finns några regler för Inkorgen som tar bort e-postmeddelandena eller flytta dem till en sido-mapp. Reglerna för Inkorgen kan till exempel fånga vissa avsändare eller vissa ord i ämnet.

    Kontrol lera också:
    
      - skräp post inställningarna för din e-postklient (t. ex. Outlook, Gmail)
      - avsändaren begränsar/skräp post inställningar/karantän inställningar för din e-postserver (t. ex. Exchange, Office 365, G-Suite)
      - inställningarna för din e-säkerhetsenhet, om några (som Barracuda, Cisco). 

1. **Har du av misstag avbrutit prenumerationen på åtgärds gruppen?** 

    Aviserings meddelandet innehåller en länk för att avbryta prenumerationen på åtgärds gruppen. För att kontrol lera om du av misstag har avbrutit prenumerationen på den här åtgärds gruppen, antingen:

    1. Öppna åtgärds gruppen i portalen och kontrol lera kolumnen Status:

    ![status kolumn för åtgärds grupp](media/alerts-troubleshoot/action-group-status.png)

    2. Sök i e-postmeddelandet för att avbryta prenumerationen:

    ![prenumerationen har avbrutits från varnings åtgärds gruppen](media/alerts-troubleshoot/unsubscribe-action-group.png)

    Om du vill prenumerera igen – använder du antingen länken i bekräftelse meddelandet för att avbryta prenumerationen som du har tagit emot eller tar bort e-postadressen från åtgärds gruppen och lägger sedan tillbaka den igen. 
 
1. **Har du betygsatts begränsad på grund av många e-postmeddelanden som ska skickas till en enda e-postadress?** 

    E-postmeddelandet är [begränsat](alerts-rate-limiting.md) till högst 100 e-postmeddelanden varje timme till varje e-postadress. Om du skickar det här tröskelvärdet släpps ytterligare e-postaviseringar.  Kontrol lera om du har fått ett meddelande som anger att din e-postadress är tillfälligt begränsad: 
 
   ![Begränsad e-posthastighet](media/alerts-troubleshoot/email-paused.png)

   Om du vill få stora mängder aviseringar utan hastighets begränsning bör du överväga att använda en annan åtgärd, till exempel webhook, Logi Kap par, Azure Function eller Automation-runbooks, ingen som är begränsad. 

## <a name="did-not-receive-expected-sms-voice-call-or-push-notification"></a>Fick inget förväntat SMS, röst samtal eller push-meddelande

Om du kan se en utlöst avisering i portalen, men inte fick meddelandet SMS, röst samtal eller push-meddelande som du har konfigurerat, följer du dessa steg: 

1. **Ignorerades åtgärden av en [Åtgärds regel](alerts-action-rules.md)?** 

    Kontrol lera genom att klicka på den utlöst aviseringen i portalen och titta på fliken Historik för undertryckta [Åtgärds grupper](action-groups.md): 

    ![Varnings historik för varnings åtgärds regel](media/alerts-troubleshoot/history-action-rule.png)

   Om det var oavsiktligt kan du ändra, inaktivera eller ta bort åtgärds regeln.
 
1. **SMS/röst: är telefonnumret korrekt?**

   Kontrol lera SMS-åtgärden för skrivfel i lands koden eller telefonnumret. 
 
1. **SMS/röst: har du begränsat pris nivån?** 

    SMS-och röst samtal är begränsade till högst ett meddelande var femte minut per telefonnummer. Om du skickar det här tröskelvärdet tas meddelandena bort. 

      - Röst samtal – kontrol lera samtals historiken och se om du har ett annat samtal från Azure under de senaste fem minuterna. 
      - SMS-kontrol lera SMS-historiken för ett meddelande som anger att ditt telefonnummer har begränsats. 

    Om du vill få stora mängder aviseringar utan hastighets begränsning bör du överväga att använda en annan åtgärd, till exempel webhook, Logi Kap par, Azure Function eller Automation-runbooks, ingen som är begränsad. 
 
1. **SMS: har du av misstag avbrutit prenumerationen på åtgärds gruppen?**

    Öppna din SMS-historik och kontrol lera om du har valt bort SMS-leverans från den här åtgärds gruppen (med hjälp av alternativet Inaktivera action_group_short_name svar) eller från alla åtgärds grupper (med hjälp av STOP-svaret). Om du vill prenumerera igen kan du antingen skicka relevant SMS-kommando (aktivera action_group_short_name eller starta) eller ta bort SMS-åtgärden från åtgärds gruppen och sedan lägga tillbaka den igen.  Mer information finns i avsnittet om [aviserings beteende i SMS i åtgärds grupper](alerts-sms-behavior.md).

1. **Har du av misstag blockerat meddelanden på din telefon?**

   Med de flesta mobil telefoner kan du blockera samtal eller SMS från vissa telefonnummer eller korta koder eller blockera push-meddelanden från vissa appar (till exempel Azure-mobilapp). Om du vill kontrol lera om du råkat blockera meddelanden på din telefon kan du söka i dokumentationen som är speciell för ditt telefon operativ system och din modell eller testa med ett annat telefonnummer och telefonnummer. 

## <a name="expected-another-type-of-action-to-trigger-but-it-did-not"></a>En annan typ av åtgärd förväntades för utlösare, men det gick inte 

Om du kan se en utlöst avisering i portalen, men dess konfigurerade åtgärd inte utlöstes, följer du dessa steg: 

1. **Ignorerades åtgärden av en åtgärds regel?** 

    Kontrol lera genom att klicka på den utlöst aviseringen i portalen och titta på fliken Historik för undertryckta [Åtgärds grupper](action-groups.md): 

    ![Varnings historik för varnings åtgärds regel](media/alerts-troubleshoot/history-action-rule.png)
 
    Om det var oavsiktligt kan du ändra, inaktivera eller ta bort åtgärds regeln. 

1. **Utlöstes en webhook?**

    1. **Har käll-IP-adresserna blockerats?**
    
       Vitlista [IP-adresserna](action-groups.md#action-specific-information) som webhooken anropas från.

    1. **Fungerar webhook-slutpunkten korrekt?**

       Kontrol lera att webhook-slutpunkten du har konfigurerat är korrekt och att slut punkten fungerar som den ska. Kontrol lera dina webhook-loggar eller instrumentens kod så att du kan undersöka (till exempel Logga inkommande nytto Last). 

    1. **Ringer du slack eller Microsoft Teams?**  
    Var och en av dessa slut punkter förväntar sig ett angivet JSON-format. Följ [dessa instruktioner](action-groups-logic-app.md) för att konfigurera en Logi Kap par-åtgärd i stället.

    1. **Slutade webhooken att svara eller returnerade fel?** 

        Vår timeout-period för webhook-svar är 10 sekunder. Webhook-anropet görs om till två ytterligare tillfällen när följande HTTP-statuskod returneras: 408, 429, 503, 504 eller när HTTP-slutpunkten inte svarar. Det första återförsöket görs efter 10 sekunder. Den andra och sista återförsöket sker efter 100 sekunder. Om det andra återförsöket misslyckas anropas inte slutpunkten igen under 30 minuter för någon åtgärdsgrupp.

## <a name="action-or-notification-happened-more-than-once"></a>En åtgärd eller ett meddelande har inträffat mer än en gång 

Om du har fått ett meddelande om en avisering (till exempel ett e-postmeddelande eller ett SMS) mer än en gång, eller om aviseringens åtgärd (t. ex. webhook eller Azure Function) har utlösts flera gånger, följer du dessa steg: 

1. **Är det verkligen samma avisering?** 

    I vissa fall utlöses flera liknande aviseringar vid samma tidpunkt. Det kan därför verka som om samma varning utlöste sina åtgärder flera gånger. Till exempel kan en aktivitets logg aviserings regel konfigureras för att utlösa båda när en händelse har startats, och när den har slutförts (lyckats eller misslyckats), genom att inte filtrera i fältet händelse status. 

    Om du vill kontrol lera om dessa åtgärder eller aviseringar kommer från olika aviseringar kan du granska aviserings informationen, till exempel dess tidstämpel och antingen aviserings-ID: t eller dess korrelations-ID. Du kan också kontrol lera listan över utlösta aviseringar i portalen. I så fall måste du anpassa varnings regelns logik eller på annat sätt konfigurera aviserings källan. 

1. **Upprepas åtgärden i flera åtgärds grupper?** 

    När en avisering utlöses bearbetas var och en av dess åtgärds grupper oberoende av varandra. Så om en åtgärd (till exempel en e-postadress) visas i flera utlösta åtgärds grupper, anropas den en gång per åtgärds grupp. 

    Du kan kontrol lera vilka åtgärds grupper som har utlösts genom att kontrol lera aviserings historik fliken. Du ser båda åtgärds grupperna definierade i aviserings regeln och åtgärds grupper som läggs till i aviseringen per åtgärds regel: 

    ![Åtgärden upprepas i flera åtgärds grupper](media/alerts-troubleshoot/action-repeated-multi-action-groups.png)

## <a name="action-or-notification-has-an-unexpected-content"></a>Åtgärden eller meddelandet har ett oväntat innehåll

Om du har fått aviseringen men tror att några av dess fält saknas eller är felaktiga, följer du dessa steg: 

1. **Valde du rätt format för åtgärden?** 

    Varje åtgärds typ (e-post, webhook osv.) har två format – standardformat, bakåtkompatibelt format och det [nyare vanliga schema formatet](alerts-common-schema.md). När du skapar en åtgärds grupp anger du det format som du vill använda per åtgärd – olika åtgärder i åtgärds grupperna kan ha olika format. 

    Till exempel för webhook-åtgärd: 

    ![schema alternativ för webhook-åtgärd](media/alerts-troubleshoot/webhook.png)

    Kontrol lera att det format som anges på åtgärds nivå är det du förväntar dig. Du kan till exempel ha utvecklat kod som svarar på aviseringar (webhook, Function, Logic app osv.), vilket förväntar sig ett format, men senare i den åtgärd du eller någon annan person angav ett annat format.  

    Kontrol lera också nytto Last formatet (JSON) för [aktivitets logg aviseringar](activity-log-alerts-webhook.md), för [loggs öknings aviseringar](alerts-log-webhook.md) (både Application Insights och Log Analytics), för [mått varningar](alerts-metric-near-real-time.md#payload-schema), för det [gemensamma aviserings schemat](alerts-common-schema-definitions.md)och för de inaktuella [klassiska mått aviseringarna](alerts-webhooks.md).

 
1. **Aktivitets logg aviseringar: är informationen tillgänglig i aktivitets loggen?** 

    [Aktivitets logg aviseringar](activity-log-alerts.md) är aviseringar som baseras på händelser som skrivits till Azure-aktivitets loggen, till exempel händelser för att skapa, uppdatera eller ta bort Azure-resurser, service hälsa och resurs hälso händelser eller resultat från Azure Advisor och Azure policy. Om du har fått en avisering baserat på aktivitets loggen men vissa fält som du behöver saknas eller är felaktiga, kontrollerar du först händelserna i själva aktivitets loggen. Om Azure-resursen inte skrev de fält som du söker efter i aktivitets logg händelsen, inkluderas inte dessa fält i motsvarande avisering. 

## <a name="action-rule-is-not-working-as-expected"></a>Åtgärds regeln fungerar inte som förväntat 

Om du kan se en utlöst avisering i portalen, men en relaterad åtgärds regel inte fungerade som förväntat, följer du dessa steg: 

1. **Är åtgärds regeln aktive rad?** 

    Kontrol lera kolumnen Status för åtgärds regel för att kontrol lera att den relaterade åtgärds rollen är aktive rad. 

    ![Infoga](media/alerts-troubleshoot/action-rule-status.png) 

    Om den inte är aktive rad kan du aktivera åtgärds regeln genom att markera den och klicka på Aktivera. 

1. **Är det en hälso varning för tjänsten?** 

    Service Health-aviseringar (övervaka tjänst = "Service Health") påverkas inte av åtgärds regler. 

1. **Fungerar åtgärds regeln på aviseringen?** 

    Kontrol lera att åtgärds regeln har bearbetat aviseringen genom att klicka på den utlöst aviseringen i portalen och titta på fliken Historik.

    Här är ett exempel på en åtgärds regel som undertrycker alla åtgärds grupper: 
 
     ![Varnings historik för varnings åtgärds regel](media/alerts-troubleshoot/history-action-rule.png)

    Här är ett exempel på en åtgärds regel som lägger till en annan åtgärds grupp:

    ![Åtgärden upprepas i flera åtgärds grupper](media/alerts-troubleshoot/action-repeated-multi-action-groups.png)
 

1. **Matchar åtgärds regelns omfattning och filter den utlöst aviseringen?** 

    Om du tror att åtgärds regeln ska ha utlösts men inte, eller att den inte borde ha utlöst, men den gjorde det, kontrollerar du noggrant åtgärds regelns omfattning och filter villkoren jämfört med egenskaperna för den utlöst aviseringen. 


## <a name="how-to-find-the-alert-id-of-a-fired-alert"></a>Så här hittar du aviserings-ID för en utlöst avisering

När du öppnar ett ärende om en angiven utlöst avisering (till exempel – om du inte har fått e-postmeddelandet) måste du ange aviserings-ID: t. 

Följ de här stegen för att hitta den:

1. I Azure Portal navigerar du till listan över aktiverade aviseringar och söker efter den aktuella aviseringen. Du kan använda filtren för att hitta det. 

1. Klicka på aviseringen för att öppna aviserings informationen. 

1. Rulla nedåt i aviserings fälten på den första fliken (fliken Sammanfattning) tills du har hittat den och kopiera den. Fältet innehåller också hjälp knappen Kopiera till Urklipp som du kan använda.  

    ![Hitta aviserings-ID](media/alerts-troubleshoot/get-alert-id.png)

## <a name="problem-creating-updating-or-deleting-action-rules-in-the-azure-portal"></a>Problem med att skapa, uppdatera eller ta bort åtgärds regler i Azure Portal

Följ dessa steg om du fick ett fel meddelande när du försökte skapa, uppdatera eller ta bort en [Åtgärds regel](alerts-action-rules.md): 

1. **Fick du ett behörighets fel?**  

    Du bör antingen ha den [inbyggda rollen övervaknings deltagare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor)eller de behörigheter som är relaterade till åtgärds regler och aviseringar.

1. **Verifierade du åtgärds regel parametrarna?**  

    Kontrol lera [Åtgärds regel dokumentationen](alerts-action-rules.md)eller kommandot [Åtgärds regel PowerShell set-AzActionRule](https://docs.microsoft.com/powershell/module/az.alertsmanagement/Set-AzActionRule?view=azps-3.5.0) . 


## <a name="next-steps"></a>Nästa steg
- Om du använder en logg avisering kan du också se [fel söknings logg aviseringar](alert-log-troubleshoot.md).
- Gå tillbaka till [Azure Portal](https://portal.azure.com) för att kontrol lera om du har löst problemet med vägledning ovan 
