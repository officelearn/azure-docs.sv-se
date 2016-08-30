<properties
    pageTitle="Förbättra prestanda i Azure API Management med cachelagring | Microsoft Azure"
    description="Lär dig hur du förbättrar svarstider, bandbreddsanvändning och webbtjänstbelastning i API Management-tjänstanrop."
    services="api-management"
    documentationCenter=""
    authors="steved0x"
    manager="erikre"
    editor=""/>

<tags
    ms.service="api-management"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/25/2016"
    ms.author="sdanie"/>

# Förbättra prestanda i Azure API Management med cachelagring

Du kan konfigurera åtgärder i API Management för cachelagring av svar. Cachelagring av svar kan avsevärt minska API:ets svarstider, bandbreddsanvändning och webbtjänstbelastning när data inte ändras så ofta.

Den här guiden beskriver hur du lägger till cachelagring av svar till ditt API och hur du konfigurerar principer för Echo API-exempelåtgärderna. När du är klar kan du bekräfta att cachelagringen fungerar genom att anropa åtgärden från utvecklarportalen.

>[AZURE.NOTE] Mer information om hur du cachelagrar objekt med nycklar med hjälp av principuttryck finns i [Anpassad cachelagring i Azure API Management](api-management-sample-cache-by-key.md).

## Krav

Innan du följer stegen i den här guiden måste du ha en API Management-tjänstinstans med ett API och en konfigurerad produkt. Om du inte har skapat en API Management-tjänstinstans än läser du [Skapa en API Management-tjänstinstans][] i självstudiekursen [Komma igång med Azure API Management][].

## <a name="configure-caching"> </a>Konfigurera en åtgärd för cachelagring

I det här steget ska du granska cachelagringsinställningarna för åtgärden **GET Resource (cached)** i Echo API-exemplet.

>[AZURE.NOTE] Varje API Management-tjänstinstans är förkonfigurerad med ett Echo-API som du kan använda för att experimentera och lära dig mer om API Management. Mer information finns i [Komma igång med Azure API Management][].

Börja genom att klicka på **Hantera** på den klassiska Azure-portalen för API Management-tjänsten. När du gör det kommer du till utgivarportalen för API Management.

![Utgivarportalen][api-management-management-console]

Klicka på **API:er** på **API Management**-menyn till vänster och klicka sedan på **Echo API**.

![Echo API][api-management-echo-api]

Klicka på fliken **Åtgärder** och klicka sedan på **GET resource (cached)** i listan **Åtgärder**.

![Echo API-åtgärder][api-management-echo-api-operations]

Visa åtgärdens cachelagringsinställningar genom att klicka på fliken **Cachelagring**.

![Fliken Cachelagring][api-management-caching-tab]

Om du vill aktivera cachelagring för en åtgärd markerar du kryssrutan **Aktivera**. I det här exemplet är cachelagring aktiverat.

Varje åtgärdssvar registreras, baserat på värdena i fälten **Variera med frågesträngparametrar** och **Variera med sidhuvuden**. Om du vill cachelagra flera svar baserat på frågesträngsparametrar eller sidhuvuden kan du konfigurera dem i dessa två fält.

**Varaktighet** anger giltighetsintervallet för de cachelagrade svaren. I det här exemplet är intervallet **3600** sekunder, vilket motsvarar en timme.

Med cachelagringskonfigurationen i det här exemplet returnerar den första begäran till **GET Resource (cached)** ett svar från backend-tjänsten. Svaret cachelagras och registreras av de angivna sidhuvudena och frågesträngsparametrarna. För efterföljande anrop till åtgärden, med matchande parametrar, returneras det cachelagrade svaret till cachlagringsintervallets slut.

## <a name="caching-policies"> </a>Granska cachelagringsprinciperna

I det här steget ska du granska cachelagringsinställningarna för åtgärden **GET Resource (cached)** i Echo API-exemplet.

När cachelagringsinställningarna har konfigurerats för en åtgärd på fliken **Cachelagring** läggs cachelagringsprinciper till för åtgärden. Dessa principer kan visas och redigeras i principredigeraren.

Klicka på **Principer** på **API Management**-menyn till vänster och välj sedan **Echo API/GET Resource (cached)** i listrutan **Åtgärd**.

![Åtgärden Principomfattning][api-management-operation-dropdown]

Visar principerna för den här åtgärden i principredigeraren.

![API Management-principredigeraren][api-management-policy-editor]

