---
title: "Guide för att skapa en datatjänst för Marketplace | Microsoft Docs"
description: "Detaljerade anvisningar för hur du skapar, certifiera och distribuera en Data-tjänst för att köpa på Azure Marketplace."
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 96194198-6991-43b4-918e-ee337e250339
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2016
ms.author: hascipio; avikova
ms.openlocfilehash: c0c9362f1c2e15c947aaaf7187f3383ad243140f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="data-service-publishing-guide-for-the-azure-marketplace"></a>Datatjänst publicering Guide för Azure Marketplace
> [!IMPORTANT]
> **Just nu vi inte längre onboarding några nya Data Service-utgivare. Nya dataservices kommer inte godkännas för lista.** Om du har ett SaaS-affärsprogram som du vill publicera på AppSource hittar du mer information [här](https://appsource.microsoft.com/partners). Om du har en IaaS-program eller developer-tjänsten som du vill publicera på Azure Marketplace du hittar mer information [här](https://azure.microsoft.com/marketplace/programs/certified/).
> 
> 

När du har slutfört steg 1, [konto skapas och registrering](marketplace-publishing-accounts-creation-registration.md), vi Interaktiv du via den [allmänna icke-tekniska](marketplace-publishing-pre-requisites.md) och [tekniska krav](marketplace-publishing-data-service-creation-prerequisites.md) av en datatjänst erbjudande på Azure Marketplace. Nu går du igenom stegen för att skapa ett tjänsterbjudande med Data på den [Publiceringsportal] [ link-pubportal] för Azure Marketplace.

## <a name="1----login-to-the-publishing-portal"></a>1.    Logga in på publicering Portal.
Gå till [https://publish.windowsazure.com](https://publish.windowsazure.com.)

**För första gången inloggningen till Publishing Portal, använder du samma konto som företagets säljare profil har registrerats i Developer Center.**  (Senare du kan lägga till anställda på företaget som medadministratör i Publishing Portal).

Klicka på den **publicera en datatjänster** panelen om det här är den första inloggningen till publishing portal.

## <a name="2----choose-data-services-in-the-navigation-menu-on-the-left-side"></a>2.    Välj **datatjänster** i navigeringsmenyn till vänster.
  ![Rita](media/marketplace-publishing-data-service-creation/pubportal-main-nav.png)

## <a name="3----create-a-new-data-service"></a>3.    Skapa en ny datatjänst
Fyll i namnet för din nya erbjuder för Data Service och klicka på ”+” till höger.

  ![Rita](media/marketplace-publishing-data-service-creation/step-3.png)

## <a name="4----review-the-sub-menu-under-the-newly-created-data-service-in-the-navigation-menu"></a>4.    Granska undermeny under den nyligen skapade tjänsten Data på navigeringsmenyn.
Klicka på den **genomgången** och granska alla nödvändiga steg som behövs för att publicera korrekt tjänsten Data på Azure Marketplace.

> [!TIP]
> Du kan alltid Klicka på länkarna på sidan ”genomgången” eller Använd flikarna på Data Service-erbjudandet undermeny på vänster sida.
> 
> 

## <a name="5----create-a-new-plan"></a>5.    Skapa en ny Plan.
### <a name="offers-plans-transactions"></a>Erbjudanden planer, transaktioner.
Varje erbjudande kan ha flera planer, men måste ha minst en (1) Plan. När slutanvändarna prenumererar på erbjudandet prenumerera de för en Plan med erbjudandet. Varje plan definierar hur slutanvändarna kommer att kunna använda tjänsten.

Stöd för närvarande inte Azure Marketplace månatlig prenumeration transaktion baserat modellen för datatjänster, d.v.s. slutanvändare betalar månadsavgift enligt priset för den plan som de som prenumererar på och kommer att kunna använda varje månad antal transaktioner som definieras av planen.

Varje transaktion som vanligtvis definieras som antalet poster som returnerar Data Service baserat på frågan skickas till tjänsten. Standardvärdet är 100. Antal transaktioner som returneras till varje fråga kommer att antalet poster som dividerat med 100 och avrundade uppåt till närmaste heltal.

Det är Azure Marketplace-tjänsten layer ansvar för att övervaka (mätaren) antal transaktioner som används av varje fråga.

> [!IMPORTANT]
> Slutanvändare som nått gränsen för transaktion under månaden blockeras inte kan fortsätta att använda tjänsten till slutet av deras månatlig prenumeration cykel.
> 
> Inkludera obegränsat antal transaktioner planen eller en av planerna kan (men måste inte).
> 
> 

### <a name="create-a-plan"></a>Skapa en plan.
1. Klicka på **”+”** bredvid den ”Lägg till en ny plan”.
2. Välj något av alternativen: **obegränsad** eller **begränsad** användning för den här planen.  Om begränsad Anger transaktionsnumret kan planen använda i en månad.
   
    ![Rita](media/marketplace-publishing-data-service-creation/step-5.1.png)  
   
    Publicera portalen föreslås också ”identifieraren för Cacheuppdateringsplanen”, som används för att meddela slutanvändarna namnet på planen i Användargränssnittet och också används av tjänsten marknadsplatsen för att identifiera planen. Du kan ändra ”planera identifieraren” om du vill.
   
   > [!NOTE]
   > ”Planera identifieraren” måste vara unikt inom omfånget för varje erbjudande. Eftersom många andra identifierare som används i identifierare Publishing Portal planera låses efter den första publiceringen till produktion och du inte kommer att kunna ändra den här identifieraren.
   > 
   > 
3. Klicka för att godkänna valet.
4. Du kommer att få ytterligare några frågor om nyskapade planen.
   
    ![Rita](media/marketplace-publishing-data-service-creation/step-5.2.png)

| Fråga | Betydelse |
| --- | --- |
| **Den här planen är ledig och tillgänglig över hela världen?** |Du kan skapa en plan för helt kostnadsfri-för-kostnad. Om det är bara planen för det här erbjudandet – innebär det att du publicerar ”kostnadsfritt erbjuda” i Marketplace. Om det är bara för ett (få) planen it kan du välja att erbjuda slutanvändarna om du vill veta mer om din tjänst med ett relativt litet antal transaktioner per månad.  Om svaret är ”Ja”, och inga ytterligare frågor. |

> [!NOTE]
> Slutanvändare kan alltid uppgradera till betald planer.
> 
> 

| Fråga | Betydelse |
| --- | --- |
| **Finns kostnadsfri utvärderingsversion?** |Du kan välja mellan ”Nej utvärderingsversion” alls eller ger ett alternativ för att använda din Plan för ”en månad”. Utgivare som du använder det här alternativet för att ge slutanvändarna möjlighet att förstå fördelarna med erbjudandet kostnadsfritt för en månad. |

> [!IMPORTANT]
> Slutanvändarna kommer bara att kunna köpa en kostnadsfri utvärderingsversion om de har etablerat betalningsalternativ t.ex. kreditkort, enterprise-avtal.
> 
> Efter en månad efter den kostnadsfria utvärderingsversionen av startar Azure Marketplace debitering kunder priset vid datumet för prenumerationen, om kunden initierade prenumeration skulle avbrytas. Inget särskilt meddelande levereras till slutanvändarna.
> 
> 

| Fråga | Betydelse |
| --- | --- |
| **Den här planen krävs befordran kod att köpa?** |Utgivare har möjlighet att begränsa åtkomsten till deras serviceplaner genom att tillhandahålla en särskild kod som kallas ”Promocode A” för specifika kunder. Slutanvändare som har den här Promocode kommer att kunna prenumerera på planen. Om du väljer ”Nej”, så att alla från den region där erbjudandet är tillgängligt (se [Marketplace Marketing Content guiden](marketplace-publishing-push-to-staging.md) för mer information) kan prenumerera på den här planen. Inga ytterligare frågor blir ombedd. |
| **Dessutom dölja den här planen som inte har en giltig befordran koden?** |Om svaret på den föregående frågan är ”Yes” har utgivaren ett alternativ för att ta bort den här planen från som visas i Användargränssnittet på Marketplace. Det innebär kunder inte ser den här planen med erbjudandet information på sidan. Slutanvändare som tar emot en promocode om du vill köpa det, kommer att kunna prenumerera på den med hjälp av den här promocode. |

## <a name="6----create-your-marketplace-marketing-content"></a>6.    Skapa din Marketplace marknadsföring innehåll
Att ange information som krävs i **marknadsföring, prissättning, Support och kategorier** flikar besök [Marketplace Marketing Content guiden](marketplace-publishing-push-to-staging.md) som är gemensamma för alla artefakter som publicerats i Azure Marketplace.  

## <a name="7----connect-your-offer-to-your-service-sql-azure-based-or-web-service-based"></a>7.    Erbjudandet att ansluta till tjänsten (SQL Azure-baserat eller webbtjänst baserat).
Klicka på den **datatjänster** undermeny.

På den övre hälften av sidan kommer du bli ombedd att ange erbjudanden **Namespace**.  

  ![Rita](media/marketplace-publishing-data-service-creation/step-7.png)

Den nedan fråga definierar hur utgivaren ska exponera nyskapad erbjudande på Azure Marketplace. (Mer information finns i [Data Services tekniska nödvändiga Guide](marketplace-publishing-data-service-creation-prerequisites.md)).

  ![Rita](media/marketplace-publishing-data-service-creation/step-7.2.png)

**Publicera databastjänsten baserat**

Klicka på **databasen**. Följande sida visas:

  ![Rita](media/marketplace-publishing-data-service-creation/step-7.3.png)

Skapa en CSDL-mappning för datauppsättningen baserat på SQL Azure DB:

  ![Rita](media/marketplace-publishing-data-service-creation/step-7.4.png)

Och sedan för varje tabell

  ![Rita](media/marketplace-publishing-data-service-creation/step-7.5.png)

  ![Rita](media/marketplace-publishing-data-service-creation/step-7.6.png)

Om webbtjänsten

  ![Rita](media/marketplace-publishing-data-service-creation/step-7.7.png)

> [!IMPORTANT]
> Läs [mappning av en befintlig webbtjänst OData via CSDL](marketplace-publishing-data-service-creation-odata-mapping.md) detaljerade anvisningar och exempel för att skapa en CSDL-webbtjänst.
> 
> 

## <a name="next-steps"></a>Nästa steg
Nu när du har skapat ditt Data tjänsterbjudande, se till att du har slutfört instruktionerna i den [Marketplace Marketing Content guiden](marketplace-publishing-push-to-staging.md) innan du gå till [testa Data Service i Förproduktion](marketplace-publishing-data-service-test-in-staging.md).

## <a name="see-also"></a>Se även
* [Komma igång: Hur du publicerar ett erbjudande på Azure Marketplace](marketplace-publishing-getting-started.md)
* Om du vill förstå den övergripande processen för OData-mappning och syfte, den här artikeln [mappning för OData-tjänsten](marketplace-publishing-data-service-creation-odata-mapping.md) att granska definitioner, strukturer och instruktioner.
* Om du är intresserad av att lära sig och förstå de specifika noderna och deras parametrar kan den här artikeln [mappa Data Service OData-noder](marketplace-publishing-data-service-creation-odata-mapping-nodes.md) definitioner och förklaringar, exempel och Använd case kontext.
* Om du vill granska exempel den här artikeln [mappa Data Service OData-exempel](marketplace-publishing-data-service-creation-odata-mapping-examples.md) att exempelkoden förstå och kodsyntax och på snabbmenyn.

[link-pubportal]:https://publish.windowsazure.com
