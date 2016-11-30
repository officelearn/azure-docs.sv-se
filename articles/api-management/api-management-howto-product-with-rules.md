---
title: Skydda ditt API med Azure API Management | Microsoft Docs
description: "Lär dig hur du skyddar ditt API med kvoter och begränsningsprinciper (frekvensbegränsning)."
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 450dc368-d005-401d-ae64-3e1a2229b12f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/25/2016
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 5050b99039da511ed3e6179b5b4ca2d04de527f7


---
# <a name="protect-your-api-with-rate-limits-using-azure-api-management"></a>Skydda ditt API med frekvensbegränsningar med hjälp av Azure API Management
Den här guiden beskriver hur du enkelt kan skydda ditt backend-API genom att konfigurera principer för kvoter och frekvensbegränsningar med Azure API Management.

I den här självstudiekursen ska du skapa en API-produkt av typen ”kostnadsfri utvärdering” som gör att utvecklare kan göra upp till 10 anrop per minut och upp till högst 200 anrop per vecka till ditt API. För att åstadkomma detta ska du använda principerna [Begränsa anropsfrekvensen per prenumeration](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate) och [Ange användningskvot per prenumeration](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota). När du är klar ska du publicera API:et och testa frekvensbegränsningsprincipen.

Mer avancerade begränsningsscenarier med principerna [rate-limit-by-key](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) och [quota-by-key](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey) finns i [Avancerad begränsning av förfrågningar med Azure API Management](api-management-sample-flexible-throttling.md).

## <a name="create-product"> </a>Så här skapar du en produkt
I det här steget ska du skapa en produkt för en kostnadsfri utvärdering som inte kräver prenumerationsgodkännande.

> [!NOTE]
> Om du redan har en konfigurerad produkt och vill använda den i den här självstudiekursen går du till [Konfigurera principer för begränsning av anropsfrekvens och kvoter][Konfigurera principer för begränsning av anropsfrekvens och kvoter] och följer självstudien därifrån med hjälp av din produkt i stället för produkten för en kostnadsfri utvärdering.
> 
> 

Börja genom att klicka på **Publisher-portal** på Azure-portalen för API Management-tjänsten.

![Utgivarportalen][api-management-management-console]

> Om du inte har skapat en API Management-tjänstinstans än läser du [Skapa en API Management-tjänstinstans][Skapa en API Management-tjänstinstans] i självstudiekursen [Hantera ditt första API i Azure API Management][Hantera ditt första API i Azure API Management].
> 
> 

Klicka på **Produkter** på **API Management**-menyn till vänster för att visa sidan **Produkter**.

![Lägg till produkt][api-management-add-product]

Klicka på **Lägg till produkt**. Dialogrutan **Lägg till ny produkt** öppnas.

![Lägg till ny produkt][api-management-new-product-window]

I rutan **Rubrik** skriver du **Kostnadsfri utvärdering**.

I rutan **Beskrivning** skriver du följande text:  **Prenumeranter kan köra 10 anrop per minut upp till högst 200 anrop per vecka, varefter åtkomsten nekas.**

Produkter i API Management kan skyddas eller vara öppna. Skyddade produkter kräver en prenumeration innan de kan användas. Öppna produkter kan användas utan en prenumeration. Se till att **Kräv prenumeration** har valts om du vill skapa en skyddad produkt som kräver en prenumeration. Det här är standardinställningen.

Om du vill att en administratör ska granska och godkänna eller avvisa prenumerationsförsök för produkten väljer du **Kräv prenumerationsgodkännande**. Om kryssrutan inte är markerad godkänns prenumerationsförsök automatiskt. Eftersom vi i det här exemplet ska godkänna prenumerationer automatiskt lämnar vi rutan avmarkerad.

Om du vill tillåta att utvecklarkonton prenumererar flera gånger på den nya produkten markerar du kryssrutan **Tillåt flera samtidiga prenumerationer**. I den här självstudiekursen ska vi inte använda flera samtidiga prenumerationer och lämnar därför kryssrutan avmarkerad.

När alla värden har angetts klickar du på **Spara** för att skapa produkten.

![Produkten läggs till][api-management-product-added]

Som standard visas nya produkter för användare i gruppen **Administratörer**. Vi ska lägga till gruppen **Utvecklare**. Klicka på **Kostnadsfri utvärdering** och sedan på fliken **Synlighet**.

