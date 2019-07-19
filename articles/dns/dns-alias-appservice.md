---
title: Värd belastnings utjämning av Azure-Webbappar i zon Apex
description: Använd en Azure DNS Ali Aset för att vara värd för belastningsutjämnade webbappar i zonen Apex
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 7/13/2019
ms.author: victorh
ms.openlocfilehash: 7d20ef750aa4556a73852982631423d3d08271f5
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/12/2019
ms.locfileid: "67854115"
---
# <a name="host-load-balanced-azure-web-apps-at-the-zone-apex"></a>Värd belastnings utjämning av Azure-Webbappar i zon Apex

DNS-protokollet förhindrar tilldelning av något annat än en A-eller AAAA-post i zonens Apex. Ett exempel på en zons Apex är contoso.com. Den här begränsningen utgör ett problem för program ägare som har belastningsutjämnade program bakom Traffic Manager. Det går inte att peka på Traffic Manager profilen från zonens Apex-post. Därför måste program ägare använda en lösning. En omdirigering på program nivån måste omdirigeras från zonens Apex till en annan domän. Ett exempel är en omdirigering från contoso.com till\.www-contoso.com. Den här ordningen visar en enskild felpunkt för omdirigerings funktionen.

Det här problemet finns inte längre med Ali Aset poster. Nu kan program ägare peka sin zon Apex-post till en Traffic Manager profil som har externa slut punkter. Program ägare kan peka på samma Traffic Manager profil som används för alla andra domäner i DNS-zonen.

Till exempel kan contoso.com och www\.-contoso.com peka på samma Traffic Manager profil. Detta är fallet så länge den Traffic Manager profilen bara har konfigurerat externa slut punkter.

I den här artikeln får du lära dig hur du skapar en aliasresurspost för din domän Apex och konfigurerar Traffic Manager profil slut punkter för dina webb program.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Du måste ha ett domännamn tillgängligt som du kan ha i Azure DNS för att testa med. Du måste ha fullständig kontroll över den här domänen. Fullständig behörighet omfattar möjligheten att ange namnserverposter (NS-poster) för domänen.

Instruktioner för att vara värd för din domän i Azure DNS [finns i Självstudier: Använda Azure DNS som värd för din domän.](dns-delegate-domain-azure-dns.md)

Den exempeldomän som används i den här självstudien är contoso.com, men du använder ditt eget domännamn.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resurs grupp som innehåller alla resurser som används i den här artikeln.

## <a name="create-app-service-plans"></a>Skapa App Services planer

Skapa två Webb App Services planer i resurs gruppen med hjälp av följande tabell för konfigurations information. Mer information om hur du skapar en App Service plan finns i [hantera ett App Service plan i Azure](../app-service/app-service-plan-manage.md).


|Namn  |Operativsystem  |Location  |Prisnivå  |
|---------|---------|---------|---------|
|ASP-01     |Windows|East US|Dev/test D1 – delad|
|ASP-02     |Windows|Centrala USA|Dev/test D1 – delad|

## <a name="create-app-services"></a>Skapa App Services

Skapa två webb program, en i varje App Service plan.

1. I det övre vänstra hörnet av Azure Portal sidan klickar du på **skapa en resurs**.
2. Skriv in **webbapp** i Sök fältet och tryck på RETUR.
3. Klicka på **webbapp**.
4. Klicka på **Skapa**.
5. Acceptera standardinställningarna och Använd följande tabell för att konfigurera de två webbapparna:

   |Namn<br>(måste vara unikt inom. azurewebsites.net)|Resursgrupp |App Service plan/plats
   |---------|---------|---------|
   |App-01|Använd befintlig<br>Välj din resursgrupp|ASP – 01 (USA, östra)|
   |App-02|Använd befintlig<br>Välj din resursgrupp|ASP-02 (Central USA)|

### <a name="gather-some-details"></a>Samla in viss information

Nu måste du anteckna IP-adressen och värd namnet för apparna.

1. Öppna resurs gruppen och klicka på din första app (**app-01** i det här exemplet).
2. Klicka på **Egenskaper**i den vänstra kolumnen.
3. Anteckna adressen under **URL**och under **utgående IP-adresser** noterar du den första IP-adressen i listan. Du kommer att använda den här informationen senare när du konfigurerar Traffic Manager slut punkter.
4. Upprepa för **app-02**.

## <a name="create-a-traffic-manager-profile"></a>Skapa en Traffic Manager-profil

Skapa en Traffic Manager-profil i din resurs grupp. Använd standardinställningarna och ange ett unikt namn i namn området trafficmanager.net.

