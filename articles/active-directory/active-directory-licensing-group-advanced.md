---
title: Azure Active Directory gruppbaserade licensiering fler scenarier | Microsoft Docs
description: Flera scenarier för Azure Active Directory gruppbaserade licensiering
services: active-directory
keywords: Azure AD-licensiering
documentationcenter: ''
author: curtand
manager: mtillman
editor: piotrci
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.component: users-groups-roles
ms.date: 06/02/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bb8bd727618eda2a887cc9e1b739889204eb87fa
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
ms.locfileid: "33764391"
---
# <a name="scenarios-limitations-and-known-issues-using-groups-to-manage-licensing-in-azure-active-directory"></a>Scenarier, begränsningar och kända problem använda grupper för att hantera licensiering i Azure Active Directory

Använda följande information och exempel för att få större kunskap om gruppbaserade licensiering i Azure Active Directory (AD Azure).

## <a name="usage-location"></a>Användningsplats

Vissa Microsoft-tjänster är inte tillgängliga på alla platser. Innan en användare kan tilldelas en licens, administratören måste ange den **användningsplats** egenskapen för användaren. I [Azure-portalen](https://portal.azure.com), du kan ange i **användare** &gt; **profil** &gt; **inställningar**.

För gruppen licenstilldelning ärver alla användare utan en användningsplats anges platsen för katalogen. Om du har användare på flera platser kan du se till att återspegla korrekt i din användarobjekt innan du lägger till användare till grupper med licenser.

> [!NOTE]
> Gruppen licenstilldelning kommer aldrig att ändra ett befintligt plats-värde för användning på en användare. Vi rekommenderar att du alltid användningsplats som en del av din användare skapa flödet i Azure AD (t.ex. via AAD Connect-konfiguration) – som innebär att resultatet av licenstilldelning alltid är korrekt, och användarna får inga tjänster på platser som inte tillåts.

## <a name="use-group-based-licensing-with-dynamic-groups"></a>Använda gruppbaserade licensiering med dynamiska grupper

Du kan använda gruppbaserade licensiering med någon säkerhetsgrupp, vilket innebär att den kan kombineras med dynamiska grupper i Azure AD. Dynamiska grupper kör regler mot användare objektattribut för att automatiskt lägga till och ta bort användare från grupper.

Du kan till exempel skapa en dynamisk grupp för vissa produkter som du vill tilldela användare. Varje grupp fylls av en regel för att lägga till användare med deras attribut, och varje grupp tilldelas de licenser som du vill kunna ta emot. Du kan tilldela attributet lokal och synkroniseras med Azure AD eller du kan hantera attributet direkt i molnet.

Licenser som har tilldelats användaren strax efter att de läggs till i gruppen. När attributet ändras användaren lämnar grupperna och licenser tas bort.

### <a name="example"></a>Exempel

Studera exemplet med en lokal lösning för Identitetshantering som bestämmer vilka användare ska ha åtkomst till Microsoft-webbtjänster. Den använder **extensionAttribute1** att lagra ett strängvärde som representerar de licenser som användaren ska ha. Azure AD Connect synkroniserar den med Azure AD.

Användare kan behöva en licens men inte en annan eller behöver du både. Här är ett exempel där du distribuerar Office 365 Enterprise E5 och Enterprise Mobility + Security (EMS) licenser till användare i grupper:

#### <a name="office-365-enterprise-e5-base-services"></a>Office 365 Enterprise E5: grundläggande tjänster

![Skärmbild av Office 365 Enterprise E5 grundläggande tjänster](media/active-directory-licensing-group-advanced/o365-e5-base-services.png)

#### <a name="enterprise-mobility--security-licensed-users"></a>Enterprise Mobility + Security: licensierade användare

![Skärmbild av Enterprise Mobility + Security licensierade användare](media/active-directory-licensing-group-advanced/o365-e5-licensed-users.png)

I det här exemplet ändrar en användare och deras extensionAttribute1 till värdet för `EMS;E5_baseservices;` om du vill att användaren har både licenser. Du kan göra den här ändringen lokalt. När ändringen synkroniseras med molnet, användaren läggs automatiskt till båda grupperna och tilldelade licenser.

![Skärmbild som visar hur du ställer in användarens extensionAttribute1](media/active-directory-licensing-group-advanced/user-set-extensionAttribute1.png)

> [!WARNING]
> Var försiktig när du ändrar en befintlig grupp medlemskapsregel. När en regel ändras medlemskap i gruppen kommer att vara ny utvärdering och användare som matchar den nya regeln inte längre kommer att tas bort (användare som fortfarande matchar den nya regeln inte påverkas under den här processen). Dessa användare har licenser tas bort under processen vilket kan resultera i förlust av tjänsten eller i vissa fall förlust av data.

> Om du har en stor dynamisk grupp beroende för licenstilldelning bör du verifiera några stora förändringar på en mindre testgrupp innan du tillämpar dem till gruppen huvudsakliga.

## <a name="multiple-groups-and-multiple-licenses"></a>Flera grupper och flera licenser

En användare kan vara medlem i flera grupper med licenser. Här följer några saker att tänka på:

- Flera licenser för samma produkt kan överlappa och de resultera i alla aktiverade tjänster som används för användaren. I följande exempel visas två licensiering grupper: *E3 grundläggande tjänster* innehåller foundation-tjänster för att distribuera först till alla användare. Och *E3 utökad services* innehåller ytterligare tjänster (gungning och Planner) för att distribuera endast till vissa användare. Användaren har lagts till båda grupperna i det här exemplet:

  ![Skärmbild av aktiverade tjänster](media/active-directory-licensing-group-advanced/view-enabled-services.png)

  Användaren har därför 7 12 tjänster i produkten aktiverad när du använder endast en licens för den här produkten.

- Att välja den *E3* licens visar mer information, inklusive information om vilka grupper orsakade vilka tjänster som ska aktiveras för användaren.

  ![Skärmbild av aktiverade tjänster efter grupp](media/active-directory-licensing-group-advanced/view-enabled-service-by-group.png)

## <a name="direct-licenses-coexist-with-group-licenses"></a>Direkt licenser samexistera med grupp-licenser

När en användare ärver en licens från en grupp, det går inte att direkt ta bort eller ändra denna licenstilldelning i användarens egenskaper. Vara måste gjorda ändringar i gruppen och sedan spridas till alla användare.

Det är dock möjligt att tilldela samma produktlicensen direkt till användaren, utöver den ärvda licensen. Du kan aktivera ytterligare tjänster från produkten för en användare utan att påverka andra användare.

Direkt tilldelade licenser kan tas bort och påverkar inte ärvt licenser. Överväg att användaren som en licens för Office 365 Enterprise E3 ärver från en grupp.

1. Inledningsvis användaren ärver licensen endast från den *E3 grundläggande tjänster* grupp, vilket gör att fyra serviceplaner som visas:

  ![Skärmbild av E3 grupp aktiverade tjänster](media/active-directory-licensing-group-advanced/e3-group-enabled-services.png)

2. Du kan välja **tilldela** direkt tilldela en E3-licens till användaren. I så fall måste ska du inaktivera alla serviceplaner utom Yammer Enterprise:

  ![Skärmbild av hur du tilldelar en licens direkt till en användare](media/active-directory-licensing-group-advanced/assign-license-to-user.png)

3. Därför kan användaren fortfarande använder endast en licens E3. Men direkttilldelning aktiverar Yammer Enterprise-tjänst för den användaren. Du kan se vilka tjänster är aktiverade som gruppmedlemskap jämfört med direkttilldelning:

  ![Skärmbild av ärvd jämfört med direkttilldelning](media/active-directory-licensing-group-advanced/direct-vs-inherited-assignment.png)

4. När du använder direkttilldelning tillåts följande åtgärder:

  - Yammer Enterprise kan stängas av på användarobjektet direkt. Den **på/av** växla i bilden har aktiverats för den här tjänsten, till skillnad från andra knapparna för tjänsten. Eftersom tjänsten är aktiverad direkt på användaren, kan den ändras.
  - Ytterligare tjänster kan aktiveras även som en del av direkt tilldelad licens.
  - Den **ta bort** knappen kan användas för att ta bort direkt licens från användaren. Du kan se att användaren bara har nu ärvda grupplicensen och bara de ursprungliga tjänsterna förbli aktiverat:

    ![Skärmbild som visar hur du tar bort direkttilldelning](media/active-directory-licensing-group-advanced/remove-direct-license.png)

## <a name="managing-new-services-added-to-products"></a>Hantera nya tjänster läggs till produkter
När Microsoft lägger till en ny tjänst för en produkt, kommer att aktiveras som standard i alla grupper som du har tilldelat produktlicensen. Användare i din klient som prenumererar på meddelanden om produktförändringar får e-post i förväg meddela dem om kommande service-tillägg.

Som administratör kan du granska alla grupper som påverkas av ändringen och vidta åtgärder, till exempel inaktivera den nya tjänsten i varje grupp. Om du har skapat grupper riktad endast vissa tjänster för distribution kan du besöker dessa grupper och se till att nyligen tillagda tjänster är inaktiverade.

Här är ett exempel på hur den här processen kan se ut:

1. Ursprungligen var du tilldelade den *Office 365 Enterprise E5* produkten till flera grupper. En av dessa grupper kallas *O365 E5 - endast Exchange* har utformats för att aktivera bara de *Exchange Online (planera 2)* tjänsten för dess medlemmar.

2. Du har fått ett meddelande från Microsoft som E5 produkten utökas med en ny tjänst - *Microsoft Stream*. När tjänsten blir tillgänglig i din klientorganisation, kan du göra följande:

3. Gå till den [ **Azure Active Directory > licenser > alla produkter** ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/LicensesMenuBlade/Products) och välj *Office 365 Enterprise E5*och välj **licensierad grupper** att visa en lista över alla grupper med produkten.

4. Klicka på den grupp som du vill granska (i det här fallet *O365 E5 - endast Exchange*). Då öppnas den **licenser** fliken. När du klickar på E5 licensen öppnas ett blad som visar en lista över alla aktiverade tjänster.
> [!NOTE]
> Den *Microsoft Stream* service har lagt till och aktiveras i den här gruppen, förutom automatiskt den *Exchange Online* tjänsten:

  ![Skärmbild av ny tjänst som lagts till i en grupp-licens](media/active-directory-licensing-group-advanced/manage-new-services.png)

5. Om du vill inaktivera den nya tjänsten i den här gruppen klickar du på den **på/av** växla bredvid tjänsten och klickar på den **spara** för att bekräfta ändringen. Azure AD kommer nu att bearbeta alla användare i gruppen för att tillämpa ändringen. ny användare läggs till i gruppen har inte den *Microsoft Stream* aktiverad.

  > [!NOTE]
  > Användare kan fortfarande ha tjänsten har aktiverats genom vissa andra licenstilldelning (en annan grupp som de är medlemmar i eller direkt licenstilldelning).

6. Om det behövs, utför du samma steg för andra grupper med den här produkten som tilldelats.

## <a name="use-powershell-to-see-who-has-inherited-and-direct-licenses"></a>Använda PowerShell för att se vem som har ärvd och direkt licenser
Du kan använda ett PowerShell-skript för att kontrollera om användarna har en licens som tilldelas direkt eller ärvdes från en grupp.

1. Kör den `connect-msolservice` för att autentisera och ansluta till din klient.

2. `Get-MsolAccountSku` kan användas för att identifiera alla etablerade produktlicenser i klienten.

  ![Skärmbild av cmdleten Get-Msolaccountsku](media/active-directory-licensing-group-advanced/get-msolaccountsku-cmdlet.png)

3. Använd den *AccountSkuId* värde för den licens som du är intresserad av i med [detta PowerShell-skript](./active-directory-licensing-ps-examples.md#check-if-user-license-is-assigned-directly-or-inherited-from-a-group). Detta genererar en lista över användare som har denna licens med information om hur de tilldelas.

## <a name="use-audit-logs-to-monitor-group-based-licensing-activity"></a>Använd granskningsloggarna för att övervaka gruppbaserade licensiering aktivitet

Du kan använda [granskningsloggar i Azure AD](./active-directory-reporting-activity-audit-logs.md#audit-logs) att se alla aktiviteter som rör gruppbaserade licensiering, inklusive:
- vem som har ändrat licenser på grupper
- När systemet har börjat bearbeta en ändring av licens och när den har slutförts
- vilka licens ändringar har gjorts till en användare på grund av en grupp licenstilldelning.

>[!NOTE]
> Granskningsloggar är tillgängliga på de flesta blad i Azure Active Directory-avsnittet i portalen. Beroende på var du komma åt dem, kanske filter före tillämpas endast visa aktivitet relevanta i kontexten på bladet. Om du inte ser oväntade resultat, granska [filtreringsalternativ](./active-directory-reporting-activity-audit-logs.md#filtering-audit-logs) eller komma åt ofiltrerade granskningsloggarna under [ **Azure Active Directory > aktivitet > granskningsloggar** ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Audit).

### <a name="find-out-who-modified-a-group-license"></a>Ta reda på vem som utförde en grupp-licens

1. Ange den **aktiviteten** filtrera *ange gruppen licens* och på **tillämpa**.
2. Resultatet innehåller alla fall av licenser som anges eller ändras på grupper.
>[!TIP]
> Du kan också skriva namnet på gruppen i den *mål* filter för att begränsa resultaten.

3. Klicka på ett objekt i listan att se information om vad som har ändrats. Under *ändrade egenskaper* både gamla och nya värdena för licenstilldelningen anges.

Här är ett exempel på gruppen licens ändringar, med information:

![Ändringar av skärmbild licens](media/active-directory-licensing-group-advanced/audit-group-license-change.png)

### <a name="find-out-when-group-changes-started-and-finished-processing"></a>Ta reda på när ändringar startat och har behandlats klart

När en licens ändras för en grupp, startar Azure AD ändringar till alla användare.

1. Om du vill se när grupper startade bearbetning, ange den **aktiviteten** filtrera *starta tillämpas licens grupp baserat på användare*. Observera att aktören för åtgärden är *Microsoft Azure AD gruppbaserade Licensing* -system-kontot som används för att köra alla ändringar i gruppen licens.
>[!TIP]
> Klicka på ett objekt i listan om du vill se den *ändrade egenskaper* fältet - visar licens-ändringar som har hämtats för bearbetning. Detta är användbart om du gjort flera ändringar i en grupp och du inte är säker på vilken bearbetades.

2. På liknande sätt när grupper har behandlats klart, Använd filtervärdet *Slutför tillämpas licens grupp baserat på användare*.
>[!TIP]
> I det här fallet den *ändrade egenskaper* fältet innehåller en sammanfattning av resultaten – det här är användbart för att snabbt kontrollera om bearbetning resulterade i fel. Exempel på utdata:
> ```
Modified Properties
...
Name : Result
Old Value : []
New Value : [Users successfully assigned licenses: 6, Users for whom license assignment failed: 0.];
> ```

3. Ange följande filter om du vill se en komplett logg för hur en grupp har behandlats, inklusive alla användarändringar:
  - **Initierad av (aktören)**”: Microsoft Azure AD gruppbaserade licensiering”
  - **Datumintervall** (valfritt): anpassade intervallet för när du vet att en specifik grupp har startat och har behandlats klart

Detta exempel på utdata visar starten för bearbetning, resulterande ändringar av användare och slutdatumet för bearbetning.

![Ändringar av skärmbild licens](media/active-directory-licensing-group-advanced/audit-group-processing-log.png)

>[!TIP]
> Artiklar som rör *ändra användarlicens* visar information om licens ändringar tillämpas på varje enskild användare.

## <a name="deleting-a-group-with-an-assigned-license"></a>Ta bort en grupp med en tilldelad licens

Det går inte att ta bort en grupp med en aktiv-licens. En administratör kan ta bort en grupp som inte inse att den kommer att orsaka licenser tas bort från användare – därför vi kräver att alla licenser som ska tas bort från gruppen först innan den kan tas bort.

När du försöker ta bort en grupp i Azure-portalen kan du se ett felmeddelanden så här: ![skärmbild grupp borttagningen misslyckades](media/active-directory-licensing-group-advanced/groupdeletionfailed.png)

Gå till den **licenser** fliken på gruppen och se om det finns några tilldelade licenser. Om Ja, ta bort dessa licenser och försök att ta bort gruppen igen.

Du kan se liknande fel vid försök att ta bort gruppen via PowerShell eller Graph API. Om du använder en grupp som synkroniserats från lokala kan Azure AD Connect också fel rapporteras om den inte kan ta bort gruppen i Azure AD. I sådana fall se till att kontrollera om det finns några licenser som tilldelats gruppen och ta bort dem först.

## <a name="limitations-and-known-issues"></a>Begränsningar och kända problem

Om du använder gruppbaserade licensiering, är en bra idé att bekanta dig med följande lista över kända problem och begränsningar.

- Gruppbaserade licensiering för närvarande stöder inte grupper som innehåller andra grupper (kapslade grupper). Om du använder en licens för en kapslad grupp har licenser för tillämpas omedelbart första nivån användare medlemmar i gruppen.

- Funktionen kan endast användas med säkerhetsgrupper. Office-grupper stöds inte för närvarande och du kommer inte att kunna använda dem i tilldelningen licens.

- Den [administrationsportalen för Office 365](https://portal.office.com ) inte stöder gruppbaserade licensiering. Om en användare som ärver en licens från en grupp, visas denna licens i administrationsportalen för Office som en vanlig användare-licens. Om du försöker ändra denna licens eller försök att ta bort licensen returnerar ett felmeddelande i portalen. Ärvda grupp licenser kan inte ändras direkt på en användare.

- När en användare tas bort från en grupp och förlorar licensen, serviceplaner från denna licens (till exempel SharePoint Online) är inställda på att en **pausad** tillstånd. Service-planer inte har angetts till ett slutligt, inaktiverat tillstånd. Den här försiktighetsåtgärden kan undvika oavsiktlig borttagning av användardata, om en administratör gör fel i grupphantering för medlemskap.

- När licenserna har tilldelats eller ändras för en stor grupp (till exempel 100 000 användare), kan det påverka prestanda. Mer specifikt volymen av ändringar som har genererats av Azure AD-automation kan påverka prestandan för din katalogsynkronisering mellan Azure AD och lokalt system.

- I vissa situationer med hög belastning, licens bearbetning kan vara fördröjd och ändringar, till exempel att lägga till/ta bort en grupp licens eller lägga till/ta bort användare från grupp, kan ta lång tid att bearbetas. Om du ser ändringarna mer än 24 timmar att bearbeta ta [skapar ett supportärende](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/supportRequest) för att vi ska undersöka. Vi kan förbättra prestandaegenskaperna för den här funktionen innan den når *allmän tillgänglighet*.

- Licens management automation reagerar inte automatiskt på alla typer av ändringar i miljön. Exempelvis kanske du har kört utanför licenser, orsakar vissa användare ska vara i ett feltillstånd. Att frigöra antal tillgängliga platser du kan ta bort vissa direkt tilldelade licenser från andra användare. Systemet dock inte automatiskt ta hänsyn till den här ändringen och åtgärda användare i den feltillstånd.

  Som en lösning för dessa typer av begränsningar kan du gå till den **grupp** bladet i Azure AD och klicka på **Ombearbeta**. Det här kommandot bearbetar alla användare i gruppen och löser fel-tillstånd om möjligt.

- Gruppbaserade licensiering registreras inte fel när en licens inte kan tilldelas en användare på grund av en duplicerad proxy-adresskonfiguration i Exchange Online; Dessa användare hoppas över under licenstilldelning. Mer information om hur du kan identifiera och lösa problemet finns [i det här avsnittet](./active-directory-licensing-group-problem-resolution-azure-portal.md#license-assignment-fails-silently-for-a-user-due-to-duplicate-proxy-addresses-in-exchange-online).

## <a name="next-steps"></a>Nästa steg

Mer information om andra scenarier för licenshantering via gruppbaserade licensiering finns:

* [Vad är gruppbaserade licensiering i Azure Active Directory?](active-directory-licensing-whatis-azure-portal.md)
* [Tilldela licenser till en grupp i Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md)
* [Identifiera och lösa eventuella problem med licens för en grupp i Azure Active Directory](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [Hur du migrerar enskilda licensierade användare till gruppbaserade licensiering i Azure Active Directory](active-directory-licensing-group-migration-azure-portal.md)