> I API Management används grupper för att hantera hur produkter visas för utvecklare. Produkter beviljar synlighet till grupper, och utvecklare kan visa och prenumerera på de produkter som är synliga för grupper som de är medlemmar i. Mer information finns i [Skapa och använda grupper i Azure API Management][Skapa och använda grupper i Azure API Management].
> 
> 

![Lägga till en utvecklargrupp][api-management-add-developers-group]

Markera kryssrutan **Utvecklare** och klicka sedan på **Spara**.

## <a name="add-api"> </a>Så här lägger du till ett API till produkten
I det här steget i självstudiekursen ska vi lägga till Echo API till den nya produkten ”Kostnadsfri utvärdering”.

> Varje API Management-tjänstinstans är förkonfigurerad med ett Echo-API som du kan använda för att experimentera och lära dig mer om API Management. Mer information finns i [Hantera ditt första API i Azure API Management][Hantera ditt första API i Azure API Management].
> 
> 

Klicka på **Produkter** på **API Management**-menyn till vänster och klicka sedan på **Kostnadsfri utvärdering** för att konfigurera produkten.

![Konfigurera produkten][api-management-configure-product]

Klicka på **Lägg till API för produkt**.

![Lägg till API för produkt][api-management-add-api]

Välj **Echo API** och klicka på **Spara**.

![Lägg till Echo API][api-management-add-echo-api]

## <a name="policies"> </a>Så här konfigurerar du principer för begränsning av anropsfrekvens och kvoter
Frekvensbegränsningar och kvoter konfigureras i principredigeraren. Klicka på **Principer** under **API Management**-menyn till vänster. Klicka på **Kostnadsfri utvärdering** i listan **Produkt**.

![Princip för produkt][api-management-product-policy]

Klicka på **Lägg till princip** för att importera principmallen och börja skapa principerna för frekvensbegränsning och kvoter.

![Lägg till princip][api-management-add-policy]

Du lägger till principer genom att placera markören i avsnittet **inbound** eller **outbound** i principmallen. Eftersom principer för frekvensbegränsning och kvoter är inkommande principer placerar du markören i ”inbound”.

![Principredigerare][api-management-policy-editor-inbound]

De två principerna som vi lägger till i den här självstudien är [Begränsa anropsfrekvensen per prenumeration](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate) och [Ange användningskvot per prenumeration](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota).

![Principrapporter][api-management-limit-policies]

När du har placerat markören i principelementet **inbound** klickar du på pilen bredvid **Begränsa anropsfrekvens per prenumeration** för att infoga dess principmall.

    <rate-limit calls="number" renewal-period="seconds">
    <api name="name" calls="number">
    <operation name="name" calls="number" />
    </api>
    </rate-limit>

**Begränsa anropsfrekvens per prenumeration** kan användas på produktnivå och även på API-nivå och för enskilda åtgärdsnamn. Eftersom vi bara använder principer på produktnivå i den här självstudien tar du bort elementen **api** och **operation** från elementet **rate-limit**, så att bara det yttre **rate-limit**-elementet är kvar, som du ser i följande exempel.

    <rate-limit calls="number" renewal-period="seconds">
    </rate-limit>

Eftersom den högsta tillåtna anropsfrekvensen i produkten  ”Kostnadsfri utvärdering” är 10 anrop per minut skriver du **10** som värde för attributet **calls** och **60** för attributet **renewal-period**.

    <rate-limit calls="10" renewal-period="60">
    </rate-limit>

Du konfigurerar principen **Ange användningskvot per prenumeration** genom att placera markören direkt under det nyligen tillagda **rate-limit**-elementet i elementet **inbound** och klickar sedan på pilen till vänster om **Ange användningskvot per prenumeration**.

    <quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
    <api name="name" calls="number" bandwidth="kilobytes">
    <operation name="name" calls="number" bandwidth="kilobytes" />
    </api>
    </quota>

Eftersom den här principen även är avsedd att användas på produktnivå tar du bor namnelementen **api** och **operation**, som du ser i följande exempel.

    <quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
    </quota>

Kvoter kan baseras på antalet anrop per intervall, på bandbredden eller båda. Eftersom vi inte begränsar baserat på bandbredd i den här självstudien tar du bort attributet **bandwidth**.

    <quota calls="number" renewal-period="seconds">
    </quota>

I produkten ”Kostnadsfri utvärdering” är kvoten 200 anrop per vecka. Ange **200** som värde för attributet **calls** och ange sedan **604800** som värde för attributet **renewal-period**.

    <quota calls="200" renewal-period="604800">
    </quota>

