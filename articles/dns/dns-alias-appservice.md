---
title: Värd för belastningsbalanserade Azure-webbappar i zonens spets
description: Använda en Azure DNS-aliaspost för att vara värd för belastningsbalanserade webbappar i zonens spets
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 08/10/2019
ms.author: rohink
ms.openlocfilehash: 8ba96a028d51e6e5503bb4a8e6735b48033c9ba1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76937372"
---
# <a name="host-load-balanced-azure-web-apps-at-the-zone-apex"></a>Värd för belastningsbalanserade Azure-webbappar i zonens spets

DNS-protokollet förhindrar tilldelning av något annat än en A- eller AAAA-post vid zonsponsan. En exempelzonsapex är contoso.com. Den här begränsningen utgör ett problem för programägare som har belastningsbalanserade program bakom Traffic Manager. Det går inte att peka på Traffic Manager-profilen från zonapexposten. Därför måste programägare använda en lösning. En omdirigering på programlagret måste omdirigeras från zonapex till en annan domän. Ett exempel är en omdirigering från contoso.com till www\.contoso.com. Det här arrangemanget visar en enda felpunkt för omdirigeringsfunktionen.

Med aliasposter finns det här problemet inte längre. Nu kan programägare rikta sin zonapex-post till en Traffic Manager-profil som har externa slutpunkter. Programägare kan peka på samma Traffic Manager-profil som används för alla andra domäner i DNS-zonen.

Till exempel kan contoso.com och\.www contoso.com peka på samma Traffic Manager-profil. Detta gäller så länge Traffic Manager-profilen bara har konfigurerat externa slutpunkter.

I den här artikeln får du lära dig hur du skapar en aliaspost för domänens apex och konfigurerar slutpunkterna i Traffic Manager-profilen för dina webbappar.

Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Krav

Du måste ha ett domännamn tillgängligt som du kan ha i Azure DNS för att testa med. Du måste ha fullständig kontroll över den här domänen. Fullständig behörighet omfattar möjligheten att ange namnserverposter (NS-poster) för domänen.

Anvisningar för att vara värd för din domän i Azure DNS finns i [Självstudie: var värd för din domän i Azure DNS](dns-delegate-domain-azure-dns.md).

Den exempeldomän som används i den här självstudien är contoso.com, men du använder ditt eget domännamn.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp för att lagra alla resurser som används i den här artikeln.

## <a name="create-app-service-plans"></a>Skapa apptjänstplaner

Skapa två Web App Service-abonnemang i resursgruppen med hjälp av följande tabell för konfigurationsinformation. Mer information om hur du skapar en App Service-plan finns [i Hantera en App Service-plan i Azure](../app-service/app-service-plan-manage.md).


|Namn  |Operativsystem  |Location  |Prisnivå  |
|---------|---------|---------|---------|
|ASP-01     |Windows|USA, östra|Utveckling/test D1-delad|
|ASP-02     |Windows|USA, centrala|Utveckling/test D1-delad|

## <a name="create-app-services"></a>Skapa apptjänster

Skapa två webbappar, en i varje App Service-plan.

1. I det övre vänstra hörnet på Azure-portalsidan väljer du **Skapa en resurs**.
2. Skriv **webbapp** i sökfältet och tryck på Retur.
3. Välj **Webbapp**.
4. Välj **Skapa**.
5. Acceptera standardinställningarna och använd följande tabell för att konfigurera de två webbapparna:

   |Namn<br>(måste vara unik inom .azurewebsites.net)|Resursgrupp |Körningsstack|Region|App Service Plan/Plats
   |---------|---------|-|-|-------|
   |App-01|Använd befintlig<br>Välj din resursgrupp|.NET Core 2.2|USA, östra|ASP-01(D1)|
   |App-02|Använd befintlig<br>Välj din resursgrupp|.NET Core 2.2|USA, centrala|ASP-02(D1)|

### <a name="gather-some-details"></a>Samla in några detaljer

Nu måste du notera IP-adressen och värdnamnet för webbapparna.

1. Öppna resursgruppen och välj din första webbapp **(App-01** i det här exemplet).
2. Välj **Egenskaper**i den vänstra kolumnen .
3. Observera adressen under **URL**och under **Utgående IP-adresser** noterar den första IP-adressen i listan. Du använder den här informationen senare när du konfigurerar slutpunkterna i Traffic Manager.
4. Upprepa för **App-02**.

## <a name="create-a-traffic-manager-profile"></a>Skapa en Traffic Manager-profil

Skapa en Traffic Manager-profil i resursgruppen. Använd standardvärdena och skriv ett unikt namn inom trafficmanager.net namnområdet.

Information om hur du skapar en Traffic Manager-profil finns i [Snabbstart: Skapa en Traffic Manager-profil för ett webbprogram med högtillgänge](../traffic-manager/quickstart-create-traffic-manager-profile.md).

