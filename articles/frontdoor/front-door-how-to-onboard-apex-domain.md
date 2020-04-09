---
title: Ombord på en rot- eller apex-domän till en befintlig Ytterdörr - Azure-portal
description: Lär dig hur du rapporterar en rot- eller apex-domän till en befintlig ytterdörr med Hjälp av Azure-portalen.
services: front-door
author: sharad4u
ms.service: frontdoor
ms.topic: article
ms.date: 5/21/2019
ms.author: sharadag
ms.openlocfilehash: 4b74338f22a82d76ef13126ee0862b841bd89a99
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878892"
---
# <a name="onboard-a-root-or-apex-domain-on-your-front-door"></a>Ombord på en rot- eller apex-domän på ytterdörren
Azure Front Door använder CNAME-poster för att validera domänägarskap för introduktion av anpassade domäner. Ytterdörren exponerar inte heller ip-adressen för klientsidan som är associerad med din front door-profil och därför kan du inte mappa din apex-domän till en IP-adress, om avsikten är att gå in på den till Azure Front Door.

DNS-protokollet förhindrar tilldelning av CNAME-poster vid zonapexen. Till exempel om din `contoso.com`domän är ; Du kan skapa CNAME-poster för `somelabel.contoso.com`; men du kan inte skapa `contoso.com` CNAME för sig själv. Den här begränsningen utgör ett problem för programägare som har belastningsbalanserade program bakom Azure Front Door. Eftersom det krävs att du använder en profil för ytterdörren är det inte möjligt att peka på frontdörrsprofilen från zonsponsan.

Det här problemet löses med hjälp av aliasposter på Azure DNS. Till skillnad från CNAME-poster skapas aliasposter i zonens spets och programägare kan använda den för att peka sin zonapexpost till en frontdörrsprofil som har offentliga slutpunkter. Programägare pekar på samma frontdörrprofil som används för alla andra domäner i DNS-zonen. Till `contoso.com` exempel, `www.contoso.com` och kan peka på samma ytterdörr profil. 

Mappa din apex eller rotdomän till din Front Door-profil kräver i princip CNAME-förenkling eller DNS-jakt, vilket är en mekanism där i DNS-leverantören rekursivt löser CNAME-posten tills den träffar en IP-adress. Den här funktionen stöds av Azure DNS för front door-slutpunkter. 

> [!NOTE]
> Det finns även andra DNS-leverantörer som stöder CNAME-förenkling eller DNS-jakt, men Azure Front Door rekommenderar att du använder Azure DNS för sina kunder för att vara värd för sina domäner.

Du kan använda Azure-portalen för att gå in på en apex-domän på ytterdörren och aktivera HTTPS på den genom att associera den med ett certifikat för TLS-avslutning. Apex-domäner kallas också rot- eller nakna domäner.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Skapa en aliaspost som pekar på din profil för ytterdörren
> * Lägga till rotdomänen i ytterdörren
> * Konfigurera HTTPS på rotdomänen

> [!NOTE]
> Den här självstudien kräver att du redan har skapat en frontdörrprofil. Referera till andra självstudier som [Snabbstart: Skapa en ytterdörr](./quickstart-create-front-door.md) eller [Skapa en ytterdörr med HTTP till HTTPS-omdirigering](./front-door-how-to-redirect-https.md) för att komma igång.

## <a name="create-an-alias-record-for-zone-apex"></a>Skapa en aliaspost för zonapex

1. Öppna **Azure DNS-konfigurationen** för domänen som ska finnas ombord.
2. Skapa eller redigera posten för zonspons.
3. Markera **posttypen** som _en_ post och välj sedan _Ja_ för **alias-postuppsättning**. **Aliastypen** ska anges till _Azure-resurs_.
4. Välj azure-prenumerationen där din frontdörrprofil finns och välj sedan front door-resursen i listrutan **Azure-resurs.**
5. Klicka på **OK** om du vill skicka in ändringarna.

    ![Aliaspost för zonapex](./media/front-door-apex-domain/front-door-apex-alias-record.png)

6. Ovanstående steg kommer att skapa en zon spets rekord som pekar på din Ytterdörr resurs och `afdverify.contosonews.com`även `afdverify.<name>.azurefd.net` en CNAME postmappning "afdverify" (exempel - ) som kommer att användas för introduktion av domänen på din Ytterdörr profil.

## <a name="onboard-the-custom-domain-on-your-front-door"></a>Ombord på den anpassade domänen på din ytterdörr

1. På fliken Designer på ytterdörren klickar du på ikonen +på frontend-värdavsnittet för att lägga till en ny anpassad domän.
2. Ange rot- eller apex-domännamnet i det `contosonews.com`anpassade värdnamnsfältet, exempel .
3. När CNAME-mappningen från domänen till ytterdörren har validerats klickar du på **Lägg** till för att lägga till den anpassade domänen.
4. Klicka på **Spara** om du vill skicka ändringarna.

![Meny för anpassad domän](./media/front-door-apex-domain/front-door-onboard-apex-domain.png)

## <a name="enable-https-on-your-custom-domain"></a>Aktivera HTTPS på din anpassade domän

1. Klicka på den anpassade domänen som lades till och ändra statusen till **Aktiverad**under avsnittet **Anpassad domän HTTPS**.
2. Välj **typen Certifikathantering** för _att använda mitt eget certifikat._

> [!WARNING]
> Hanteringstyp för front door-hanterade certifikat stöds för närvarande inte för apex- eller rotdomäner. Det enda alternativet som är tillgängligt för att aktivera HTTPS på en apex- eller rotdomän för Ytterdörren använder ditt eget anpassade TLS/SSL-certifikat som finns på Azure Key Vault.

3. Se till att du har ställt in rätt behörigheter för ytterdörren för att komma åt ditt nyckelvalv som anges i användargränssnittet, innan du fortsätter till nästa steg.
4. Välj ett **Key Vault-konto** från din aktuella prenumeration och välj sedan lämplig **hemlig** och **hemlig version** för att mappa till rätt certifikat.
5. Klicka på **Uppdatera** för att spara markeringen och klicka sedan på **Spara**.
6. Klicka på **Uppdatera** efter ett par minuter och klicka sedan på den anpassade domänen igen för att se förloppet för certifikatetablering. 

> [!WARNING]
> Kontrollera att du har skapat lämpliga routningsregler för din apex-domän eller lagt till domänen i befintliga routningsregler.

## <a name="next-steps"></a>Nästa steg

- Läs hur du [skapar en Front Door](quickstart-create-front-door.md).
- Läs [hur Front Door fungerar](front-door-routing-architecture.md).