> Principintervall anges i sekunder. Om du vill beräkna intervallet för en vecka kan du multiplicera antalet dagar (7) med antalet timmar under en dag (24) med antalet minuter på en timme (60) med antalet sekunder på en minut (60): 7 * 24 * 60 * 60 = 604800.
> 
> 

När du har konfigurerat principen bör den se ut som i följande exempel.

    <policies>
        <inbound>
            <rate-limit calls="10" renewal-period="60">
            </rate-limit>
            <quota calls="200" renewal-period="604800">
            </quota>
            <base />

    </inbound>
    <outbound>

        <base />

        </outbound>
    </policies>

När de önskade principerna har konfigurerats klickar du på **Spara**.

![Spara en princip][api-management-policy-save]

## <a name="publish-product"> </a>Så här publicerar du produkten
Nu när API:erna läggs till och principerna konfigureras måste produkten publiceras så att den kan användas av utvecklare. Klicka på **Produkter** på **API Management**-menyn till vänster och klicka sedan på **Kostnadsfri utvärdering** för att konfigurera produkten.

![Konfigurera produkten][api-management-configure-product]

Klicka på **Publicera** och sedan på **Ja, publicera den** för att bekräfta.

![Publicera produkten][api-management-publish-product]

## <a name="subscribe-account"> </a>Så här prenumererar du på ett utvecklarkonto för produkten
Nu när produkten har publicerats kan utvecklare prenumerera på den och använda den.

> Administratörer av en API Management-instans har automatiskt en prenumeration på alla produkter. I det här steget i självstudien ska vi prenumerera på produkten ”Kostnadsfri utvärdering” med ett av utvecklarkontona som inte är kopplat till en administratör. Om ditt utvecklarkonto är en del av rollen Administratörer kan du följa med i det här steget, även om du redan har en prenumeration.
> 
> 

Klicka på **Användare** på **API Management**-menyn till vänster och klicka sedan på namnet på utvecklarkontot. I det här exemplet använder vi utvecklarkontot för **Clayton Gragg**.

![Konfigurera en utvecklare][api-management-configure-developer]

Klicka på **Lägg till prenumeration**.

![Lägg till en prenumeration][api-management-add-subscription-menu]

Välj **Kostnadsfri utvärdering** och klicka sedan på **Prenumerera**.

![Lägg till en prenumeration][api-management-add-subscription]

> [!NOTE]
> I den här självstudien är flera samtidiga prenumerationer inte aktiverat för produkten ”Kostnadsfri utvärdering”. I så fall skulle du uppmanas att namnge prenumerationen, som du ser i följande exempel.
> 
> 

![Lägg till en prenumeration][api-management-add-subscription-multiple]

När du klickar på **Prenumerera** visas produkten i listan **Prenumeration** för användaren.

![Prenumerationen har lagts till][api-management-subscription-added]

## <a name="test-rate-limit"> </a>Så här anropar du en åtgärd och testar frekvensbegränsningen
Nu när produkten ”Kostnadsfri utvärdering” har konfigurerats och publicerats kan vi anropa vissa åtgärder och testa frekvensbegränsningsprincipen.
Växla till utvecklarportalen genom att klicka på **Utvecklarportal** på menyn längst upp till höger.

![Utvecklarportalen][api-management-developer-portal-menu]

Klicka på **API:er** på den översta menyn och sedan på **Echo API**.

![Utvecklarportalen][api-management-developer-portal-api-menu]

Klicka på **GET Resource** och klicka sedan på **Prova**.

![Öppna konsolen][api-management-open-console]

Behåll standardparametervärdena och välj sedan din prenumerationsnyckel för produkten ”Kostnadsfri utvärdering”.

![Prenumerationsnyckel][api-management-select-key]

> [!NOTE]
> Om du har flera prenumerationer är du noga med att välja nyckeln för **Kostnadsfri utvärdering**. Annars tillämpas inte principerna som konfigurerades i föregående steg.
> 
> 

Klicka på **Skicka** och visa svaret. Observera **svarsstatusen** **200 OK**.

![Åtgärdsresultat][api-management-http-get-results]

Klicka på **Skicka** med en frekvens som är högre än de 10 anropen per minut i frekvensbegränsningsprincipen. När frekvensbegränsningsprincipen överskrids returneras svarsstatusen **429 För många förfrågningar**.

![Åtgärdsresultat][api-management-http-get-429]

**Svarsinnehållet** anger det återstående intervallet innan omförsök kommer att lyckas.

