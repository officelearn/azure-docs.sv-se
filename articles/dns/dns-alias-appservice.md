---
title: Värden Utjämning av nätverksbelastning Azure-webbappar i basdomänen
description: Använda en Azure DNS alias post till värd Utjämning av nätverksbelastning web apps på zonens apex
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 11/3/2018
ms.author: victorh
ms.openlocfilehash: 2b14753237e118540da6306fa9f06816f3e58b71
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2018
ms.locfileid: "50979866"
---
# <a name="host-load-balanced-azure-web-apps-at-the-zone-apex"></a>Värden Utjämning av nätverksbelastning Azure-webbappar i basdomänen

DNS-protokollet förhindrar tilldelningen av något annat än en A eller AAAA-post på zonens apex. En exempel-basdomänen är contoso.com. Den här begränsningen utgör ett problem för programägare som har belastningsutjämnade program bakom Traffic Manager. Det inte går att den pekar på Traffic Manager-profilen från zonen apex posten. Programägare måste därför använda en lösning. En omdirigering på programnivå omdirigera från basdomänen till en annan domän. Ett exempel är en omdirigering från contoso.com till www.contoso.com. Den här ordningen anger en enskild felpunkt för funktionen omdirigering.

Det här problemet finns inte längre med alias poster. Nu kan programägare peka deras zon apex-poster på en Traffic Manager-profil som har externa slutpunkter. Programägare kan peka på samma Traffic Manager-profilen som används för andra domäner i sina DNS-zonen.

Till exempel kan contoso.com och www.contoso.com peka på samma Traffic Manager-profilen. Detta är fallet så länge Traffic Manager-profilen har endast externa slutpunkter som har konfigurerats.

I den här artikeln får du lära dig hur du skapar en aliaspost för din domän apex och konfigurera dina slutpunkter för Traffic Manager-profil för web apps.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Du måste ha ett domännamn tillgängligt som du kan ha i Azure DNS för att testa med. Du måste ha fullständig kontroll över den här domänen. Fullständig behörighet omfattar möjligheten att ange namnservern (NS)-poster för domänen.

Anvisningar för att vara värd för din domän i Azure DNS finns i [Självstudie: var värd för din domän i Azure DNS](dns-delegate-domain-azure-dns.md).

I den här självstudien exempel domänen är contoso.com, men använda ditt eget domännamn.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp för att lagra alla resurser som används i den här artikeln.

## <a name="create-app-service-plans"></a>Skapa App Service-planer

Skapa två Web App Service-planer i resursgruppen med hjälp av tabellen nedan för information om konfiguration. Läs mer om hur du skapar en App Service plan [hantera en App Service-plan i Azure](../app-service/app-service-plan-manage.md).


|Namn  |Operativsystem  |Plats  |Prisnivå  |
|---------|---------|---------|---------|
|ASP-01     |Windows|Östra USA|Utveckling/testning i D1-delade|
|ASP-02     |Windows|Centrala USA|Utveckling/testning i D1-delade|

## <a name="create-app-services"></a>Skapa App Services

Skapa två webbappar, en i varje App Service-plan.

1. Övre vänstra hörnet på sidan för Azure-portalen, klicka på **skapa en resurs**.
2. Typ **webbapp** i sökfältet och tryck på RETUR.
3. Klicka på **Web App**.
4. Klicka på **Skapa**.
5. Acceptera standardinställningarna och använda tabellen nedan för att konfigurera två web apps:

   |Namn<br>(måste vara unika inom. azurewebsites.net)|Resursgrupp |App Service-Plan/plats
   |---------|---------|---------|
   |App-01|Använd befintlig<br>Välj en resursgrupp|ASP-01(East US)|
   |App-02|Använd befintlig<br>Välj en resursgrupp|ASP-02(Central US)|

### <a name="gather-some-details"></a>Samla in viss information

Nu ska du anteckna IP-adress och värddatornamn namnet för appar.

1. Öppna din resursgrupp och klicka på din första app (**App-01** i det här exemplet).
2. I den vänstra kolumnen klickar du på **egenskaper**.
3. Anteckna adressen under **URL**, och under **utgående IP-adresser** Notera den första IP-adressen i listan. Du använder den här informationen senare när du konfigurerar din Traffic Manager-slutpunkter.
4. Upprepa detta för **App-02**.