### <a name="create-endpoints"></a>Skapa slutpunkter

Nu kan du skapa slutpunkterna för de två webbapparna.

1. Öppna resursgruppen och välj din Traffic Manager-profil.
2. Välj **Slutpunkter**i den vänstra kolumnen .
3. Välj **Lägg till**.
4. Använd följande tabell för att konfigurera slutpunkterna:

   |Typ  |Namn  |Mål  |Location  |Inställningar för anpassat huvud|
   |---------|---------|---------|---------|---------|
   |Extern slutpunkt     |Slut-01|IP-adress som du har registrerat för App-01|USA, östra|värd:\<webbadressen du spelade in för App-01\><br>Exempel: **värd:app-01.azurewebsites.net**|
   |Extern slutpunkt     |Slut-02|IP-adress som du har registrerat för App-02|USA, centrala|värd:\<webbadressen du spelade in för App-02\><br>Exempel: **värd:app-02.azurewebsites.net**

## <a name="create-dns-zone"></a>Skapa DNS-zon

Du kan antingen använda en befintlig DNS-zon för testning eller skapa en ny zon. Information om hur du skapar och delegerar en ny DNS-zon i Azure finns i [Självstudiekurs: Vara värd för din domän i Azure DNS](dns-delegate-domain-azure-dns.md).

## <a name="add-a-txt-record-for-custom-domain-validation"></a>Lägga till en TXT-post för anpassad domänverifiering

När du lägger till ett anpassat värdnamn i dina webbappar letar den efter en specifik TXT-post för att validera domänen.

1. Öppna resursgruppen och välj DNS-zonen.
2. Välj **Postuppsättning**.
3. Lägg till postuppsättningen med följande tabell. Använd webbadressen för den faktiska webbappen som du tidigare har spelat in för värdet:

   |Namn  |Typ  |Värde|
   |---------|---------|-|
   |@     |TXT|App-01.azurewebsites.net|


## <a name="add-a-custom-domain"></a>Lägga till en anpassad domän

Lägg till en anpassad domän för båda webbapparna.

1. Öppna resursgruppen och välj din första webbapp.
2. Välj **Anpassade domäner**i den vänstra kolumnen .
3. Under **Anpassade domäner**väljer du **Lägg till anpassad domän**.
4. Skriv ditt eget domännamn under **Anpassad domän.** Till exempel contoso.com.
5. Välj **Verifiera**.

   Domänen ska godkännas och visa gröna bockar bredvid **Tillgänglighet för Värdnamn** och **Domänägarskap**.
5. Välj **Lägg till anpassad domän**.
6. Uppdatera webbläsaren om du vill se det nya värdnamnet under **Värdnamn som tilldelats webbplatsen.** Uppdateringen på sidan visar inte alltid ändringar direkt.
7. Upprepa den här proceduren för din andra webbapp.

## <a name="add-the-alias-record-set"></a>Lägga till aliaspostuppsättningen

Lägg nu till en aliaspost för zonsponsen.

1. Öppna resursgruppen och välj DNS-zonen.
2. Välj **Postuppsättning**.
3. Lägg till postuppsättningen med följande tabell:

   |Namn  |Typ  |Aliaspostuppsättning  |Aliastyp  |Azure-resurs|
   |---------|---------|---------|---------|-----|
   |@     |A|Ja|Azure-resurs|Traffic Manager - din profil|


## <a name="test-your-web-apps"></a>Testa dina webbappar

Nu kan du testa för att se till att du kan nå din webbapp och att den är belastningsbalanserad.

1. Öppna en webbläsare och bläddra till din domän. Till exempel contoso.com. Du bör se standardwebbappsidan.
2. Stoppa din första webbapp.
3. Stäng webbläsaren och vänta några minuter.
4. Starta webbläsaren och bläddra till din domän. Du bör fortfarande se standardwebbappsidan.
5. Stoppa din andra webbapp.
6. Stäng webbläsaren och vänta några minuter.
7. Starta webbläsaren och bläddra till din domän. Du bör se Fel 403, som anger att webbappen har stoppats.
8. Starta din andra webbapp.
9. Stäng webbläsaren och vänta några minuter.
10. Starta webbläsaren och bläddra till din domän. Du bör se standardwebbappsidan igen.

## <a name="next-steps"></a>Nästa steg

Mer information om aliasposter finns i följande artiklar:

- [Självstudiekurs: Konfigurera en aliaspost för att referera till en offentlig Azure-IP-adress](tutorial-alias-pip.md)
- [Självstudie: Konfigurera en aliaspost för att hantera apex-domännamn med Traffic Manager](tutorial-alias-tm.md)
- [Vanliga frågor och svar om DNS](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)

Mer information om hur du migrerar ett aktivt DNS-namn finns i [Migrera ett aktivt DNS-namn till Azure App Service](../app-service/manage-custom-dns-migrate-domain.md).