När frekvensbegränsningsprincipen som begränsar antalet anrop till 10 per minut tillämpas misslyckas efterföljande anrop tills 60 sekunder har förflutit från det första av de 10 lyckade anropen till produkten innan frekvensgränsen överskreds. I det här exemplet är det återstående intervallet 54 sekunder.

## <a name="next-steps"> </a>Nästa steg
* Titta på en demonstration om hur du ställer in frekvensbegränsningar och kvoter i följande videoklipp.

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Rate-Limits-and-Quotas/player]
> 
> 

[api-management-management-console]: ./media/api-management-howto-product-with-rules/api-management-management-console.png
[api-management-add-product]: ./media/api-management-howto-product-with-rules/api-management-add-product.png
[api-management-new-product-window]: ./media/api-management-howto-product-with-rules/api-management-new-product-window.png
[api-management-product-added]: ./media/api-management-howto-product-with-rules/api-management-product-added.png
[api-management-add-policy]: ./media/api-management-howto-product-with-rules/api-management-add-policy.png
[api-management-policy-editor-inbound]: ./media/api-management-howto-product-with-rules/api-management-policy-editor-inbound.png
[api-management-limit-policies]: ./media/api-management-howto-product-with-rules/api-management-limit-policies.png
[api-management-policy-save]: ./media/api-management-howto-product-with-rules/api-management-policy-save.png
[api-management-configure-product]: ./media/api-management-howto-product-with-rules/api-management-configure-product.png
[api-management-add-api]: ./media/api-management-howto-product-with-rules/api-management-add-api.png
[api-management-add-echo-api]: ./media/api-management-howto-product-with-rules/api-management-add-echo-api.png
[api-management-developer-portal-menu]: ./media/api-management-howto-product-with-rules/api-management-developer-portal-menu.png
[api-management-publish-product]: ./media/api-management-howto-product-with-rules/api-management-publish-product.png
[api-management-configure-developer]: ./media/api-management-howto-product-with-rules/api-management-configure-developer.png
[api-management-add-subscription-menu]: ./media/api-management-howto-product-with-rules/api-management-add-subscription-menu.png
[api-management-add-subscription]: ./media/api-management-howto-product-with-rules/api-management-add-subscription.png
[api-management-developer-portal-api-menu]: ./media/api-management-howto-product-with-rules/api-management-developer-portal-api-menu.png
[api-management-open-console]: ./media/api-management-howto-product-with-rules/api-management-open-console.png
[api-management-http-get]: ./media/api-management-howto-product-with-rules/api-management-http-get.png
[api-management-http-get-results]: ./media/api-management-howto-product-with-rules/api-management-http-get-results.png
[api-management-http-get-429]: ./media/api-management-howto-product-with-rules/api-management-http-get-429.png
[api-management-product-policy]: ./media/api-management-howto-product-with-rules/api-management-product-policy.png
[api-management-add-developers-group]: ./media/api-management-howto-product-with-rules/api-management-add-developers-group.png
[api-management-select-key]: ./media/api-management-howto-product-with-rules/api-management-select-key.png
[api-management-subscription-added]: ./media/api-management-howto-product-with-rules/api-management-subscription-added.png
[api-management-add-subscription-multiple]: ./media/api-management-howto-product-with-rules/api-management-add-subscription-multiple.png

[Lägga till åtgärder till ett API]: api-management-howto-add-operations.md
[Lägga till och publicera en produkt]: api-management-howto-add-products.md
[Övervakning och analys]: ../api-management-monitoring.md
[Lägga till API:er till en produkt]: api-management-howto-add-products.md#add-apis
[Publicera en produkt]: api-management-howto-add-products.md#publish-product
[Hantera ditt första API i Azure API Management]: api-management-get-started.md
[Skapa och använda grupper i Azure API Management]: api-management-howto-create-groups.md
[Visa prenumeranter för en produkt]: api-management-howto-add-products.md#view-subscribers
[Komma igång med Azure API Management]: api-management-get-started.md
[Skapa en API Management-tjänstinstans]: api-management-get-started.md#create-service-instance
[Nästa steg]: #next-steps

[Skapa en produkt]: #create-product
[Konfigurera principer för begränsning av anropsfrekvens och kvoter]: #policies
[Lägga till ett API till produkten]: #add-api
[Publicera produkten]: #publish-product
[Prenumerera på ett utvecklarkonto för produkten]: #subscribe-account
[Anropa en åtgärd och testa frekvensbegränsningen]: #test-rate-limit

[Begränsa anropsfrekvensen]: https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate
[Ange användningskvoter]: https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota



<!--HONumber=Nov16_HO2-->