## <a name="create-a-traffic-manager-profile"></a>Skapa en Traffic Manager-profil

Skapa en Traffic Manager-profil i resursgruppen. Använd standardinställningarna och ange ett unikt namn inom trafficmanager.net-namnområdet.

Information om hur du skapar en Traffic Manager-profilen finns i [Snabbstart: skapa en Traffic Manager-profil för en högtillgänglig webbprogram](../traffic-manager/quickstart-create-traffic-manager-profile.md).

### <a name="create-endpoints"></a>Skapa slutpunkter

Nu kan du skapa slutpunkter för de två web apps.

1. Öppna din resursgrupp och klicka på Traffic Manager-profilen.
2. I den vänstra kolumnen klickar du på **slutpunkter**.
3. Klicka på **Lägg till**.
4. Använd tabellen nedan för att konfigurera slutpunkter:

   |Typ  |Namn  |Mål  |Plats  |Den anpassade rubrikinställningar|
   |---------|---------|---------|---------|---------|
   |Extern slutpunkt     |End-01|IP-adress som du registrerade för App-01|Östra USA|värden:\<URL: en som du registrerade för App-01\><br>Exempel: **värden: app-01.azurewebsites.net**|
   |Extern slutpunkt     |End-02|IP-adress som du registrerade för App-02|Centrala USA|värden:\<URL: en som du registrerade för App-02\><br>Exempel: **värden: app-02.azurewebsites.net**

## <a name="create-dns-zone"></a>Skapa DNS-zon

Du kan antingen använda en befintlig DNS-zon för testning eller du kan skapa en ny zon. För att skapa och delegera en ny DNS-zon i Azure, se [självstudie: vara värd för din domän i Azure DNS](dns-delegate-domain-azure-dns.md).

### <a name="add-the-alias-record-set"></a>Lägga till en alias-postuppsättning

När DNS-zonen är klar kan du lägga till en aliaspost för zonens apex.

1. Öppna din resursgrupp och klicka på DNS-zonen.
2. Klicka på **Postuppsättning**.
3. Lägg till posten med hjälp av följande tabell:

   |Namn  |Typ  |Alias-postuppsättning  |Aliastypen  |Azure-resurs|
   |---------|---------|---------|---------|-----|
   |@     |A|Ja|Azure-resurs|Traffic Manager - profilen|

## <a name="add-custom-hostnames"></a>Lägg till anpassade värddatornamn

Lägg till ett anpassat värdnamn till båda web apps.

1. Öppna din resursgrupp och klicka på din första webbapp.
2. I den vänstra kolumnen klickar du på **anpassade domäner**.
3. Klicka på **Lägg till värddatornamn**.
4. Under värdnamn, skriver du domännamnet på din. Till exempel contoso.com.

   Din domän bör verifiera och visar gröna bockmarkeringar bredvid **tillgänglighet för värdnamn** och **domänägarskap**.
5. Klicka på **Lägg till värddatornamn**.
6. Se det nya värdnamnet under **värdnamn som tilldelats platsen**, uppdatera din webbläsare. Uppdatering på sidan visar alltid inte ändringar direkt.
7. Upprepa proceduren för andra web Apps.

## <a name="test-your-web-apps"></a>Testa web apps

Nu kan du testa för att se till att du kan nå din webbapp och att de load-balanced.

1. Öppna en webbläsare och gå till din domän. Till exempel contoso.com. Du bör se app standardwebbsidan.
2. Stoppa din första webbapp.
3. Stäng webbläsaren och Vänta några minuter.
4. Starta webbläsaren och bläddra till din domän. Du bör fortfarande se app standardwebbsidan.
5. Stoppa andra web Apps.
6. Stäng webbläsaren och Vänta några minuter.
7. Starta webbläsaren och bläddra till din domän. Du bör se fel 403, som anger att webbappen har stoppats.
8. Starta webbappen andra.
9. Stäng webbläsaren och Vänta några minuter.
10. Starta webbläsaren och bläddra till din domän. Du bör se standardwebbsidan för appen igen.

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om alias poster, finns i följande artiklar:

- [Självstudie: Konfigurera en aliaspost för att referera till en Azure offentlig IP-adress](tutorial-alias-pip.md)
- [Självstudie: Konfigurera en aliaspost för att stödja apex-domännamn med Traffic Manager](tutorial-alias-tm.md)
- [Vanliga frågor och svar om DNS](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)