Information om hur du skapar en Traffic Manager-profil [finns i snabb start: Skapa en Traffic Manager-profil för ett webb program](../traffic-manager/quickstart-create-traffic-manager-profile.md)med hög tillgänglighet.

### <a name="create-endpoints"></a>Skapa slutpunkter

Nu kan du skapa slut punkter för de två webbapparna.

1. Öppna resurs gruppen och klicka på din Traffic Manager-profil.
2. Klicka på **slut punkter**i den vänstra kolumnen.
3. Klicka på **Lägg till**.
4. Använd följande tabell för att konfigurera slut punkterna:

   |type  |Namn  |Mål  |Location  |Anpassade huvud inställningar|
   |---------|---------|---------|---------|---------|
   |Extern slut punkt     |Slut – 01|IP-adress som du har spelat in för app-01|East US|värd:\<den URL som du registrerade för app-01\><br>Exempel: **värd: app-01.azurewebsites.net**|
   |Extern slut punkt     |End-02|IP-adress som du har spelat in för app-02|Centrala USA|värd:\<den URL som du registrerade för appen-02\><br>Exempel: **värd: app-02.azurewebsites.net**

## <a name="create-dns-zone"></a>Skapa DNS-zon

Du kan antingen använda en befintlig DNS-zon för testning, eller så kan du skapa en ny zon. Information om hur du skapar och delegerar en ny DNS- [zon i Azure finns i Självstudier: Använda Azure DNS som värd för din domän.](dns-delegate-domain-azure-dns.md)

### <a name="add-the-alias-record-set"></a>Lägg till post uppsättningen alias

När din DNS-zon är klar kan du lägga till en aliasresurspost för zonens Apex.

1. Öppna resurs gruppen och klicka på DNS-zonen.
2. Klicka på **Postuppsättning**.
3. Lägg till post uppsättningen i följande tabell:

   |Namn  |type  |Aliasuppsättning för post uppsättning  |Typ av alias  |Azure-resurs|
   |---------|---------|---------|---------|-----|
   |@     |A|Ja|Azure-resurs|Traffic Manager – din profil|

## <a name="add-custom-hostnames"></a>Lägg till anpassade värdnamn

Lägg till ett anpassat värdnamn i båda webbapparna.

1. Öppna din resurs grupp och klicka på din första webbapp.
2. Klicka på **anpassade domäner**i den vänstra kolumnen.
3. Klicka på **Lägg till värdnamn**.
4. Under värdnamn anger du ditt domän namn. Till exempel contoso.com.

   Din domän bör klara verifiering och Visa gröna kryss markeringar bredvid **värd namns tillgänglighet** och **domän ägarskap**.
5. Klicka på **Lägg till värdnamn**.
6. Uppdatera webbläsaren om du vill se det nya värd namnet under **värdnamn som har tilldelats till platsen**. Uppdateringen på sidan visar inte alltid ändringar omedelbart.
7. Upprepa proceduren för den andra webb programmet.

## <a name="test-your-web-apps"></a>Testa dina webb program

Nu kan du testa för att kontrol lera att du kan komma åt din webbapp och att den är belastningsutjämnad.

1. Öppna en webbläsare och bläddra till din domän. Till exempel contoso.com. Du bör se sidan standard webb program.
2. Stoppa din första webbapp.
3. Stäng webbläsaren och vänta några minuter.
4. Starta webbläsaren och bläddra till din domän. Du bör fortfarande se sidan standard webb program.
5. Stoppa den andra webbappen.
6. Stäng webbläsaren och vänta några minuter.
7. Starta webbläsaren och bläddra till din domän. Du bör se fel 403, vilket indikerar att webbappen har stoppats.
8. Starta din andra webbapp.
9. Stäng webbläsaren och vänta några minuter.
10. Starta webbläsaren och bläddra till din domän. Du bör se sidan standard webb program igen.

## <a name="next-steps"></a>Nästa steg

Mer information om aliin poster finns i följande artiklar:

- [Självstudier: Konfigurera en aliasresurspost för att referera till en offentlig Azure-IP-adress](tutorial-alias-pip.md)
- [Självstudier: Konfigurera en aliasresurspost som stöder spetsiga domän namn med Traffic Manager](tutorial-alias-tm.md)
- [Vanliga frågor och svar om DNS](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)

Information om hur du migrerar ett aktivt DNS-namn finns i [Migrera ett aktivt DNS-namn till Azure App Service](../app-service/manage-custom-dns-migrate-domain.md).
