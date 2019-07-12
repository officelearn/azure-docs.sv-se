---
title: Publicera en rot- eller apex domän till en befintlig åtkomsten med hjälp av Azure portal
description: Lär dig hur att publicera en rot- eller apex domän till en befintlig åtkomsten med hjälp av Azure portal.
services: front-door
author: sharad4u
ms.service: frontdoor
ms.topic: article
ms.date: 5/21/2019
ms.author: sharadag
ms.openlocfilehash: 8fe8da95a61d2f2bb35095236131670cb6ef0e70
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605783"
---
# <a name="onboard-a-root-or-apex-domain-on-your-front-door"></a>Publicera en rot- eller apex domän på ytterdörren
Azure ytterdörren använder CNAME-poster för att verifiera domänägarskap för onboarding av anpassade domäner. Dessutom ytterdörren exponera inte klientdelens IP-adress som är associerade med din ytterdörren profil och så du kan inte mappa din apex-domän till en IP-adress om avsikten är att publicera den till Azure ytterdörren.

Tilldelningen av CNAME-poster i basdomänen förhindrar att DNS-protokollet. Exempel: om din domän är `contoso.com`; du kan skapa CNAME-poster för `somelabel.contoso.com`; men du kan inte skapa CNAME-post för `contoso.com` själva. Den här begränsningen utgör ett problem för programägare som har belastningsutjämnade program bakom Azure ytterdörren. Eftersom du använder en profil för ytterdörren kräver skapandet av en CNAME-post, är det inte går att peka på profilen som ytterdörren från zonens apex.

Det här problemet kan lösas med hjälp av alias poster i Azure DNS. Till skillnad från CNAME-poster skapas alias poster i basdomänen och programägare kan använda den för att rikta sin zon apex-post till en ytterdörren-profil som har offentliga slutpunkter. Programägare peka på samma ytterdörren profil som används för andra domäner i sina DNS-zonen. Till exempel `contoso.com` och `www.contoso.com` kan peka på samma ytterdörren profil. 

Mappa din apex eller rotcertifikatet domän till din ytterdörren profil i princip kräver CNAME plattar ut eller DNS-jaga, vilket är en mekanism där i DNS providern rekursivt löser CNAME-posten tills den når en IP-adress. Den här funktionen stöds av Azure DNS för ytterdörren slutpunkter. 

> [!NOTE]
> Det finns andra DNS-providers samt som har stöd för CNAME förenkla eller DNS jaga, men Azure ytterdörren rekommenderar att du använder Azure DNS för sina kunder som värd för deras domäner.

Du kan använda Azure portal för att publicera en apex-domän på ytterdörren och aktivera HTTPS på den genom att associera den med ett certifikat för SSL-avslutning. Apex-domäner kallas även rot- eller utan www domäner.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Skapa en aliaspost som pekar på din ytterdörren-profil
> * Lägga till rotdomänen i åtkomsten
> * Konfigurera HTTPS på rotdomänen

> [!NOTE]
> Den här självstudien krävs att du redan har en ytterdörren-profil som har skapats. Se andra självstudier som [Snabbstart: Skapa en ytterdörren](./quickstart-create-front-door.md) eller [skapa en ytterdörren med HTTP till HTTPS-omdirigering](./front-door-how-to-redirect-https.md) att komma igång.

## <a name="create-an-alias-record-for-zone-apex"></a>Skapa en aliaspost för zonens apex

1. Öppna **Azure DNS** konfiguration för domänen som ska publiceras.
2. Skapa eller redigera posten för zonens apex.
3. Välj posten **typ** som _A_ spela in och välj sedan _Ja_ för **Alias postuppsättning**. **Aliastypen** ska vara inställd på _Azure-resurs_.
4. Välj den Azure-prenumeration där din ytterdörren profil finns och välj sedan ytterdörren resursen från den **Azure-resurs** listrutan.
5. Klicka på **OK** att skicka ändringarna.

    ![Aliaspost för zonens apex](./media/front-door-apex-domain/front-door-apex-alias-record.png)

6. Ovanstående steg skapas en post för apex av zonen som pekar på din ytterdörren resurs och en CNAME-Postmappning 'afdverify' (exempel – `afdverify.contosonews.com`) till `afdverify.<name>.azurefd.net` som kommer att vara används för onboarding av domänen på din ytterdörren-profil.

## <a name="onboard-the-custom-domain-on-your-front-door"></a>Publicera den anpassade domänen på ytterdörren

1. På fliken ytterdörren designer klickar du på ”+”-ikonen i Frontend-värdarna avsnittet för att lägga till en ny anpassad domän.
2. Ange rot- eller apex domännamnet i namnfältet på anpassade värden exempel `contosonews.com`.
3. När CNAME-mappningen från domänen till dörren har verifierats klickar du på **Lägg till** att lägga till den anpassade domänen.
4. Klicka på **spara** att skicka ändringarna.

![Meny för anpassad domän](./media/front-door-apex-domain/front-door-onboard-apex-domain.png)

## <a name="enable-https-on-your-custom-domain"></a>Aktivera HTTPS på din domän

1. Klicka på den anpassade domänen som har lagts till och under avsnittet **HTTPS för anpassad domän**, ändrar du statusen till **aktiverad**.
2. Välj den **certifikattyp management** till _”Använd mitt eget certifikat”_ .

> [!WARNING]
> Främre dörren hanteras certifikathanteringstyp stöds inte för närvarande för apex eller rotcertifikatet domäner. Det enda alternativet som är tillgängliga för att aktivera HTTPS på en domän för apex eller rotcertifikatet för ytterdörren använder egna anpassat SSL-certifikat finns i Azure Key Vault.

3. Kontrollera att du har installationen rätt behörigheter för åtkomsten till din key Vault enligt vad som anges i Användargränssnittet för innan du fortsätter till nästa steg.
4. Välj en **Key Vault-kontot** från din aktuella prenumeration och välj sedan lämplig **hemlighet** och **hemlig version** att mappa till rätt certifikat.
5. Klicka på **uppdatering** spara markeringen och klicka sedan på **spara**.
6. Klicka på **uppdatera** efter ett par minuter och sedan klicka på den anpassade domänen igen för att se förloppet för tilldelning av certifikat. 

> [!WARNING]
> Se till att du har skapat lämpliga routningsregler för apex-domän eller lagt till domänen till befintliga regler för routning.

## <a name="next-steps"></a>Nästa steg

- Läs hur du [skapar en Front Door](quickstart-create-front-door.md).
- Läs [hur Front Door fungerar](front-door-routing-architecture.md).