Principdefinitionen för den här åtgärden innehåller principerna som definierar cachelagringskonfigurationen som vi granskade på fliken **Cachelagring** i föregående steg.

    <policies>
        <inbound>
            <base />
            <cache-lookup vary-by-developer="false" vary-by-developer-groups="false">
                <vary-by-header>Accept</vary-by-header>
                <vary-by-header>Accept-Charset</vary-by-header>
            </cache-lookup>
            <rewrite-uri template="/resource" />
        </inbound>
        <outbound>
            <base />
            <cache-store caching-mode="cache-on" duration="3600" />
        </outbound>
    </policies>

>[AZURE.NOTE] Ändringar som görs i cachelagringsprinciperna i principredigeraren visas på fliken **Cachelagring** för en åtgärd, och tvärtom.

## <a name="test-operation"> </a>Anropa en åtgärd och testa cachelagringen

Om du vill se hur cachelagringen fungerar i praktiken kan du anropa åtgärden från utvecklarportalen. Klicka på **Utvecklarportal** på menyn längst upp till höger.

![Utvecklarportalen][api-management-developer-portal-menu]

Klicka på **API:er** på den översta menyn och välj **Echo API**.

![Echo API][api-management-apis-echo-api]

>Om du bara har ett API som är konfigurerat eller som visas för ditt konto och du klickar på API:er så kommer du direkt till åtgärderna för det API:et.

Välj åtgärden **GET Resource (cached)** och klicka på **Öppna konsol**.

![Öppna konsolen][api-management-open-console]

Från konsolen kan du anropa åtgärder direkt från utvecklarportalen.

![Konsolen][api-management-console]

Behåll standardvärdena för **param1** och **param2**.

Välj önskad nyckel i listrutan **subscription-key**. Om ditt konto bara har en prenumeration är den redan vald.

Ange **sampleheader:value1** i textrutan **Begärandehuvuden**.

Klicka på **HTTP Get** och notera svarshuvudena.

Ange **sampleheader:value2** i textrutan **Begärandehuvuden** och klicka sedan på **HTTP Get**.

Observera att värdet för **sampleheader** fortfarande är **value1** i svaret. Prova några olika värden och observera att det cachelagrade svaret från det första anropet returneras.

Ange **25** i fältet **param2** och klicka sedan på **HTTP Get**.

Observera att värdet för **sampleheader** i svaret nu är **value2**. Eftersom åtgärdsresultatet registreras av frågesträngen returnerades inte det föregående cachelagrade svaret.

## <a name="next-steps"> </a>Nästa steg

-   Titta gärna igenom avsnitten i självstudiekursen [Komma igång med avancerad API-konfiguration][].
-   Mer information om cachelagringsprinciper finns i [Cachelagringsprinciper][] i [Principreferens för API Management][].
-   Mer information om hur du cachelagrar objekt med nycklar med hjälp av principuttryck finns i [Anpassad cachelagring i Azure API Management](api-management-sample-cache-by-key.md).

[api-management-management-console]: ./media/api-management-howto-cache/api-management-management-console.png
[api-management-echo-api]: ./media/api-management-howto-cache/api-management-echo-api.png
[api-management-echo-api-operations]: ./media/api-management-howto-cache/api-management-echo-api-operations.png
[api-management-caching-tab]: ./media/api-management-howto-cache/api-management-caching-tab.png
[api-management-operation-dropdown]: ./media/api-management-howto-cache/api-management-operation-dropdown.png
[api-management-policy-editor]: ./media/api-management-howto-cache/api-management-policy-editor.png
[api-management-developer-portal-menu]: ./media/api-management-howto-cache/api-management-developer-portal-menu.png
[api-management-apis-echo-api]: ./media/api-management-howto-cache/api-management-apis-echo-api.png
[api-management-open-console]: ./media/api-management-howto-cache/api-management-open-console.png
[api-management-console]: ./media/api-management-howto-cache/api-management-console.png


[Lägga till åtgärder i ett API]: api-management-howto-add-operations.md
[Lägga till och publicera en produkt]: api-management-howto-add-products.md
[Övervakning och analys]: api-management-monitoring.md
[Lägga till API:er till en produkt]: api-management-howto-add-products.md#add-apis
[Publicera en produkt]: api-management-howto-add-products.md#publish-product
[Komma igång med Azure API Management]: api-management-get-started.md
[Komma igång med avancerad API-konfiguration]: api-management-get-started-advanced.md

[Principreferens för API Management]: https://msdn.microsoft.com/library/azure/dn894081.aspx
[Cachelagringsprinciper]: https://msdn.microsoft.com/library/azure/dn894086.aspx

[Skapa en API Management-tjänstinstans]: api-management-get-started.md#create-service-instance

[Konfigurera en åtgärd för cachelagring]: #configure-caching
[Granska cachelagringsprinciperna]: #caching-policies
[Anropa en åtgärd och testa cachelagringen]: #test-operation
[Nästa steg]: #next-steps



<!--HONumber=jun16_HO2-->


